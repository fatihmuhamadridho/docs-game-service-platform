# Sprint 1 - Admin Authentication and Session - Backend Task Detail

> **Document Type:** Backend Task Detail  
> **Status:** Draft  
> **Story / AC:** BR-006, BR-007  
> **Solutioning:** [Login](../../../solutioning/dashboard/auth/login.md), [Refresh Token](../../../solutioning/dashboard/auth/refresh-token.md), [Logout](../../../solutioning/dashboard/auth/logout.md); [Admin Account ERD](../../../erd/admin-account-erd.md), [Role ERD](../../../erd/role-erd.md)

## Task Overview

Estimates are planning estimates; confirm with delivery team. One Backend Developer.

| PIC               | Total Mandays |
| ----------------- | ------------: |
| Backend Developer |           8.0 |
| **Total**         |       **8.0** |

## Task Breakdown

### Authentication foundation and login

| ID          | Task / Deliverable                                                                                                                                                                                                                                                                                           |                                 Related AC / API | Mandays | PIC               |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -----------------------------------------------: | ------: | ----------------- |
| BE-AUTH-001 | Implement shared admin cookie authentication middleware: verify JWT issuer/audience/signature/expiry, resolve active session family, recheck account ACTIVE/non-deleted and current role/permission on each request; return safe 401/403 envelopes and never trust stale permission cache.                   |           BR-006, BR-007 / shared dashboard auth |    1.25 | Backend Developer |
| BE-AUTH-002 | Implement login validation and lookup by trimmed identifier using email when it contains `@`, otherwise username; use case-insensitive collation, require ACTIVE non-deleted account, Argon2id credential, and exactly one active role. Keep credential failures generic and do not mutate on invalid input. | BR-006, BR-007 / `POST /api/admin/auth/v1/login` |    1.00 | Backend Developer |
| BE-AUTH-003 | Add per-IP 20-request and per-normalized-identifier 5-failure rolling 15-minute throttles with `Retry-After`; enforce exact Origin allowlist, credentialed CORS, preflight behavior, and safe errors.                                                                                                        |                  BR-006, BR-007 / login, refresh |    0.75 | Backend Developer |
| BE-AUTH-004 | Issue 15-minute access JWT, 7-day opaque refresh token and CSRF token; persist only refresh/CSRF digests and session family, set exact `__Host-` HttpOnly/Secure/SameSite=None cookies, return profile and CSRF token only, and set `Cache-Control: no-store`.                                               |                                   BR-007 / login |    1.00 | Backend Developer |

### Refresh, logout, and lifecycle

| ID          | Task / Deliverable                                                                                                                                                                                                                                                                                       |                                   Related AC / API | Mandays | PIC               |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------: | ------: | ----------------- |
| BE-AUTH-005 | Rotate refresh token within same session family atomically: mark prior generation rotated and persist next generation; replay of a rotated valid token revokes entire family. Validate CSRF/origin and reject expired, revoked, inactive-account, or invalid sessions without partial rotation.          | BR-006, BR-007 / `POST /api/admin/auth/v1/refresh` |    1.25 | Backend Developer |
| BE-AUTH-006 | Revoke current session family on logout, clear both cookies with matching attributes, and return idempotent safe outcome for missing/expired session as specified by logout contract.                                                                                                                    |  BR-006, BR-007 / `POST /api/admin/auth/v1/logout` |    0.50 | Backend Developer |
| BE-AUTH-007 | Implement controlled bootstrap/provisioning support for exactly one immutable active `SUPER_ADMIN`, initial account, Argon2id credential and assignment; document safe secret injection, ensure no default secret or secret logging. Add account offboarding hook to revoke all active session families. |      BR-004, BR-007 / bootstrap; Admin Account ERD |    1.25 | Backend Developer |
| BE-AUTH-008 | Add persistence indexes/TTL and transactional repository behavior for session generations, digest uniqueness, family revocation and account status checks; sanitize logs and verify expected 400/401/403/429/500 mappings and no partial writes on store failure.                                        |                 BR-006, BR-007 / Admin Account ERD |    1.00 | Backend Developer |

## Dependencies

- **Dependencies:** MongoDB collections and indexes per admin-account/role ERDs; configured HTTPS dashboard Origin allowlist; deployment signing secret; shared permission catalog and account provisioning/offboarding mechanism.

## Estimation Notes

- **Basis:** Planning estimate split by independently verifiable auth, session, and persistence outcomes; validate against chosen Go framework and existing codebase patterns.
- **Included:** Endpoint behavior, validation, authorization/session middleware, persistence, rate limits, cookie/CSRF/CORS behavior, safe error mapping, and focused implementation verification.
- **Excluded:** Customer authentication, email/SMTP/reset flow, admin dashboard account CRUD, UI behavior, and load/pentest execution (QA/operations scope).
