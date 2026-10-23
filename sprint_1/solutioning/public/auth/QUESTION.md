# Public Authentication — Open Contract Decisions

Current Go implementation evidence is in [auth source audit](source-audit-2026-10-23.md) and [auth handling baseline](../../auth-user-handling.md). Proposals below remain separate from implemented behavior.

## Q1 — Is immediate account activation and session creation the onboarding target?

- **Question:** Should customer registration retain current immediate account/session creation, or require email/WhatsApp verification before sign-in?
- **Evidence gap:** Go registration is feature-gated; when enabled it creates the account and session immediately, sets access/refresh cookies, and sends no verification message. The registration IFA proposes verification and no immediate session. No approved lifecycle decision found.
- **Affected routes/docs:** `POST /api/auth/v1/register`; [registration IFA](register.md), [auth handling baseline](../../auth-user-handling.md), source audit.
- **Decision owner:** Product/identity owner.
- **Impact:** Registration response, session state, verification/resend endpoints, notification integration, and release criteria.

## Q2 — Which customer login identifiers are supported?

- **Question:** Should login accept username/email only as current Go does, or include WhatsApp as proposed in some target material?
- **Evidence gap:** Source handler accepts `identifier`, `username`, or `email`; presenter/repository resolves supported identity fields. No WhatsApp lookup is documented in current Go source audit; some proposal text includes WhatsApp.
- **Affected routes/docs:** `POST /api/auth/v1/login`; [login IFA](login.md), source audit.
- **Decision owner:** Product/identity owner.
- **Impact:** Login UI fields, account lookup normalization, uniqueness policy, and customer support.

## Q3 — What are approved cookie and refresh-session lifetimes/policies?

- **Question:** Approve Go's current 15-minute access and 7-day refresh cookie behavior, or define idle/absolute lifetimes, rotation grace, and CSRF policy before changing it?
- **Evidence gap:** Source audit establishes 15m/7d cookies and refresh rotation; proposals retain TBD absolute lifetime, SameSite/domain, and CSRF behavior. No identity/session policy document found.
- **Affected routes/docs:** Customer/admin login, logout, refresh, session; auth IFAs and source audit.
- **Decision owner:** Security/identity owner.
- **Impact:** Browser compatibility, CSRF protections, token replay handling, and session continuity.

## Q4 — Which routes need rate limiting and recovery controls?

- **Question:** Keep current rate limits only on login/register/forgot/reset, or extend limits to logout, refresh, and session endpoints?
- **Evidence gap:** Source audit shows exact current action thresholds and that logout/refresh/session do not invoke limiter. No approved threat model or traffic target sets scope for the remaining handlers.
- **Affected routes/docs:** All auth routes; source audit and auth IFAs.
- **Decision owner:** Security owner.
- **Impact:** Abuse protection, availability, and client retry behavior.
