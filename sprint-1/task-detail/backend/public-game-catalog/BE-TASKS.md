# Sprint 1 - Public Game Catalog - Backend Task Detail

> **Document Type:** Backend Task Detail  
> **Status:** Draft  
> **Story / AC:** BR-001, BR-002, BR-006  
> **Solutioning:** [Public Game List](../../../solutioning/public/games/get-list-games.md), [Popular Games](../../../solutioning/public/games/get-popular-games.md); [Game Catalog ERD](../../../erd/game-catalog-erd.md)

## Task Overview

Estimates are planning estimates; confirm with delivery team. One Backend Developer.

| PIC               | Total Mandays |
| ----------------- | ------------: |
| Backend Developer |           3.5 |
| **Total**         |       **3.5** |

## Task Breakdown

### Public catalog APIs

| ID             | Task / Deliverable                                                                                                                                                                                                                                                                                         |                                  Related AC / API | Mandays | PIC               |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------: | ------: | ----------------- |
| BE-PUBGAME-001 | Implement public catalog query restricted to active, non-deleted games; support case-insensitive name/slug search, platform MOBILE/PC/CONSOLE filter, page 1/limit 20 defaults (max 100), sort default `SORT_ORDER_ASC`, `POPULAR`, and `NAME_ASC`; ignore unknown query parameters and `Accept-Language`. | BR-001, BR-002, BR-006 / `GET /api/games/v1/list` |    1.25 | Backend Developer |
| BE-PUBGAME-002 | Shape public list response to include only approved display identity/content, required HTTPS `imageUrl`, optional platform, and pagination metadata; exclude inactive/deleted records and internal admin-only fields, rating and review count.                                                             |                      BR-001, BR-002 / public list |    0.50 | Backend Developer |
| BE-PUBGAME-003 | Implement popular endpoint constrained by `isActive=true`, `isPopular=true`, and non-deleted; order by sortOrder ascending, paginate default 6/max 50, and return public-safe display projection.                                                                                                          |      BR-001, BR-002 / `GET /api/games/v1/popular` |    0.75 | Backend Developer |
| BE-PUBGAME-004 | Add indexes/query plans for public visibility, popular flag, sort order, search and platform; apply public rate limit 1,200 requests/IP/minute and `Retry-After`; map invalid filters/page/limit, no results, throttling and database errors to documented envelopes.                                      |         BR-001, BR-002, BR-006 / Game Catalog ERD |    1.00 | Backend Developer |

## Dependencies

- **Dependencies:** Admin game management persists canonical fields; MongoDB catalog indexes; public API response schema from linked IFAs.
- **Contract note:** Trace to BR-001/BR-002/BR-006 and linked IFAs because no dedicated acceptance-criteria documents exist. Public ordering uses `sortOrder` ascending; the Game Catalog ERD requires `sort_order` to be unique across all stored records, so valid data has no tie to break.

## Estimation Notes

- **Basis:** Planning estimate for two public read endpoints, response projections, pagination/filtering and index-backed queries.
- **Included:** Public visibility rules, supported sorting/search/filter, validation, rate limiting and safe failures.
- **Excluded:** Rating/review data, category/service discovery, localization, admin CRUD, UI, and load test execution.
