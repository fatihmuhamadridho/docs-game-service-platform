# Sprint 1 - Admin Account Directory - Backend Task Detail

> **Document Type:** Backend Task Detail  
> **Status:** Draft  
> **Story / AC:** BR-003, BR-006  
> **Solutioning:** [List Admin Accounts](../../../solutioning/dashboard/admin-accounts/get-list-admin-accounts.md), [Admin Account Detail](../../../solutioning/dashboard/admin-accounts/get-admin-account-detail.md); [Admin Account ERD](../../../erd/admin-account-erd.md)

## Task Overview

Estimates are planning estimates; confirm with delivery team. One Backend Developer.

| PIC               | Total Mandays |
| ----------------- | ------------: |
| Backend Developer |           3.5 |
| **Total**         |       **3.5** |

## Task Breakdown

### Read-only administrator directory

| ID           | Task / Deliverable                                                                                                                                                                                                                                                                                                      |                                          Related AC / API | Mandays | PIC               |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------: | ------: | ----------------- |
| BE-ADMIN-001 | Implement admin-account read authorization and repository projection that never returns credential, session, password hash, CSRF or refresh-token data; require active authenticated admin and `admin-account.read` (or Super Admin), Origin and CSRF checks.                                                           |                        BR-003, BR-006 / shared admin auth |    0.50 | Backend Developer |
| BE-ADMIN-002 | Implement paginated account list with case-insensitive search on username/name/email, status and roleId filters, default page 1/limit 20 and max 100, stable `username ASC, admin_account_id ASC` ordering; exclude soft-deleted accounts and include each account's single active role, `lastActive`, and `updatedAt`. |             BR-003 / `GET /api/admin/admin-users/v1/list` |    1.00 | Backend Developer |
| BE-ADMIN-003 | Implement account detail lookup with `admin_` ID validation, read-only profile and one active role; return 404 for absent/deleted account and safe failure envelope for repository/role lookup errors.                                                                                                                  | BR-003 / `GET /api/admin/admin-users/v1/detail/{adminId}` |    0.75 | Backend Developer |
| BE-ADMIN-004 | Implement indexed queries and stable joins/lookups for account status, role assignment and last activity; fail the whole list/detail request with a safe technical error if the one-active-role invariant is violated; apply admin read throttles (600/admin/min, 1,200/IP/min) with `Retry-After`.                     |              BR-003, BR-006 / Admin Account and Role ERDs |    0.75 | Backend Developer |
| BE-ADMIN-005 | Map invalid query/ID, unauthenticated, forbidden, not found, rate limited and technical failures to documented 400/401/403/404/429/500 responses without leaking private fields or internal details.                                                                                                                    |                             BR-003, BR-006 / list, detail |    0.50 | Backend Developer |

## Dependencies

- **Dependencies:** Admin auth/permission middleware; controlled account provisioning and offboarding; ACCOUNT_ROLE and ADMIN_ACCOUNT collections/indexes.

## Estimation Notes

- **Basis:** Planning estimate for two read-only endpoints plus projections, query/index behavior and error mapping.
- **Included:** Authorization, input validation, pagination, deterministic response mapping and privacy checks.
- **Excluded:** Dashboard account create/update/delete, provisioning/offboarding execution workflow, role assignment API, and UI.
