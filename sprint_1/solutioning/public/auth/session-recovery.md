# Go Auth Recovery and Session APIs - Interface Agreement

> **Document Type:** IFA / API Contract  
> **Status:** Source-mapped draft; target envelope and account lifecycle decisions pending  
> **Service / Domain:** Go Auth  
> **Consumer / Partner:** JokiHub customer and admin web clients

## Design

Internal-service IFA covering six registered routes: `GET /api/auth/v1/session`, customer `POST /api/auth/v1/forgot-password` and `/reset-password`, and admin `POST /api/admin/auth/v1/login`, `/logout`, and `/refresh-token`. Current Go handlers are evidence for method, path, validations, authentication, and status. Frontend has calls to customer recovery and session refresh; admin-specific routes are registered but no FE call was found. These routes do not imply email-provider configuration or invitation availability.

## Sequence Diagram

```mermaid
sequenceDiagram
    actor Client as Customer/Admin client
    participant API as Go Auth HTTP
    participant Auth as AuthPresenter
    participant DB as MongoDB
    participant Mail as SMTP
    Client->>API: Request using registered method/path
    API->>API: Decode JSON (max 1 MiB; reject unknown fields)
    alt Recovery request
        API->>DB: Create hashed, expiring reset token if account exists
        API->>Mail: Send reset URL when configured
        API-->>Client: 202 generic accepted (AUTS00000)
    else Login/refresh/session
        API->>Auth: Verify credentials, refresh token, or access token
        Auth->>DB: Read/update account and session state
        API-->>Client: Set isolated admin cookies or return session status
    else Invalid request/session
        API-->>Client: Source status and AUT/ADM error code
    end
```

## Performance / SLA Targets

| Success Rate                                                    | VUser                                        | CPU / Memory                | Response Time                            |
| --------------------------------------------------------------- | -------------------------------------------- | --------------------------- | ---------------------------------------- |
| TBD; auth criticality target requires product/security approval | TBD; peak login/recovery concurrency unknown | TBD; capacity test required | TBD; do not infer from Playbook baseline |

## General Information

| Component          | Value                                                                                   |
| ------------------ | --------------------------------------------------------------------------------------- |
| Description        | Current Go contract for session status, password recovery, and admin session operations |
| Resource URL       | `https://{host}/api/...`                                                                |
| Transport Protocol | HTTPS REST                                                                              |
| Request Verb       | GET or POST per endpoint matrix below                                                   |
| Content Type       | `application/json` for POST; no body for GET                                            |
| Interaction Type   | Synchronous                                                                             |
| Security Policy    | HttpOnly isolated account cookies; rate limiting; trusted Origin required by logout     |

### Path / Endpoint

| Section        | Value                                |
| -------------- | ------------------------------------ |
| Host / Domain  | `{host}`                             |
| Service Name   | Auth                                 |
| Version        | `v1`                                 |
| Resource       | Exact path in endpoint matrix        |
| Final Endpoint | Exact method/path in endpoint matrix |

### API Versioning

- Decision: Existing v1 routes; this document records current Go behavior.
- Backward compatibility: Preserve existing route and cookie semantics. Any envelope or identity-lifecycle change requires explicit consumer decision.

## Business Rules

1. Account type is isolated: admin login/session uses admin access and refresh cookies; customer flows use customer cookies.
2. Forgot-password always returns generic acceptance when SMTP and base URL are configured, avoiding account enumeration. No email is sent when dependencies are unavailable; response is 503.
3. Reset token must match source token pattern, be unexpired, and be consumed once. Success changes password and revokes customer refresh sessions.
4. Admin credentials are checked only against admin identity for admin login. Authenticated admin routes require valid admin access token.
5. Rate limiting returns HTTP 429 and `Retry-After`; no retry target should be inferred by client.
6. Go source envelope is `{ "code": "...", "message": "...", "data": ... }`. The nested `{status:{code,message},data}` shape in examples below is a **proposed target**, not current wire behavior. Do not change clients until envelope decision is approved.

## Request Headers

| Header       | Type   | M/O/C |                 Max Length | Description                                                                        | Example                |
| ------------ | ------ | ----- | -------------------------: | ---------------------------------------------------------------------------------- | ---------------------- |
| Content-Type | String | C     |                          - | Required for POST JSON bodies                                                      | `application/json`     |
| Origin       | String | C     | Not constrained by handler | Logout compares supplied origin to request origin and trusted-origin configuration | `https://shop.example` |
| Cookie       | String | C     |                          - | Admin access/refresh cookie for admin session routes                               | `{httpOnly-cookie}`    |

## Request Parameters

| Parameter     | Type   | M/O/C |                 Max Length | Description                                                                                     | Example |
| ------------- | ------ | ----- | -------------------------: | ----------------------------------------------------------------------------------------------- | ------- |
| `accountType` | String | O     | Not constrained by handler | `CUSTOMER` or `ADMIN` selects one session; absent or any other value checks admin then customer | `ADMIN` |

## Request Body

| Attribute    | Type   | M/O/C | Max Length | Description                                                        | Example                                    |
| ------------ | ------ | ----- | ---------: | ------------------------------------------------------------------ | ------------------------------------------ |
| `email`      | String | M     |        254 | Forgot-password address; valid email syntax                        | `player@example.test`                      |
| `token`      | String | M     |         60 | Reset action token, 40–60 chars matching `[A-Za-z0-9_-]`           | `AbCdEf0123456789AbCdEf0123456789AbCdEf01` |
| `password`   | String | M     |        128 | New password, 8–128 chars                                          | `ExamplePass!234`                          |
| `identifier` | String | C     |        254 | Admin login username/email; aliases `username` or `email` accepted | `operator@example.test`                    |
| `username`   | String | C     |        254 | Login identifier alias; body may use one recognized identifier key | `operator`                                 |
| `email`      | String | C     |        254 | Login identifier alias                                             | `operator@example.test`                    |
| `password`   | String | M     |        128 | Admin credential                                                   | `ExamplePass!234`                          |

### Example Request Body

```json
{ "email": "player@example.test" }
```

The table groups fields across endpoints; `email`/`password` for login and recovery fields are not sent together. Logout, refresh, and session endpoints have no body.

### Request Validation

1. Semua body POST memakai JSON maksimal 1 MiB; field yang tidak dikenal dan JSON value tambahan ditolak.
2. `forgot-password` menerima hanya `email`; validasi email dan panjang maksimal 254. Feature `FEATURE_PASSWORD_RECOVERY` harus aktif.
3. `reset-password` menerima tepat `token` dan `password`; token harus sesuai pola sumber dan password 8–128 karakter. Feature recovery harus aktif.
4. Admin login menerima hanya `identifier`, `username`, `email`, `password`; identifier nonkosong maksimal 254 dan password maksimal 128.
5. `session` query `accountType=CUSTOMER` hanya mencoba customer; `ADMIN` hanya mencoba admin. Jika absent atau nilai lain, Go mencoba admin lalu customer; handler tidak menolak nilai lain.

## Response Body

Current Go envelope: `{ "code":"AUTS00000", "message":"...", "data":{} }`; errors use `data:null`. Current per-route data keys are listed below. Target nested envelope example is retained for cross-document template compatibility and is not wire evidence.

| Attribute                 | Type    | M/O/C |     Max Length | Description                        | Example                 |
| ------------------------- | ------- | ----- | -------------: | ---------------------------------- | ----------------------- |
| `code`                    | String  | M     |             10 | Current Go service code            | `AUTS00000`             |
| `message`                 | String  | M     |            200 | Current Go message                 | `Status sesi.`          |
| `data.accountType`        | String  | C     |              - | Session account type               | `ADMIN`                 |
| `data.name`               | String  | C     |            150 | Authenticated account display name | `Operator`              |
| `data.username`           | String  | C     |             30 | Authenticated username             | `operator`              |
| `data.role`               | String  | C     |              - | Admin/customer role                | `ADMIN`                 |
| `data.roleLabel`          | String  | C     |              - | Localized role label               | `Admin`                 |
| `data.canAccessDashboard` | Boolean | C     |              - | Admin dashboard access result      | `true`                  |
| `data.loggedOut`          | Boolean | C     |              - | Logout result                      | `true`                  |
| `data.expiresIn`          | Integer | C     |        seconds | Access-token lifetime              | `900`                   |
| `data.tokenType`          | String  | C     |              - | Admin login token type             | `Bearer`                |
| `data.admin.id`           | String  | C     | Source-defined | Admin account identifier           | `{admin-id}`            |
| `data.admin.name`         | String  | C     |            150 | Admin display name                 | `Operator`              |
| `data.admin.username`     | String  | C     |             30 | Admin username                     | `operator`              |
| `data.admin.email`        | String  | C     |            254 | Admin email                        | `operator@example.test` |
| `data.admin.role`         | Object  | C     |              - | Role projection                    | `{}`                    |

### Success Example

Current Go session wire example:

```json
{
  "code": "AUTS00000",
  "message": "Status sesi.",
  "data": {
    "accountType": "ADMIN",
    "name": "Operator",
    "username": "operator",
    "role": "ADMIN",
    "roleLabel": "Admin",
    "canAccessDashboard": true
  }
}
```

Current Go admin login response returns `tokenType`, `expiresIn`, and `admin` in `data`; access/refresh tokens are set in HttpOnly cookies, not returned as JSON. Logout returns `data.loggedOut`; refresh returns `data.accountType` and `data.expiresIn` and rotates cookies.

Forgot-password success (`202`) returns generic message and `data:null`; reset-password success (`200`) returns confirmation message and `data:null`.

Proposed template target (not current Go wire):

```json
{ "status": { "code": "AUTS00000", "message": "Request accepted" }, "data": {} }
```

### Business Error Example

```json
{ "code": "AUTB02001", "message": "Data tidak valid.", "data": null }
```

### Technical Error Example

```json
{ "code": "AUTT03001", "message": "Layanan autentikasi sedang tidak tersedia.", "data": null }
```

## HTTP Status Codes

| HTTP Status | Usage                                                                              |
| ----------- | ---------------------------------------------------------------------------------- |
| 200         | Admin login/session/logout/refresh, reset success                                  |
| 202         | Recovery request accepted with generic message                                     |
| 400         | Invalid payload or reset token                                                     |
| 401         | Invalid credentials/session                                                        |
| 403         | Admin logout Origin is not trusted; other routes use source-specific auth outcomes |
| 404         | Feature disabled                                                                   |
| 429         | Rate limit                                                                         |
| 500         | Auth/session dependency failure                                                    |
| 503         | Rate-limit store, SMTP, or recovery dependency unavailable                         |

## Service Status Codes

| Code                      | Type      | Category | Meaning                                          |
| ------------------------- | --------- | -------- | ------------------------------------------------ |
| `AUTS00000` / `ADMS00000` | Success   | 00       | Auth/session operation accepted                  |
| `AUTB01001` / `ADMB01001` | Business  | 01       | Invalid credentials/session                      |
| `AUTB01002`               | Business  | 01       | Rate limit exceeded                              |
| `AUTB02001` / `ADMB02001` | Business  | 02       | Invalid request                                  |
| `AUTB02005`               | Business  | 02       | Reset token invalid or expired                   |
| `AUTT02004`               | Technical | 02       | Feature or email recovery dependency unavailable |
| `AUTT03001` / `ADMT03001` | Technical | 03       | Auth/database dependency failure                 |

## API Exposure Classification

| Classification | Rationale                                                                                                                       |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Public         | Customer/admin web clients access internet-facing auth endpoints; admin operations require session protections and rate limits. |

## Testing & Documentation Scope

| Scope      | Required | Rationale / Handoff                                                                                                                                     |
| ---------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| NFT        | Yes      | Login/recovery are customer-facing; target VUser, CPU/memory, and TPS TBD from traffic forecast.                                                        |
| VA/Pentest | Yes      | Public authentication and token lifecycle; cover enumeration, brute force, cookie isolation, CSRF/origin, reset-token replay, and privilege separation. |

## References

- [Public auth QUESTION.md](QUESTION.md)
- `../../../../../go-game-service-platform/internal/controller/http/auth_http.go`
