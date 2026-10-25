# Sprint 1 - Role and Permission Management - Backend Task Detail

> **Document Type:** Backend Task Detail  
> **Status:** Draft  
> **Story / AC:** BR-004, BR-006, BR-008  
> **Solutioning:** [Permission Model](../../../solutioning/dashboard/accounts-roles/permission-model.md), [List](../../../solutioning/dashboard/accounts-roles/get-list-roles.md), [Detail](../../../solutioning/dashboard/accounts-roles/get-detail-role.md), [Create](../../../solutioning/dashboard/accounts-roles/create-role.md), [Update](../../../solutioning/dashboard/accounts-roles/update-role.md), [Delete](../../../solutioning/dashboard/accounts-roles/delete-role.md); [Role ERD](../../../erd/role-erd.md)

## Task Overview

Estimates are planning estimates; confirm with delivery team. One Backend Developer.

| PIC               | Total Mandays |
| ----------------- | ------------: |
| Backend Developer |           8.0 |
| **Total**         |       **8.0** |

## Task Breakdown

### Seed, authorization, and read APIs

| ID          | Task / Deliverable                                                                                                                                                                                                                                                                       |                                           Related AC / API | Mandays | PIC               |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------: | ------: | ----------------- |
| BE-ROLE-001 | Seed system permission catalog and exactly one immutable ACTIVE `SUPER_ADMIN` role, which authorizes all admin functions including future catalog entries; ensure grants reference permission records while APIs expose stable permission keys.                                          |                      BR-004 / bootstrap + permission model |    1.00 | Backend Developer |
| BE-ROLE-002 | Implement role API authorization using shared session checks and current permission state; require `role.read/create/update/delete` per operation, allow SUPER_ADMIN bypass, enforce Origin/CSRF and safe 401/403 envelopes.                                                             |               BR-004, BR-006 / all `/api/admin/roles/v1/*` |    0.75 | Backend Developer |
| BE-ROLE-003 | Implement paginated role list with name/code case-insensitive search, ACTIVE/INACTIVE filtering, allowed sort fields and deterministic `id ASC` tie-break; defaults page 1, limit 10, max 100, search max 100. Exclude deleted roles; include `SUPER_ADMIN` and granted permission keys. |                    BR-004 / `GET /api/admin/roles/v1/list` |    1.00 | Backend Developer |
| BE-ROLE-004 | Implement role detail read by `role_` ID, validate ID, return full assigned permission keys and system marker; return 404 for missing/deleted/unavailable role and safe 500 on repository failure.                                                                                       | BR-004, BR-008 / `GET /api/admin/roles/v1/detail/{roleId}` |    0.50 | Backend Developer |

### Mutations and integrity

| ID          | Task / Deliverable                                                                                                                                                                                                                                                                           |                                              Related AC / API | Mandays | PIC               |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------: | ------: | ----------------- |
| BE-ROLE-005 | Implement create validation and persistence: trimmed name/description, case-insensitive unique name, uppercase code regex and uniqueness, ACTIVE default, max field lengths, and only active known permission keys. Reject duplicate business keys with 409; persist role/grants atomically. |            BR-004, BR-006 / `POST /api/admin/roles/v1/create` |    1.00 | Backend Developer |
| BE-ROLE-006 | Implement update as atomic replacement of supplied role fields and permission grants; validate permissions are active/known, enforce uniqueness, reject `SUPER_ADMIN` changes, preserve omitted fields, and make no-op update succeed without changing `updatedAt`.                          |    BR-004, BR-006 / `PUT /api/admin/roles/v1/update/{roleId}` |    1.25 | Backend Developer |
| BE-ROLE-007 | Implement soft-delete with `deleted_at`; reject deleting `SUPER_ADMIN` and any role assigned to active accounts; make repeated delete of retained tombstone succeed, return 404 for unknown/purged ID, and keep grants/history until retention purge.                                        | BR-004, BR-006 / `DELETE /api/admin/roles/v1/delete/{roleId}` |    1.00 | Backend Developer |
| BE-ROLE-008 | Add unique/partial indexes and transaction/session handling for role, grants and assignment constraints; ensure latest permission changes apply on next request, implement admin mutation throttles (60/admin/min, 1,200/IP/min), and map 400/401/403/404/409/429/500 safely.                |                                     BR-004, BR-006 / Role ERD |    1.50 | Backend Developer |

## Dependencies

- **Dependencies:** Admin authentication middleware and bootstrap; canonical permission catalog; MongoDB transaction-capable deployment; active `ACCOUNT_ROLE` assignment data.

## Estimation Notes

- **Basis:** Planning estimates cover API reads, mutations, authorization and data integrity; exact effort depends on existing Go persistence and transaction abstractions.
- **Included:** Validation, current-state authorization, pagination/filter/sort, uniqueness, atomic permission replacement, soft-delete, expected HTTP/service errors and indexes.
- **Excluded:** Admin-account role assignment endpoint (out of scope), permission catalog CRUD, UI, audit-event storage, and retention scheduler implementation if owned by platform operations.
