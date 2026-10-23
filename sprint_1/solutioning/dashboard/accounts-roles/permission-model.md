# Admin Permission Model — Interface Agreement

> **Document Type:** IFA / Authorization Contract  
> **Status:** Proposed target; current Go checks are implementation evidence  
> **Service / Domain:** Admin Access and Role Management  
> **Consumer / Partner:** Go admin API and JokiHub Admin Dashboard

## Design

UI design reference is not applicable to this authorization contract. Permission evaluation is server-side and applies to every protected admin API request. UI visibility may follow permissions for usability, but never replaces API enforcement.

## Sequence Diagram

```mermaid
sequenceDiagram
    actor Admin
    participant UI as Admin Dashboard
    participant API as Go Admin API
    participant Auth as Admin Authenticator
    participant RBAC as Permission Evaluator
    participant DB as Protected Resource
    Admin->>UI: Perform dashboard action
    UI->>API: Request with admin session/token
    API->>Auth: Validate token, account status, session
    alt Invalid or inactive admin
        Auth-->>API: Reject
        API-->>UI: 401 Unauthorized
    else Authenticated
        Auth-->>API: Admin subject and assigned roles
        API->>RBAC: Check required permission for action
        alt Permission missing or unknown
            RBAC-->>API: Deny by default
            API-->>UI: 403 Forbidden; no protected data access
        else Permission granted
            RBAC-->>API: Allow
            API->>DB: Validate and execute authorized operation
            DB-->>API: Result
            API-->>UI: Success or operation error
        end
    end
```

## SLA Targets

| Success Rate                                                  | VUser                                          | CPU / Memory                                                                         | Response Time                                 |
| ------------------------------------------------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------------ | --------------------------------------------- |
| 99.9% monthly availability; valid-request server error ≤ 0.1% | 50 VUser / 20 requests per second NFT workload | Initial per-instance baseline 2 vCPU / 4 GiB; CPU ≤ 70%, memory ≤ 75% at target load | Read p95 ≤ 500 ms; write p95 ≤ 1 s; p99 ≤ 2 s |

These are initial targets and workload assumptions, not measured results. Validate authorization overhead and full route mix through NFT before release.

## Performance / SLA Targets

| Success Rate                                                  | VUser                                          | CPU / Memory                                                                | Response Time                                                                                         |
| ------------------------------------------------------------- | ---------------------------------------------- | --------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| 99.9% monthly availability; valid-request server error ≤ 0.1% | 50 VUser / 20 requests per second NFT workload | Initial 2 vCPU / 4 GiB per instance; CPU ≤ 70%, memory ≤ 75% at target load | Authorization overhead p95 ≤ 50 ms; protected read p95 ≤ 500 ms; protected write p95 ≤ 1 s; p99 ≤ 2 s |

Targets are initial assumptions, not measured results. Validate authorization overhead and full route mix through NFT before release.

## General Information

| Component          | Value                                                                                                                                        |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Description        | Resolves whether an authenticated admin may perform a protected action, using active role-to-permission grants and server-side route policy. |
| Resource URL       | No standalone permission endpoint is introduced by this contract. Enforcement applies to registered protected admin routes.                  |
| Transport Protocol | HTTPS REST for protected route requests                                                                                                      |
| Request Verb       | Per protected feature route                                                                                                                  |
| Content Type       | Inherited from protected feature route                                                                                                       |
| Interaction Type   | Synchronous authorization check before protected data access                                                                                 |
| Security Policy    | Deny by default; enforce on backend for every request.                                                                                       |

### Path / Endpoint

No new route is defined. Each feature IFA declares the permission required for its method/path.

### API Versioning

- **Decision:** Reuse existing v1 feature routes. Permission evaluation is shared server behavior, not a new public API.
- **Backward Compatibility:** Existing paths remain unchanged. Any new permission key or policy change requires coordinated role grants and regression validation.
- **Reason:** Avoid exposing an authorization bypass surface through a separate permission-check endpoint.

## Business Rules

1. **Authentication precedes authorization.** Reject missing, invalid, expired, or inactive-admin sessions before reading protected data.
2. **Deny by default.** Missing role, missing grant, unknown permission key, invalid policy mapping, or evaluator failure must not grant access.
3. **Server-side enforcement is mandatory.** Hiding a menu or disabling a button in the dashboard is not authorization.
4. **Permission catalog evidence:** current Go handlers check `catalog-manage`, `users-manage`, `settings-manage`, `orders-manage`, `finance-read`, and `dashboard-read`; activity-log reads accept `dashboard-read` OR `settings-manage`. These keys describe current source behavior, not an approved final role matrix.
5. **Action mapping:** each protected route has one explicit required permission or an explicitly documented OR rule. Do not infer permissions from UI route names.
6. **Role assignment boundary:** only Super Admin may assign roles or delegate permissions, matching the observed source restriction. A caller may not grant permissions they do not hold; this proposal retains the stronger Super Admin-only boundary unless Security/Product approves a change.
7. **Super Admin:** source behavior includes a privileged bypass. Target policy should represent this as an explicit, auditable system role with controlled bootstrap and no ordinary dashboard CRUD. Bootstrap credentials and secrets must not appear in source or logs.
8. **Permission changes:** role-permission changes take effect on the next authorization decision. If grants are cached, invalidate or version the cache on mutation; stale grants must not survive revocation beyond the approved session/cache policy.
9. **Failure behavior:** authorization-store/evaluator failure returns a safe server error and does not continue to the handler. Never fail open.
10. **Audit:** log denied privileged access and role/permission changes with actor, action, target, timestamp, request ID, and outcome; do not log credentials, tokens, or secrets.
11. **Account directory is read-only in Sprint 1.** Admin list/detail reads require `settings-manage` under current source mapping. Admin account create/update/delete are not target operations.

## Request Headers

Headers are inherited from each route contract. The shared admin session is supplied using the approved bearer token or secure admin cookie. Missing/invalid authentication returns HTTP 401 before permission evaluation.

## Request Parameters

Not applicable at the shared authorization layer. Feature-level parameters must be validated by the corresponding endpoint after authorization, according to its contract.

## Request Body

Not applicable. Permission is derived from authenticated server-side identity and stored grants; callers cannot submit a permission claim to authorize their own request.

## Response Body

No standalone response body is exposed. The protected route returns its own success/error contract. Authorization denial returns that route's standard error envelope with no protected resource data.

## HTTP Status Codes

| HTTP Status | Usage                                                                                   |
| ----------- | --------------------------------------------------------------------------------------- |
| 401         | Admin session is missing, invalid, expired, or tied to an inactive account.             |
| 403         | Authenticated caller lacks the required permission or attempts unauthorized delegation. |
| 500         | Authorization policy/store cannot be evaluated safely; request is denied (fail closed). |

## Service Status Codes

Use the feature endpoint's established error envelope and service-code convention. Do not introduce a shared service code until the API owner aligns the Go `{code,message,data}` envelope with target IFA conventions.

## Exposure

Authorization is internal middleware/service behavior for dashboard APIs, not a separately callable endpoint. All dashboard routes remain protected by perimeter controls, authentication, backend authorization, audit, and rate limiting appropriate to deployment.

## Testing

| Scope      | Required | Rationale / Handoff                                                                                                                                                                       |
| ---------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| NFT        | Yes      | Measure authorization overhead at 50 VUser / 20 requests per second across the representative route mix; verify target latency and headroom.                                              |
| VA/Pentest | Yes      | Test missing/expired session, permission omission, unknown permission, horizontal/vertical escalation, Super Admin boundary, role delegation, cache revocation, and fail-closed behavior. |
| Regression | Yes      | Verify each route's required permission and documented OR rules; verify denied requests do not read or mutate protected resources.                                                        |

## Source / Model References

- [Current Go dashboard route IFA](../../../sprint_3/solutioning/backend-go/dashboard-source-ifa.md) — observed permission keys and route checks.
- [Role ERD](../../../../sprint_1/erd/role-erd.md) — role-permission and account-role relationships.
- [Role update IFA](update-role.md) — permission list replacement and source-mapped role mutation contract.
- [Dashboard decision log](../../dashboard-management/QUESTION.md) — product decisions and initial SLA baseline.

## Decisions Still Needed Before Policy Enforcement Changes

- Security/Product must approve the final permission-to-role matrix, including which roles receive each permission.
- Confirm whether the Super Admin bypass remains permanent and define its break-glass/bootstrap controls.
- Define session revocation and permission-cache lifetime; this contract requires prompt revocation and fail-closed behavior but has no measured deployment-specific cache value.
