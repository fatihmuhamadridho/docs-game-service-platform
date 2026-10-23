# Sprint 2 Solutioning — Catalog, Settings, and Audit

Second delivery group: service categories and services, public catalog discovery, platform settings, and audit-recording foundations. These features depend on early game catalog and admin access foundations from [Sprint 1](../../sprint_1/solutioning/README.md).

## Catalog contracts

- [Public catalog source analysis](public/catalog)
- [Public service categories](public/service-categories)
- [Public services by game](public/services)
- [Public catalog Go extensions](backend-go/public-catalog-extensions-ifa.md)
- [Admin service categories](dashboard/service-categories)
- [Admin services](dashboard/services)
- [Game catalog ERD](../../sprint_1/solutioning/erd/game-catalog-erd.md) · [Service category ERD](erd/service-category-erd.md) · [Service ERD](erd/service-erd.md)

## Settings and audit

- [General platform settings](dashboard/settings/general-settings.md)
- [Platform settings ERD](erd/platform-settings-erd.md)
- [Audit/activity log ERD](erd/activity-log-erd.md)
- Admin activity read and analytics remain in [Sprint 3 dashboard solutioning](../../sprint_3/solutioning/README.md); mutation audit recording is a cross-cutting implementation requirement.

## Dependencies and decisions

- Catalog publishing depends on active games, categories, and service records established in Sprint 1 or earlier in this group.
- Price, quote-only services, public settings exposure, and route aliases remain governed by linked source evidence and open questions; contracts do not imply approval.
