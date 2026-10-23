# Checkout — Open Contract Decisions

Current Go endpoint behavior is summarized in [the order/payment source IFA](../../backend-go/order-payment-ifa.md) and [implementation alignment](../../../../sprint_1/solutioning/source-inventory/backend-go/implementation-alignment.md). Existing checkout IFA describes proposed target behavior; source and target remain distinct.

## Q1 — Which checkout state and response contract should clients adopt?

- **Question:** Should the target use Go's current initial `orderStatus`/`paymentStatus`, returned `trackingToken`, `nextAction`, and idempotent replay semantics, or preserve the earlier proposal's status/envelope/next-action shape?
- **Evidence gap:** Go controller creates `UNPAID` payment and `PENDING_PAYMENT` order, returns tracking token (15-minute expiry), `nextAction` (`REPORT_PAYMENT` or `UPLOAD_PAYMENT_PROOF`), and replay flag; proposed IFA used different example IDs/statuses and nested envelope. No consumer acceptance decision found.
- **Affected routes/docs:** `POST /api/orders/v1/checkout`; [continue to payment IFA](continue-to-payment.md), [order/payment source IFA](../../backend-go/order-payment-ifa.md), route inventory.
- **Decision owner:** Product/API owner.
- **Impact:** Checkout UI step transitions, state display, token storage, and idempotent retries.

## Q2 — Which Idempotency-Key retention/replay behavior is contractual?

- **Question:** Approve current key syntax and 24-hour retention, including HTTP 200 replay and HTTP 409 for same key/different request or in-progress reservation?
- **Evidence gap:** Go requires `^[A-Za-z0-9._:-]{8,200}$`, hashes request/key, stores reservation for 24 hours, and replays stored response. Older proposal does not fix the retention interval or all replay outcomes.
- **Affected routes/docs:** `POST /api/orders/v1/checkout`; continue-to-payment IFA and implementation alignment.
- **Decision owner:** API/order owner.
- **Impact:** Client key generation, retry window, and duplicate-order handling.

## Q3 — Which next action and payment-report sequence should Sprint 2 clients follow?

- **Question:** Should checkout keep returning `nextAction=UPLOAD_PAYMENT_PROOF` when `FEATURE_SPRINT_2=true`, with `POST .../proofs` required before `POST .../report-paid`, or should customer confirmation alone remain available?
- **Evidence gap:** `OrderController.Checkout` switches `nextAction` on the feature flag. `ReportPayment` requires at least one proof under Sprint 2; otherwise it reports without proof. These are current Go behaviors, but product/release owner has not approved when the flag is enabled or which experience is the release gate.
- **Affected routes/docs:** `POST /api/orders/v1/checkout`, `POST /api/orders/v1/payments/{paymentId}/report-paid`, `POST /api/orders/v1/payments/{paymentId}/proofs`; [continue to payment IFA](continue-to-payment.md), [submit proof IFA](submit-payment-proof.md), [Go order/payment IFA](../../backend-go/order-payment-ifa.md).
- **Decision owner:** Product/API owner (release owner not identified in source).
- **Impact:** Checkout next-step UI, required evidence, availability of payment confirmation, feature-flag rollout and support instructions.
