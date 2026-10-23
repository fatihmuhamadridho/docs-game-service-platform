# Checkout and Payment Questions

## Q-PAY-001 — Evidence submission release gate

**Question:** How should BR-010 evidence-submission criteria be reconciled with Go proof routes gated by `FEATURE_SPRINT_2`? Is proof upload deferred, or is another approved Sprint 1 evidence path required?

**Source / evidence gap:** BR-010 requires evidence submission; Go upload/finalize routes are feature-gated, and the private proof-image route is also Sprint 2 gated. The route flow uses provider upload-token/callback followed by finalization, not direct multipart upload.

**Affected ACs / routes:** [Payment Evidence Submission](../../../../sprint_3/acceptance-criteria/public/checkout/payment-evidence-submission.md), [Manual Payment Instructions](manual-payment-instructions.md), `/order/[gameSlug]`, `/order/[gameSlug]/result`, proof upload/finalize routes.

**Decision owner:** Product/operations roles are not identified for the release-gate decision in supplied sources.

**Impact:** Sprint 1 acceptance, customer evidence flow, and dependencies on private storage cannot be finalized.

## Q-PAY-002 — Report-paid review outcome

**Question:** What manual-review outcome and allowed status transitions follow a valid customer payment report, and which role is authorized to decide it?

**Source / evidence gap:** Go exposes `POST /api/orders/v1/payments/{paymentId}/report-paid` and `POST /api/orders/v1/tracking/{orderNumber}/payment`; submission alone does not establish settlement. No approved review policy or transition table was supplied.

**Affected ACs / routes:** [Customer Payment Report](payment-report.md), [Manual Payment Instructions](manual-payment-instructions.md), [Admin Order Operations](../../../../sprint_2/acceptance-criteria/dashboard/orders/admin-order-operations.md), both report-paid routes above.

**Decision owner:** Product and operations roles are identified in the payment-report story notes.

**Impact:** Customer messaging and downstream admin decisions must not equate a report with confirmed payment until this policy is set.

## Q-PAY-003 — Payment status consumers

**Question:** Which customer flow owns the payment-status read, and how should it relate to tracking payment details/status?

**Source / evidence gap:** Go separately registers `GET /api/orders/v1/payments/{paymentId}/status` and `GET /api/orders/v1/tracking/{orderNumber}/payment`; route presence does not show which frontend consumer is intended.

**Affected ACs / routes:** [Customer Payment Report](payment-report.md), [Customer Order Status Timeline](../orders/order-status-timeline.md), the two GET routes above.

**Impact:** The customer status contract and route ownership remain ambiguous; avoid treating these endpoints as interchangeable.
