# Public Storefront Settings Questions

## Q-SET-001 — Consumers, exposure, and fallback

**Question:** Which public pages consume `GET /api/settings/v1/public`, which fields may be exposed, and what should users see when settings are unavailable?

**Source / evidence gap:** Go exposes a public settings route, but the Next.js route inventory shows no consumer and no approved public-field allowlist or unavailable-settings behavior.

**Affected ACs / routes:** [Public Storefront Settings](./public-storefront-settings.md), `GET /api/settings/v1/public`, BR-020.

**Decision owner:** Product/API roles are identified in the story notes.

**Impact:** Consumer work, privacy boundaries, and failure behavior cannot be finalized until the field contract and fallback are agreed.
