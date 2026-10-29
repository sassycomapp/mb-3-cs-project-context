---
title: "anvil-ui-build-standard-v1-2026-07-05"
doc-id: "mb-6e69563b"
updated: "2026-10-29"
---
# Anvil UI Build Standard (Consolidated) — v1

**Purpose:** Single reference to accompany the per-UI prompt sent to Anvil's AI agent when converting each of the 70 HTML wireframes + screenshots into native Anvil Forms.

**Source:** Consolidated from OpenCode's internal audit ([[[mb-3-cs-project-context:standard-operating-procedures-local/internal-standards-anvil-wireframe-conversion|Anvil Wireframe Conversion]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/standard-operating-procedures-local/internal-standards-anvil-wireframe-conversion.md), 2026-06-27), which searched the project library via GBrain.

**Status note:** Three items below were marked "not found" in the internal audit (breakpoints, event handler naming, formal accessibility spec). These were originally slated for external research (your planned 2nd tranche). I have not seen that output — if you ran it separately, send it over and I'll fold it in and reissue this as v2. Until then, I've proposed conservative defaults for these three, clearly marked **[PROVISIONAL]**, so production isn't blocked. Treat anything marked that way as "build with this, but confirm/codify before treating it as final."

---

## 1. Hard Prohibitions (check these first — most common failure modes)

- **No hardcoded colour values.** Every colour must resolve through a CSS custom property defined in `theme_patched.css`. No literal hex, `rgb()`, `rgba()`, or `hsl()` — except the MUI card-shadow `hsla()` values specified in §4.
- **No legacy `Label` component.** Use `Text` instead. `lbl_` prefix is banned.
- **No component outside the approved M3 list** (§7.1).
- **No MUI React components, no `sx` props, no MUI imports, no MUI CDN links — ever.** MUI is a CSS aesthetic overlay only, never a component library.
- **App bar must use `var(--surface)`** (white/near-white in Professional Blue). Never black, dark grey, or any hardcoded dark colour.
- **Wireframe HTML is the structural authority.** Build only what's in the supplied HTML — no added, removed, or restructured components, even if the screenshot seems to imply more.

---

## 2. Visual Tokens

**Colour:** Professional Blue is the default palette (19 CSS custom properties, e.g. `--primary: #493ef3`, `--surface: #fdfcff`), sourced from `theme_patched.css`. Status colours (green=active/success, orange=pending/warning, red=error/cancelled) are an accepted exception — semantic, not part of the M3 palette.

**Typography:** Font family `'Roboto', sans-serif`. M3 scale:

| Style | Size |
|---|---|
| Display | 57px |
| Headline Large | 32px |
| Headline Small | 24px |
| Title Large | 22px |
| Title Medium | 16px |
| Body Large | 16px |
| Body Medium | 14px |
| Body Small | 12px |
| Label Large | 14px |
| Label Medium | 12px |
| Label Small | 11px |

**Spacing (4px base grid):** `--space-xs:4px` `--space-sm:8px` `--space-md:16px` `--space-lg:24px` `--space-xl:32px` `--space-2xl:48px`
**Radius:** `--radius-sm:4px` `--radius-md:8px` `--radius-lg:12px` `--radius-xl:16px` `--radius-full:100px`

**Breakpoints [PROVISIONAL]:** Not codified anywhere in the project library. Proposed default (standard M3 convention): mobile `<600px`, tablet `600–1024px`, desktop `>1024px`. Existing layout max-widths are defined per form type (auth cards 400px, editor forms 900px, list forms 1100px) — these take precedence where they apply.

---

## 3. MUI Aesthetic Overlay (mandatory on every screen)

| # | Aspect | MUI value | M3 default |
|---|---|---|---|
| 1 | Surface distinction | Two-level: body `var(--surface)`, panels/nav `var(--surface-container-low)` | Single surface |
| 2 | Card shadow | `hsla(220,30%,5%,0.07) 0 4px 16px 0, hsla(220,25%,10%,0.07) 0 8px 16px -5px` | M3 elevation |
| 3 | Card radius | 8px | 12px |
| 4 | Heading weight | 600 | 400 |
| 5 | Dividers | outline-variant @ 40% opacity | Solid |
| 6 | Hover state opacity | 0.08 | M3 default |
| 7 | Pressed/focused opacity | 0.12 | M3 default |
| 8 | App bar | `var(--surface)`, 1px hairline bottom border @ outline-variant 40% | M3 default |
| 9 | Left nav | `var(--surface-container-low)`, 1px hairline right border @ outline-variant 40% | M3 default |

**Preserved from M3 (MUI does NOT touch these):** Button radius 100px (pill) · Dialog radius 28px · Input radius 4px · Nav item radius 100px · Data grid radius 24px · Font family Roboto · 4px spacing rhythm · M3 colour-role structure.

---

## 4. Naming Conventions

**Component prefixes:**

| Component | Prefix | Example |
|---|---|---|
| ColumnPanel | `col_` | `col_email_config` |
| FlowPanel | `flow_` | `flow_sender_fields` |
| Card | `card_` | `card_sender` |
| DataRowPanel | `drp_` | `drp_user_row` *(adopted working convention — not yet formally added to `nomenclature.md`)* |
| Heading | `hdg_` | `hdg_email_title` |
| Text | `txt_` | `txt_email_subtitle` |
| TextBox / TextArea | `txt_` | `txt_from_email` |
| DropdownMenu | `dd_` | `dd_timezone` |
| Button / IconButton | `btn_` | `btn_save`, `btn_edit` |
| CheckBox | `cb_` | `cb_gateway_connected` |
| Switch | `sw_` | `sw_test_mode` |
| RepeatingPanel | `rp_` | `rp_secrets` |
| DataGrid | `dg_` | `dg_users` |
| Icon | `ic_` / `ico_` | `ic_empty_vault` |
| Link | `nav_` | `nav_forgot_password` |
| DatePicker | `dp_` | `dp_date_from` |
| Plot | `plot_` | `plot_revenue_trend` |
| Image | `img_` | `img_service` |
| RadioButtonGroup | `rbg_` | `rbg_meeting_type` |
| FileLoader | `fu_` | `fu_attachment` |

**File naming:** `wireframe-{package}-{FormName}.html` / `screen-{package}-{FormName}.html` / `wireframe-{package}-{FormName}-RowTemplate.html`. Anvil does **not** accept hyphens in file or component names — use PascalCase for the `{FormName}` portion.

**Event handler naming [PROVISIONAL]:** Not codified anywhere. Proposed default, following Anvil's own convention: `{component_name}_{event}`, e.g. `btn_save_click`, `txt_email_change`, `dd_timezone_change`.

---

## 5. HTML → Anvil Component Mapping

| HTML Element | Anvil Component | Notes |
|---|---|---|
| `<h1>`, `<h2>` | Heading | `hdg_` |
| `<p>` | Text | `txt_` |
| `<input type="text">` | TextBox | `appearance="outlined"` |
| `<input type="email">` | TextBox | |
| `<input type="password">` | TextBox | `hide_text = True` |
| `<textarea>` | TextArea | |
| `<select>` | DropdownMenu | |
| `<input type="date">` | DatePicker | |
| `<input type="checkbox">` | CheckBox | |
| `<button>` | Button | |
| `<a>` | Link | |
| `<div>` (vertical stack) | ColumnPanel | |
| `<div>` (horizontal flow) | FlowPanel | |
| `<div>` (card) | Card | |
| `<table>` / list grid | DataGrid | |
| DataGrid row | DataRowPanel | |
| Repeating list | RepeatingPanel | |
| Toggle | Switch | |
| Radio buttons | RadioButtonGroup | |
| Chart | Plot | |
| Icon span | Icon | Material Icons ligature, `class="material-icons"` |
| File upload | FileLoader | |

**Structural rule:** FlowPanel = horizontal layout, ColumnPanel = vertical layout, DataRowPanel = DataGrid row templates. Containers must be built as actual containers with components nested inside — not flattened.

---

## 6. Approved Components & Writeback

**Approved M3 components (nothing outside this list):**
ColumnPanel, FlowPanel, Card, DataRowPanel, Heading, Text, Link, RichText, TextBox, TextArea, DropdownMenu, DatePicker, CheckBox, Switch, RadioButtonGroup, RadioButton, Slider, Button, IconButton, DataGrid, RepeatingPanel, Icon, Image, FileLoader, Plot, LinearProgressIndicator, CircularProgressIndicator.

**Writeback-capable (specify `writeback = W` in properties):**

| Component | Writeback property |
|---|---|
| TextBox / TextArea | `text` |
| Checkbox | `checked` |
| Switch | `selected` |
| RadioGroupPanel | `selected_value` |
| Slider | `value` |
| DropdownMenu | `selected_value` |
| DatePicker | `selected_date` |

**Not writeback-capable:** RadioButton, FileLoader. **Display-only:** Text, Link, RichText, Heading, LinearProgressIndicator, CircularProgressIndicator.

**Properties rule:** Only Designer-mandatory properties should appear/be set — don't add extraneous properties beyond what's needed to match the wireframe.

---

## 7. Custom HTML Components

Only these five shared custom components exist and may be used — never invent new custom components for a one-off form:

`ClauseBuilder`, `FooterComponent`, `VideoDisplayComponent`, `TimeLapseCarouselComponent`, `ParallaxComponent`

Use a custom component only if (a) it's one of the five above, and (b) it serves a reusable purpose across multiple forms. Layout variants (e.g. a "tiled" vs "classic" list template) are **not** standalone components — they're content panels built into the parent Form.

---

## 8. Data, Empty States & Access

- **Sample/placeholder data standard:** South African consulting/services vertical, ZAR currency, plausible business data. Never "Lorem ipsum."
- **Empty state (mandatory on every data-driven form):** exactly 4 components — Icon, Heading, Body text, Primary action button.
- **RBAC:** Roles are Owner, Manager, Admin, Staff, Customer. Every server function must carry the `@authenticated_endpoint` decorator.
- **Dark mode:** Deferred from V1 — do not build dark-mode variants unless explicitly asked.
- **Accessibility [PROVISIONAL]:** No formal spec exists. Proposed baseline until one is codified: WCAG AA contrast minimums (4.5:1 text), all interactive components keyboard-operable, visible focus states on inputs/buttons.

---

## 9. Build Process Rules

1. One Form built per conversation/prompt — no batching multiple UIs in one go.
2. Authority chain: Architecture → Wireframes → Screens. The Form being built must match the supplied wireframe HTML structurally and the screenshot visually — HTML wins on structure, screenshot wins on visual styling ambiguity.
3. Material Icons font must be imported on any Form using navigation or icon components.

---

## Open Items Requiring Your Decision

| Item | Current state | Action needed |
|---|---|---|
| Breakpoints | Provisional default proposed (§2) | Confirm or supply real spec |
| Event handler naming | Provisional default proposed (§4) | Confirm or supply real spec |
| Accessibility spec | Provisional baseline proposed (§8) | Confirm or supply real spec |
| `drp_` prefix | Adopted as working standard | Get formally added to `nomenclature.md` |

*End of v1.*
