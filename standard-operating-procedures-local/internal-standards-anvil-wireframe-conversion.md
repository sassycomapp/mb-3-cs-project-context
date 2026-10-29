---
title: "internal-standards-anvil-wireframe-conversion"
doc-id: "mb-aa66a967"
updated: "2026-10-29"
---
# Internal Standards & Specifications — Anvil Wireframe Conversion

**Audit date:** 2026-06-27
**Scope:** Existing mb-3-cs-project-library documentation covering rules, conventions, and specs that apply to converting ~70 HTML/CSS wireframes with PNG screenshots into native Anvil Forms.
**Method:** GBrain search across mb-3-cs-project-library docs, ADRs, gstack-outputs, and prompts; filesystem verification of all cited files.

---

## 1. Design System / Brand Guidelines

### 1.1 Colour Palette

**Clearly defined.**

| Source | Rule |
|--------|------|
| [[[mb-3-cs-project-context:docs-local/design_palette_section\|DESIGN Palette Section]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN_palette_section.md) | Professional Blue is the default demonstration palette. 19 CSS custom properties defined (e.g. `--primary: #493ef3`, `--surface: #fdfcff`). Four palettes available; Professional Blue is the default. |
| `docs/theme_patched.css` | Deployment-ready theme with MUI patch. All CSS custom properties in screen output MUST derive from this file. |
| `docs/theme_mui_patch.css` | MUI patch in isolation (7 sections, 9 variables). |

**Hardcoded colour ban:** Every color value in output MUST resolve through a CSS custom property from `theme_patched.css`. No hardcoded hex, `rgb()`, `rgba()` with specific color values, or `hsl()` values. The only exception is the MUI card shadow `hsla()` values as defined in DESIGN.md MUI section.

**Status colour exception:** Status colors (green for active/success, orange for pending/warning, red for error/cancelled) use semantic colors not part of the M3 palette but accepted as standard UI status indicators.

### 1.2 Typography Scale

**Clearly defined.**

| Source | Rule |
|--------|------|
| [[[mb-3-cs-project-context:docs-local/design\|DESIGN]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN.md) | Full M3 typography scale: Display (57px), Headline Large (32px), Headline Small (24px), Title Large (22px), Title Medium (16px), Body Large (16px), Body Medium (14px), Body Small (12px), Label Large (14px), Label Medium (12px), Label Small (11px). Font family: `'Roboto', sans-serif`. |

### 1.3 Spacing Scale

**Clearly defined.**

| Source | Rule |
|--------|------|
| [[[mb-3-cs-project-context:docs-local/design\|DESIGN]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN.md) | 4px base grid. Spacing tokens: `--space-xs: 4px`, `--space-sm: 8px`, `--space-md: 16px`, `--space-lg: 24px`, `--space-xl: 32px`, `--space-2xl: 48px`. Border radius tokens: `--radius-sm: 4px`, `--radius-md: 8px`, `--radius-lg: 12px`, `--radius-xl: 16px`, `--radius-full: 100px`. |

### 1.4 Breakpoints

**Not found — needs external research.**

No responsive breakpoint specification was found in the mb-3-cs-project-library docs, ADRs, or DESIGN.md. Screen-production-standard.md and DESIGN.md define layout max-widths (e.g. 400px for auth cards, 900px for editor forms, 1100px for list forms) but no explicit responsive breakpoint values.

---

## 2. Naming Conventions

### 2.1 Component Prefixes

**Clearly defined.**

| Source | Rule |
|--------|------|
| `specifications-global/spec-nomenclature.md` (global) | Authoritative component prefix table. |

| Component Type | Prefix | Example |
|----------------|--------|---------|
| ColumnPanel | `col_` | `col_email_config` |
| FlowPanel | `flow_` | `flow_sender_fields` |
| Card | `card_` | `card_sender` |
| Heading | `hdg_` | `hdg_email_title` |
| TextBox | `txt_` | `txt_from_email` |
| TextArea | `txt_` | `txt_clause_body` |
| DropdownMenu | `dd_` | `dd_timezone` |
| Button | `btn_` | `btn_save` |
| CheckBox | `cb_` | `cb_gateway_connected` |
| Switch | `sw_` | `sw_test_mode` |
| RepeatingPanel | `rp_` | `rp_secrets` |
| DataGrid | `dg_` | `dg_users` |
| Text | `txt_` | `txt_email_subtitle` |
| Icon | `ic_` / `ico_` | `ic_empty_vault` |
| Link | `nav_` | `nav_forgot_password` |
| IconButton | `btn_` | `btn_edit` |
| DatePicker | `dp_` | `dp_date_from` |
| Plot | `plot_` | `plot_revenue_trend` |
| Image | `img_` | `img_service` |
| RadioButtonGroup | `rbg_` | `rbg_meeting_type` |

**Known gap:** `nomenclature.md` does not define a prefix for DataRowPanel. Wireframes use `drp_` as a convention (noted but not yet codified).

### 2.2 File Naming

**Clearly defined.**

| Source | Rule |
|--------|------|
| `specifications-global/spec-nomenclature.md` (global), production reports | Wireframe files: `wireframe-{package}-{FormName}.html`. Screen files: `screen-{package}-{FormName}.html`. Row templates: `wireframe-{package}-{FormName}-RowTemplate.html`. |
| Production reports (2026-06-24) | Anvil does not accept hyphens in file or component names. Existing hyphenated folders (e.g. `CommentModerationForm-RowTemplate/`) may need renaming to PascalCase. |

### 2.3 Event Handler Naming

**Not found — needs external research.**

No naming convention for Anvil event handlers (e.g. `btn_save_click`, `txt_email_change`) was found in the mb-3-cs-project-library documentation.

---

## 3. HTML-Element-to-Anvil-Component Mapping Rules

**Clearly defined.**

| Source | Rule |
|--------|------|
| [[[default:specifications-global/spec-m3_component_mapping\|m3_component_mapping.md]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md) (global) | Authoritative mapping of Anvil Material 3 Theme components to their Anvil classes, usage notes, and writeback support. |

Key mappings observed across production reports:

| Wireframe/HTML Element | Anvil Component | Notes |
|------------------------|-----------------|-------|
| `<h1>`, `<h2>` | Heading | `hdg_` prefix |
| `<p>` | Text | `txt_` prefix |
| `<input type="text">` | TextBox | `txt_` prefix, `appearance="outlined"` |
| `<input type="email">` | TextBox | `txt_` prefix |
| `<input type="password">` | TextBox | `txt_` prefix, `hide_text = True` |
| `<textarea>` | TextArea | `txt_` prefix |
| `<select>` | DropdownMenu | `dd_` prefix |
| `<input type="date">` | DatePicker | `dp_` prefix |
| `<input type="checkbox">` | CheckBox | `cb_` prefix |
| `<button>` | Button | `btn_` prefix |
| `<a>` | Link | `nav_` prefix |
| `<div>` (vertical stack) | ColumnPanel | `col_` prefix |
| `<div>` (horizontal flow) | FlowPanel | `flow_` prefix |
| `<div>` (card) | Card | `card_` prefix |
| `<table>` / DataGrid | DataGrid | `dg_` prefix |
| Repeating list | RepeatingPanel | `rp_` prefix |
| DataGrid row | DataRowPanel | `drp_` prefix (not yet in nomenclature.md) |
| Toggle | Switch | `sw_` prefix |
| Radio buttons | RadioGroupPanel | `rbg_` prefix |
| Chart | Plot | `plot_` prefix |
| Icon span | Icon | Material Icons ligature, `class="material-icons"` |
| File upload | FileLoader | `fu_` prefix |

**Legacy component ban:** The legacy `Label` (`lbl_`) component must NOT be used. Use `Text` component instead.

---

## 4. Accessibility or QA Requirements

### 4.1 Formal Accessibility Spec

**Not found — needs external research.**

No dedicated accessibility specification (WCAG level targets, ARIA requirements, keyboard navigation rules, contrast ratio minimums, focus management) was found in the mb-3-cs-project-library docs or ADRs.

### 4.2 Dark Mode

**Partially defined.**

| Source | Rule |
|--------|------|
| Production reports (2026-06-24) | Screens include `@media (prefers-color-scheme: dark)` dark mode support. |
| [[[default:adr-global/adr-dark-mode\|Dark Mode — V1 Inclusion]]](C:/dev/project-library-global/adr-global/adr-dark-mode.md) (global) | Dark mode included in V1 per ADR-46. |

**Note:** Dark mode is included in V1 per ADR-46. All screens must include dark mode token overrides. Authoritative dark-mode tokens defined in [[[mb-3-cs-project-context:docs-local/design_palette_section|DESIGN Palette Section]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN_palette_section.md).

### 4.3 Reduced Motion

**Partially defined.**

| Source | Rule |
|--------|------|
| Production reports (2026-06-24) | Screens include `@media (prefers-reduced-motion: reduce)` support. |

No detailed reduced-motion specification found.

### 4.4 RBAC (Role-Based Access Control)

**Clearly defined.**

| Source | Rule |
|--------|------|
| [[[default:specifications-global/spec-security-architecture\|Security & Secrets Architecture]]](C:/dev/project-library-global/specifications-global/spec-security-architecture.md) (global) | RBAC role limits and security posture. The former docs/test-specifications.md (full per-form RBAC access matrix, Owner/Manager/Admin/Staff/Customer, `@authenticated_endpoint` requirement) was retired — no longer on disk anywhere (verified 2026-09-18); its content was elevated to the global spec. |

### 4.5 QA Verification Checklist

**Clearly defined (for wireframes).**

| Source | Rule |
|--------|------|
| [[[default:adr-global/adr-design-rules\|Wireframe Design Rules]]](C:/dev/project-library-global/adr-global/adr-design-rules.md) (global) Section 7 | 15-item compliance checklist applied to every wireframe. |

---

## 5. Prior AI Prompt Templates, Build Rules, or Agent Instructions

**Clearly defined.**

Two canonical prompt templates exist in gbrain (not on disk):

| Source | Purpose |
|--------|---------|
| `prompts/wireframes` (gbrain slug) | Wireframe production prompt. Governs the /design-consultation workflow for creating wireframes per ADR-27. |
| `prompts/screens` (gbrain slug) | Screen production prompt. Governs the /design-html workflow for creating screen HTML from wireframes. |

### Key Build Rules from Prompts

1. **Source of authority chain:** Architecture → Wireframes → Screens. ADR-27 Section 1.3.
2. **One at a time:** Each wireframe/screen produced individually, not batched.
3. **Wireframes are structural authority:** Every component in a screen must exist in the corresponding wireframe. No additions, removals, or restructuring.
4. **No sample data in wireframes.** Sample data belongs in screens only.
5. **No behaviour text in wireframes.** All behaviour, data, layout logic belongs in specification documents (ADR-27 Section 4.3).
6. **Material Icons required:** All screens with navigation or icon components must import `Material Icons` font.
7. **Style reference:** `output-gstack/design-reference-screens-old.html` is the canonical visual reference.
8. **Empty state mandatory:** Every data-driven form must include an empty state with exactly 4 components: Icon, Heading, Body text, Primary action button.
9. **Sample data standard:** South African consulting/services vertical, ZAR currency, plausible business data. No "Lorem ipsum".
10. **One screen at a time, no batching, no templating.**
11. **GBrain search before production:** Agents must search gbrain for package-specific context before producing any wireframe or screen.
12. **Mandatory pre-work reading list:** 13 documents must be read before producing any screen.

### Prompt Template References (mandatory reading list for screen production)

| # | Document |
|---|----------|
| 1 | [[[default:specifications-global/spec-screen-production-standard\|Screen Production Standard]]](C:/dev/project-library-global/specifications-global/spec-screen-production-standard.md) (global) |
| 2 | [[[mb-3-cs-project-context:docs-local/design\|DESIGN]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN.md) |
| 3 | [[[mb-3-cs-project-context:docs-local/design_palette_section\|DESIGN Palette Section]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN_palette_section.md) |
| 4 | `docs/theme_patched.css` |
| 5 | `docs/theme_mui_patch.css` |
| 6 | [[[default:specifications-global/spec-m3_component_mapping\|m3_component_mapping.md]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md) (global) |
| 7 | [[[default:specifications-global/spec-ui-standards\|UI Standards]]](C:/dev/project-library-global/specifications-global/spec-ui-standards.md) (global) |
| 8 | [[[default:specifications-global/spec-material-3-theme\|Material 3 Theme Specification]]](C:/dev/project-library-global/specifications-global/spec-material-3-theme.md) (global) |
| 9 | `adr/37-ui-customization-approach.md` |
| 10 | [[[default:adr-global/adr-material-3-theme-component-scope\|Material 3 Theme Component Scope]]](C:/dev/project-library-global/adr-global/adr-material-3-theme-component-scope.md) (global) |
| 11 | [[[default:adr-global/adr-design-rules\|Wireframe Design Rules]]](C:/dev/project-library-global/adr-global/adr-design-rules.md) (global) |
| 12 | Package-specific architecture/ADR docs |
| 13 | Material Icons import (mandatory in all screens) |

---

## 6. Material 3 Theme Component Usage

**Clearly defined.**

| Source | Rule |
|--------|------|
| [[[default:specifications-global/spec-m3_component_mapping\|m3_component_mapping.md]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md) (global) | Authoritative list of approved M3 components and their Anvil classes. |
| [[[default:specifications-global/spec-material-3-theme\|Material 3 Theme Specification]]](C:/dev/project-library-global/specifications-global/spec-material-3-theme.md) (global) | Theme architecture, dependency details, customization hierarchy. |
| [[[default:specifications-global/spec-ui-standards\|UI Standards]]](C:/dev/project-library-global/specifications-global/spec-ui-standards.md) (global) | M3 mandate, component naming, Designer-only properties. |
| [[[default:adr-global/adr-material-3-theme-component-scope\|Material 3 Theme Component Scope]]](C:/dev/project-library-global/adr-global/adr-material-3-theme-component-scope.md) (global) | Permitted M3 components, custom composition rules. |

### Writeback Support (from [[[default:specifications-global/spec-m3_component_mapping|m3_component_mapping.md]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md))

| Component | Writeback Property |
|-----------|-------------------|
| TextBox | `text` |
| TextArea | `text` |
| Checkbox | `checked` |
| Switch | `selected` |
| RadioGroupPanel | `selected_value` |
| Slider | `value` |
| DropdownMenu | `selected_value` |
| DatePicker | `selected_date` |

**Not writeback-capable:** RadioButton, FileLoader.
**Display-only (no writeback):** Text, Link, RichText, Heading, LinearProgressIndicator, CircularProgressIndicator.

### Properties Table Rule

Only Designer-mandatory properties appear in wireframe properties tables. `writeback = W` must be specified for all writeback-capable input components.

---

## 7. MUI Overlay Aspects Applied

**Clearly defined.**

| Source | Rule |
|--------|------|
| [[[mb-3-cs-project-context:docs-local/design\|DESIGN]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN.md) (MUI Aesthetic Implementation section) | Authoritative — 9 adopted MUI variables. |
| `adr/37-ui-customization-approach.md` | ADR decision: MUI is aesthetic-only CSS overlay, not a component library. Approach 2 adopted (M3 with custom CSS overrides). |
| [[[mb-3-cs-project-context:output-gstack/eng-review-2026-06-16\|Mybizz CS V1]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/output-gstack/eng-review-2026-06-16.md) (the surviving gstack review report — the 2026-06-16 plan-design-review report no longer exists anywhere, verified 2026-09-18) | MUI findings report with full comparison table. |

### The 9 MUI Aesthetic Variables (mandatory in every screen)

| # | Variable | MUI Value | M3 Default |
|---|----------|-----------|------------|
| 1 | Surface distinction | Two-level: body `var(--surface)`, panels/nav `var(--surface-container-low)` | Single surface |
| 2 | Card shadow | Soft dual-layer `hsla(220,30%,5%,0.07) 0px 4px 16px 0px, hsla(220,25%,10%,0.07) 0px 8px 16px -5px` | M3 elevation system |
| 3 | Card border radius | 8px | 12px |
| 4 | Typography heading weight | 600 | 400 |
| 5 | Dividers | outline-variant at 40% opacity | Solid |
| 6 | State hover opacity | 0.08 | M3 default |
| 7 | State pressed/focused opacity | 0.12 | M3 default |
| 8 | App bar | `var(--surface)` background, 1px hairline bottom border at outline-variant 40% opacity | M3 default |
| 9 | Left nav | `var(--surface-container-low)` background, 1px hairline right border at outline-variant 40% opacity | M3 default |

### What MUI Does NOT Affect (preserved from M3)

- Button radius: 100px (pill)
- Dialog radius: 28px
- Input radius: 4px
- Navigation item radius: 100px
- Data grid radius: 24px
- Font family: Roboto
- Spacing rhythm: 4px base grid
- M3 color role structure

### Critical Prohibitions

- **No MUI React components, no `sx` props, no MUI imports, no MUI CDN links — ever.**
- **App bar must use `var(--surface)`** (white/near-white in Professional Blue). Never black, dark grey, or any hardcoded dark color.

---

## 8. Approved/Restricted Anvil Component List

### 8.1 Approved Components

**Clearly defined.**

| Source | Rule |
|--------|------|
| [[[default:specifications-global/spec-m3_component_mapping\|m3_component_mapping.md]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md) (global) | Authoritative approved component list. |
| [[[default:adr-global/adr-material-3-theme-component-scope\|Material 3 Theme Component Scope]]](C:/dev/project-library-global/adr-global/adr-material-3-theme-component-scope.md) (global) | Permitted components and custom composition rules. |

Approved Anvil Material 3 Theme components:
- ColumnPanel, FlowPanel, Card, DataRowPanel
- Heading, Text, Link, RichText
- TextBox, TextArea, DropdownMenu, DatePicker
- CheckBox, Switch, RadioButtonGroup, RadioButton, Slider
- Button, IconButton
- DataGrid, RepeatingPanel
- Icon, Image, FileLoader
- Plot
- LinearProgressIndicator, CircularProgressIndicator

### 8.2 Restricted/Banned Components

**Clearly defined.**

| Restriction | Source |
|-------------|--------|
| Legacy `Label` (`lbl_`) component — **BANNED** | [[[default:adr-global/adr-design-rules\|Wireframe Design Rules]]](C:/dev/project-library-global/adr-global/adr-design-rules.md) (global) Section 3.2, all production reports |
| Any component not in the approved M3 list — **BANNED** | [[[default:adr-global/adr-design-rules\|Wireframe Design Rules]]](C:/dev/project-library-global/adr-global/adr-design-rules.md) (global) Section 3.1 |

### 8.3 Custom HTML Components

**Clearly defined.**

| Source | Rule |
|--------|------|
| [[[mb-3-cs-project-context:docs-standard-local/scaffold-spec\|Scaffold Specification]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-standard-local/scaffold-spec.md) | 5 shared custom components defined: `ClauseBuilder`, `FooterComponent`, `VideoDisplayComponent`, `TimeLapseCarouselComponent`, `ParallaxComponent`. |
| `obsolete/revised-anvil-worklist` | Component inventory with usage contexts. |
| [[[mb-3-cs-project-context:output-gstack/eng-review-2026-06-16\|Mybizz CS V1]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/output-gstack/eng-review-2026-06-16.md) (the surviving gstack review report — the 2026-06-16 plan-design-review report no longer exists anywhere, verified 2026-09-18) | 5 custom components noted; some lack interaction specs, wireframes, and state definitions. |

**When custom HTML components are allowed:**
- Only when specified in [[[mb-3-cs-project-context:docs-standard-local/scaffold-spec|Scaffold Specification]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-standard-local/scaffold-spec.md) as a shared custom component.
- Only when the component serves a reusable purpose across multiple forms (e.g. `FooterComponent` on all public pages).
- Layout variants (e.g. `BlogListTiledTemplate`, `BlogPostClassicTemplate`) are NOT standalone components — they are content panels built INTO the parent form per scaffold-spec v2.

### 8.4 Component-Purpose Rules

**Clearly defined.**

| Source | Rule |
|--------|------|
| [[[default:adr-global/adr-design-rules\|Wireframe Design Rules]]](C:/dev/project-library-global/adr-global/adr-design-rules.md) (global) Section 3.3 | FlowPanel for horizontal layout, ColumnPanel for vertical layout, DataRowPanel for DataGrid row templates. Containers must be drawn as containers with components inside. |

---

## Source File Index

All files verified to exist on disk at `/mnt/c/dev/dev-mb-3-cs/mb-3-cs-project-library/`:

| File | Role |
|------|------|
| [[[mb-3-cs-project-context:docs-local/design\|DESIGN]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN.md) | Canonical design system — tokens, typography, spacing, roles, empty states, MUI section |
| `specifications-global/spec-nomenclature.md` (global) | Component naming conventions and prefixes |
| [[[default:specifications-global/spec-m3_component_mapping\|m3_component_mapping.md]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md) (global) | Approved M3 components, Anvil classes, writeback support |
| [[[default:specifications-global/spec-ui-standards\|UI Standards]]](C:/dev/project-library-global/specifications-global/spec-ui-standards.md) (global) | M3 mandate, component naming, Designer-only properties |
| [[[default:specifications-global/spec-screen-production-standard\|Screen Production Standard]]](C:/dev/project-library-global/specifications-global/spec-screen-production-standard.md) (global) | Screen production rules — M3 token block, layout patterns, form components |
| [[[default:specifications-global/spec-material-3-theme\|Material 3 Theme Specification]]](C:/dev/project-library-global/specifications-global/spec-material-3-theme.md) (global) | Theme architecture, dependency details, customization hierarchy |
| [[[mb-3-cs-project-context:docs-standard-local/scaffold-spec\|Scaffold Specification]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-standard-local/scaffold-spec.md) | Form inventory, component classification, architecture backing |
| [[[mb-3-cs-project-context:docs-local/design_palette_section\|DESIGN Palette Section]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN_palette_section.md) | Professional Blue palette tokens (19 tokens) |
| `docs/theme_patched.css` | Production theme.css with MUI patch |
| `docs/theme_mui_patch.css` | MUI patch in isolation |
| [[[default:specifications-global/spec-security-architecture\|Security & Secrets Architecture]]](C:/dev/project-library-global/specifications-global/spec-security-architecture.md) (global) | RBAC role limits, security posture (the former docs/test-specifications.md RBAC matrix was retired — verified absent 2026-09-18; content elevated to the global spec) |
| [[[default:adr-global/adr-design-rules\|Wireframe Design Rules]]](C:/dev/project-library-global/adr-global/adr-design-rules.md) (global) | Governing document for every wireframe (9 rules, 15-item compliance checklist) |
| [[[default:adr-global/adr-material-3-theme-component-scope\|Material 3 Theme Component Scope]]](C:/dev/project-library-global/adr-global/adr-material-3-theme-component-scope.md) (global) | Permitted M3 components, custom composition rules |
| `adr/37-ui-customization-approach.md` | MUI is aesthetic-only CSS overlay decision |
| `output-gstack/design-reference-screens-old.html` | Canonical visual style reference |

---

*End of audit document.*
