# Public Services Integration Questions

## Q-SVC-001 — Frontend and Go route reconciliation

**Question:** Which services-by-game endpoint should the Next.js checkout call, and should Go's alternate route remain supported?

**Source / evidence gap:** Frontend checkout calls `GET /api/games/{slug}/services`; Go registers `GET /api/games/v1/{gameSlug}/services` and `GET /api/services/v1/games/{gameSlug}`. Neither Go path exactly matches the frontend call. Their validation behavior also differs.

**Affected ACs / routes:** [Public Services for a Game](./public-game-services.md), `/order/[gameSlug]`, the three routes above.

**Decision owner:** Product/API owner role is not identified in supplied sources.

**Impact:** Checkout service discovery may fail or use inconsistent slug/query validation until client and backend agree on the canonical contract.
