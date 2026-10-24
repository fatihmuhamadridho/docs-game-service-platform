# JokiHub Documentation

Project documentation uses three proposed delivery groups, ordered from foundational work to the most data- and decision-dependent work. Sprint placement orders work; it does not approve requirements, policies, estimates, or release scope.

## Delivery grouping

- [Sprint plan and dependency gates](./SPRINT-PLAN.md)
- [Sprint 1 BRD — Access, identity, and games](./sprint_1/BRD.md)
- [Sprint 2 BRD — Catalog and supporting features](./sprint_2/BRD.md)
- [Sprint 3 BRD — Payment lifecycle and dashboard reads](./sprint_3/BRD.md) · [BR-010 payment evidence candidate](./sprint_3/payment-evidence-BRD.md)
- [Sprint 1 — Admin access and games](./sprint_1/BRD.md)
- [Sprint 2 — Catalog and supporting features](./sprint_2/README.md)
- [Sprint 3 — Payment lifecycle and dashboard reads](./sprint_3/README.md)
- [Shared glossary](./sprint_1/GLOSSARY.md)
- [Sprint 3 artwork reference](./sprint_3/reference/game-artwork-sources.md)

Solutioning documents remain drafts unless owners approve them. Existing acceptance-criteria files are historical working material; no new acceptance criteria are planned. Existing UI, routes, and source contracts describe current evidence, not product approval or complete implementation.

## Sprint grouping and status

| Sprint                           | Scope group                                                                                                           | Status and BRD traceability                                                                                               |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| [Sprint 1](./sprint_1/BRD.md)    | Verify admin access → single seeded Super Admin role → read-only admin account directory → games/discovery            | Super Admin has full admin access and is immutable; additional roles are created explicitly, not seeded by default.       |
| [Sprint 2](./sprint_2/README.md) | Service categories/services, settings/audit, lower-risk public content/recovery, prototype and operational candidates | Catalog/supporting capabilities. Uncommitted; BR-003–005, BR-019/020/022 plus gated candidates.                           |
| [Sprint 3](./sprint_3/README.md) | Email provider → member management, registration, password recovery → payment/order lifecycle and dashboard reads     | Email-dependent identity plus payment/dashboard work; BR-006/016, BR-007–015, BR-021, BR-024/025. Decisions remain gated. |

Sprint 1 verifies existing admin login/access before protected work. Role and permission management remain in Sprint 1; administrator account directory is read-only, and Super Admin is provisioned through a controlled backend/database/script bootstrap without email provider. Member management, customer registration, and password recovery move to Sprint 3 with any required email-provider dependency. Customer login/session remains separate. Games precede categories and services. Shared authorization is an early prerequisite; retry and privacy controls remain cross-sprint safeguards. Sprint 3 order/transaction reads require persisted order/payment records, activity history requires audit events, and overview follows all read models plus approved metric definitions.

Automated settlement/reconciliation, refund and payout execution, fulfillment execution, and admin payment approval/rejection remain excluded unless separately approved. Mock admin features remain unapproved candidates. Questions, TBD estimates, and source gaps remain unresolved.
