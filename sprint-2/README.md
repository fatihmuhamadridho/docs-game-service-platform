# Sprint 2 — Catalog and Supporting Features

> **Status:** Proposed middle-complexity group; not a delivery commitment. Product and policy decisions remain open.

## Ordered scope

1. **Service categories** — admin management and public discovery, after games exist.
2. **Services** — admin management and public discovery, after category/game relationships and pricing/availability rules are approved.
3. **Platform settings** — settings policy and public projection must be approved before release. **Audit recording** is a separate cross-cutting requirement with its own taxonomy, retention, and failure-policy decisions.
4. **Supporting public page candidates** — help/about/home rendering, settings content, and access-denied/not-found states require a Product Owner scope decision and approved content. The existing `GET /api/settings/v1/public` data route ([local source audit](./solutioning/public/catalog/source-audit-2026-10-23.md)) is a separate interface; its existence does not approve page delivery or target exposure (D-01/D-09). These pages are not committed delivery.
5. **Gated and cross-sprint candidates** — password recovery, prototype-derived admin functions, and admin order/payment review remain unapproved Sprint 2 candidates; their story/AC IDs and count are unverified. Sprint 3 planning sequences password recovery after email-provider decisions and keeps payment review gated; reconcile these allocations against the missing Sprint 2 backlog before treating them as approved exclusions or transfers.

The requester confirmed that the acceptance-criteria/backlog index is not currently available. Source story documents are also not present in this repository, so the full story count, exact IDs, and dispositions cannot be verified here. The BRD and solutioning README record the Sprint 2 scope currently evidenced by the files in this repository and the open questions that still require the approved backlog.

BR-ID references are sprint-scoped until the portfolio register is confirmed. Sprint 1 says portfolio IDs are retained, but BR-003/004/005 have different meanings in Sprint 1 and Sprint 2, while BR-006/007/008 differ between Sprint 1 and Sprint 3. Do not merge traceability by BR number alone; see the Sprint 2 BRD for the current scope.

## Dependencies and open decisions

- Sprint 1 supplies approved game records and early identity/access foundations. Category and service availability depends on game, category, pricing, and publication rules.
- Sprint 3 planning sequences password recovery after email-provider decisions; its Sprint 2 backlog disposition remains unverified (D-13/D-15). Approve help, about, home, and public settings content before release only if the Product Owner confirms those pages are in scope.
- Decide settings ownership and public projection. Separately decide audit event taxonomy, retention, redaction, and audit-write failure behavior.
- Keep mock admin features as observed UI candidates until explicit product approval.
- Admin payment review/completion stays gated on payment lifecycle and BR-010 allocation decisions; implementation is sequenced with late payment work only if approved.
- Sprint 3 checkout also requires an approved source of customer-facing payment instructions; owner and whether this is platform settings or a separate payment configuration remain TBD (D-16).
- Estimates and dates remain TBD.

## Documents

- [Sprint 2 BRD](./BRD.md)
- [Sprint 2 admin and public IFA](./solutioning/)
- [Sprint 2 glossary](./GLOSSARY.md)
