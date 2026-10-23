# Sprint 2 Acceptance Criteria — Catalog, Settings, and Gated Candidates

Twenty-two story documents form a proposed middle delivery group. This placement does not approve product scope. Prototype-derived and payment-operation stories remain explicitly uncommitted.

## Proposed delivery sequence

1. Public category discovery (AC-PUB-002; BR-003) and admin category management (AC-ADM-017; BR-019) after approved games exist.
2. Public services for a game (AC-PUB-003; BR-004/005) and admin service management (AC-ADM-018; BR-019) after category, availability, and pricing rules are approved.
3. Admin platform settings (AC-ADM-019; BR-020), then audit recording (AC-SHR-021; BR-022) after audit taxonomy/retention decisions.
4. Public informational/settings pages and error states, only after content/exposure decisions.

## Core and public stories

- [Admin Service Category Management](./dashboard/service-categories/admin-category-management.md) — AC-ADM-017; BR-019.
- [Admin Service Management](./dashboard/services/admin-service-management.md) — AC-ADM-018; BR-019.
- [Admin General Platform Settings](./dashboard/settings/admin-general-settings.md) — AC-ADM-019; BR-020.
- [Administrative Audit Recording](./shared/shared-audit-recording.md) — AC-SHR-021; BR-022.
- [Public Service Category Discovery](./public/service-categories/public-category-discovery.md) — AC-PUB-002; BR-003.
- [Public Services for a Game](./public/services/public-game-services.md) — AC-PUB-003; BR-004/005.
- [Customer Password Recovery](./public/auth/password-recovery.md) — AC-AUTH-001; release and security policy unresolved.
- [Help Center](./public/help/help-center.md) — AC-HELP-001; content/scope approval required.
- [About JokiHub](./public/about/about-jokihub.md) — AC-ABOUT-001; copy approval required.
- [Storefront Home](./public/home/storefront-home.md) — AC-HOME-001; prototype claims need approval.
- [Public Storefront Settings](./public/settings/public-storefront-settings.md) — AC-SET-001/002; exposure policy unresolved.
- [Access Denied and Not Found States](./shared/error-and-not-found-pages.md) — AC-SYS-001.

## Decision-gated candidates (10)

- [Admin Order Payment Review and Completion](./dashboard/orders/admin-order-operations.md) — AC-ADM-021-001/002; workflow/policy decision required.
- Nine observed-UI prototype stories remain unapproved; local/mock behavior does not establish production workflow: [Announcements](./dashboard/announcements/admin-announcements-prototype.md) (AC-MOCK-001), [Bank Accounts](./dashboard/bank-accounts/admin-bank-accounts-prototype.md) (002), [Banners](./dashboard/banners/admin-banners-prototype.md) (003), [Complaints](./dashboard/complaints/admin-complaints-prototype.md) (004), [FAQs](./dashboard/faqs/admin-faqs-prototype.md) (005), [Manual Top-Ups](./dashboard/manual-top-ups/admin-manual-top-ups-prototype.md) (006), [Static Pages](./dashboard/pages/admin-pages-prototype.md) (007), [Payouts](./dashboard/payouts/admin-payouts-prototype.md) (008), and [Refunds](./dashboard/refunds/admin-refunds-prototype.md) (009). Payout/refund execution remains excluded.

See [Sprint 2 BRD](../BRD.md) and [admin decisions](./dashboard/users/QUESTION.md). Estimate, sizing, dates, and policy owners remain TBD.
