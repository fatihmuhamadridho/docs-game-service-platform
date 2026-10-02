# Customer Request Tracking - BRD

## 1. Project Overview

The Customer Request Tracking project will let customers check progress of service requests they submitted. Today, customers contact the support team to ask for updates, increasing support workload and making request progress difficult to track.

---

## 2. Objective

- Let customers view the current status and latest update for each of their service requests.
- Reduce status-update inquiries handled by the support team by 25% within three months of launch.
- Give support staff a consistent way to keep customers informed about request progress.

---

## 3. Scope

### In Scope

- Customers can view a list of service requests they submitted.
- Customers can view request details, current status, and the date of the latest update.
- Support staff can update a request’s status and add a customer-visible update.
- The service is available on supported desktop and mobile browsers.

### Out of Scope

- Submitting new service requests.
- Live chat between customers and support staff.
- Changing the company’s request-processing policies.
- Sending SMS notifications about status changes.

---

## 4. Requirements

| ID     | Requirement                                                                                                                   |
| ------ | ----------------------------------------------------------------------------------------------------------------------------- |
| BR-001 | Customers need to view a list of service requests they submitted so they can track their requests without contacting support. |
| BR-002 | Customers need to view the current status and latest customer-visible update for a request so they understand its progress.   |
| BR-003 | Customers must only be able to view requests associated with their own account.                                               |
| BR-004 | Support staff need to update a request’s status and add a customer-visible update so customers receive current information.   |
| BR-005 | The request list must identify each request by its reference number and submission date.                                      |

---

## 5. Assumptions

- Customers have an account and can sign in to the existing customer portal.
- Service requests already have a reference number and status in the company’s current process.
- Support staff will provide accurate status updates through the agreed business process.
- The initial release will use the existing supported languages of the customer portal.

---

## 6. Task Breakdown & Timeline

| Task                          | Description                                                                      |    Estimated Time |
| ----------------------------- | -------------------------------------------------------------------------------- | ----------------: |
| Requirements confirmation     | Confirm request statuses, customer-visible information, and staff roles.         |  2–3 working days |
| Experience and process design | Define customer and support staff flows and review them with stakeholders.       |  3–5 working days |
| Delivery                      | Build and connect the request tracking capability to existing request data.      | 2–3 working weeks |
| Business review               | Validate the experience and outcomes with customers and support representatives. |  3–5 working days |

These are illustrative planning estimates, not approved delivery commitments. Actual timing depends on confirmation of existing request data access and stakeholder availability.

---

## 7. Open Questions

- Which request statuses should customers see, and are any internal statuses hidden from customers?
- What information should support staff include in a customer-visible update?
- Should customers be notified when a request status changes? If yes, which channels are required?
- Which roles are allowed to update request status and customer-visible updates?
- What target browsers and accessibility standard should the release support?
