# Customer Authentication Questions

## Q-AUTH-001 — Password recovery release and security policy

**Question:** Is forgot/reset password included in release scope, and what token expiry/reuse, anti-enumeration response, and email-delivery rules should apply?

**Source / evidence gap:** The Next.js forms call Go recovery routes, but recovery is feature-gated and not explicit in BR-006. Supplied sources do not establish release approval, token policy, public response behavior, or SMTP availability policy.

**Affected ACs / routes:** [Customer Password Recovery](./password-recovery.md), `/forgot-password`, `/reset-password`, `POST /api/auth/v1/forgot-password`, `POST /api/auth/v1/reset-password`.

**Decision owner:** Product and security roles are identified in the story notes.

**Impact:** Scope, abuse controls, user messaging, and production readiness remain unresolved. Do not infer approval from visible forms or registered routes.
