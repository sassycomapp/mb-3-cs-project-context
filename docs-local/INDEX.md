---
title: "INDEX"
doc-id: "mb-33cd8aba"
updated: "2026-10-29"
---
# Mybizz CS — Local Documentation Index

**Project:** mb-3-cs (Consulting & Services)
**Last Updated:** 2026-07-22
**Total Docs:** 17

Project-specific documentation for mb-3-cs. Check this index before writing feature specs, design decisions, or build plans. Read only the specific document relevant to the current task.

Global resources referenced from these documents live in:
- `adr-global/` — Architecture Decision Records
- `specifications-global/` — Binding specifications
- `docs-standard-global/` — Standard document templates and contracts (placeholder — in development)

---

## Architecture & Platform

| File | Title | Description |
|---|---|---|
| [[[mb-3-cs-project-context:docs-local/architecture\|Architecture]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/architecture.md) | Architecture | Five-app system, package structure, module/navigation architecture, specifications table |
| [[[mb-3-cs-project-context:docs-local/platform-architecture\|Platform Architecture]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/platform-architecture.md) | Platform Architecture | Vertical scope, feature set, technical stack, business model, platform constraints |
| [[[mb-3-cs-project-context:docs-local/platform-architecture-narrative\|Comprehensive Application Treatise]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/platform-architecture-narrative.md) | Platform Narrative | Comprehensive treatise — executive summary, problem/solution, capabilities, competitive positioning, V2 roadmap |
| [[[mb-3-cs-project-context:docs-local/build-plan\|Build Plan (Definitive)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/build-plan.md) | Build Plan (Definitive) | Phased implementation plan — 6 phases with deliverables, completion criteria, and review task integration |

---

## Design System

| File | Title | Description |
|---|---|---|
| [[[mb-3-cs-project-context:docs-local/design\|DESIGN]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN.md) | DESIGN (Canonical) | Authoritative design system — tokens, typography, spacing, component maps, form/page specs, invoicing/booking design |
| [[[mb-3-cs-project-context:docs-local/design-direction\|Design Direction (Canonical)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/design-direction.md) | Design Direction (Canonical) | Visual direction, color rule, Anvil role map, project-specific M3 deviations |
| [[[mb-3-cs-project-context:docs-local/design_palette_section\|DESIGN Palette Section]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN_palette_section.md) | Palette System Tokens | Full M3 CSS custom property token sets for all 4 palettes (light + dark mode) |
| [[[mb-3-cs-project-context:docs-local/palette_selector_tokens\|Token Swap Blocks]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/palette_selector_tokens.md) | Palette Selector Tokens | Python dict token blocks for the palette selector component |
| [[[mb-3-cs-project-context:docs-local/custom-component-requirements-matrix\|Custom Component Requirements Matrix]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/custom-component-requirements-matrix.md) | Custom Component Requirements Matrix | Shared and local custom components inventory, justification, and V1 scope |

---

## Schema & Feature Specs

| File | Title | Description |
|---|---|---|
| [[[mb-3-cs-project-context:docs-local/authoritative-schema\|Authoritative Database Schema]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/authoritative-schema.md) | Authoritative Database Schema | Complete 44-table schema with field definitions, architectural patterns, and V1 vs V2 scope |
| [[[mb-3-cs-project-context:docs-local/calendar-feature-spec\|Calendar Feature Spec]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/calendar-feature-spec.md) | Calendar Feature Plan | Anvil + Material 3 calendar implementation — form structure, view architecture, responsive strategy |
| [[[mb-3-cs-project-context:docs-local/user-flows\|User Flows]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/user-flows.md) | User Flows | All 13 user flows — onboarding, booking, admin operations, CRM, campaigns, error recovery |

---

## Business & Strategy

| File | Title | Description |
|---|---|---|
| [[[mb-3-cs-project-context:docs-local/positioning-strategy\|Business Strategy & Positioning Document]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/positioning-strategy.md) | Business Strategy & Positioning | Target market, competitive landscape, pricing, go-to-market, brand identity, support model |
| [[[mb-3-cs-project-context:docs-local/pricing-strategy-consultation\|Pricing Strategy Consultation]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/pricing-strategy-consultation.md) | Pricing Strategy Consultation | Market position, pricing comparison, messaging themes, strategic options |
| [[[mb-3-cs-project-context:docs-local/founder-strategic-review\|Founder Strategic Review]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/founder-strategic-review.md) | Founder Strategic Review | Strategic product review and direction |

---

## Project Records

| File | Title | Description |
|---|---|---|
| [[[mb-3-cs-project-context:docs-local/changelog\|CHANGELOG]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/CHANGELOG.md) | Changelog | Versioned change history |
| [[[mb-3-cs-project-context:docs-local/context\|mb-3-cs (Mybizz Consulting Services)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/CONTEXT.md) | Project Context | Domain glossary, repositories and key concepts |

---

## Notes

- **README.md** does not exist in this directory. The project README lives at the repository root.
- All cross-references to global documents (ADRs, specifications) use descriptive slugs (e.g., [[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR, [[[default:specifications-global/spec-deployment|Deployment Specification]]](C:/dev/project-library-global/specifications-global/spec-deployment.md)). Legacy numeric-prefix references (e.g., `4-lead-capture-simultaneous-creation.md`) are stale — see the integration report for full findings.
- The `docs-standard-global/` directory contains placeholder documents not yet verified for integration — their correction is a separate workstream.
