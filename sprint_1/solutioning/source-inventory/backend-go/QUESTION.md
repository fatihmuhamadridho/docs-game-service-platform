# Backend Go — Open Contract Decisions

Questions below remain open after checking current Go handlers and controllers. These are target decisions, not evidence that the current route is undocumented. Current Go behavior remains in each linked IFA.

## Q1 — Should response envelopes be standardized across Go route families?

- **Question:** Should admin/order/catalog Go routes that emit `{code,message,data}` be standardized with auth/public-catalog routes that already emit `{status:{code,message},data}`?
- **Evidence gap:** Source audits show two live envelope formats: admin/order handlers use top-level `code`,`message`; auth and public catalog use nested `status`. IFA target template also uses nested `status`. No approved API-wide normalization or consumer migration record found.
- **Affected routes/docs:** Admin, order/payment, auth, and public catalog route families; [route inventory](route-service-inventory.md), [implementation alignment](implementation-alignment.md), [auth source audit](../../public/auth/source-audit-2026-10-23.md), [catalog source audit](../../../../sprint_2/solutioning/public/catalog/source-audit-2026-10-23.md).
- **Decision owner:** API owner (role inferred from contract governance; no named owner in source).
- **Impact:** Breaking client parsing, error handling, and examples if changed in v1.

## Q2 — Is alternate public services alias retained?

- **Question:** Should `GET /api/services/v1/games/{gameSlug}` remain a supported alias alongside `GET /api/games/v1/{gameSlug}/services`?
- **Evidence gap:** Both handlers are registered; alias handler accepts different request/query behavior. No deprecation/consumer evidence or product need was found.
- **Affected routes/docs:** Both routes; [public catalog extensions](../../../../sprint_2/solutioning/backend-go/public-catalog-extensions-ifa.md), [service-by-game IFA](../../../../sprint_2/solutioning/public/services/get-list-services-by-game.md), route inventory.
- **Decision owner:** API owner.
- **Impact:** Determines route support, compatibility tests, and alias-specific error/query behavior.

## Q3 — What target flow replaces provider-token proof upload proposal?

- **Question:** Adopt current Go Vercel Blob token/callback flow, or retain proposed multipart/direct proof upload contract under a separate endpoint/version?
- **Evidence gap:** Go registers `/proofs/upload`, issues a signed client token, verifies `x-vercel-signature`, then finalizes via `/proofs`; older target proposal described `/proof-upload`/multipart. No approved migration decision found.
- **Affected routes/docs:** Upload, finalize, admin image; [order payment IFA](../../../../sprint_3/solutioning/backend-go/order-payment-ifa.md), [proof submission IFA](../../../../sprint_3/solutioning/public/checkout/submit-payment-proof.md), [alignment](implementation-alignment.md).
- **Decision owner:** API/platform owner.
- **Impact:** Frontend upload integration, storage provider coupling, idempotency, and endpoint compatibility.

## Q4 — When is manual payment report allowed relative to proof submission?

- **Question:** For `FEATURE_SPRINT_2`, should Go require a proof uploaded after the last report before allowing another report, as it currently does?
- **Evidence gap:** `OrderController.ReportPayment` checks proof `UploadedAt > payment.UpdatedAt`; older target payment-report behavior and AC do not establish that sequencing rule as approved.
- **Affected routes/docs:** `POST /api/orders/v1/payments/{paymentId}/report-paid`, tracking payment POST; order payment IFA and payment-report/proof-submission target IFAs.
- **Decision owner:** Product/payment policy owner.
- **Impact:** Changes whether a customer can resubmit report without new evidence; affects payment state transition and support workflow.

## Q5 — Which Go admin order mutations belong in approved business scope?

- **Question:** Are `POST /api/admin/orders/v1/payment-review` and `POST /api/admin/orders/v1/complete/{orderId}` approved Sprint 1 capabilities, and what policy determines approval/rejection/completion?
- **Evidence gap:** Routes and state guards exist in Go, but BRD/AC ownership is unresolved in the source inventory; current implementation does not itself establish product approval.
- **Affected routes/docs:** [admin order mutations IFA](../../../../sprint_3/solutioning/backend-go/admin-order-mutations-ifa.md), dashboard order detail, route inventory, order-related AC.
- **Decision owner:** Product/order owner.
- **Impact:** Business permission, allowed state transitions, audit requirements, and release gate.

## Q6 — Are probe routes liveness-only and should `/hello` remain exposed?

- **Question:** Confirm whether `/api/health` is process liveness only or should check readiness dependencies, and whether `/api/hello` should remain available in production.
- **Evidence gap:** `GET /api/health` returns static JSON without dependency checks; `/api/hello` returns static diagnostic JSON. Source cannot determine deployment ingress or orchestrator probe semantics.
- **Affected routes/docs:** Both operational routes; [operational probe spec](operational-probes.md), route inventory.
- **Decision owner:** Platform/operations owner.
- **Impact:** Probe configuration, dependency readiness behavior, and production exposure.
