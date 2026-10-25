# Sprint 1 - Admin Account Directory - Frontend Task Detail

> **Document Type:** Frontend Task Detail  
> **Status:** Draft  
> **Story / AC:** BR-003, BR-006  
> **Design:** [dashboard.png](../../design/dashboard.png); account directory screens TBD

## Task Overview

| PIC                | Total Mandays |
| ------------------ | ------------: |
| Frontend Developer |          2.50 |
| **Total**          |      **2.50** |

## Task Breakdown

### Account List

| ID           | Task / Deliverable                                                                                                                                                                                                                                 | Related AC     | Mandays | PIC                |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------: | ------------------ |
| FE-ADMIN-001 | Build read-only administrator directory showing username, name, email, status, lastActive, and assigned role; include pagination and detail navigation without create/edit/delete or role-assignment controls.                                     | BR-003         |    0.75 | Frontend Developer |
| FE-ADMIN-002 | Integrate `GET /api/admin/admin-users/v1/list` with search on username/name/email, status and roleId filters, page default 1 and limit default 20/max 100; render loading, no-results, fetch-error, and rate-limit states for non-deleted records. | BR-003, BR-006 |    0.50 | Frontend Developer |

### Account Detail and Access

| ID           | Task / Deliverable                                                                                                                                                                                                                 | Related AC     | Mandays | PIC                |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------: | ------------------ |
| FE-ADMIN-003 | Build account detail view and integrate `GET /api/admin/admin-users/v1/detail/{adminId}`; show username, name, email, status, the single active assigned role, `lastActive`, and `updatedAt`, with safe 401/403/404/500 handling.  | BR-003, BR-006 |    0.75 | Frontend Developer |
| FE-ADMIN-004 | Gate directory navigation and list/detail requests with `admin-account.read`; show forbidden state on 403 and ensure no dashboard affordance implies account provisioning, offboarding, credential management, or role assignment. | BR-003, BR-006 |    0.50 | Frontend Developer |

## Dependencies

- **Dependencies:** [Admin account list](../../solutioning/dashboard/admin-accounts/get-list-admin-accounts.md), [detail](../../solutioning/dashboard/admin-accounts/get-admin-account-detail.md), [Admin Account ERD](../../erd/admin-account-erd.md), [Sprint 1 HLD](../../hld/sprint-1-hld.md) session flow, and account directory design.

## Estimation Notes

- **Basis:** Coarse estimate for one list surface, one detail surface, and read permission behavior.
- **Included:** Responsive read-only views, pagination, API integration, loading/empty/error states, and client verification.
- **Excluded:** Admin account CRUD, account provisioning/offboarding, credential operations, role assignment, and backend access enforcement.
