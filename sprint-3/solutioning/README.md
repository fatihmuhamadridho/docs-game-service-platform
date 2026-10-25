# Sprint 3 Solutioning — Customer Identity, Orders, Payments, and Dashboard Reads

Final delivery group: email-dependent customer identity and account management, order lifecycle, checkout, payment reporting/proof, tracking, and admin dashboard reads. Payment-evidence submission remains a separate BR-010 candidate with its existing release gate; technical source evidence does not approve release allocation.

## Customer identity and email dependency

- [Email and customer-auth open questions](public/auth/QUESTION.md)
- [Registration and login/session IFAs](public/auth)
- [Admin-managed account IFAs](dashboard/members)
- [Customer account and member ERDs](erd/user-management-erd.md) · [Member profile ERD](erd/member-erd.md)

These flows remain gated until email-provider, verification, and account-lifecycle decisions are approved. The administrator account and role models remain in Sprint 1.

## Order and payment lifecycle

- [Checkout and manual payment implementation](checkout-payment-implementation.md)
- [Order tracking implementation](order-tracking-implementation.md)
- [Checkout and proof APIs](public/checkout)
- [Public order tracking](public/orders)
- [Order/payment/proof Go APIs](backend-go/order-payment-ifa.md)
- [Admin order review/completion mutations](backend-go/admin-order-mutations-ifa.md)
- [Order ERD](erd/order-erd.md) · [Transaction ERD](erd/transaction-erd.md)

Checkout depends on catalog availability and approved guest/account and order-state policy. Payment instructions depend on configured bank/QRIS data; proof submission depends on private object storage and validation policy. Proof submission is not gateway settlement. BR-010 release allocation remains gated.

## Admin dashboard reads

- [Dashboard source analysis and route matrix](backend-go/dashboard-source-ifa.md)
- [Dashboard overview](dashboard/dashboard/get-overview.md)
- [Order list/detail](dashboard/orders)
- [Transaction list/detail](dashboard/transactions)
- [Activity history](dashboard/activity-logs)

These reads depend on persisted order/payment/activity records and approved metric definitions. Admin auth/RBAC behavior is verified as an access prerequisite in Sprint 1 account and role solutioning.

## Shared late controls

- Retry/idempotency and privacy remain cross-feature late hardening. Their AC live in [Sprint 3 shared criteria](../acceptance-criteria/shared); no separate solutioning contract is duplicated here.
