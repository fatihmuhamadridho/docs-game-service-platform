# Admin Order Mutations — Open Contract Decisions

## Q1 — Are review and completion mutations approved business capabilities?

- **Question:** Are `POST /api/admin/orders/v1/payment-review` and `POST /api/admin/orders/v1/complete/{orderId}` in approved product scope, and which policy governs approval, rejection, and completion?
- **Evidence gap:** Go routes and state guards exist, but implementation does not establish product approval or authorized order/payment transitions.
- **Affected routes/docs:** [Admin order mutations IFA](../../backend-go/admin-order-mutations-ifa.md), [order detail IFA](get-detail-order.md), and [order/payment source IFA](../../backend-go/order-payment-ifa.md).
- **Decision owner:** Product/order owner.
- **Impact:** Admin permissions, allowed state transitions, audit requirements, and release gating.
