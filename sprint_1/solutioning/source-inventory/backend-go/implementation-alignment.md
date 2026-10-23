# Go Implementation Alignment: API, Frontend, and Payment

> **Status:** Source-derived implementation alignment; technical reference, not approval of Sprint scope. API IFAs remain drafts unless separately approved. Go source is current runtime evidence; Next.js calls are integration evidence only.

## 1. Summary

This note maps shipped Go route behavior to the Next.js caller paths and identifies where proposed IFAs differ. It closes route-level documentation gaps for settings, auth/session, order payment/reporting/tracking, proof handling, and admin mutations. It does not promote mock UI or unconfigured integrations to supported production behavior.

## 2. Scope and evidence

- Go evidence: `go-game-service-platform/main.go`, `internal/controller/http/*.go`, `internal/controller/*.go`, repositories, and feature gates.
- Frontend evidence: `nextjs-game-service-platform/src/features/**` `apiFetch` call sites.
- Proposed contract evidence: adjacent `solutioning/public/**` and `solutioning/dashboard/**` IFAs.
- Request bodies are JSON unless identified as blob-provider protocol. Go shared JSON decoder caps body at 1 MiB, rejects unknown keys and trailing JSON values. Responses generally use `{code,message,data?}` through `writeJSON`; errors are translated by domain.
- Runtime feature gates/configuration matter: `FEATURE_SPRINT_2` gates proof routes and selected order behavior; payment instructions and private blob storage need configuration. This document does not infer deployment settings.

## 3. API flow and integration map

| User flow               | Frontend caller                                                       | Go route                                                                                                     | Current integration evidence                                                                                                                                                         |
| ----------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Storefront/catalog      | Home, Games, Checkout                                                 | `GET /api/games/v1/popular`, `/list`, `/api/service-categories/v1/list`, `/api/games/v1/{gameSlug}/services` | Calls exist in HomeV2, Games, Checkout; API contracts cover catalog. Checkout services-by-game path matches Go route.                                                                |
| Customer auth/recovery  | Login, Register, ForgotPassword, ResetPassword, session refresher     | `POST /api/auth/v1/login                                                                                     | register                                                                                                                                                                             | logout                                                                                                                                                                                        | refresh-token | forgot-password | reset-password`; `GET /api/auth/v1/session` | Calls exist for login/register/recovery/refresh. Logout/session presence must be checked in shared shell before treating as wired. No admin-specific auth route is called by FE evidence found. |
| Checkout/manual payment | Checkout, OrderPayment                                                | `POST /api/orders/v1/checkout`; `GET                                                                         | POST /api/orders/v1/tracking/{orderNumber}/payment`; `GET /api/orders/v1/payments/{paymentId}/status`                                                                                | Checkout and tracking payment GET/POST calls found; proof finalize calls `/proofs`. Payment status polling path exists in Go; confirm actual frontend call site before asserting it is wired. |
| Tracking                | OrderTracking, OrderTrackingResult                                    | `POST /api/orders/v1/tracking/lookup`; `GET /api/orders/v1/tracking/{orderNumber}/status`                    | Lookup call found. Result component constructs endpoint dynamically; Go handler requires bearer tracking token.                                                                      |
| Admin catalog           | AdminGames, AdminServices, AdminServiceCategories, AdminAccountsRoles | corresponding `/api/admin/...` routes                                                                        | FE list/detail/mutation calls found. Some contract IFAs use REST resource paths or PATCH while Go uses `/detail`, `/update`, `/delete` and PUT; use Go path for current integration. |
| Admin orders            | AdminOrders                                                           | list, `/detail/{orderId}`, `/payment-review`, `/complete/{orderId}`, proof image route                       | FE calls list/detail/review/complete. Proof image access is sourced from Go handler; FE rendering path needs runtime confirmation.                                                   |
| Admin settings/read     | settings, dashboard, activity, transaction, member/admin management   | corresponding `/api/admin/...` routes                                                                        | Calls found for settings/dashboard/activity/transactions/members/admin accounts; source authorization remains server-side.                                                           |

`apiFetch` calls target external `BASE_API_URL`; Next.js contains no API route handlers. A source call establishes intended integration, not successful deployment or backend reachability.

### Exact FE integration evidence for route deltas

| Next.js feature          | FE method/path evidence                                                             | Go registration                       | Finding                                                                                                                                         |
| ------------------------ | ----------------------------------------------------------------------------------- | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `AdminServices`          | GET list; POST `/create`; PUT `/update/{id}`; DELETE `/delete/{id}`                 | Go also registers GET `/detail/{id}`  | FE mutation methods/paths match Go. FE page does not issue detail GET; edit uses list-row data. IFA detail/update/delete resource paths differ. |
| `AdminServiceCategories` | GET list; POST `/create`; PUT `/update/{id}`; DELETE `/delete/{id}`                 | Go also registers GET `/detail/{id}`  | FE mutation methods/paths match Go. FE page does not issue detail GET; edit uses list-row data. IFA detail/update/delete resource paths differ. |
| `AdminAccountsRoles`     | GET list; POST `/create`; PUT `/update/{id}`; DELETE `/delete/{id}`                 | Go also registers GET `/detail/{id}`  | FE update uses PUT matching Go; IFA proposes PATCH. FE page does not issue detail GET; edit uses list-row data.                                 |
| `AdminOrders`            | list, `/detail/{id}`, POST `/payment-review`, POST `/complete/{id}`                 | same Go routes and methods            | Exact route/method matches evidenced for all four FE calls.                                                                                     |
| `OrderPayment`           | GET and POST `/tracking/{orderNumber}/payment`; POST `/payments/{paymentId}/proofs` | GET/POST payment; POST proof finalize | Exact route/method match. Separate upload-token provider protocol wiring is not established by the inspected `apiFetch` call list.              |

The current FE implementation uses Go action-style update/delete paths for these three admin pages. No corresponding detail GET is issued from these pages; list data populates edit forms. This proves current call-site compatibility for observed operations, not that the alternative IFA paths should be changed without API owner approval.

## 4. Current Go route contracts without dedicated endpoint IFAs

All responses below use the Go JSON envelope except the upload-token response and proof-image binary response. Exact field schemas remain source-defined; inventory links here document current implementation and do not replace external-party IFAs.

| Method + path                                            | Request / authorization                                                                                                                                                                                | Current success and failure behavior                                                                                                                                                                                   | Feature / dependency                                                                                          |
| -------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `GET /api/settings/v1/public`                            | No body/auth; `Cache-Control: no-store`                                                                                                                                                                | Public settings envelope; exact returned keys are source-defined.                                                                                                                                                      | Catalog presenter; no dedicated public settings IFA.                                                          |
| `GET /api/auth/v1/session`                               | Session cookies; checks customer/admin access sessions                                                                                                                                                 | Session status envelope; invalid access cookies may be cleared.                                                                                                                                                        | Auth; no dedicated session IFA.                                                                               |
| `POST /api/orders/v1/payments/{paymentId}/report-paid`   | JSON `orderNumber`, `whatsapp`; payment ObjectID; rate-limited; validates order/phone and payment ownership                                                                                            | `200` on accepted report; controller-mapped validation/state failures. Indicates request for admin validation, never proves settlement.                                                                                | `FEATURE_SPRINT_2` affects report behavior; no dedicated IFA.                                                 |
| `GET /api/orders/v1/tracking/{orderNumber}/payment`      | Bearer tracking token scoped to order; order number path                                                                                                                                               | Returns order summary and configured payment instructions. Missing instruction/configuration or invalid scope maps to error envelope.                                                                                  | Manual QRIS/BCA settings; no gateway.                                                                         |
| `POST /api/orders/v1/tracking/{orderNumber}/payment`     | Bearer tracking token; no body; reporting applies to authorized payment                                                                                                                                | `200` accepted report envelope; does not mark paid.                                                                                                                                                                    | Payment-report behavior; feature-gated by service.                                                            |
| `POST /api/admin/orders/v1/payment-review`               | Admin `orders-manage`; JSON `orderId`, `paymentId`, `decision` (`APPROVE`/`REJECT`), optional note <=500                                                                                               | Records review; approval transitions payment/order to paid/processing and creates transaction; rejection returns unexpired order to pending payment, expired order to cancelled/failed.                                | Admin order controller + MongoDB; FE call is POST.                                                            |
| `POST /api/admin/orders/v1/complete/{orderId}`           | Admin `orders-manage`; order ID path; no body                                                                                                                                                          | Records completion and actor; controller-mapped failures for invalid state/not found.                                                                                                                                  | Admin order controller; no dedicated completion IFA.                                                          |
| `POST /api/orders/v1/payments/{paymentId}/proofs/upload` | Blob protocol JSON event; upload callback verified with `x-vercel-signature`, or client-token event with `payload.clientPayload` containing orderNumber/trackingToken and validated `payload.pathname` | Callback returns provider protocol `{"type":"blob.upload-completed","response":"ok"}`; token request returns upload token JSON. Invalid event/signature/path => 400; disabled feature=>404; unconfigured storage=>503. | `FEATURE_SPRINT_2`; private Vercel Blob-compatible storage; five-minute token. Not multipart upload endpoint. |
| `POST /api/orders/v1/payments/{paymentId}/proofs`        | Bearer tracking token; exact JSON keys `orderNumber`, `pathname`; ObjectID; valid proof path                                                                                                           | `201` new proof / `200` idempotent replay; pending review envelope. Invalid input=>400, missing storage=>503, feature off=>404.                                                                                        | `FEATURE_SPRINT_2`; finalizes already uploaded private blob.                                                  |
| `GET /api/admin/orders/v1/proofs/{proofId}/image`        | Admin access token/cookie + `orders-manage`                                                                                                                                                            | `200 image/png`; private/no-store, nosniff, sandbox CSP. Errors use JSON envelope.                                                                                                                                     | `FEATURE_SPRINT_2`; private blob read.                                                                        |

### Existing proposal path/method deltas

| Current Go route                                         | Proposed IFA route                                                                                       | Resolution for implementation alignment                                                                         |
| -------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| `GET /api/services/v1/games/{gameSlug}`                  | `GET /api/games/v1/{gameSlug}/services`                                                                  | Keep IFA path as proposed canonical route; inventory labels alternate Go alias uncovered by dedicated contract. |
| `POST /api/orders/v1/payments/{paymentId}/proofs/upload` | `POST /api/orders/v1/payments/{paymentId}/proof-upload`                                                  | Go source path controls current integration; retain IFA route as proposal delta pending API owner decision.     |
| `PUT /api/admin/roles/v1/update/{roleId}`                | `PATCH /api/admin/roles/v1/update/{roleId}`                                                              | Frontend/source must use PUT for current Go behavior; IFA method remains proposed until aligned/approved.       |
| Category detail/update/delete                            | IFA uses `/{categoryId}`; Go uses `/detail/{categoryId}`, `/update/{categoryId}`, `/delete/{categoryId}` | Current Go routes govern FE integration; IFA paths are proposed delta.                                          |
| Service detail/update/delete                             | IFA uses `/{serviceId}`; Go uses `/detail/{serviceId}`, `/update/{serviceId}`, `/delete/{serviceId}`     | Current Go routes govern FE integration; IFA paths are proposed delta.                                          |
| Admin order detail                                       | IFA uses `/{orderId}`; Go uses `/detail/{orderId}`                                                       | Current Go route governs FE integration; IFA path is proposed delta.                                            |

Do not silently edit a proposal to claim parity: route alignment needs API owner decision and coordinated FE/backend change. The inventory marks method/path contract match separately from semantic alignment.

## 5. Payment evidence and release gate

### Current state and evidence

- Checkout creates order/payment and returns instructions only when QRIS image or BCA transfer details are configured. This is manual payment instruction, not gateway processing.
- Customer report-paid and proof submission move payment into admin review; neither is settlement evidence.
- Only admin approval sets payment status `PAID`, order payment status `PAID`, order status `PROCESSING`, and records successful transaction/history. Admin review is a privileged state transition.
- Proof storage is private and feature-gated. Upload requires storage configuration; finalization records metadata after provider upload; image retrieval requires admin `orders-manage`.
- Rejection retains review note and allows replacement only while order remains unexpired; expiry path cancels order and fails payment.
- UI text “Menunggu Verifikasi” is pending review, not paid confirmation. Never display payment as successful from report-paid, upload completion, provider callback, or client-side evidence alone.

### Release gate

1. Verify deployed Go revision contains registered handlers and expected feature flags (`FEATURE_SPRINT_2` for proof/report features); route registration alone is not deployment evidence.
2. Confirm payment instruction settings are valid and rendered from server response. If missing, checkout must fail explicitly (503), with no fake success.
3. For proof flow, verify private storage credentials/HTTPS, signed callback verification, path ownership, five-minute token expiry, finalize idempotency, and admin-only image retrieval.
4. Verify payment state changes only after authorized admin approval and corresponding order/payment histories plus transaction record persist atomically/consistently.
5. Verify reject-before-expiry permits replacement and reject-after-expiry cancels; ensure frontend presents pending/rejected/paid states from server response.
6. Confirm frontend `BASE_API_URL` reaches this Go service, credentials/token forwarding matches route authorization, and exact method/path agree with source. FE call-site evidence is not a release sign-off.

No live deployment, provider credentials, callback, or payment settlement was verified during this documentation audit. Therefore payment readiness remains unverified; production payment acceptance needs operational evidence for the above gates.

## 6. Frontend-to-Go integration rules

- Treat Go registration as the current method/path authority; use the method listed in `main.go` registration, then verify handler validation and authorization.
- Treat IFA method/path as target proposal where it differs. Route-level match does not establish payload/response parity.
- Keep tracking token in authorization header; never place it in URL. FE route segment `[orderId]` must carry public order number for Go status/payment handlers, not Mongo internal ID.
- `POST /payments/{paymentId}/proofs/upload` is provider upload protocol. Source supports token/callback exchange; the inspected FE call site confirms only `/proofs` finalize endpoint. Verify provider SDK endpoint wiring before release; `POST /proofs` is separate finalize call.
- Admin mutations require current admin session plus permission enforced by Go. A visible UI action is not proof of authorization or persistence.
- Admin order list/detail call route source paths with `/detail`; review and completion use POST.

## 7. Non-business technical open items

- Deployment evidence: active Go revision, environment feature flags, CORS/`BASE_API_URL`, secure cookie behavior, and runtime MongoDB connectivity.
- Provider integration evidence: configured QRIS/BCA instructions, blob credentials/callback URL/signature secret, and SMTP/identity-provider availability where applicable.
- Contract parity: current Go request/response projections, field omission/nullability, and handler error-code/status mappings are source-audited across 66 business routes. Remaining parity work is target approval for the documented differences and envelope/state semantics; route mapping alone does not approve target behavior.
- Payment consistency under concurrent approve/reject/report requests needs operational/concurrency evidence from conditional repository updates and persistence behavior.

Business decisions (manual payment acceptance policy, approval authority, Sprint inclusion, and whether aliases remain) are outside technical evidence and remain with product/API owners.

## Decision logs

- [Backend Go QUESTION.md](QUESTION.md)
- [Checkout QUESTION.md](../../../../sprint_3/solutioning/public/checkout/QUESTION.md)
- [Orders QUESTION.md](../../../../sprint_3/solutioning/public/orders/QUESTION.md)
- [Public auth QUESTION.md](../../public/auth/QUESTION.md)
- [Public catalog QUESTION.md](../../../../sprint_2/solutioning/public/catalog/QUESTION.md)
