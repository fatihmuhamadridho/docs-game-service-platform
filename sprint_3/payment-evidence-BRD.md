# JokiHub — Sprint 3 Business Requirements Document

> **Status:** Candidate scope only; no commitment or approval implied.  
> **Sprint:** 3  
> **Source:** Requirements retain portfolio BR identifiers; see the [root README](../README.md#sprint-grouping-and-status) for cross-sprint sequencing.

## 1. Project Overview

Records payment-evidence submission as a candidate business requirement plus candidate prototype-derived work. BR-010 retains its original identifier. Other Sprint 3 ACs are not approved business requirements unless owners accept them separately.

Current screens may contain prototype data or simulated interactions. This document does not approve mock values, prices, service availability, guarantees, contact details, or marketing claims as production data.

## 2. Objectives

- Deliver the approved capabilities assigned to Sprint 3.
- Keep business rules, scope boundaries, and unresolved decisions traceable to the original BR identifiers.
- Preserve security, privacy, and customer-safe behavior wherever relevant to this sprint.

Success measures, estimates, sprint duration, and delivery dates remain **TBD** until owners approve them.

## 3. Scope

### In Scope

Candidate: securely submit evidence for an eligible manual payment attempt so an administrator can review it. Successful upload means pending review, not confirmed settlement. Any additional candidate page or workflow requires separate product approval.

### Out of Scope / Gated

Automated settlement/reconciliation, payment gateway callbacks, admin approve/reject, order fulfillment, refunds, payouts, and payment reversals remain excluded unless separately approved. Prototype behavior alone does not establish product scope.

## 4. Business Requirements

| ID     | Requirement                                                                                                                                                                                                               |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BR-010 | As a customer, I need to submit payment evidence securely for an eligible payment attempt so that an administrator can review the payment. Successful evidence submission means pending review, not confirmed settlement. |

## 5. Assumptions

- Sprint solutioning and AC documents are drafts/proposals until approved by their owners.
- Requirement IDs are preserved from the portfolio requirement set; omitted IDs belong to another sprint or remain unallocated.
- Prototype values and simulated interactions are evidence of current UI only, not approved production behavior.
- Estimates, performance targets, owners, and delivery timing remain TBD.

## 6. Dependencies and Timeline

Release allocation decision for BR-010; approved private storage, file validation/scanning, access and retention policy; payment status transitions and manual review procedure.

No approved estimates, sprint duration, or release dates were supplied; timing remains **TBD**.

## 7. Open Questions

- Is payment evidence submission allocated to Sprint 3, and which proof-upload/finalize routes are approved for release? — Owner: relevant product/operations/security/API owner; Needed by: TBD
- Who reviews payment evidence, and what status transitions, file limits, malware scanning, private storage, and retention policy apply? — Owner: relevant product/operations/security/API owner; Needed by: TBD
- Are admin payment review and order completion in scope? These remain excluded until explicitly approved. — Owner: relevant product/operations/security/API owner; Needed by: TBD
- Which prototype-derived pages, content, persistence, and workflows are approved as product scope? — Owner: relevant product/operations/security/API owner; Needed by: TBD
- What production copy, support details, and public claims may appear on home, About, Help, and settings pages? — Owner: relevant product/operations/security/API owner; Needed by: TBD
