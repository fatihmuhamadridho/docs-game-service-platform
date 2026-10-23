# Sprint 3 — Customer Identity and Email Provider Questions

These questions gate email-dependent member and customer identity flows. Sprint placement does not select a provider or approve a delivery policy.

## Q1 — Which email provider and sender configuration will be used?

- **Question:** Which provider, sender domain/address, secret-management method, and environment configuration will support member invitations, customer registration verification, and password recovery?
- **Evidence gap:** Current Go member invitation returns HTTP 503 because identity-provider invitation is unavailable. Registration currently creates an account/session without sending verification email. Password recovery requires SMTP and an application base URL.
- **Affected docs/routes:** [Account management IFAs](../dashboard/members/), [registration IFA](register.md), [auth source audit](source-audit-2026-10-23.md), Sprint 2 password-recovery candidate.
- **Decision owner:** Identity/platform owner.
- **Impact:** Member creation, registration verification, recovery delivery, environment setup, monitoring, and operational retry behavior remain gated.

## Q2 — What account verification and invitation lifecycle should be implemented?

- **Question:** Should registration and member invitations require email verification? Define token expiry, resend limits, duplicate-account behavior, activation state, and delivery failure response.
- **Evidence gap:** Existing source registration activates immediately and sends no message; member create does not persist or send an invitation. No approved lifecycle decision is recorded.
- **Decision owner:** Product/identity/security owner.
- **Impact:** Registration and member IFA contracts, account status transitions, and release criteria.

## Q3 — What password-recovery policy applies?

- **Question:** Approve recovery feature availability, generic response behavior, token expiry/one-time use, password rules, rate limits, and post-reset session revocation.
- **Evidence gap:** Go source has a feature-gated SMTP recovery flow, but no approved product policy or delivery SLA was found.
- **Decision owner:** Product/security owner.
- **Impact:** Recovery remains disabled until provider and account-lifecycle decisions are approved.

## Q4 — Which customer login identifiers are supported?

- **Question:** Should login accept the identifiers currently supported by Go, or include WhatsApp as proposed in some target material?
- **Evidence gap:** Source accepts `identifier`, `username`, or `email`; no WhatsApp lookup is documented in the current source audit.
- **Affected docs/routes:** `POST /api/auth/v1/login`; [login IFA](login.md), [source audit](source-audit-2026-10-23.md).
- **Decision owner:** Product/identity owner.
- **Impact:** Login UI, account lookup normalization, uniqueness policy, and support procedures.

## Q5 — What customer cookie and refresh-session policies apply?

- **Question:** Approve current Go access/refresh cookie lifetimes or define new idle/absolute lifetimes, rotation grace, and CSRF policy.
- **Evidence gap:** Source audit records 15-minute access and 7-day refresh cookies; target lifetime, SameSite/domain, and CSRF behavior remain unresolved.
- **Affected docs/routes:** Customer login, logout, refresh, and session IFAs.
- **Decision owner:** Security/identity owner.
- **Impact:** Browser compatibility, CSRF protection, replay handling, and session continuity.

## Q6 — Which auth routes require rate limits?

- **Question:** Keep current limits on login, registration, and recovery, or extend controls to logout, refresh, and session endpoints?
- **Evidence gap:** Current handler applies rate limits to login/register/forgot/reset, not logout/refresh/session. No approved threat model or traffic target defines broader coverage.
- **Affected docs/routes:** Customer auth routes and [source audit](source-audit-2026-10-23.md).
- **Decision owner:** Security owner.
- **Impact:** Abuse controls, availability, and client retry behavior.

## Q7 — Should Go response envelopes be standardized?

- **Question:** Should Go route families using `{code,message,data}` migrate to `{status:{code,message},data}`, or should both formats remain supported in v1?
- **Evidence gap:** Go handlers expose both formats; no API-wide migration or compatibility decision is recorded.
- **Affected docs/routes:** Auth, admin, order/payment, and public catalog APIs.
- **Decision owner:** API owner.
- **Impact:** Client parsing, error handling, examples, and backward compatibility.
