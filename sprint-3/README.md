# Sprint 3 — Payment Lifecycle and Dashboard Reads

> **Status:** Proposed final, highest-dependency group; not a delivery or release commitment. Payment and dashboard decisions remain gated.

## Scope and sequence

1. **Email provider and customer identity** — provider setup precedes member management, customer registration, and password recovery; all remain gated on policy and provider decisions.
2. **Customer checkout and order creation** — requires approved catalog/pricing, customer or guest identity rules, and order lifecycle.
3. **Payment instructions, reporting, and evidence** — payment methods and manual instructions require approval; BR-010 evidence upload remains separately gated.
4. **Order lookup and customer-safe status** — follows approved order/payment transitions and privacy policy.
5. **Admin order and transaction reads** — follow persisted order/payment records and approved visible fields.
6. **Admin activity history** — follows audit recording and available event data.
7. **Dashboard overview last** — follows read models and approved metric definitions, currency basis, and freshness.

Acceptance criteria are not planned deliverables. The separate [BR-010 candidate BRD](./payment-evidence-BRD.md) is not scope approval.

## Dependencies and open decisions

- Sprint 1 provides admin access, role, admin-account, and game foundations; Sprint 2 provides service catalog and audit foundations. Customer member/registration scope is assigned to Sprint 3.
- Email-provider selection/configuration is a Sprint 3 prerequisite for member invitations, registration verification, and password recovery. No provider is selected yet.
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
- [Sprint 3 solutioning](./solutioning/README.md)
- [Sprint 3 identity/email open questions](./solutioning/public/auth/QUESTION.md)
- [Sprint 3 glossary](./GLOSSARY.md)
