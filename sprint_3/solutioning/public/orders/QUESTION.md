# Order Tracking — Open Contract Decisions

## Q1 — Which tracking response/timeline is approved for customer display?

- **Question:** Should clients display current Go status labels and timeline entries as returned, including `description:null`, and use `view=result` only after payment reaches `PENDING_REVIEW` or `PAID`?
- **Evidence gap:** Go `TrackingByToken` constructs order/payment timeline from history, returns current labels, and rejects result view with HTTP 409 `ORDB02004` before payment report. Existing target IFA proposes a timeline display but no explicit approval for label text, empty descriptions, or result gating.
- **Affected routes/docs:** `GET /api/orders/v1/tracking/{orderNumber}/status`; [get order status IFA](get-order-status.md), [track order IFA](track-order.md), order/payment source IFA.
- **Decision owner:** Product/order owner.
- **Impact:** Customer status page, result disclosure, and compatibility of status descriptions.

## Q2 — What is intended lifetime and renewal policy for tracking tokens?

- **Question:** Is Go's 15-minute tracking token lifetime and no refresh route the approved behavior, or should token renewal be supported?
- **Evidence gap:** Go issues `trk_` plus 32 random bytes in URL-safe base64, stores token hash, expires after 15 minutes; route inventory contains no token refresh endpoint. No target lifecycle decision found.
- **Affected routes/docs:** Checkout, tracking lookup/status/payment, proof upload/finalize; order tracking and checkout IFAs.
- **Decision owner:** Security/API owner.
- **Impact:** Customer session continuity, re-lookup UX, and bearer-token exposure window.

## Q3 — Which payment confirmation route is the supported customer action?

- **Question:** Should the client call `POST /api/orders/v1/payments/{paymentId}/report-paid` with order number and WhatsApp, or use `POST /api/orders/v1/tracking/{orderNumber}/payment` with the scoped tracking token? Should both remain supported?
- **Evidence gap:** Go registers both routes. The first verifies payment ID + order number + normalized WhatsApp; the second derives the contact from the authorized order/token. Both call the same report operation, but checkout's `nextAction` names the action generically and no source establishes which consumer route is canonical. Sprint 2 gates report-paid on existing proof.
- **Affected routes/docs:** Both report routes, `POST /api/orders/v1/payments/{paymentId}/proofs`, [Go order/payment IFA](../../backend-go/order-payment-ifa.md), [checkout IFA](../checkout/continue-to-payment.md), [proof IFA](../checkout/submit-payment-proof.md).
- **Decision owner:** Product/API owner.
- **Impact:** Customer API call, whether PII is resent, token-only access policy, compatibility and abuse controls.

## Q4 — What release policy controls proof routes and image access?

- **Question:** What environment/rollout state enables `FEATURE_SPRINT_2`, and is proof upload/finalize plus admin image read required before this flag is enabled?
- **Evidence gap:** All three proof routes return 404 while the flag is false. Upload additionally requires configured private blob storage and callback URL; checkout changes `nextAction`, and payment report requires proof, when enabled. No rollout owner or release acceptance decision is recorded in source.
- **Affected routes/docs:** Checkout, report-paid, proof upload/finalize, admin proof image; [Go order/payment IFA](../../backend-go/order-payment-ifa.md), [proof IFA](../checkout/submit-payment-proof.md).
- **Decision owner:** Release/API owner (not identifiable in source).
- **Impact:** Checkout completion, proof submission and admin review availability, feature rollout safety, support escalation.
