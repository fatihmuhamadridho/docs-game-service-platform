# Refund Questions

## Q-REF-001 — Refund execution policy

**Question:** Is refund execution in scope despite BRD excluding it, and what eligibility, authorization, state transitions, customer notification, reconciliation, and audit rules apply?

**Source / evidence gap:** Admin page uses mock approved/completed states; no refund execution contract exists in the Go inventory and BRD excludes refund execution.

**Affected ACs / routes:** [Admin Refunds Prototype](admin-refunds-prototype.md), `/admin/refunds`.

**Impact:** Mock status does not move funds; no refund action can be treated as approved or operationally complete.
