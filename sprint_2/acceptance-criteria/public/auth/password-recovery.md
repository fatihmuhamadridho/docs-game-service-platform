# Customer Password Recovery

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As a Customer, I want to request a recovery email and set a new password using a recovery token so that I can regain access to an account.

## Acceptance Criteria

### AC-AUTH-001-001

> **Given :** Password recovery is enabled and customer can access recovery page
> **When :** Customer submits an email address
> **Then :**
>
> 1. The form requires an email-formatted value up to 254 characters and shows a neutral recovery notice when request succeeds; service errors are shown as failure notices.

### AC-AUTH-001-002

> **Given :** Customer opens the reset form with a new password and confirmation
> **When :** The values do not match or fall outside the form's required length
> **Then :**
>
> 1. The reset request is not submitted and the form shows validation feedback.

### AC-AUTH-001-003

> **Given :** Customer submits a valid new password, matching confirmation, and recovery token
> **When :** The reset request succeeds or fails
> **Then :**
>
> 1. Success shows a completion notice and hides the submit action.
> 2. Failure shows a failure notice and does not claim the password changed.

## Technical Flow

1. Customer requests recovery with account email.
2. System validates the request and may send recovery instructions when enabled/configured.
3. Customer follows the recovery link containing a token.
4. Customer submits matching new password and confirmation.
5. System reports success or a failure without changing password.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                                                         | Mandays | Story Point |
| -------------------------------------------------------------------------------------------- | ------: | ----------: |
| Present email request and password reset forms, busy states, validation, and result notices. |     TBD |         TBD |
| **Total**                                                                                    | **TBD** |     **TBD** |

### Backend

| Task                                                                                                     | Mandays | Story Point |
| -------------------------------------------------------------------------------------------------------- | ------: | ----------: |
| TBD — Go recovery route exists and is feature-gated; email delivery and production policy need approval. |     TBD |         TBD |
| **Total**                                                                                                | **TBD** |     **TBD** |

### Summary

> **FE :** TBD
> **BE :** TBD
> **Size Task :** TBD

## Sizing Reference

| SIZE | SCORE RANGE | ESTIMATE COMPLETION | STORY POINT |
| ---- | ----------- | ------------------- | ----------- |
| S    | ≦ 100       | < 2 days            | 1-3         |
| M    | 101-200     | 2 – 3 days          | 4-6         |
| L    | 201-250     | > 3 – 5 days        | 7-10        |
| XL   | > 250       | > 5 days            | > 10        |

## Notes

- Open estimation question: [Cross-feature Questions](../../../../sprint_1/acceptance-criteria/QUESTION.md) â€” sizing and effort remain TBD pending an approved rubric and estimates.
- Open feature decisions: [Feature Questions](./QUESTION.md).
- Source traceability: BR-006 is related; recovery is not explicit in approved Sprint 1 scope. Product scope status remains unresolved where noted below.
- Observed source: ForgotPasswordForm and ResetPasswordForm. UI uses POST recovery routes; reset requires matching confirmation, password length 8–128, and token query parameter. Product/security decision needed: include recovery in release, token expiry/reuse rules, response anti-enumeration behavior, and email delivery policy. Do not treat feature-gated UI as approved Sprint 1 scope.
- This document records observable UI/code behavior and open decisions; it does not claim implementation completeness or production readiness.
