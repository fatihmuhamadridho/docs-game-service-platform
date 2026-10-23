# Admin Order Operations Questions

## Q-ORD-001 — Payment review and completion scope

**Question:** Are payment approval/rejection and order completion in Sprint 1 scope? If so, what evidence, authorization, allowed state transitions, and completion meaning apply?

**Source / evidence gap:** Admin UI calls the Go payment-review and completion routes, but BRD excludes admin payment-proof decisions and fulfillment execution absent separate approval. Go also exposes private proof image behind `FEATURE_SPRINT_2`.

**Affected ACs / routes:** [Admin Order Payment Review and Completion](admin-order-operations.md), `/admin/orders`, `POST /api/admin/orders/v1/payment-review`, `POST /api/admin/orders/v1/complete/{orderId}`, `GET /api/admin/orders/v1/proofs/{proofId}/image`.

**Decision owner:** Product and operations roles are identified in the story notes.

**Impact:** These actions can change financial/order state. Do not infer approval from visible controls or registered routes.
