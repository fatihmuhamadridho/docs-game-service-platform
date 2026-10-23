# Public Storefront Settings

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As a visitor, I want to see only approved public storefront information so that I can use current platform guidance.

## Acceptance Criteria

### AC-SET-001

> **Given :** Public storefront settings are configured and approved for display
> **When :** A visitor opens a storefront area that consumes those settings
> **Then :**
>
> 1. The visitor sees only configured public-facing values relevant to that area.
> 2. Internal or unapproved settings are not exposed in the public experience.

### AC-SET-002

> **Given :** Public settings are unavailable or no consuming page is defined
> **When :** A visitor opens the storefront
> **Then :**
>
> 1. The experience does not invent or expose setting values.
> 2. The exact fallback and consuming UI remain an open product decision.

## Technical Flow

1. Visitor: Opens a storefront page.
2. Storefront: Uses approved public settings when a consumer is defined.
3. System: Returns only public-facing configured values.
4. Storefront: Omits unavailable/unapproved values and follows an approved fallback.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                                         | Mandays | Story Point |
| ---------------------------------------------------------------------------- | ------: | ----------: |
| TBD — identify approved settings consumers and render public values/fallback |     TBD |         TBD |
| **Total**                                                                    | **TBD** |     **TBD** |

### Backend

| Task                                                                | Mandays | Story Point |
| ------------------------------------------------------------------- | ------: | ----------: |
| Provide public-only configured settings through existing capability |     TBD |         TBD |
| **Total**                                                           | **TBD** |     **TBD** |

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
- BR-020 is related: [Sprint 2 BRD](../../../../sprint_2/BRD.md) includes maintaining public-facing platform information.
- Source route: Go inventory lists `GET /api/settings/v1/public`; no consumer appears in the current Next.js route inventory, and the exact public field allowlist is not documented here.
- Product/API owners must decide which pages consume these settings, exact approved fields, and unavailable-settings fallback before treating this behavior as release-ready.
- Sizing and estimates remain TBD because team scoring evidence is unavailable. This requirement does not claim implementation completeness.
