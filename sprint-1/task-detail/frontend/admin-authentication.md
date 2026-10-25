# Sprint 1 - Admin Authentication - Frontend Task Detail

> **Document Type:** Frontend Task Detail  
> **Status:** Draft  
> **Story / AC:** BR-007, BR-006  
> **Design:** [login.png](../../design/login.png); session flows in [Sprint 1 HLD](../../hld/sprint-1-hld.md)

## Task Overview

| PIC                | Total Mandays |
| ------------------ | ------------: |
| Frontend Developer |          3.00 |
| **Total**          |      **3.00** |

## Task Breakdown

### Login

| ID          | Task / Deliverable                                                                                                                                                                                                                                                                    | Related AC     | Mandays | PIC                |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------: | ------------------ |
| FE-AUTH-001 | Implement login page from approved login design with labeled username/email and password inputs, submit action, keyboard/focus behavior, and responsive layout.                                                                                                                       | BR-007         |    0.75 | Frontend Developer |
| FE-AUTH-002 | Integrate `POST /api/admin/auth/v1/login` with credentials enabled; trim identifier only, preserve password exactly, handle field validation (identifier required/max 254; password required/max 128), disable duplicate submits, and route successful admin profile to `/dashboard`. | BR-007, BR-006 |    0.75 | Frontend Developer |
| FE-AUTH-003 | Render `Username/email atau password tidak sesuai.` for all 401 credential/account/role failures; show safe origin/validation/service errors for 400/403/500 and rate-limit feedback for 429 using `Retry-After`; never expose token cookies or sensitive response details.           | BR-006, BR-007 |    0.50 | Frontend Developer |

### Session Lifecycle

| ID          | Task / Deliverable                                                                                                                                                                                                                                                                             | Related AC     | Mandays | PIC                |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------: | ------------------ |
| FE-AUTH-004 | Implement shared admin session handling: send cookies with dashboard API calls, keep only returned CSRF token in localStorage for cross-tab use, attach `X-CSRF-Token` to cookie-authenticated mutations, and refresh access session through `POST /api/admin/auth/v1/refresh` once on expiry. | BR-006, BR-007 |    0.50 | Frontend Developer |
| FE-AUTH-005 | Handle refresh rejection and refresh-token replay outcomes by clearing CSRF/session UI state and returning to login; avoid redirect loops and show a safe session-expired notice.                                                                                                              | BR-006, BR-007 |    0.25 | Frontend Developer |
| FE-AUTH-006 | Implement explicit sign-out through `POST /api/admin/auth/v1/logout` with cookies and CSRF token; clear local CSRF state and navigate to login only on confirmed success, otherwise keep user informed that sign-out did not complete.                                                         | BR-006, BR-007 |    0.25 | Frontend Developer |

## Dependencies

- **Dependencies:** Auth API contracts for [login](../../solutioning/dashboard/auth/login.md), [refresh](../../solutioning/dashboard/auth/refresh-token.md), and [logout](../../solutioning/dashboard/auth/logout.md); configured dashboard/API origins; successful admin bootstrap; approved login design.

## Estimation Notes

- **Basis:** Coarse FE estimate; 0.25-0.75 manday per page/integration/state unit.
- **Included:** Login UI and request integration, cookie-based credential handling, CSRF header propagation, refresh/logout flows, safe error states, and client-side verification.
- **Excluded:** Credential provisioning, backend session/security implementation, password reset/SMTP, customer authentication, and dashboard feature authorization enforcement on the server.
