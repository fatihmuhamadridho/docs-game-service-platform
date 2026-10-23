# Sprint 2 — Catalog and Supporting Features

> **Status:** Proposed middle-complexity group; not a delivery commitment. Product and policy decisions remain open.

## Ordered scope

1. **Service categories** — admin management and public discovery, after games exist.
2. **Services** — admin management and public discovery, after category/game relationships and pricing/availability rules are approved.
3. **Platform settings and audit recording** — settings policy and audit taxonomy/retention must be approved before release.
4. **Supporting public features** — recovery, help/about/home/settings content, and system-support pages, subject to identity-provider, content, and exposure decisions.
5. **Gated candidates** — nine prototype-derived admin stories and admin order/payment review remain unapproved and separately gated.

This sprint contains 22 story documents: catalog/settings/audit stories, supporting public stories, nine mock-derived candidates, and gated operational work. See [AC index](./acceptance-criteria/README.md) for exact story IDs and dispositions.

## Dependencies and open decisions

- Sprint 1 supplies approved game records and early identity/access foundations. Category and service availability depends on game, category, pricing, and publication rules.
- Decide password recovery provider/OTP policy; approve help, about, home, and public settings content before release.
- Decide settings ownership, audit event taxonomy, retention, redaction, and audit-write failure behavior.
- Keep mock admin features as observed UI candidates until explicit product approval.
- Admin payment review/completion stays gated on payment lifecycle and BR-010 allocation decisions; implementation is sequenced with late payment work only if approved.
- Estimates and dates remain TBD.

## Documents

- [Sprint 2 BRD](./BRD.md)
- [Sprint 2 AC index](./acceptance-criteria/README.md)
- [Sprint 2 solutioning](./solutioning/README.md)
- [Sprint 2 glossary](./GLOSSARY.md)
