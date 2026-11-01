---
title: "anvil-ui-build-standard"
doc-id: "mb-6e69563b"
updated: "2026-11-01"
---
# Mybizz CS — Anvil UI Build Standard

How this application's screens are built in Anvil.

**Standard:** [[[default:specifications-global/spec-ui-standards]]](C:/dev/project-library-global/specifications-global/spec-ui-standards.md)

**Purpose:** the rules that go with every per-form instruction to Anvil's AI at Step 29, when each approved wireframe, its screen HTML and its screen PNG are built into a native Anvil form. The design values themselves are in `DESIGN.md` and `palette-tokens.md`; where this document and `DESIGN.md` differ on a design value, `DESIGN.md` is correct.

---

## Hard Prohibitions

Check these first; they are the most common failures.

- **No hardcoded colour.** Every colour comes from the Anvil M3 Colour Scheme: `%color:Name%` in the Designer and in `theme.css`, `var(--anvil-m3-…)` in CSS read at runtime. A translucent colour is made with `color-mix()` from a token. No hex, `rgb()`, `rgba()`, `hsl()` or `hsla()` value anywhere, including shadows. No exception exists.
- **No component outside [[[default:specifications-global/spec-m3_component_mapping|M3 Component Mapping]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md)** (Approved Components and Writeback).
- **No legacy `Label`.** Use `Text`.
- **No `HTMLTemplate`**, no `navigate_to`. Menu items in the layouts are the layouts' `NavigationLink` components with lambda click handlers that capture their loop variables (`scaffold-spec.md`, Navigation Architecture).
- **No visible text typed into the Designer.** Every visible text (headings, labels, button text, placeholders, tooltips, empty states) is set in code from a phrase key with `t(key)` from `shared/translations/` (F-25). The Designer holds component names and Designer-only properties, never visible words.
- **No MUI component, `sx` property, MUI import or MUI script.** The MUI influence is the set of project deviations in `DESIGN.md`, applied through `theme.css`; nothing else.
- **No data rule, credential or secret in a form.** Forms show data and call server functions; every rule lives on the server.
- **No schema change.** A schema change Anvil's AI proposes is not applied; it goes back to Step 17.
- **The wireframe HTML is the structural authority.** Build what the wireframe holds: no added, removed or rearranged components, even when the screen PNG seems to show more.

---

## Visual Tokens

All values come from `DESIGN.md`. The ones Anvil's AI needs most:

### Colour

- The Colour Scheme holds the 46 colours of the Anvil M3 theme plus the three status colours `status-success`, `status-pending`, `status-error` (`DESIGN.md`, Color System).
- The client's palette (Professional Blue, Warm Coral, Calm Teal, Sophisticated Purple) is applied at runtime by `shared/palette_utils.py`, which sets the `--anvil-m3-*` properties (`palette-tokens.md`). A form never sets a colour itself.
- Dark mode follows the device on every screen, by the same mechanism. Every form is checked in light and in dark.
- Status is always shown as an outlined status badge with its word, never by colour alone (`DESIGN.md`, Status Badges).

### Typography

Roboto, with the M3 scale and this project's weights (`DESIGN.md`, Type Scale): page titles Headline Large 32/40 at 600, section headings Headline Small 22/28 at 600, card titles Title 18/24 at 500, body 14/20 at 400, labels 12/16 at 500.

### Spacing, radius, elevation

- Spacing on the 4px grid: 4, 8, 12, 16, 24, 32px (`DESIGN.md`, Spacing Scale).
- Radius: buttons 100px, cards 8px, dialogs 28px, outlined inputs 4px, navigation items 100px, data grids 24px.
- Elevated cards use the project's soft dual-layer shadow, built with `color-mix()` from On Surface; other cards have no shadow.
- The project deviations (cards, headings, shadow, dividers, surfaces, app bar, navigation, state layers) are listed in `DESIGN.md`, Project Deviations from M3 Defaults. They are implemented once, in `theme.css`.

### theme.css

- `theme.css` holds only the project deviations and the project roles of `DESIGN.md`, written with `%color:Name%` and `var(--anvil-m3-…)`.
- It holds no `--md-sys-color-*` property (the M3 theme does not read them), no typed colour value, no rule for the classic Anvil theme (`.btn`, `.app-bar`, `.left-nav`, `.structure`), and no rule that repaints every `ColumnPanel`.
- It ends with the reduced-motion rule: under `prefers-reduced-motion: reduce`, animations and transitions are reduced to near zero.

### Responsive behaviour

Responsive behaviour is set per container with `wrap_on`, never with a breakpoint table ([[[default:adr-global/adr-responsive-behaviour-mechanism|Responsive Behaviour Mechanism]]](C:/dev/project-library-global/adr-global/adr-responsive-behaviour-mechanism.md) ADR):

- A container whose contents stack on a phone sets `wrap_on = "mobile"`.
- A container whose contents stack on a tablet as well sets `wrap_on = "tablet"`.
- A container that keeps its shape at every width sets `wrap_on = "never"`.
- Navigation collapses into a drawer by itself in the native M3 layouts; no form sets it.

Every form works at every width down to 375px, with no sideways scrolling, and with text up to 40% longer than the English (French, Portuguese, Spanish, German, Italian): buttons and labels wrap or grow, never cut text off.

**Maximum width by form type:**

| Form type | Maximum width |
|---|---|
| Sign-in and password forms | 400px, centred |
| List forms | 1200px |
| Dashboard | 1200px |
| Editor and viewer forms | 900px |
| Public pages | 900px |
| Settings | 1000px |
| Onboarding | 600px, centred |
| Card in a grid | 320px |
| Dialog holding a form | 480px |
| Dialog showing detail | 640px |
| Error pages | 480px, centred |

Custom components have no width of their own: they take the width of the place they are put. `FooterComponent` and `ParallaxComponent` run full width.

---

## Naming Conventions

### Components

Prefixes follow [[[default:specifications-global/spec-nomenclature|Nomenclature]]](C:/dev/project-library-global/specifications-global/spec-nomenclature.md):

| Component | Prefix | Example |
|---|---|---|
| Button | `btn_` | `btn_save` |
| IconButton (with a click) | `btn_` | `btn_edit` |
| IconButton (decorative icon, no click) | `ico_` | `ico_empty_vault` |
| Text | `text_` | `text_subtitle` |
| TextBox, TextArea | `txt_` | `txt_from_email` |
| Heading | `hdg_` | `hdg_page_title` |
| Link, NavigationLink | `nav_` | `nav_forgot_password` |
| DropdownMenu | `dd_` | `dd_timezone` |
| DatePicker | `dp_` | `dp_date_from` |
| Checkbox | `cb_` | `cb_gateway_connected` |
| RadioButton | `rb_` | `rb_monthly` |
| RadioGroupPanel | `rgp_` | `rgp_meeting_type` |
| Switch | `sw_` | `sw_test_mode` |
| FileLoader | `fu_` | `fu_attachment` |
| Image | `img_` | `img_service` |
| ColumnPanel | `col_` | `col_email_config` |
| FlowPanel | `flow_` | `flow_sender_fields` |
| Card, InteractiveCard | `card_` | `card_sender` |
| DataGrid | `dg_` | `dg_users` |
| DataRowPanel | `drp_` | `drp_user_row` |
| RepeatingPanel | `rp_` | `rp_secrets` |
| Plot | `plot_` | `plot_revenue_trend` |

`text_`, `rgp_`, `sw_` and `img_` are this project's prefixes and are added to Nomenclature at the next library update.

### Event handlers

Anvil's own form: `{component_name}_{event}` — `btn_save_click`, `txt_email_change`, `dd_timezone_change`.

### Files

- Wireframe: `wireframe-{package}-{FormName}.html`; screen: `screen-{package}-{FormName}.html` and `.png`; a row template: `wireframe-{package}-{FormName}-RowTemplate.html`.
- Form and component names are PascalCase; Anvil accepts no hyphens in form or component names.

---

## HTML to Anvil Component Mapping

| HTML in the wireframe | Anvil component | Notes |
|---|---|---|
| `<h1>`, `<h2>`, `<h3>` | Heading | style and scale per `DESIGN.md` |
| `<p>`, `<span>` text | Text | |
| Rich text block | RichText | |
| `<input type="text">`, `email`, `tel`, `number` | TextBox | `appearance = "outlined"` |
| `<input type="password">` | TextBox | `hide_text = True`, set in the Designer |
| `<textarea>` | TextArea | `appearance = "outlined"` |
| `<select>` | DropdownMenu | |
| `<input type="date">` | DatePicker | |
| `<input type="checkbox">` | Checkbox | |
| Toggle | Switch | |
| Radio buttons | RadioButton inside a RadioGroupPanel | |
| `<button>` | Button | appearance per the button hierarchy |
| Icon-only button | IconButton | `icon = "mi:name"` |
| Icon `<span>` | IconButton, no click | `icon = "mi:name"` |
| `<a>` | Link | |
| `<img>` | Image | |
| `<div>` vertical stack | ColumnPanel | |
| `<div>` horizontal row | FlowPanel | |
| Card `<div>` | Card | appearance per `DESIGN.md` Cards |
| Clickable card | InteractiveCard | |
| `<hr>` | Divider | |
| `<table>`, list grid | DataGrid | |
| DataGrid row | DataRowPanel | |
| Repeated list | RepeatingPanel | |
| Chart | Plot | colours from tokens |
| File upload | FileLoader | |
| Progress bar or spinner | LinearProgressIndicator, CircularProgressIndicator | |
| Tabs, segmented control | FlowPanel of Buttons with shared selected state | per M3 Component Mapping |
| Chip, badge | Outlined status badge: FlowPanel with a dot and a Text | `DESIGN.md` Status Badges |

**Structure:** containers are built as containers with their components inside, never flattened. FlowPanel is horizontal; ColumnPanel is vertical; DataRowPanel is the DataGrid row template.

---

## Approved Components and Writeback

**Approved components:** the components in [[[default:specifications-global/spec-m3_component_mapping|M3 Component Mapping]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md). Those this project uses: Text, Heading, RichText, Link, Button, IconButton, ButtonMenu, IconButtonMenu, AvatarMenu, MenuItem, Card, InteractiveCard, Avatar, TextBox, TextArea, DropdownMenu, DatePicker, Checkbox, RadioButton, RadioGroupPanel, Switch, Slider, FileLoader, LinearProgressIndicator, CircularProgressIndicator, Divider, Image, Plot, ColumnPanel, FlowPanel, DataGrid, DataRowPanel, RepeatingPanel, and the native M3 layouts. If this list and the mapping differ, the mapping is correct.

**Writeback:** switched on in the Designer's Data Bindings panel (the W toggle); it cannot be set in code.

| Component | Writeback property |
|---|---|
| TextBox, TextArea | `text` |
| Checkbox | `checked` |
| Switch | `selected` |
| RadioGroupPanel | `selected_value` |
| Slider | `value` |
| DropdownMenu | `selected_value` |
| DatePicker | `selected_date` |

**Not writeback-capable:** RadioButton (use its RadioGroupPanel), FileLoader. **Display only:** Text, Heading, RichText, Link, LinearProgressIndicator, CircularProgressIndicator.

**Properties:** set only the properties the wireframe needs. Inputs use `appearance = "outlined"`. A failed validation sets the input's `error` property and shows the message in its supporting text; both are cleared when the input is valid.

**Button hierarchy:** one filled button per screen for the primary action; outlined for secondary; text for tertiary.

**Set in the Designer and checked before a form is accepted, because code cannot repair them afterwards:**
- **Writeback** on every writeback-capable input the wireframe declares.
- **Row templates:** placing a DataGrid makes a RepeatingPanel and a row-template form. The RepeatingPanel's `template` property is set to that row-template form (`{FormName}RowTemplate`). The row template's own contents are built separately, from their own wireframe.
- **Appearance** of every TextBox, TextArea, DropdownMenu, DatePicker and Button, matched to the screen PNG.
- **`hide_text`** on every password field.

---

## Custom HTML Components

- Only the eleven shared custom components in `custom-component-requirements-matrix.md` exist: `VideoDisplayComponent`, `FooterComponent`, `TimeLapseCarouselComponent`, `ParallaxComponent`, `ClauseBuilder`, `AIHelperComponent`, `StarRatingComponent`, `HelpTooltipComponent`, `EditModeComponent`, `SharePanelComponent`, `ReaderShareComponent`. They live in `client_code/components/`.
- Every public page form, landing page template and blog post template carries `EditModeComponent` and registers its editable elements (`DESIGN.md`, Edit Mode).
- Every feature heading and field that has help carries `HelpTooltipComponent` with its `help_key`.
- One local component exists: `PaletteSettingsForm`, in SettingsForm's folder.
- No new custom component is created for a single form. A page template (a homepage, blog or landing page layout) is a form in its parent's folder, not a custom component (`scaffold-spec.md`).
- The booking calendar is a feature module built to `calendar-feature-spec.md`, not a custom component.
- JavaScript is reached only through `anvil.js`, for the uses `DESIGN.md` approves (Implementation Notes, JavaScript Usage); never `eval`.

---

## Form Boundary and Icons

### The wireframe's outer container is the whole build

A screen PNG shows the whole page, including the layout around the form: navigation, app bar, footer, sign-out. That layout belongs to the form's layout (`AdminLayout`, `CustomerLayout`, `PublicLayout`, `BlankLayout`), not to the form. Only what sits inside the wireframe's outer container is built. Rows shown inside a DataGrid or RepeatingPanel belong to the row-template form and are not built with the parent form.

### Icons

Icons use Material Icons names with the `mi:` prefix. When the screen PNG shows an icon on a Button, an IconButton or a TextBox (`leading_icon`) that the wireframe does not name, the icon is added from this table, or the nearest Material Icons name, and the choice is noted:

| Button label or purpose | Icon |
|---|---|
| Save | `mi:save` |
| Add | `mi:add` |
| Edit | `mi:edit` |
| Cancel, close | `mi:close` |
| Search | `mi:search` |
| Filter | `mi:filter_list` |
| Download | `mi:download` |
| Upload | `mi:upload` |
| Send | `mi:send` |
| Block | `mi:block` |
| Back | `mi:arrow_back` |
| Next, continue | `mi:arrow_forward` |

A Button's `icon_alignment` is set to match the screen PNG (`leading` or `trailing`). Icons inside DataGrid rows belong to the row-template form.

---

## Data, Empty States and Access

- **Sample data in wireframes and tests:** a South African consulting or service business, in ZAR, with plausible names and figures. Never "Lorem ipsum".
- **Empty state** on every data-driven screen: an icon, a heading, a line of body text and, where there is a next step, a primary button. The wording for each screen is in `DESIGN.md`, Empty States.
- **Roles:** Owner, Manager, Admin, Staff, Customer. A form hides what a role cannot use; every server function enforces the role with the role decorators of `server_auth/rbac` (F-01).
- **Dark mode:** built on every screen, following the device (F-31). No form has a light-only appearance.
- **Accessibility:** WCAG 2.1 AA. Text contrast at least 4.5:1, large text and interface elements at least 3:1, in every palette in light and dark; every control usable by keyboard; focus always visible; every input has a label; status never shown by colour alone; motion reduced when the device asks.

---

## Build Process Rules

1. One form per conversation with Anvil's AI; a new conversation for each form.
2. Inputs for each form: its wireframe HTML (structure), its screen HTML and its screen PNG (appearance), the form's name, the M3 components and roles to use, and what must not be touched (other forms, server modules, the schema).
3. Authority: architecture, then wireframe, then screen. The wireframe HTML decides structure; the screen PNG decides appearance where the HTML leaves it open.
4. A plan is asked for and approved before any change. The form is built in small steps: layout, components, then navigation and events, each checked in the running app.
5. Every form is checked in light and dark mode, at compact and expanded widths, before it is accepted, against this list:
   - only approved components; no `Label`, no `HTMLTemplate`;
   - nothing built outside the wireframe's outer container;
   - writeback, row-template, appearance and `hide_text` set in the Designer;
   - icons matched and correctly placed;
   - no hardcoded colour;
   - no visible text in the Designer; every text set from a phrase key;
   - `wrap_on` set on every container that must stack;
   - names and event handlers follow Naming Conventions.
6. The result is pulled locally at Step 30 and validated (`anvil --json validate`) with the UI conformance scans of Step 29.

---

*End of file — Anvil UI Build Standard*
