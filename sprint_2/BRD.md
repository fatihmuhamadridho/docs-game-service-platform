# JokiHub — Sprint 2 Business Requirements: Catalog, Settings, and Audit

> **Status:** Proposed middle sequence; not a delivery commitment. Prototype and operational candidates remain unapproved.  
> **Sprint:** 2  
> **Source:** IDs preserve portfolio traceability. Sprint 1 covers admin access and game slices; Sprint 3 covers member identity, payments, and dashboard/read consumers.

## 1. Project Overview

Sprint 2 extends approved games into service categories/services, settings, audit recording, and selected public informational pages. Current screens may contain prototype data or simulated interactions; they do not approve mock values, persistence, or production behavior.

## 2. Objectives

- Sequence categories and services after approved games and catalog policy.
- Record administrative changes under an approved audit and retention policy.
- Keep unapproved prototype and payment-operation screens visibly gated.

Estimates, owners, success metrics, sprint duration, and dates remain **TBD**.

## 3. Scope

### Proposed sequence

- Public category discovery and admin category management, followed by public service discovery and admin service management (BR-003/004/005 and BR-019 slices).
- General platform settings (BR-020) and administrative audit recording (BR-022).
- Public help/about/home/settings and access-denied/not-found states, subject to approved content and exposure policy.

### Out of Scope / Gated

Customer password recovery (AC-AUTH-001) remains feature/security gated. Admin order payment review/completion (AC-ADM-021-001/002) requires explicit workflow approval. Nine admin pages are observed UI/mock candidates only (AC-MOCK-001–009), not committed production functions. Checkout/payment lifecycle, order tracking, and dashboard/read consumers are assigned to Sprint 3. Refund/payout execution, settlement, reconciliation, and fulfillment execution remain excluded absent separate approval.

## 4. Business Requirements

| ID     | Requirement                                                                                                                                                                                                                                                                                        |
| ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BR-003 | As a visitor, I need to see active service categories so I can navigate by service type; categories without eligible public services should not lead to empty purchase choices.                                                                                                                    |
| BR-004 | As a customer, I need to view active services for the game identified by the order page slug so I can select a valid service.                                                                                                                                                                      |
| BR-005 | As a customer, I need to distinguish fixed-price services from quote-required services so I am not shown an invented price or allowed to pay an unapproved quote.                                                                                                                                  |
| BR-019 | As an authorized administrator, I need to manage games, service categories, and services so the public catalog reflects approved offerings. Sprint 2 covers category/service management slices; game-management slice is in Sprint 1. Inactive/removed entries must not be offered for new orders. |
| BR-020 | As an authorized administrator, I need to update approved platform information, contacts, regional preferences, maintenance mode, and member-registration availability. Public exposure of settings remains a separate decision.                                                                   |
| BR-022 | As the business, I need administrative actions recorded as append-only audit events with actor, action, target, outcome, and time. Secrets and unnecessary personal/payment data must be redacted.                                                                                                 |

## 5. Assumptions and Dependencies

- Games and approved active-game data precede categories/services; catalog eligibility/pricing policy precedes public service selection.
- Audit recording requires approved event taxonomy, retention, redaction, and failure behavior. Sprint 3 activity-history views depend on audit/activity records being available.
- Current prototype UI does not establish persistence or approved workflow.
- Estimates, owners, performance targets, and timing remain TBD.

## 6. Open Questions

- Which category/service actions, fields, eligibility rules, pricing/quote states, and deletion behavior are approved? — Owner not identified; before catalog release.
- Which general settings fields are editable and who may change maintenance/registration switches? — Owner not identified; before settings release.
- What audit events, retention, privacy masking, and write-failure behavior are required? — Owner not identified; before audit implementation.
- Are public help/about/home/settings pages in release scope and what content/exposure is approved? — Product owner not identified; before publication.
- Is password recovery enabled, and what token expiry, anti-enumeration, and delivery rules apply? — Product/security owner; before enabling feature flag.
- Are mock admin pages or order/payment review intended for release? If yes, what persisted workflows and authorities apply? — Product/operations owner not identified; before scope commitment.
