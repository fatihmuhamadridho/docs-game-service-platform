# JokiHub Sprint Plan — Proposed Delivery Order

> **Status:** Planning proposal only. Sprint assignment orders delivery; it does not approve scope, policy, estimates, or release commitments. BR and AC identifiers remain unchanged.

## Sprint sequence

| Sprint                           | Scope and delivery order                                                                                                                                           | Acceptance criteria | Requirement/status                                                                                                                                    |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------: | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Sprint 1](./sprint_1/BRD.md)    | Verify admin access → single seeded Super Admin role → read-only admin account directory → admin games → public game discovery                                     |                   — | Super Admin has full admin access and is immutable; additional roles are created explicitly, not seeded by default.                                   |
| [Sprint 2](./sprint_2/README.md) | Categories/services → settings/audit → lower-risk public content and recovery → mock/gated admin candidates                                                        |                   — | Mid-complexity catalog/supporting capabilities; BR-003–005, BR-019/020/022 plus candidates. Uncommitted.                                              |
| [Sprint 3](./sprint_3/README.md) | Email provider → member management, customer registration and password recovery → checkout/payment/order lifecycle → admin reads/dashboard; retry/privacy controls |                   — | Email-dependent customer identity plus payment lifecycle and dashboard consumers; BR-006/016, BR-007–015, BR-021, BR-024/025. Decisions remain gated. |

Acceptance criteria are not part of the planned deliverables going forward. Existing AC documents remain historical working material until separately reviewed; counts are omitted from this plan.

## Why this order

- **Access prerequisite:** Verify current admin login and authorization before protected admin mutations. Existing code has shared login UI and distinct customer/admin auth behavior. Legacy AC-PUB-004 spans registration/sign-in/session; registration is assigned to Sprint 3, while existing customer login/session is a separate flow. Admin authentication is a verification prerequisite, not a new admin-login story.
- **Sprint 1:** Start with exactly one seeded role, `SUPER_ADMIN`, with full admin access; protect it from update/delete. Additional roles are created explicitly, not seeded by default. Verify existing admin access before protected work. Super Admin is provisioned through a controlled backend/database/script bootstrap; dashboard admin account operations are read-only and require no email provider. Member management, customer registration, and email-dependent recovery move to Sprint 3; customer login/session remains its existing separate flow.
- **Sprint 2:** Category and service records depend on games. Settings, audit recording, and lower-risk public/supporting content follow the core identity/catalog foundation. Mock UI and payment-review candidates remain gated.
- **Sprint 3:** Email provider setup precedes member management, customer registration, and password recovery. Payment and order lifecycle require approved catalog/pricing, customer/guest identity rules, payment instructions, and order-state policy. Dashboard order, transaction, activity, and overview reads depend on persisted source records; overview also depends on approved metrics, currency basis, and freshness.
- **Cross-sprint controls:** Authorization is an early prerequisite. Retry safety and privacy remain late shared stories but block any earlier affected operation until approved and implemented.
- **BR-010:** Payment-evidence upload remains separately gated; see [candidate BRD](./sprint_3/payment-evidence-BRD.md).

## Traceability

- [Sprint 1 BRD](./sprint_1/BRD.md) · [Sprint 2 BRD](./sprint_2/BRD.md) · [Sprint 3 BRD](./sprint_3/BRD.md)
- Acceptance criteria are not planned deliverables; see each sprint BRD for scope and open decisions.

Questions, sizing TBDs, exclusions, and solutioning evidence gaps remain unresolved until their decision owners close them.
