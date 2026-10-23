# Admin Bank Account Questions

## Q-BANK-001 — Payment instruction configuration

**Question:** Is bank-account CRUD in scope to support BR-009 manual payment, and what validation, privacy masking, primary-account uniqueness, ownership, and public-instruction integration rules apply?

**Source / evidence gap:** Prototype shows bank name, account number, holder, label, active, and primary fields. BR-009 covers manual payment methods but does not approve this admin CRUD workflow; no persisted backend route is established.

**Affected ACs / routes:** [Admin Bank Accounts Prototype](admin-bank-accounts-prototype.md), `/admin/bank-accounts`, [Manual Payment Instructions](../../../../sprint_3/acceptance-criteria/public/checkout/manual-payment-instructions.md).

**Decision owner:** Product/operations roles are not identified in supplied sources.

**Impact:** Admin changes could expose account data or make customer instructions inconsistent without approved data ownership and controls.
