# Sprint 1 - Public Game Catalog - Frontend Task Detail

> **Document Type:** Frontend Task Detail  
> **Status:** Draft  
> **Story / AC:** BR-001, BR-002  
> **Design:** [homepage.png](../../design/homepage.png), [game-list.png](../../design/game-list.png)

## Task Overview

| PIC                | Total Mandays |
| ------------------ | ------------: |
| Frontend Developer |          3.50 |
| **Total**          |      **3.50** |

## Task Breakdown

### Public Catalog

| ID             | Task / Deliverable                                                                                                                                                                                                                                                                              | Related AC     | Mandays | PIC                |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------: | ------------------ |
| FE-PUBGAME-001 | Implement public game discovery surfaces from homepage and game-list designs; render game artwork, name, and optional platform without requiring admin sign-in.                                                                                                                                 | BR-001         |    0.75 | Frontend Developer |
| FE-PUBGAME-002 | Integrate `GET /api/games/v1/list` for active games; support name/slug search, platform filter, default `SORT_ORDER_ASC` and `POPULAR`/`NAME_ASC` sorts, page default 1, limit default 20/max 100, and loading/empty/error states.                                                              | BR-001, BR-002 |    1.00 | Frontend Developer |
| FE-PUBGAME-003 | Integrate `GET /api/games/v1/popular` for popular games in API sort order; support page default 1, limit default 6/max 50, plus loading/empty/failure states.                                                                                                                                   | BR-001, BR-002 |    0.50 | Frontend Developer |
| FE-PUBGAME-004 | Implement pagination and query-state synchronization for search, platform, sort, and page changes; use explicit search submit (button or Enter), reset page when filters/search/sort change, preserve supported state on navigation, and ignore unsupported locale/query behavior per contract. | BR-002         |    0.75 | Frontend Developer |
| FE-PUBGAME-005 | Handle unavailable/broken artwork with a safe fallback, omit platform label when value is absent, and present public API 429/service errors without implying unpublished or failed requests are successful results.                                                                             | BR-001, BR-002 |    0.50 | Frontend Developer |

## Dependencies

- **Dependencies:** [Public game list](../../solutioning/public/games/get-list-games.md), [popular games](../../solutioning/public/games/get-popular-games.md), [Game Catalog ERD](../../erd/game-catalog-erd.md), [Sprint 1 HLD](../../hld/sprint-1-hld.md), homepage/game-list designs, and public image URLs from active game records. Homepage renders up to six popular cards; the game list defaults to 20 items per page and follows API pagination.

## Estimation Notes

- **Basis:** Coarse estimate split across page rendering, two read integrations, query/pagination behavior, and presentation fallbacks.
- **Included:** Responsive public pages, API integration, filters/search/sort/pagination, and loading/empty/error states.
- **Excluded:** Admin catalog operations, rating/review data, category/service discovery, multi-language content, backend filtering, and automated QA execution.
