# Manual Top-Up Questions

## Q-TOP-001 — Separate operational workflow

**Question:** Is manual top-up a separate product capability from BR-007 checkout, and what owners, PII permissions, validation, payment/fulfillment transitions, and persistence apply?

**Source / evidence gap:** Prototype contains customer/WhatsApp, game account, server, package, amount, payment reference, notes, and local sample statuses. BRD has no dedicated manual-top-up operations workflow or backend contract.

**Affected ACs / routes:** [Admin Manual Top-Up Prototype](admin-manual-top-ups-prototype.md), `/admin/manual-top-ups`.

**Impact:** Scope and state-changing behavior cannot be inferred from mock controls; PII access and financial transitions need explicit rules.
