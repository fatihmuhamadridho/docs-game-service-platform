# Sprint 3 Acceptance Criteria — Payment Lifecycle and Dashboard Reads

Twelve story documents are sequenced late because order/payment lifecycle data feeds admin read models. This proposed placement does not approve payment policy or gated proof handling.

## Prerequisites and gates

- Approved catalog/pricing, checkout, payment instruction, order/payment status, customer lookup privacy, and retry policy.
- Shared privacy and retry stories below apply as prerequisites to relevant flows. BR-010 evidence submission remains separately decision-gated.
- Persisted order/payment data precedes admin order/transaction reads; audit writes precede activity history; the dashboard overview follows approved read models and metric definitions.

## Customer payment and order stories

1. [Fixed-Price Order Checkout](./public/checkout/customer-checkout.md) — AC-PUB-005; BR-007/008.
2. [Manual Payment Instructions](./public/checkout/manual-payment-instructions.md) — AC-PUB-006; BR-009.
3. [Customer Payment Report](./public/checkout/payment-report.md) — AC-PAY-001/002/003; BR-009/010/024. Three AC IDs, one story document; BR-010 behavior remains decision-gated as noted in its questions.
4. [Payment Evidence Submission](./public/checkout/payment-evidence-submission.md) — AC-PUB-007; private storage and release allocation remain decision-gated.
5. [Customer Order Lookup](./public/orders/order-lookup.md) — AC-PUB-008; BR-011.
6. [Customer Order Status Timeline](./public/orders/order-status-timeline.md) — AC-PUB-009; BR-012.

## Admin read consumers

7. [Admin Order Records](./dashboard/orders/admin-order-records.md) — AC-ADM-011; BR-014. Requires persisted orders and approved visible fields.
8. [Admin Financial Transactions](./dashboard/transactions/admin-financial-transactions.md) — AC-ADM-012; BR-015. Requires persisted payment/transaction data and finance field policy.
9. [Admin Activity History](./dashboard/activity-logs/admin-activity-history.md) — AC-ADM-020; BR-021. Requires audit/activity data; Sprint 2 audit recording must precede it.
10. [Admin Dashboard Overview](./dashboard/dashboard/admin-dashboard-overview.md) — AC-ADM-010; BR-013. Last; depends on all read models and approved metrics/currency/freshness.

## Shared safeguards

- [Order and Evidence Retry Safety](./shared/shared-idempotent-retries.md) — AC-SHR-023; BR-024.
- [Customer, Payment, and Audit Data Privacy](./shared/shared-data-privacy.md) — AC-SHR-024; BR-025.

See [Sprint 3 BRD](../BRD.md), the separate [BR-010 candidate BRD](../payment-evidence-BRD.md), and [portfolio crosswalk](../../sprint_1/acceptance-criteria/portfolio-index.md). Estimates, policy, and release allocation remain TBD.
