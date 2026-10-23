# JokiHub — Sprint 3 Business Requirements: Member Identity, Email, Payment, and Dashboard Reads

> **Status:** Proposed late sequence; not a delivery commitment or product approval.  
> **Sprint:** 3  
> **Source:** Requirements retain portfolio IDs; admin access/games and catalog requirements appear in Sprint 1/2 BRDs. [BR-010 evidence submission](./payment-evidence-BRD.md) remains a separate decision-gated candidate.

## 1. Project Overview

Sprint 3 begins with email-provider selection/configuration, member management, customer registration, and password recovery because these depend on email delivery and customer lifecycle decisions. It also contains customer checkout, payment reporting, order lookup/status, and admin read views that consume persisted order/payment/audit data. Dashboard overview comes last after its read models and metric policy are approved.

## 2. Objectives

- Complete approved customer order/payment lifecycle behavior without implying payment success before validation.
- Establish customer registration and account-management flows after email-provider and lifecycle decisions are approved.
- Provide safe order/payment reads after persisted source data exists.
- Gate evidence-file handling and dashboard metrics on explicit policy decisions.

Estimates, owners, performance targets, sprint duration, and dates remain TBD.

## 3. Scope and sequence

### Proposed sequence

1. Select and configure email provider, sender/domain, templates, secrets, and delivery/failure monitoring. This is a dependency, not provider approval.
2. Member management and customer registration, including approved invitation/verification and account lifecycle rules; password recovery also depends on email delivery.
3. Fixed-price checkout and purchase-time order snapshots; approved manual payment instructions and payment reporting.
4. Evidence submission only if BR-010 allocation, private storage, file policy, and review workflow are approved; otherwise remains gated.
5. Customer order lookup and status timeline, with privacy-safe matching and distinct order/payment statuses.
6. Admin order records and transaction reads after persisted order/payment data and field-level privacy policy are available.
7. Admin activity history after Sprint 2 audit writes and event data exist.
8. Admin dashboard overview last, after source read models and approved metric definitions, currency basis, and freshness.

### Out of Scope / Gated

Payment gateway settlement, refunds/payout execution, fulfillment execution, quote workflow, promotions, unapproved production data, and BR-010 evidence submission absent explicit approval. Admin order payment review/completion remains a separate gated Sprint 2 candidate. Sprint placement does not approve any of these policies.

## 4. Business Requirements

| ID     | Requirement                                                                                                                                                                                                                                       |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BR-007 | As a customer, I need to submit an order with contact, game account identifier, selected service, and quantity so JokiHub can process it. Availability and total price must be calculated from authoritative server data.                         |
| BR-006 | As a customer, I need to register and authenticate through the approved identity flow so that I can use account-based features. Registration and email verification are assigned here; customer login/session behavior remains separately scoped. |
| BR-016 | As an authorized administrator, I need to manage member accounts separately from admin accounts so that customer lifecycle actions do not grant admin privileges implicitly.                                                                      |
| BR-008 | As a customer, I need order and item details preserved as purchase-time snapshots so later catalog changes do not alter history or agreed amount.                                                                                                 |
| BR-009 | As a customer, I need an available payment method and current payment instructions. An order created for payment must not be represented as paid.                                                                                                 |
| BR-011 | As a customer, I need to check an order using order number and checkout WhatsApp number so only a matching requester can see status; failures must not disclose which value matched.                                                              |
| BR-012 | As a customer, I need a safe order status/timeline; order status and payment status remain distinct.                                                                                                                                              |
| BR-013 | As an authorized administrator, I need an overview of orders, revenue, users, services, and activity. Metric definitions and currency basis require approval before values are authoritative.                                                     |
| BR-014 | As an authorized administrator, I need to list and inspect orders without exposing unnecessary customer data.                                                                                                                                     |
| BR-015 | As an authorized administrator, I need to list and inspect financial transactions for order payments/refunds; execution workflows remain separate.                                                                                                |
| BR-021 | As an authorized administrator, I need to review recent administrative activity with search/category/date filters to investigate changes and operational events.                                                                                  |
| BR-024 | As the business, I need order-creation and payment-proof retries to avoid duplicate effects.                                                                                                                                                      |
| BR-025 | As the business, I need customer PII and uploaded payment evidence access-controlled, minimized, and retained under an approved policy.                                                                                                           |

## 5. Assumptions and Dependencies

- Approved games/categories/services, pricing, payment instructions, payment method owners, order/payment state transitions, and customer lookup rules must exist before checkout release.
- Admin order/transaction reads depend on persisted records and approved field visibility/privacy policy. Activity history depends on Sprint 2 audit recording and source events.
- Dashboard overview depends on approved metric definitions, revenue basis, currency, comparison periods, and freshness.
- Sprint 1 admin access/roles and Sprint 2 audit story remain prerequisites for applicable operations; privacy/retry safeguards in this sprint block affected flows until approved.
- Email delivery provider, sender identity/domain, credential storage, template/content ownership, verification policy, retry/bounce handling, and privacy/retention rules must be approved before member invitation, registration verification, or password recovery is enabled. Until then, those flows remain gated.
- Guest checkout, customer account linkage, and operational recovery remain open policy choices.

## 6. Open Questions

- Is guest checkout allowed, and how are guest orders securely linked/recovered? — Product/security owner not identified.
- Which payment methods, instruction owners, expiry/fee rules, and order/payment status transitions are approved? — Product/operations owner not identified.
- Is BR-010 evidence submission allocated? Which private storage, file validation, retention, and review workflow apply? — Product/security/operations owner not identified.
- Which customer/payment fields may admin reads expose and what retention/deletion policy applies? — Privacy/product owner not identified.
- Which dashboard metrics, revenue basis, currency, comparison periods, and freshness targets are authoritative? — Product/finance owner not identified.
- Are order, payment, and activity records available and complete enough for the intended views? — Engineering/operations owner not identified.
- Which email provider and sender configuration will support member invitations, customer registration verification, and password recovery? Who owns templates, delivery monitoring, and failure handling? — Identity/platform/product owner not identified.
- Which API paths/status mappings are approved where frontend and backend inventories differ? — Product/API owner not identified.
