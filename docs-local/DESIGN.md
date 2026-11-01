---
title: "DESIGN"
doc-id: "mb-190df6bf"
updated: "2026-11-01"
---
# Mybizz CS — DESIGN

This document is the design system of Mybizz CS. It is built on the **Anvil Material 3 theme** (dependency ID `4UK6WHQ6UX7AKELK`) and applies to every Mybizz CS screen: the public website, the admin area, the customer portal, and the Mybizz management app (App 5).

- The colour values of the four palettes live in `palette-tokens.md`; this document defines how colour is used.
- Generic Material 3 rules (accessibility, the `wrap_on` responsive mechanism, state-specification patterns) live in [[[default:specifications-global/spec-m3-design-standards|M3 Design & Component Standards]]](C:/dev/project-library-global/specifications-global/spec-m3-design-standards.md) and are not repeated here. This document states this project's choices and its deviations from the M3 defaults.
- Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct.
- Feature numbers (F-01 to F-36) are those of `mb-3-cs-concept.md`.

---

## Design Direction Summary

### Visual Direction: Clean Professional SaaS

Mybizz CS uses a refined, business-focused Material Design 3 system that emphasizes clarity, efficiency, trust, and professional polish.
The design must support consulting and services businesses such as salons, spas, therapists, consultants, and coaches.

The intended character is:

- Clean and structured, never playful or gimmicky.
- Professional and trustworthy, suitable for real business operations.
- Easy to scan and efficient in management contexts.
- Warm and inviting in public-facing contexts.
- Consistent across public pages, admin tools, onboarding, CRM, blog, bookings, and dashboard views.

### Core Principles

- Clarity over decoration.
- Consistency over experimentation.
- Reusable components over page-specific styling.
- Token-driven theming over hardcoded values.
- Layout variation through composition, not through parallel CSS systems.
- Public and management UI should feel related, but not identical in tone.

### Do Emulate

- Material 3 color roles and token-based hierarchy.
- Card-based content organization.
- Clear typography scale with predictable hierarchy.
- 4px spacing rhythm.
- Filled primary actions, outlined secondary actions, text-based tertiary actions.
- Left navigation rail on desktop and mobile-safe navigation patterns.
- Rounded but controlled corners.
- Elevated cards only where emphasis is needed.

### Do Not Emulate

- Bright gradients, flashy hero treatments, or decorative visual noise.
- Childish or overly playful interface language.
- Designing in dark mode first. Light mode is the design baseline; dark mode follows the device setting on every screen ([[[default:adr-global/adr-dark-mode|Dark Mode]]](C:/dev/project-library-global/adr-global/adr-dark-mode.md) ADR).
- Heavy animation, glassmorphism, or neumorphism.
- Complex charts or visual systems beyond what is operationally useful.
- Template-specific CSS divergence that breaks the shared design system.

---

## Dual-Context Product Tone

The product has two distinct but related presentation contexts.
Both contexts use one shared design system. The difference is in emphasis, density, and composition, not in building two separate design languages.

### Management UI

The management side should feel like an office tool.
Its principles are easy, obvious, and relevant.

Characteristics:

- Higher contrast and clearer hierarchy.
- Neutral surfaces with restrained primary-color emphasis.
- Clear affordances for actions, forms, filters, and status feedback.
- Slightly more compact and operationally efficient layouts.
- Obvious state changes for active, focused, disabled, and error elements.

### Public-Facing UI

The public side should feel more accommodating and engaging.
Its principles are comfort, trust, warmth, and approachability.

Characteristics:

- Softer visual rhythm and more generous whitespace.
- Palette-driven accents for brand expression.
- Clear CTAs with subtle elevation.
- Template flexibility without loss of component consistency.
- A more welcoming presentation suitable for websites, landing pages, and blog content.

### Consistency Rule

These two contexts must use the same underlying design system:

- Same typography family and scale.
- Same spacing rhythm.
- Same border radius rules.
- Same token structure.
- Same core components.
- Same role naming logic.

---

## Design Tokens

### Color System

All colour comes from the Anvil M3 theme's Colour Scheme. **No hardcoded colour values, anywhere, without exception.**

- In the Designer and in CSS, colours are referenced by Colour Scheme name: `%color:Primary%`.
- At runtime, the Anvil M3 theme exposes every Colour Scheme colour as a CSS custom property on `:root`: `--anvil-m3-` followed by the colour name in lower case with hyphens (`--anvil-m3-primary-container`). The project's own roles in `theme.css` use `var(--anvil-m3-…)`, so that palette switching and dark mode reach every component, native or custom.
- A translucent colour is made from a token with `color-mix()`, for example `color-mix(in srgb, var(--anvil-m3-on-surface) 7%, transparent)`, never with a typed colour value.

The Colour Scheme holds the 46 colours of the Anvil M3 theme:

| Group | Colours |
|---|---|
| Primary | Primary, On Primary, Primary Container, On Primary Container |
| Secondary | Secondary, On Secondary, Secondary Container, On Secondary Container |
| Tertiary | Tertiary, On Tertiary, Tertiary Container, On Tertiary Container |
| Error | Error, On Error, On Error Container |
| Surface and neutral | Background, On Background, Surface, On Surface, Surface Variant, On Surface Variant, Surface Container Low, Surface Container, Surface Container High, Surface Container Highest, Outline, Outline Variant, Inverse Surface, Inverse On Surface |
| Overlays and disabled | Primary Overlay 1–3, Light Overlay 1–2, Dark Overlay 1–3, Error Overlay 1–2, On Surface Variant Overlay 1–2, Inverse On Surface Overlay 1–2, On Disabled, Disabled Container, Disabled Container 2 |

#### Status Colors

Material 3 provides no success or pending colour. The platform fixes three status colours once for every Mybizz app ([[[default:specifications-global/spec-m3-design-standards|M3 Design & Component Standards]]](C:/dev/project-library-global/specifications-global/spec-m3-design-standards.md) section 10), added as custom colours in the Colour Scheme and identical in every palette:

| Role | Meaning |
|---|---|
| `status-success` | Success, confirmed, paid |
| `status-pending` | Pending, awaiting action |
| `status-error` | Failure, overdue, no-show |

Status is never shown by colour alone: every status badge also carries its status word (section "Status Badges").

#### Hardcoded Exception Policy

No hardcoded colour exception is approved. A colour need that the Colour Scheme cannot meet is resolved by deriving the colour from a token with `color-mix()`.

---

## Palette System

### Palette Strategy

The platform supports four preset palettes for client customisation. Every palette is a complete Anvil M3 Colour Scheme, generated from a locked source colour with the Material 3 algorithm, in light and dark. Only the palette changes; layout, typography, spacing and the interaction system remain fixed.

### Preset Palettes

| Palette | Best for | Intended effect |
|---|---|---|
| Professional Blue (default) | Consultants, clinics, therapists | Trust, professionalism, stability |
| Warm Coral | Salons, spas, lifestyle, beauty | Warmth, creativity, approachability |
| Calm Teal | Fitness, wellness, physiotherapy, health services | Health, balance, growth |
| Sophisticated Purple | Executive coaching, premium consulting, high-end services | Premium, refined, exclusive |

Source colours, the generation method and every colour value: `palette-tokens.md`.

### Palette Selector Requirements

The palette selector (Settings → Brand Palette, and onboarding) shows for each palette:

- Primary colour swatch.
- Sample filled button.
- Sample outlined button.
- Sample card treatment.
- Sample heading and link treatment.
- A clear label stating the target business fit.

Choosing a palette previews it live across the screen before it is saved.

### Palette Governance

- Palette switching is token-driven: it sets the `--anvil-m3-*` properties and nothing else.
- Switching a palette never requires page-specific redesign or branching CSS.
- Every palette swap sets the full Colour Scheme — accent, surface, neutral, overlay and disabled colours.
- The chosen palette applies to every screen of the client's instance: public website, admin area and customer portal.
- Dark mode is applied by the same mechanism, from the palette's dark block, whenever the device prefers dark.

---

## Typography

### Font Family

Primary family: Roboto  
Fallbacks: Noto Sans, Arial, sans-serif

### Type Scale

The M3 type scale ([[[default:specifications-global/spec-m3-design-standards|M3 Design & Component Standards]]](C:/dev/project-library-global/specifications-global/spec-m3-design-standards.md) section 11) with this project's heading weight:

| Scale | Size | Line Height | Weight | Use Case |
|---|---:|---:|---:|---|
| Display | 57px | 64px | 400 | Rare hero headlines, error codes |
| Headline Large | 32px | 40px | 600 | Page titles |
| Headline Small | 22px | 28px | 600 | Section headers |
| Title | 18px | 24px | 500 | Card titles |
| Body Large | 16px | 24px | 400 | Larger body copy, public pages |
| Body | 14px | 20px | 400 | Standard body text |
| Button | 14px | 14px | 500 | Button labels |
| Label | 12px | 16px | 500 | Form labels |
| Caption | 12px | 16px | 400 | Helper text and captions |

### Typography Rules

- Use Roboto consistently across public and management surfaces.
- Preserve hierarchy through scale and weight, not decorative typography.
- Page titles should use Headline Large.
- Section headings should use Headline Small.
- Card titles and emphasized local headings should use Title.
- Body text should remain compact, readable, and operationally efficient.
- Labels and captions should never become the dominant visual element.

---

## Spacing, Radius, Elevation, and Density

### Spacing Scale

| Token | Value | Use Case |
|---|---:|---|
| `--space-1` | 4px | Tiny spacing, tight gaps |
| `--space-2` | 8px | Small spacing, padding |
| `--space-3` | 12px | Medium spacing |
| `--space-4` | 16px | Large spacing, card padding |
| `--space-6` | 24px | Section gaps |
| `--space-8` | 32px | Large layout spacing |

Rules:

- The 4px rhythm is mandatory.
- Padding, gaps, and section spacing should resolve through this scale.
- Public pages may feel looser than management screens, but must still use the same underlying rhythm.

### Border Radius Rules

| Element | Radius | Notes |
|---|---:|---|
| Buttons | 100px | Pill shape |
| Cards | 8px | Standard card shape (project deviation from the M3 default of 12px) |
| Dialogs | 28px | Modal/dialog treatment |
| Inputs (Outlined) | 4px | Text boxes and dropdowns |
| Inputs (Filled) | 0px | Bottom-border-only pattern |
| Chips/Badges | 8px | Tags and chips |
| Navigation Items | 100px | Pill treatment |
| Data Grid | 24px | Header and container |

### Elevation Rules

| Element | Elevation | Notes |
|---|---:|---|
| Surface | 0dp | Flat base |
| Card (Elevated) | soft dual-layer shadow | Emphasized content (see "Project Deviations from M3 Defaults") |
| Card (Tonal) | 0dp | Tonal separation only |
| Dialog | 24dp | Modal emphasis |
| Navigation Rail | 0dp | Border-only structure |
| App Bar | 0dp | Border-only structure |

### Density Guidance

| Density | Row Height | Padding | Gap | Use Case |
|---|---:|---:|---|---|
| Compact | 40px | 8px | 4px to 8px | Data-heavy screens |
| Default | 48px | 12px | 8px to 16px | Standard forms and lists |
| Comfortable | 56px | 16px | 12px to 24px | Read-only and public screens |

---

## Input and State Rules

### Input States

| State | Border Color | Background | Text Color | Notes |
|---|---|---|---|---|
| Default | Outline | Surface Variant | On Surface | Normal state |
| Focused | Primary | Surface Variant | On Surface | Active/focused |
| Error | Error | Surface Variant | Error | Validation failure |
| Disabled | On Disabled | Dark Overlay 1% | On Disabled | Reduced emphasis |
| Read-only | Outline | Surface | On Surface Variant | Non-editable display |

### State Rules

- Focus state must be obvious and consistent.
- Error state must be visible without becoming visually noisy.
- Disabled state must clearly communicate non-interactivity.
- Read-only state must not be confused with disabled state.
- Inputs across all templates must follow the same state logic.

---

## Component and Role Map

### Buttons

| Role Name | Appearance | Use Case | Min Height |
|---|---|---|---:|
| `primary-button` | Filled | Primary actions | 40px |
| `secondary-button` | Outlined | Secondary actions | 40px |
| `tertiary-button` | Text | Low-emphasis actions | 40px |
| `icon-button` | Icon only | Utility actions | 36px |
| `fab-button` | Filled circular | Create-new action | 56px |

### Cards

| Role Name | Card Type | Use Case |
|---|---|---|
| `surface-card` | Outlined | Standard content |
| `elevated-card` | Elevated | Emphasized content |
| `tonal-card` | Filled/tonal | Grouped content |
| `data-card` | Outlined | Data rows or lists |
| `metric-card` | Elevated | KPI and dashboard metrics |

### Containers

| Role Name | Use Case |
|---|---|
| `page-container` | Main content wrapper |
| `section-container` | Section grouping |
| `form-container` | Form grouping |
| `card-container` | Card collections |
| `grid-container` | Responsive grids |

### Headings

| Role Name | Size | Use Case |
|---|---|---|
| `page-title` | Headline Large | Page headings |
| `section-title` | Headline Small | Section headings |
| `card-title` | Title | Card headings |
| `label-title` | Body Large | Form labels |

### Navigation

| Role Name | Use Case |
|---|---|
| `nav-item` | Standard navigation item |
| `nav-item-selected` | Active navigation item |
| `app-bar-link` | App bar navigation |
| `breadcrumb-item` | Breadcrumb navigation |

### Forms

| Role Name | Input Type | Use Case |
|---|---|---|
| `outlined-input` | TextBox, TextArea | Standard text input |
| `outlined-select` | DropdownMenu | Select input |
| `outlined-date` | DatePicker | Date selection |
| `input-error` | All inputs | Error state |
| `input-disabled` | All inputs | Disabled state |
| `input-readonly` | All inputs | Read-only state |

### Status Badges

A status badge is an outlined pill: a small dot in the status colour, followed by the status word in On Surface. The word carries the meaning; the colour supports it. This keeps every badge readable in every palette and in dark mode.

| Status | Dot colour |
|---|---|
| Confirmed, paid, delivered, approved | `status-success` |
| Pending, awaiting approval, in progress, sent | `status-pending` |
| Overdue, failed, no-show | `status-error` |
| Completed | Primary |
| Draft, cancelled, inactive | On Surface Variant (cancelled items are also shown at reduced opacity in lists and the calendar) |

### Component Consistency Rule

All templates must reuse the same underlying components and role rules.
Template variation is allowed through arrangement, visibility, and composition, but not through design-system divergence.

---

## Project Deviations from M3 Defaults

Where this project's values differ from the Anvil M3 theme defaults, the deviation is stated here so it is never silently inherited. These deviations give Mybizz CS its refined "MUI-inspired" look, taken from the MUI dashboard reference.

| Element | M3 default | Mybizz CS value |
|---|---|---|
| Card corner radius | 12px | 8px |
| Headline weight (Headline Large, Headline Small) | 400 | 600 |
| Card shadow (elevated) | M3 elevation level 1 | Soft dual layer: `0 4px 16px 0 color-mix(in srgb, var(--anvil-m3-on-surface) 7%, transparent), 0 8px 16px -5px color-mix(in srgb, var(--anvil-m3-on-surface) 7%, transparent)` |
| Dividers | Outline Variant | Outline Variant at 40% opacity (hairline) |
| Surface distinction | Single surface | Two levels: Background for the page, Surface Container Low for panels and navigation |
| App bar | Surface Container | Surface, hairline bottom border, no gradient |
| Navigation rail / drawer | Surface Container | Surface Container Low, hairline right border |
| State layer — hover | 8% | 8% (unchanged) |
| State layer — pressed / focused | 10–12% | 12% |

Unchanged from M3: button radius 100px (pill), dialog radius 28px, outlined input radius 4px, navigation item radius 100px, Roboto, the 4px spacing grid, and the M3 colour role structure. All deviations are implemented in `theme.css` through `--anvil-m3-*` properties and Anvil roles.

---

## Forms and Pages

This section defines the primary implementation-facing forms and pages in the Mybizz CS application. Each entry states its purpose, architectural boundary, and key design constraints.

### OnboardingForm

**Purpose:** Guided first-run experience for new Owners (F-03, flow UF-02).
**Architecture:** Onboarding is a separate form, not part of Settings. Configuration that belongs to Settings is done by linking to the Settings tab and returning to onboarding.
**Wireframe:** `wireframe-onboarding-OnboardingForm.html`.

**Flow structure (10 steps), each with its help video:**
1. Welcome and orientation.
2. App Language — choice of the languages whose translation is complete. Every later step follows it.
3. Business Profile — links to Settings → Business Profile; the AI helper offers the first website text.
4. Brand Palette — links to Settings → Brand Palette.
5. System Currency — dropdown plus confirm button. Required. Locked once the first booking or invoice exists. See [[[default:adr-global/adr-system-currency-selection-and-immutability|System Currency, Display Currency, and Immutability]]](C:/dev/project-library-global/adr-global/adr-system-currency-selection-and-immutability.md) ADR.
6. Legal Acknowledgement — two checkboxes plus confirm button. Required. Recorded with a timestamp in the instance and in the Mybizz management app. See [[[default:adr-global/adr-legal-policy-responsibility-acknowledgement-and-clause-builder-architecture|Legal Policy Responsibility Acknowledgement and Clause-Builder Architecture]]](C:/dev/project-library-global/adr-global/adr-legal-policy-responsibility-acknowledgement-and-clause-builder-architecture.md) ADR.
7. Payment Gateway — links to Settings → Payment Gateways. See [[[default:adr-global/adr-payment-gateway-configuration-is-a-settings-function-and-is-rbac-governed|Payment Gateway Configuration Is a Settings Function and Is RBAC-Governed]]](C:/dev/project-library-global/adr-global/adr-payment-gateway-configuration-is-a-settings-function-and-is-rbac-governed.md) ADR.
8. Connect the Domain — the list of website and email DNS records with a live status per record; App 5 writes them in the client's Cloudflare DNS, or the Owner copies them into another provider. A confirmation dialog appears before the MX record moves the domain's email into the Team Inbox.
9. Import Contacts — CSV upload, source tool selector (HubSpot, Mailchimp, Acuity, Other), column mapping table, preview of the first rows, Import. Skippable.
10. Completion — onboarding complete.

**Key design rules:**
- Progress indicator visible throughout.
- Back and forward navigation between steps.
- "Go to Settings" links open the Settings tab and return to onboarding when done.
- Legal acknowledgement is a required action in onboarding, not a Settings configuration.
- Onboarding state is tracked in `business_profile.onboarding_status`; an Owner who leaves returns to the next unfinished step. See [[[default:adr-global/adr-onboarding-resumability|Onboarding Resumability]]](C:/dev/project-library-global/adr-global/adr-onboarding-resumability.md) ADR.

**Step 6 — Legal Acknowledgement (two checkboxes):**
- `cb_legal_ack` (Checkbox) — "I understand I must configure my own Privacy Policy and Terms & Conditions in Settings → Legal Pages. Mybizz is not responsible for the content of those pages." Sets `business_profile.legal_ack_accepted`.
- `cb_data_governance_ack` (Checkbox) — "I understand that Mybizz retains certain records for legal and compliance purposes as described above." Sets `business_profile.data_governance_ack_accepted`.
- "Confirm Acknowledgement" button (`btn_confirm_legal`) is enabled only when both checkboxes are ticked.
- The acknowledgement is recorded once and cannot be reversed.

**Per-step Continue button logic:**

| Step | Continue Button | Logic |
|------|----------------|-------|
| 1 (Welcome) | Always enabled | Informational only |
| 2 (App Language) | Always enabled | English is preselected; saves `business_profile.app_language` |
| 3 (Business Profile) | Always enabled | Link step — can be completed later |
| 4 (Brand Palette) | Always enabled | Link step — Professional Blue applies until a palette is chosen |
| 5 (System Currency) | Disabled until confirmed | Enabled after `btn_confirm_currency` is clicked and `business_profile.system_currency` is set |
| 6 (Legal Acknowledgement) | Disabled until confirmed | Enabled after both checkboxes are ticked and `btn_confirm_legal` is clicked |
| 7 (Payment Gateway) | Always enabled | Link step — bookings that take payment stay unavailable until a gateway is connected |
| 8 (Connect the Domain) | Always enabled | The website and email stay on their temporary addresses until every record is live; the status updates every few minutes |
| 9 (Import Contacts) | Always enabled | Skippable |
| 10 (Completion) | "Go to Dashboard" | Sets `onboarding_status = "completed"`, `onboarding_completed = True`, navigates to the Dashboard |

### SettingsForm

**Purpose:** Single tabbed form for all configuration after onboarding (F-05).
**Architecture:** One form with tab-based navigation using M3 buttons. The tab list is the one in `mb-3-cs-architecture-reference.md` F-05.

**Tab structure:**

| Tab | Content | RBAC Visibility |
|---|---|---|
| Business Profile | Name, description, email, phone, address, timezone, app language, entity type, logo, social links, accreditations, home and services video addresses | Owner, Manager |
| Brand Palette | Palette selection (PaletteSettingsForm — local component) | Owner, Manager |
| System Currency | Currency display (read-only, locked after first transaction) | Owner |
| Display Currency | Optional per-service display currency | Owner, Manager |
| Legal Acknowledgement | Read-only acknowledgement history | Owner |
| Payment Gateways | Card gateway (Stripe or Paystack), optional PayPal, test/live mode (secret keys in the Vault) | Owner |
| Invoicing | Default payment terms, tax rate and tax label, tax registration number, invoice numbering, default quote validity | Owner |
| Users and Permissions | Link to UserManagementForm | Owner |
| Email | EmailSettingsForm: addresses, desks, members, signatures, auto-replies, default sender | Owner, Manager |
| Domain | DomainSettingsForm: website and email DNS record status | Owner |
| Theme | Public website header style | Owner, Manager |
| Feature Switches | `marketing_enabled`, `blog_enabled`, blog comments | Owner |
| Legal Pages | PolicyEditorForm (Clause Builder) | Owner |
| Data | History view from R2, export all data as CSV, Clear sample data | Owner |

**Key design rules:**
- Tab visibility is RBAC-governed; different roles see different tabs.
- "Return to Onboarding" button in the header, visible only while onboarding is in progress.
- Secret keys are entered only in the Vault, which requires TOTP every time ([[[default:adr-global/adr-payment-security-boundary-vault|Payment Security Boundary: Secret Keys Deferred to the Vault]]](C:/dev/project-library-global/adr-global/adr-payment-security-boundary-vault.md) ADR).
- Legal Acknowledgement is read-only; the acknowledgement action belongs to onboarding.
- Payment gateway configuration is a Settings function, not an onboarding function.
- PaletteSettingsForm is a local component used only in the Brand Palette tab.
- Users and Permissions is a link tab that opens UserManagementForm.
- Dark mode has no tab: it follows the device setting.

### VaultForm

**Purpose:** Owner-only Vault UI for managing encrypted API keys and credentials.  
**Architecture:** Standalone form accessed from Settings. Requires TOTP step-up on every open.  
**Completion criteria (Stage 1.5):** TOTP required each time, no grace period, email notification on every access, secrets stored encrypted in `vault` table.

**Key design rules:**
- TOTP verification notice displayed prominently at top of form.
- Stored secrets listed with name, last updated date, and usage context.
- Each secret has View (masked reveal) and Delete actions.
- Add Secret form at bottom with name and password input.
- Secret values are encrypted at rest and never displayed after saving.
- RBAC: Owner only.

**TOTP entry screen:**
- Displayed before the vault content is visible. Full-screen overlay or prominent card at top of form.
- Shows: "TOTP Verification Required" heading, instruction text ("Enter the code from your authenticator app"), 6-digit input field, "Verify" button.
- On success: TOTP overlay dismissed, vault content (secrets list + add form) becomes visible.
- On failure: Error message "Invalid code. Please try again." with the input field cleared. After 5 failed codes the Vault locks for 15 minutes and the Owner is emailed, because a six-digit code must not be open to unlimited guessing.
- No grace period: TOTP is required on every VaultForm open, even if the user just verified 30 seconds ago.

**Vault empty state (no secrets stored):**
- Displayed when the `vault` table has zero rows for this client instance.
- Shows: Lock icon (or vault icon), heading "No secrets stored yet", body text "Add your API keys and credentials below. They will be encrypted at rest and never displayed after saving.", "Add Your First Secret" primary button.

**Vault error state (TOTP failure):**
- If the TOTP verification server call fails (not wrong code, but actual server error): show error banner "Unable to verify TOTP. Please try again or contact support." with a "Retry" button.
- If the encryption key is missing from Anvil Secrets: show critical error "Vault is not configured. Contact Mybizz support." — this is a provisioning error, not a user error.

**Vault loading state (encryption/decryption in progress):**
- When adding a new secret: "Add Secret" button shows spinner, text changes to "Encrypting...".
- When viewing a secret (masked reveal): the eye icon shows a small spinner while the server decrypts and returns the value.
- When deleting a secret: the delete icon shows a spinner, the row fades slightly until deletion is confirmed.

### EmailSettingsForm

**Purpose:** Settings → Email: the client's email addresses (F-18, flow UF-39). Build detail: `team-inbox-spec.md`.
**Architecture:** Opened from the Email tab. Addresses in `email_addresses`; the default sender in `email_config.default_from_address_id`. The client enters no email credential: Amazon SES is set up by App 5.

**Key design rules:**
- Address list: address, kind badge (personal or desk, outlined pill with the word), display name, members, status.
- Add Address: the part before the `@` with the domain shown after it, kind, display name, members (multi-select of staff), signature (rich text), auto-reply text with start and end dates.
- Default sender selector for confirmations, reminders, invoices and receipts.
- RBAC: Owner, Manager.

### DomainSettingsForm

**Purpose:** Settings → Domain: the status of the client's domain for the website and email (F-03, F-18).
**Architecture:** Reads `email_config` and the live DNS check from App 5.

**Key design rules:**
- One row per record (website A, MX, three DKIM, MAIL FROM MX, SPF, DMARC): type, name, value with a copy button, status badge with its word (Live, Waiting, Wrong value).
- Check Now button; the status refreshes every few minutes while the form is open.
- RBAC: Owner only.

### PaymentGatewayForm

**Purpose:** Configure the card gateway (Stripe or Paystack) and the optional PayPal gateway (F-15).
**Architecture:** Standalone form opened from Settings → Payment Gateways. One active card gateway per client instance ([[[default:adr-global/adr-payment-gateway-mutability|Payment Gateway Mutability]]](C:/dev/project-library-global/adr-global/adr-payment-gateway-mutability.md) ADR); PayPal can be enabled alongside it. Publishable keys are stored in `payment_config`; secret keys only in the Vault.

**Schema fields (`payment_config`):** `active_gateway`, `stripe_publishable_key`, `stripe_connected`, `stripe_connected_at`, `paystack_connected`, `paystack_connected_at`, `paypal_enabled`, `paypal_connected_at`, `test_mode`, `configured_at`.

**Key design rules:**
- Card gateway selector with Stripe and Paystack options (radio buttons or cards).
- PayPal switch with its own connection status.
- Test Mode switch for sandbox and live keys.
- Publishable key input — safe to expose in client-side code.
- No secret key input on this form. Secret keys are stored exclusively in the Vault ([[[default:adr-global/adr-payment-security-boundary-vault|Payment Security Boundary: Secret Keys Deferred to the Vault]]](C:/dev/project-library-global/adr-global/adr-payment-security-boundary-vault.md) ADR).
- A Vault notice explains where secret keys are stored.
- RBAC: Owner only.

### ThemeConfigForm

**Purpose:** Settings → Theme: the public website's header style.
**Architecture:** Configuration stored in `theme_config`.

**Schema fields (`theme_config`):** `header_style`, `updated_at`.

**Key design rules:**
- Header Style dropdown: Standard, Centered, Minimal.
- Live preview of the public website header.
- Colours come only from the four palettes (Brand Palette tab); typography is Roboto only. Free colour pickers and font choices are not offered, because they would break the palette system, contrast and the single type family.
- RBAC: Owner, Manager.

### UserManagementForm

**Purpose:** Invite team members, assign roles, and manage user access.  
**Architecture:** Standalone form. Users stored in `users` table with role, permissions, account_status fields.

**Key design rules:**
- Role filter dropdown (All, Owner, Manager, Admin, Staff, Customer).
- Invite User button (primary action).
- User table with Name, Email, Role, Status, Actions columns.
- Role badges as outlined pills with the role word; no colour coding by role.
- Status indicators (Active, Inactive, Pending).
- Row actions: Edit (all), Deactivate (active users), Reactivate (inactive users).
- RBAC: Owner only.

### PolicyEditorForm

**Purpose:** Settings → Legal Pages: editing the Privacy Policy and Terms & Conditions with the ClauseBuilder component (F-22).  
**Architecture:** Hosted in the Legal Pages tab of Settings. Uses ClauseBuilder as a shared custom component.  
**Wireframe:** `wireframe-settings-PolicyEditorForm.html` (created).

**Key design rules:**
- Contains a document type selector (Privacy Policy vs Terms & Conditions).
- Embeds the ClauseBuilder component, parameterized by document type.
- Publish/update button writes to `privacy_policy` or `t_and_c` table and publishes to the corresponding public page.
- See [[[default:adr-global/adr-legal-policy-responsibility-acknowledgement-and-clause-builder-architecture|Legal Policy Responsibility Acknowledgement and Clause-Builder Architecture]]](C:/dev/project-library-global/adr-global/adr-legal-policy-responsibility-acknowledgement-and-clause-builder-architecture.md) ADR.

### ClauseBuilder (Shared Custom Component)

**Purpose:** Standalone reusable clause editor for legal policy documents.  
**Architecture:** Shared custom component used by Privacy Policy and Terms & Conditions editing flows.  
**Wireframe:** `wireframe-custom-component-ClauseBuilder.html` (created).

**Key design rules:**
- Parameterized by document type (`privacy_policy` or `t_and_c`).
- Category dropdown selector for clause grouping.
- Seeded clauses from master template (green border, editable text, not deletable).
- Custom clauses added by Owner (blue border, editable and deletable).
- "+ Add Custom Clause" button reveals add form.
- "Edit" link on each clause reveals edit form.
- "Save" button writes changes to datatable and publishes to public page.
- Has its own data model: `category`, `title`, `clause`, `sort_order`, `is_seeded`, `is_active`.
- Used in 2+ places (PrivacyPolicyEditor, TermsConditionsEditor). Qualifies as shared.

### PrivacyPolicyPage

**Purpose:** Public-facing page that renders the Owner's privacy policy.  
**Architecture:** Reads from `privacy_policy` table. Renders clauses grouped by category.  
**Wireframe:** To be created.

**Key design rules:**
- Uses standard public page layout with FooterComponent.
- Clauses rendered in category order, then sort_order within category.
- Only active clauses (`is_active = True`) are displayed.
- Follows public-facing UI tone (comfortable density, generous whitespace).

### TermsConditionsPage

**Purpose:** Public-facing page that renders the Owner's terms and conditions.  
**Architecture:** Reads from `t_and_c` table. Renders clauses grouped by category.  
**Wireframe:** To be created.

**Key design rules:**
- Uses standard public page layout with FooterComponent.
- Clauses rendered in category order, then sort_order within category.
- Only active clauses (`is_active = True`) are displayed.
- Follows public-facing UI tone (comfortable density, generous whitespace).

---

## Template and Layout Architecture

### Architecture Principle

Layouts are shells; content panels are swappable templates.
The layout system should support variation without duplicating the design system.

### Content Panel Pattern

```text
Public Layout (NavigationDrawerLayout)
├── App Bar
├── Navigation
└── ContentPanel
    ├── HomePageTemplate
    ├── BlogListTemplate
    ├── BlogPostTemplate
    ├── AboutPageTemplate
    ├── ContactPageTemplate
    └── ServicePageTemplate
```

### Template Governance

- Templates must share the same tokens, roles, and interaction rules.
- Template differences should be structural and compositional.
- UI improvements to a shared component should propagate across all templates.
- Avoid template-specific CSS that breaks reuse.

### Homepage Template Variants

Supported homepage variants:

- Classic
- Services
- Booking
- Minimalist

These are variation patterns built on the same design system, not separate site themes.

**Implementation model:** One HomePage form with four template variants. All four are configurable content/layout presets inside a single form, driven by the `business_profile.homepage_template` field. They share the same underlying data, the same component inventory, and the same routing/permission model. Templates differ only by layout, section ordering, emphasis, and the default visibility of optional sections.

**Design coverage:** Four discrete screen artefacts with matching wireframe views (one per template variant).

#### Shared Section Inventory

All four templates draw from the same governed section inventory:

1. **Hero (Parallax)** — ParallaxComponent with hero heading, subheading, and primary CTA.
2. **Services showcase** — TimeLapseCarouselComponent + service card grid (M3 Cards).
3. **Intro / About summary** — Short text section with heading + body text.
4. **Video story** — VideoDisplayComponent, bound to `business_profile.home_video_url`, hidden when no URL configured.
5. **Testimonials** — 2–3 testimonial cards with quote text and author details.
6. **Trust / proof strip** — 3–4 trust data points using M3 text and card patterns.
7. **CTA / contact band** — Headline, explanatory text, primary button, optional secondary action.
8. **Footer** — Shared FooterComponent across all public pages.

#### Template Differentiation Matrix

**Classic** (default, balanced):
Section order: Hero → Intro → Services → Video → Testimonials → Trust strip → CTA band → Footer.
All major sections visible by default (video respects no-URL-hidden rule).

**Services** (services-first):
Section order: Hero (shorter) → Services showcase → Trust strip → Testimonials → Intro (shorter) → CTA band → Video (optional) → Footer.
Services section moves directly below hero. About and video support but do not lead.

**Booking** (conversion-focused):
Section order: Hero (strong booking CTA) → CTA band → Trust strip → Services showcase → Testimonials → Video (optional) → Intro (short reinforcing) → Footer.
CTA appears twice near top. Trust data moved up to support conversion.

**Minimalist** (clean, minimal):
Section order: Hero (concise) → Intro (very brief) → Trust strip (compact) → CTA band → Footer.
Hides: carousel, full services grid, full testimonials, video. Uses fewer text blocks and more whitespace.

**Video visibility rule:** Video section is controlled by `business_profile.home_video_url` and hidden automatically when no URL is configured, for all templates.

### ErrorLayout

**Purpose:** Application-level error and exception presentation (404, 500, 403, etc.).
**Architecture:** Full-viewport centred layout with NO chrome — no app bar, no sidebar, no navigation.

**Structure:**
```text
ErrorLayout (BlankLayout)
└── col_error (ColumnPanel) — max-width: 480px, centred
    ├── hdg_error_code (Heading) — Display scale (57px), colour Primary
    ├── hdg_error_title (Heading) — Headline Large (32px)
    ├── text_error_message (Text) — Body Large (16px), colour On Surface Variant
    └── btn_go_home (Button) — filled, "Go Home"
```

**Key design rules:**
- All four elements (error code, heading, message, button text) are set programmatically based on error type.
- Error code uses the Display scale (`57px/64px 400`), colour `var(--anvil-m3-primary)`.
- Heading uses Headline Large (`32px/40px 600`), colour `var(--anvil-m3-on-surface)`.
- Message uses Body Large (`16px/24px 400`), colour `var(--anvil-m3-on-surface-variant)`, max-width: 480px.
- "Go Home" button: filled primary, pill radius. Navigates to homepage (unauthenticated) or Dashboard (authenticated).
- Container is centred vertically and horizontally. No sidebar, no app bar.
- Responsive: 480px max-width container works at all breakpoints. No sidebar to collapse.

**Supported error types:**

| Error | Code | Heading | Message |
|-------|------|---------|---------|
| Not Found | 404 | Page Not Found | The page you're looking for doesn't exist or has been moved. |
| Server Error | 500 | Something Went Wrong | An unexpected error occurred. Please try again or contact support. |
| Forbidden | 403 | Access Denied | You don't have permission to access this page. |

### AdminLayout

**Purpose:** Admin panel shell — native M3 `NavigationRailLayout` with sidebar navigation.
**Architecture:** native M3 `NavigationRailLayout`; each admin page form names AdminLayout as its layout in the Designer. Uses the layout's NavigationLink components with lambda click handlers ([[[default:adr-global/adr-navigation-lambda-link-open-form|Navigation Standard: Lambda/Link/open_form]]](C:/dev/project-library-global/adr-global/adr-navigation-lambda-link-open-form.md) ADR). HTMLTemplate is banned per the [[[default:adr-global/adr-htmltemplate-use|HTMLTemplate Use]]](C:/dev/project-library-global/adr-global/adr-htmltemplate-use.md) ADR.

**Structure:**
```text
AdminLayout (NavigationRailLayout)
├── app_bar (Container) — "Mybizz CS" title, notifications icon, user menu icon
├── sidebar (ColumnPanel) — 260px fixed width, navigation groups below
└── content slot — the page form opened with open_form() fills it; the layout stays
```

**Sidebar navigation:** the groups, items and their visibility are defined once, in `mb-3-cs-architecture-reference.md` section 9. The admin area of the Mybizz management app (App 5) uses the same layout with its own menu.

**Key design rules:**
- Sidebar uses NavigationLink components with lambda click handlers that capture their loop variables. `navigate_to` is never used.
- The Inbox item shows the user's unread count.
- Sidebar width: 260px on desktop (>=998px). Collapses to hamburger drawer overlay on mobile (<998px).
- Active link highlighting via M3 primary-container background.
- Sign Out is always visible at sidebar bottom for all roles.
- Vault link is Owner-only. Settings link is Owner/Manager only.
- Navigation is `open_form('package.FormName')`: the page form opens in AdminLayout's content slot and the layout stays (`scaffold-spec.md`, Navigation Architecture).
- App bar shows notifications icon and user menu icon (both icon-only buttons).

**Responsive behavior:**
- Desktop (>=998px): Fixed sidebar (260px) + content fills remainder.
- Mobile (<998px): Sidebar collapses to hamburger drawer overlay. Content is full-width.

### Page Templates (12)

Page layouts are forms built from shared sections and components; they are not custom components.

| Template | Purpose |
|---|---|
| BlogListTiledTemplate | Tiled grid blog listing |
| BlogListListTemplate | List-with-thumbnail blog listing |
| BlogPostClassicTemplate | Classic centred blog post |
| BlogPostTwoColumnTemplate | Two-column blog post with sidebar |
| BlogPostFullWidthTemplate | Full-width immersive blog post |
| HomePageClassicTemplate | Classic homepage |
| HomePageServicesTemplate | Services-first homepage |
| HomePageBookingTemplate | Booking-focused homepage |
| HomePageMinimalistTemplate | Minimalist homepage |
| LeadCaptureTemplate | Lead capture landing page |
| EventRegistrationTemplate | Event registration landing page |
| VslTemplate | Video sales letter landing page |

---

## Blog Design System

### Blog List Views

Supported public-facing list patterns:

#### Tiled Grid View

- Multi-column grid on desktop.
- Card-based presentation.
- Featured image, category badge, title, excerpt, metadata, and read-more action.
- Intended for browse-friendly visual scanning.

#### List with Thumbnail View

- Row-based layout with image and content split.
- Better for denser reading and editorial scan patterns.
- Supports alternating row backgrounds where appropriate.

### Blog Post Templates

Supported post templates:

#### Classic Centered

- Centered single-column article layout.
- Best for long-form editorial content.

#### Two-Column with Sidebar

- Main article plus sidebar.
- Sidebar may include author bio, related posts, categories, and newsletter content.
- Best for engagement and internal linking.

#### Full-Width Immersive

- Full-width visual treatment.
- Best for image-rich or storytelling content.
- Pullquotes and generous line height may be used.

### Blog Admin Controls

Blog settings should support:

- Default blog list view.
- Per-post template selection.
- Comment moderation controls.
- SEO meta inputs.
- Related post strategy by tags.

---

## Custom Components

Every custom component, where it is used and why it is needed is listed in `custom-component-requirements-matrix.md`, the single home of the component list. Every custom component uses the tokens, typography, spacing and roles defined in this document.

---

## Motion and Interaction Guidance

Motion remains subtle and functional.
It should support clarity and polish, not spectacle.

Rules:

- Avoid decorative animation with no product purpose.
- Use motion for transitions, emphasis, reveal, or orientation only where it improves comprehension.
- Public-facing templates may use subtle micro-interactions.
- Management UI should remain restrained and operational.
- Animated custom components must still respect M3 clarity and professional tone.

Examples of acceptable motion:

- Subtle hover and press feedback on buttons.
- Gentle carousel transitions.
- Minimal parallax effects where they do not impair readability.
- Progressive reveal for public-facing sections when appropriate.

Examples to avoid:

- Bouncy or playful transitions.
- Large animated gradients.
- Heavy page-load effects.
- Motion that interferes with speed or focus.

---

## Implementation Notes

### Styling Implementation

All styling should use:

- Anvil M3 theme tokens.
- Built-in M3 component properties.
- Anvil roles for semantic styling.
- CSS custom properties defined in `theme.css` where needed.

### JavaScript Usage

JavaScript stays minimal and is called through `anvil.js` (never `eval`). Approved uses:

- Palette application and the device light/dark watch (`palette-tokens.md`).
- Parallax scroll effects.
- Microphone capture and browser speech synthesis for WhatsUp voice (F-27).
- Clipboard copy and the device share menu (`navigator.share`) for social media sharing (F-21, `sm-syndication-spec.md`).
- Character counters for SEO fields.
- Back-to-top.

### Performance Guidance

- Use lazy loading for comments and other deferred content.
- Optimize image sizes and dimensions.
- Keep template switching lightweight.
- Avoid unnecessary runtime complexity in presentation layers.

### Text and Language

Every piece of screen text is set in code from a phrase key in `shared/translations/` (F-25); no visible text is typed into the Designer. Layouts allow for text up to 40% longer than the English, because French, Portuguese, Spanish, German and Italian run longer: buttons and labels wrap or grow, never cut text off.

### Storage Guidance

The palette key is read from `business_profile.palette` at startup. It may be cached in the browser's session storage to avoid a flash of the default palette; the stored value is always replaced by the server value once loaded.

---

## Design-Related Schema Expectations

These fields support the design. Their home is `authoritative-schema.md`, which must hold every one of them.

### business_profile

`palette`, `palette_updated_at`, `app_language`, `blog_list_view`, `enable_comments`, `homepage_template`, `business_logo`, `accreditations`, `social_facebook`, `social_instagram`, `social_linkedin`, `social_x`, `google_maps_embed_url`, `contact_email`, `phone`, `home_video_url`, `home_video_type`, `services_video_url`, `services_video_type`, `tax_rate`, `tax_label`, `tax_registration_number`, `default_payment_terms_days`.

### blog_posts

`template`, `meta_title`, `meta_description`, `keywords`, `pullquote`, `video_url`, `video_type`, `scheduled_at`, `share_captions`.

### blog_comments

`post_id`, `commenter_name`, `commenter_email`, `comment_text`, `status`, `created_at`, `updated_at`.

### privacy_policy and t_and_c

`category`, `title`, `clause`, `sort_order`, `is_seeded`, `is_active`, `created_at`, `updated_at`.

### theme_config

`header_style`, `updated_at`.

---

## Mybizz Management App

The Mybizz management app (App 5, F-34) is built before launch and uses this design system unchanged: the same theme, tokens, roles, typography and AdminLayout pattern, in the Professional Blue palette.

---

## Invoice Design

### Tax Handling

Tax is set per client in Settings → Invoicing:

- `business_profile.tax_rate` — a percentage, default **0** (no tax).
- `business_profile.tax_label` — the name shown on invoices, for example VAT, GST or Sales Tax.
- `business_profile.tax_registration_number` — shown on invoices when set.

Calculation:
- `invoice_items.amount` = `quantity` × `unit_price` (per line item)
- Subtotal = sum of all `invoice_items.amount`
- Tax = subtotal × the tax rate at the moment the invoice is issued, stored on the invoice as `tax_rate` and `tax_amount`
- `invoice.total_amount` = subtotal + tax

When the tax rate is 0, the invoice shows no tax line. A client registered for VAT in South Africa sets 15% and label VAT.

### Invoice Status Lifecycle

The `invoice.status` field supports five values: `draft`, `sent`, `paid`, `overdue`, `cancelled`.

Every invoice carries payment terms (`payment_terms_days`, from the Settings default unless changed on the invoice) and a `due_date` = issue date + payment terms.

**Status transitions:**
- `draft` → `sent` (when invoice is emailed to customer)
- `sent` → `paid` (when payment is recorded)
- `sent` → `overdue` (when the due date passes without payment; set by a nightly task, which also emails the customer a reminder)
- `overdue` → `paid` (when payment is recorded)
- `draft` / `sent` / `overdue` → `cancelled` (when invoice is voided)
- `cancelled` and `paid` are terminal states

**Status-based permitted actions in InvoiceViewerForm:**

| Status | Edit | Send | Record Payment | Cancel | Download PDF |
|--------|------|------|----------------|--------|--------------|
| Draft | Yes | Yes | No | Yes | Yes |
| Sent | No | No | Yes | Yes | Yes |
| Paid | No | No | No | No | Yes (plus Refund) |
| Overdue | No | No | Yes | Yes | Yes |
| Cancelled | No | No | No | No | Yes |

**Status-based permitted actions in InvoiceListForm row actions:**

| Status | View | Edit |
|--------|------|------|
| Draft | Yes | Yes |
| Sent | Yes | No |
| Paid | Yes | No |
| Overdue | Yes | No |
| Cancelled | Yes | No |

### InvoiceViewerForm Completeness

The viewer must display all architecturally defined invoice fields:
- Invoice number and status badge
- Bill To: contact name, email, phone, address (from linked `contacts` record)
- Invoice Date, Due Date, Payment Terms, Currency
- Line Items table (description, quantity, unit price, amount)
- Totals: Subtotal, Tax (shown only when the tax rate is above 0, with the client's tax label and registration number), Total
- Notes (if present on the invoice record)
- Status-appropriate action buttons (per table above)

### InvoiceEditorForm Scope

The editor supports both create and edit workflows:
- **Create:** New invoice with customer selection, dates, payment terms, currency, line items (typed, or pulled from time entries, an order or a package), and notes
- **Edit:** Modify existing draft invoices only (status must be `draft`)
- Action buttons: "Save Draft" (sets status to `draft`) and "Send Invoice" (sets status to `sent` and triggers email delivery)
- A "Cancel" button returns to the list without saving

### InvoiceListForm Scope

The list form provides invoice discovery and status awareness:
- Search by invoice number or customer name
- Filter by status (All, Draft, Sent, Paid, Overdue, Cancelled)
- Row actions: View (all statuses), Edit (Draft only)
- Primary action: "+ Create Invoice" opens InvoiceEditorForm in create mode

---

## Booking Design

### Booking Status Lifecycle

The `bookings.status` field supports five values: `pending`, `confirmed`, `completed`, `no_show`, `cancelled`.

**Status transitions:**
- `pending` → `confirmed` (online payment succeeds, or manual confirmation)
- `pending` → `cancelled` (payment hold expires, or the booking is cancelled)
- `confirmed` → `completed` (the appointment took place; triggers the invoice or balance invoice, and the review request)
- `confirmed` → `no_show` (the customer did not attend)
- `confirmed` → `cancelled` (cancelled after confirmation)
- `completed`, `no_show` and `cancelled` are terminal

Bookings booked on pay on the day or invoice terms are `confirmed` at once. Bookings are never deleted; they are cancelled, so their history is kept.

**Status badges:** per "Status Badges" — confirmed `status-success`, pending `status-pending`, completed Primary, no-show `status-error`, cancelled On Surface Variant.

**Status-based permitted actions in BookingViewerForm:**

| Status | Edit | Reschedule | Mark Completed | Mark No-Show | Cancel | View Invoice |
|--------|------|------------|----------------|--------------|--------|-------------|
| Pending | Yes | Yes | No | No | Yes | No |
| Confirmed | No | Yes | Yes | Yes | Yes | Yes |
| Completed | No | No | No | No | No | Yes |
| No-show | No | No | No | No | No | Yes |
| Cancelled | No | No | No | No | No | No |

### Booking Calendar

The booking calendar is specified in full in `calendar-feature-spec.md`.

### 7-Step Booking Flow Per-Step UI Layout

The booking flow uses 3 server round-trips maximum ([[[default:adr-global/adr-form-architecture-and-state|Form Architecture and State Management]]](C:/dev/project-library-global/adr-global/adr-form-architecture-and-state.md) ADR). Each step has a defined UI:

**Round-trip 1: `get_booking_context(service_id)`**

| Step | UI Elements | Server Call |
|------|-------------|-------------|
| 1. Select Service | Service cards (image, name, price, duration), search/filter. Selected card highlights with primary-container border. | Part of RT1 (pre-loaded) |
| 2. Select Provider | Provider cards (avatar, name, specialty). Available providers from RT1 response. Selected card highlights. | Client-side from RT1 cache |

**Round-trip 2: `get_available_slots(provider_id, date_range)`**

| Step | UI Elements | Server Call |
|------|-------------|-------------|
| 3. Select Date/Time | Date picker (month calendar) + time slot grid. Available slots from RT2. Selected slot highlights with primary background. Unavailable slots greyed out. | RT2 |

**Client-side (no server call):**

| Step | UI Elements | Server Call |
|------|-------------|-------------|
| 4. Meeting Type | Cards: at the business, at my location (with address entry), video call, phone call — only the types the service offers. Selected option highlights. | None — from RT1 cache |
| 5. Intake Form | Dynamic form fields from `booking_metadata_schemas`. Text inputs, dropdowns, textareas as defined by schema. | None — from RT1 cache |

**Round-trip 3: `confirm_booking(booking_data)`**

| Step | UI Elements | Server Call |
|------|-------------|-------------|
| 6. Payment | Order summary card (service, provider, date, time, meeting type, address, amount). The service's payment option: pay in full, pay a deposit, pay on the day, invoice with payment terms, or use a package session. Button text follows the option ("Confirm & Pay", "Confirm Booking"). Processing spinner during RT3. | RT3 |
| 7. Confirmation | Success card with check icon, booking ID, details summary. "View Booking" and "Book Another" buttons. | None — client-side |

**Global booking flow rules:**
- globals.py `booking_flow_state` preserves selections across steps (see [[[default:docs-standard-global/docstd-globals-contract|globals.py Contract]]](C:/dev/project-library-global/docs-standard-global/docstd-globals-contract.md)).
- Back navigation preserves state — no re-fetch if data is cached in `booking_flow_state.cached_context`.
- Step transitions show a brief loading indicator (LinearProgressIndicator or spinner) during server calls.
- 30-second timeout on any server call — shows "Connection timed out. Please try again." with retry button.
- Mid-payment navigation: booking holds for 15 minutes, then expires.

### BookingListForm Columns

| Column | Content | Notes |
|--------|---------|-------|
| Booking # | `BK-YYYY-NNN` format | Auto-generated |
| Customer | Contact name | Linked to contact |
| Service | Service name | From services table |
| Date | Booking date | Formatted per client timezone |
| Time | Start time | Formatted per client timezone |
| Status | Status badge | Color-coded per status lifecycle |
| Actions | View, Edit, Cancel | Icon buttons; Edit only for Pending; bookings are cancelled, never deleted |

**Primary actions:** "+ Add Booking" (filled), "Calendar View" (outlined — opens BookingCalendarForm).
**Search/Filter:** Search TextBox + Status DropdownMenu + Date Range DropdownMenu.

---

## Edit Mode (F-06, F-08, F-21)

- For an Owner or Manager signed in, every public page, landing page and published blog post shows a floating **Edit** button (filled tonal, bottom right).
- In edit mode, every editable text, image and video element gets a dashed `outline` border and a small edit icon on hover and focus; a slim top bar shows "Editing — changes are not live until you save" with **Save** (filled) and **Cancel** (outlined).
- Selecting a text element opens it in place for typing, with the AI helper button beside it; selecting an image opens an upload dialog; selecting a video opens its address field.
- Save publishes at once and shows a confirmation snackbar; Cancel discards every change.

## Sample Data Banner

- A full-width banner at the top of every admin screen while sample data exists: info icon, "You are viewing sample data.", and a **Clear sample data** button (Owner only; other roles see the message without the button).
- Colours: `secondary-container` background, `on-secondary-container` text. The banner cannot be dismissed while sample data exists.

## Share Panel (F-21)

- A side sheet opened by **Share** on a published post: the post preview card (featured image, title, excerpt), then one card per platform — Facebook, LinkedIn, WhatsApp, Instagram — each with its editable caption, a **Regenerate** text button and the platform's share button.
- Tapping a platform shows a snackbar: "Caption copied — paste it into your post."
- Reader share buttons on the public post: icon buttons for Facebook, LinkedIn, WhatsApp and Copy link, below the post title and at the end of the post.

## Team Inbox Design (F-18)

- **TeamInboxForm** in AdminLayout, three panes on desktop: addresses and folders (left, 240px), message list (middle), message and conversation (right). On a phone, one pane at a time with a back button.
- Message list rows: sender or recipient, subject, first line, date; unread rows in title weight; star and attachment icons; a checkbox per row for bulk actions, with a bulk action bar that replaces the list header when rows are selected.
- List header: sort selector, filter chips row built from outlined buttons (Unread, Starred, Attachments, Date, Sender) and a search box.
- Compose opens as a side sheet on desktop and full screen on a phone: From selector, To, Cc, Bcc, subject, rich text body, attachments, signature, AI draft button, Send (filled) and Discard (text).
- Every received email is shown as plain text or as sanitised HTML with remote images blocked until the user selects "Show images".

## Sales Design (F-35)

- **DealBoardForm:** one column per stage (Lead, Qualified, Proposal, Negotiation, Won, Lost), each with its count and total value; a deal card shows title, contact or company, value, owner and expected close date; cards move by drag or by a stage menu. A list view with the same filters sits behind a view toggle.
- **QuoteEditorForm:** as InvoiceEditorForm, with the validity date in place of payment terms; actions Save Draft and Send Quote.
- **QuoteAcceptPage** (public, `/quote/{token}`): the quote laid out like the invoice PDF, with **Accept quote** (filled) and **Decline** (outlined); after acceptance, a confirmation with the invoice's payment link.
- Quote status badges: draft On Surface Variant, sent `status-pending`, accepted `status-success`, declined `status-error`, expired On Surface Variant.

## Accounts Design (F-28)

- **ExpenseEditorForm:** date, supplier, category, amount, tax included, payment method, notes, and a receipt area with Take Photo (on a phone) and Upload; the receipt shows as a thumbnail.
- **AccountsForm:** tabs Profit and Loss, Outstanding, Tax, Statements, Export; figures in tables with the period selector at the top; Export offers the period, the format (Wave, Xero, QuickBooks, CSV) and one Export button.

---

## Empty States

Every data-driven screen must define an empty state. The empty state pattern has three elements: a warm message, a primary action button, and a brief explanation of what will appear here.

**General pattern:**
```text
[Icon — relevant to the screen]
[Heading — warm, not clinical]
[Body text — brief explanation of what will appear here]
[Primary action button — the most likely next step]
```

### Empty State Specifications

| Screen | Icon | Heading | Body Text | Primary Action |
|--------|------|---------|-----------|----------------|
| Invoice list | Receipt icon | No invoices yet | Invoices will appear here when you create them for your bookings. | "+ Create Invoice" |
| Task list | Task icon | No tasks yet | Tasks are created automatically from bookings and campaigns, or you can add them manually. | "+ Add Task" |
| Campaign list | Mail icon | No campaigns yet | Email campaigns help you stay in touch with your clients. Create your first campaign to get started. | "+ Create Campaign" |
| Blog list | Article icon | No blog posts yet | Blog posts help you share expertise and attract visitors to your site. | "+ New Post" |
| Landing page list | Page icon | No landing pages yet | Landing pages capture leads from marketing campaigns. Create one to start collecting sign-ups. | "+ Create Landing Page" |
| Service list | Service icon | No services yet | Add your first service so clients can book appointments with you. | "+ Add Service" |
| Segment list | People icon | No segments yet | Segments group your contacts by behaviour or attributes. They are created automatically or manually. | (no action — explain that segments are auto-created) |
| Activity feed | Activity icon | No recent activity | Activity from bookings, emails, and contacts will appear here as your business operates. | (no action — explain that activity appears automatically) |
| Vault | Lock icon | No secrets stored yet | Add your API keys and credentials below. They will be encrypted at rest and never displayed after saving. | "Add Your First Secret" |
| Analytics dashboard | Chart icon | No data yet | Analytics will appear here once you have bookings, payments, and contacts in the system. | (no action — explain that data appears as the business operates) |
| Order list | Package icon | No orders yet | Orders for delivered work appear here when customers order a fixed-price service. | (no action — explain how orders arrive) |
| Time entries | Timer icon | No time logged yet | Log time worked for a customer, then turn billable hours into an invoice. | "Start Timer" |
| Reviews | Star icon | No reviews yet | Customers are invited to review after each completed appointment. Approved reviews can appear on your homepage. | (no action — explain how reviews arrive) |
| Packages | Bundle icon | No packages yet | Packages let customers buy several sessions up front. | "+ Create Package" |
| Inbox | Mail icon | No emails yet | Emails to your addresses arrive here. | "New Email" |
| Deals | Pipeline icon | No deals yet | Deals follow each sale from lead to won. | "+ New Deal" |
| Quotes | Document icon | No quotes yet | Quotes are accepted online and become invoices. | "+ New Quote" |
| Companies | Building icon | No companies yet | Companies group the contacts of a business customer. | "+ Add Company" |
| Expenses | Receipt icon | No expenses yet | Record what the business spends, with a photo of each receipt. | "+ New Expense" |

---

## Related Specifications

| Specification | Relevance |
|---|---|
| [[[default:specifications-global/spec-component-properties\|Component Properties Specification]]](C:/dev/project-library-global/specifications-global/spec-component-properties.md) | Component property standards, Designer vs Code classification |
| [[[default:specifications-global/spec-m3-design-standards\|M3 Design & Component Standards]]](C:/dev/project-library-global/specifications-global/spec-m3-design-standards.md) | M3 design standards, typography, spacing, accessibility |
| [[[default:specifications-global/spec-m3_component_mapping\|m3_component_mapping.md]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md) | M3 to Anvil component mapping, support levels |
| [[[default:specifications-global/spec-material-3-theme\|Material 3 Theme Specification]]](C:/dev/project-library-global/specifications-global/spec-material-3-theme.md) | M3 theme implementation, token structure, CSS overrides |
| [[[default:specifications-global/spec-screen-and-wireframe-production-standards\|Screen & Wireframe Production Standards]]](C:/dev/project-library-global/specifications-global/spec-screen-and-wireframe-production-standards.md) | Screen and wireframe production rules |
| [[[default:specifications-global/spec-screen-production-standard\|Screen Production Standard]]](C:/dev/project-library-global/specifications-global/spec-screen-production-standard.md) | Screen production process, M3 token block |
| [[[default:specifications-global/spec-ui-standards\|UI Standards]]](C:/dev/project-library-global/specifications-global/spec-ui-standards.md) | UI standards, component naming, form types |
