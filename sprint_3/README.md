# Sprint 3 — Payment Lifecycle and Dashboard Reads

> **Status:** Proposed final, highest-dependency group; not a delivery or release commitment. Payment and dashboard decisions remain gated.

## Scope and sequence

1. **Customer checkout and order creation** — requires approved catalog/pricing, customer or guest identity rules, and order lifecycle.
2. **Payment instructions, reporting, and evidence** — payment methods and manual instructions require approval; BR-010 evidence upload remains separately gated.
3. **Order lookup and customer-safe status** — follows approved order/payment transitions and privacy policy.
4. **Admin order and transaction reads** — follow persisted order/payment records and approved visible fields.
5. **Admin activity history** — follows audit recording and available event data.
6. **Dashboard overview last** — follows read models and approved metric definitions, currency basis, and freshness.

This sprint contains 12 AC story documents: four admin read/dashboard stories, six payment/order customer stories, and two shared retry/privacy stories. See [AC index](./acceptance-criteria/README.md). The separate [BR-010 candidate BRD](./payment-evidence-BRD.md) is not scope approval.

## Dependencies and open decisions

- Sprint 1 provides early games and account/access foundations; Sprint 2 provides service catalog and audit foundations.
- Approve prices, quote policy, guest/account checkout, payment methods/instructions, order/payment states, reporting behavior, and retry handling.
- Payment evidence requires separate approval for release allocation, private storage, validation, access, and retention.
- Admin-visible financial/customer fields require privacy and finance approval.
- Dashboard metrics require product/finance approval; values must not be treated as authoritative before definitions and source freshness are set.
- Shared retry safety and privacy remain cross-sprint prerequisites for affected earlier operations.
- Estimates, performance targets, and dates remain TBD.

## Documents

- [Sprint 3 BRD](./BRD.md)
- [BR-010 payment-evidence candidate BRD](./payment-evidence-BRD.md)
- [Sprint 3 AC index](./acceptance-criteria/README.md)
- [Cross-sprint AC and route index](../sprint_1/acceptance-criteria/portfolio-index.md)
- [Sprint 3 solutioning](./solutioning/README.md)
- [Sprint 3 glossary](./GLOSSARY.md)
- [Artwork reference](./reference/game-artwork-sources.md)
