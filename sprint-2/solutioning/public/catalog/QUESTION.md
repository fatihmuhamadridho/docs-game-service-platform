# Public Catalog — Open Contract Decisions

Current Go implementation evidence is in [catalog source audit](source-audit-2026-10-23.md). Questions below concern target policy/behavior, not whether current routes exist.

## Q1 — Should popular-games scan expand beyond first 100 active games?

- **Question:** Should `GET /api/games/v1/popular` scan all active games or retain current fixed 100-item source scan before applying popular filter and pagination?
- **Evidence gap:** `CatalogPresenter` obtains at most 100 sorted active games, then filters popular. No source requirement or acceptance decision says whether this bound is intentional.
- **Affected routes/docs:** `GET /api/games/v1/popular`; [popular games IFA](../../../../sprint-1/solutioning/public/games/get-popular-games.md), source audit.
- **Decision owner:** Catalog/product owner.
- **Impact:** Completeness of popular results and database query/load strategy.

## Q2 — What precision contract should public service prices use?

- **Question:** Should public catalog keep current JSON numeric `float64` price representation or use decimal string/minor-unit representation for exact currency values?
- **Evidence gap:** Mongo Decimal128 converts to float64 in current catalog presenters. No approved money serialization standard is present in the source audit or target contract.
- **Affected routes/docs:** Public game services and catalog routes; [services-by-game IFA](../services/get-list-services-by-game.md), [checkout IFA](../../../../sprint-3/solutioning/public/checkout/continue-to-payment.md), source audit.
- **Decision owner:** API/finance owner.
- **Impact:** Client display precision and authoritative checkout price comparison.

## Q3 — What caching policy applies to public settings and catalog reads?

- **Question:** Should public settings remain `Cache-Control: no-store`, and what cache/CDN policy and invalidation target should apply to catalog reads?
- **Evidence gap:** Go explicitly marks public settings no-store; catalog handlers do not set a cache policy. IFAs propose caching/invalidation but no TTL, freshness target, or platform decision exists.
- **Affected routes/docs:** Public settings, games, popular games, categories, services; source audit and public catalog IFAs.
- **Decision owner:** Product/platform owner.
- **Impact:** Exposure/freshness of settings and catalog, cache invalidation correctness, and read capacity.

## Q4 — Which query compatibility policy is intended?

- **Question:** Preserve route-specific current behavior (strict query allowlist on canonical services/categories, ignored query on alternate alias), or standardize query rejection/acceptance across public catalog endpoints?
- **Evidence gap:** Go source audit documents these different behaviors; target IFAs vary between rejecting unknown query keys and forward-compatible ignore. No API-wide policy found.
- **Affected routes/docs:** Public games list/popular, service categories, canonical/alternate services-by-game; source audit and catalog IFAs.
- **Decision owner:** API owner.
- **Impact:** Client compatibility, typo handling, and route alias equivalence.

## Q5 — Should the alternate services-by-game route remain supported?

- **Question:** Should `GET /api/services/v1/games/{gameSlug}` remain a supported alias alongside canonical `GET /api/games/v1/{gameSlug}/services`?
- **Evidence gap:** Both handlers are registered, but their query behavior differs and no deprecation or consumer decision is recorded.
- **Affected routes/docs:** Both routes; [services-by-game IFA](../services/get-list-services-by-game.md) documents the canonical route and alias, alongside the [catalog source audit](source-audit-2026-10-23.md).
- **Decision owner:** API/catalog owner.
- **Impact:** Route compatibility, query policy, and maintenance of the alternate handler.
