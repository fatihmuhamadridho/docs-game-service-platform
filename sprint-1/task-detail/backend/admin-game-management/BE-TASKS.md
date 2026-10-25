# Sprint 1 - Admin Game Management - Backend Task Detail

> **Document Type:** Backend Task Detail  
> **Status:** Draft  
> **Story / AC:** BR-005, BR-006, BR-008  
> **Solutioning:** [Create](../../../solutioning/dashboard/games/create-game.md), [List](../../../solutioning/dashboard/games/get-list-games.md), [Detail](../../../solutioning/dashboard/games/get-detail-game.md), [Update](../../../solutioning/dashboard/games/update-game.md), [Delete](../../../solutioning/dashboard/games/delete-game.md); [Game Catalog ERD](../../../erd/game-catalog-erd.md)

## Task Overview

Estimates are planning estimates; confirm with delivery team. One Backend Developer.

| PIC               | Total Mandays |
| ----------------- | ------------: |
| Backend Developer |           7.5 |
| **Total**         |       **7.5** |

## Task Breakdown

### Game reads

| ID          | Task / Deliverable                                                                                                                                                                                 |                                           Related AC / API | Mandays | PIC               |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------: | ------: | ----------------- |
| BE-GAME-001 | Implement shared dashboard auth/permission checks for game reads and mutations (`game.read/create/update/delete`), Super Admin bypass, current-state checks, Origin/CSRF and standard rate limits. |                   BR-005, BR-006 / all dashboard game APIs |    0.75 | Backend Developer |
| BE-GAME-002 | Implement admin game list with page 1/limit 20 defaults, limit max 100, supported search/filter/sort from IFA, stable ordering, deleted-record exclusion, and full admin fields.                   |                    BR-005 / `GET /api/admin/games/v1/list` |    1.00 | Backend Developer |
| BE-GAME-003 | Implement admin detail using canonical `game_` + 26-character uppercase Crockford ULID ID; return all dashboard fields and 404 for missing/deleted record.                                         | BR-005, BR-008 / `GET /api/admin/games/v1/detail/{gameId}` |    0.50 | Backend Developer |

### Game mutations and integrity

| ID          | Task / Deliverable                                                                                                                                                                                                                                                                                                        |                                              Related AC / API | Mandays | PIC               |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------: | ------: | ----------------- |
| BE-GAME-004 | Implement create validation/persistence for required name, slug, HTTPS absolute image URL, optional Indonesian description max 500, optional MOBILE/PC/CONSOLE platform, defaults `isActive=false` and `isPopular=false`, and integer sortOrder; enforce unique active slug and return conflict without duplicate record. |            BR-005, BR-006 / `POST /api/admin/games/v1/create` |    1.25 | Backend Developer |
| BE-GAME-005 | Implement update with field-level validation and last-write-wins; only Super Admin may directly change `isActive`/`isPopular`; require `isPopular` only when active, preserve omitted fields, and return success without updating `updatedAt` for effective no-op.                                                        |  BR-005, BR-006 / `PATCH /api/admin/games/v1/update/{gameId}` |    1.25 | Backend Developer |
| BE-GAME-006 | Implement soft-delete and lifecycle side effects: set `deleted_at`, `isActive=false`, `isPopular=false`; repeated deletion of retained tombstone succeeds, unknown/purged ID returns 404, and nonempty DELETE body returns 400.                                                                                           | BR-005, BR-006 / `DELETE /api/admin/games/v1/delete/{gameId}` |    0.75 | Backend Developer |
| BE-GAME-007 | Add uniqueness/index support, persistence transactions where required, mutation/read rate limits and safe 400/401/403/404/409/429/500 mappings; ensure public readers cannot observe deleted/inactive records and retain tombstones two months.                                                                           |                             BR-005, BR-006 / Game Catalog ERD |    2.00 | Backend Developer |

## Dependencies

- **Dependencies:** Admin authentication and permission catalog; MongoDB indexes; canonical slug and ULID behavior from game solutioning and ERD.

## Estimation Notes

- **Basis:** Planning estimate across five CRUD flows, shared authorization and data constraints; validate against existing Go service/repository patterns.
- **Included:** Contract validation, permission enforcement, uniqueness, soft delete, concurrency behavior, safe error mapping, persistence/index work.
- **Excluded:** Public catalog endpoints, game image upload/storage, category/service management, external rating integration, UI, and load/pentest execution.
