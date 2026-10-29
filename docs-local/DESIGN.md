---
title: "DESIGN"
doc-id: "mb-190df6bf"
updated: "2026-10-29"
---
# Mybizz CS — DESIGN

**Status:** Reconciled canonical design system  
**Date:** 2026-06-02  
**Project:** Mybizz CS  
**Platform:** Anvil.works with Material Design 3 (M3)  
**Purpose:** Authoritative design system input for GStack workflows, especially `/design-shotgun` and `/design-html`.

---

## Authority and Source Documents

This file is the canonical design system document for the Mybizz CS project.
It exists to provide the single design reference that GStack expects as `DESIGN.md` in the workflow sequence.

This document is consolidated from the following approved design consultation outputs:

- [[[mb-3-cs-project-context:docs-local/design-direction|Design Direction (Canonical)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/design-direction.md) — primary source for visual direction, tokens, typography, spacing, elevation, Anvil role map, and component styling rules.
- `design-consultation-plan-workflows.md` — source for workflow-specific component definitions, template inventory, implementation notes, schema additions, and design documentation expectations.
- `design-consultation-plan-descriprive.md` — source for workstream framing, palette strategy, blog template strategy, layout architecture, and client-facing customization model.

Where these source documents overlap, this file takes precedence as the authoritative consolidated reference.
If deeper implementation detail is needed, the source documents remain supporting references, but this file is the first design authority to consult.

### Reconciliation Notes (2026-06-02)

This file has been reconciled against:
- `sanity-audit-final-report.md` (2026-06-01)
- `plan-tune-verification-report.md` (2026-06-01)
- `wireframe-reconciliation-audit.md` (2026-06-01)
- [[[mb-3-cs-project-context:docs-local/platform-architecture|Platform Architecture]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/platform-architecture.md) (current)
- `nomenclature.md` (current)

Key corrections applied:
- OnboardingPaletteForm removed from active inventory (obsolete).
- ClauseBuilder added as shared custom component.
- PaletteSettingsForm downgraded to local/form-specific.
- OnboardingForm, SettingsForm, PolicyEditorForm, PrivacyPolicyPage, TermsConditionsPage now explicitly covered.
- SettingsForm updated to 11-tab structure with RBAC governance.
- Onboarding/Settings architectural boundary reflected.
- Legal acknowledgement placed in onboarding scope.
- Payment gateway configuration placed in Settings scope.
- Mybizzmanagement treated as out-of-scope context only.
- privacy_policy and t_and_c tables added to schema expectations.

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
- Dark mode as the primary design target. (Dark mode is included in V1 per [[[default:adr-global/adr-dark-mode|Dark Mode — V1 Inclusion]]](C:/dev/project-library-global/adr-global/adr-dark-mode.md) ADR.)
- Heavy animation, glassmorphism, or neumorphism.
- Complex charts or visual systems in V1 beyond what is operationally useful.
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

The project follows a Material 3 token model.
All colors should resolve through Anvil theme tokens or documented CSS custom properties.
Hardcoded colors should be avoided unless explicitly approved.

#### Primary Palette

- Primary: `%color:Primary%`
- On Primary: `%color:On Primary%`
- Primary Container: `%color:Primary Container%`
- On Primary Container: `%color:On Primary Container%`

#### Secondary Palette

- Secondary: `%color:Secondary%`
- On Secondary: `%color:On Secondary%`
- Secondary Container: `%color:Secondary Container%`
- On Secondary Container: `%color:On Secondary Container%`

#### Neutral Palette

- Surface: `%color:Surface%`
- On Surface: `%color:On Surface%`
- Surface Variant: `%color:Surface Variant%`
- On Surface Variant: `%color:On Surface Variant%`
- Outline: `%color:Outline%`
- Background: `%color:Background%`
- On Background: `%color:On Background%`

#### Status Colors

- Error: `%color:Error%`
- On Error: `%color:On Error%`
- Disabled Container: `%color:Disabled Container%`
- On Disabled: `%color:On Disabled%`
- Light Overlay 1%: `%color:Light Overlay 1%`
- Light Overlay 2%: `%color:Light Overlay 2%`
- Dark Overlay 1%: `%color:Dark Overlay 1%`
- Dark Overlay 2%: `%color:Dark Overlay 2%`

#### Hardcoded Exception Policy

No hardcoded exception is currently approved as part of the baseline system.
If a hardcoded value becomes necessary, it must be documented explicitly in this file or in a supporting design decision note.

---

## Palette System

### Palette Strategy

The platform supports a unified palette system for client customization.
All palettes derive from the same M3 token structure.
Only the palette changes; the layout, typography, spacing, and interaction system remain fixed.

The palette system applies to:

- Primary button colors.
- Navigation active states.
- Accent surfaces and highlights.
- Link colors.
- Card emphasis and selected states where appropriate.

### Preset Palettes

#### Professional Blue

- Best for consultants, clinics, therapists.
- Intended effect: trust, professionalism, stability.

#### Warm Coral

- Best for salons, spas, lifestyle, beauty.
- Intended effect: warmth, creativity, approachability.

#### Calm Teal

- Best for fitness, wellness, physiotherapy, health services.
- Intended effect: health, balance, growth.

#### Sophisticated Purple

- Best for executive coaching, premium consulting, high-end services.
- Intended effect: premium, refined, exclusive.

### Palette Selector Requirements

The palette selector component should show:

- Primary color swatch.
- Sample filled button.
- Sample outlined button.
- Sample card treatment.
- Sample heading and link treatment.
- A clear label indicating target business fit.

### Palette Governance

Palette switching is a product customization concern and should remain token-driven.
Switching a palette must not require page-specific redesign or branching CSS logic.

### Source reference 
Palette System Reference Documents
The following documents are authoritative supporting references for the Palette System section of this file.
They were generated from source colors using the M3 HCT tonal palette algorithm and are treated as design constants.
Do not modify these files without regenerating tokens from source colors.

Document	Purpose	Status
Document	Purpose	Status
DESIGN_palette_section.md	Full M3 CSS token sets for all 4 palettes. Replaces the Palette System section in this file. Contains 32 tokens per palette in CSS custom property format.	Authoritative
palette_selector_tokens.md	Python dict blocks for the palette selector component. Contains the same 32 tokens per palette in Python dict format plus the apply_palette usage pattern.	Authoritative
Source Colors (Locked)
Palette	Source Hex	Algorithm
Professional Blue	#2563EB	M3 HCT TonalSpot
Warm Coral	#FF6F61	M3 HCT TonalSpot
Calm Teal	#008080	M3 HCT TonalSpot
Sophisticated Purple	#6B46C1	M3 HCT TonalSpot
If a source color changes, both reference documents must be regenerated together and this table updated.

---

## Typography

### Font Family

Primary family: Roboto  
Fallbacks: Noto Sans, Arial, sans-serif

### Type Scale

| Scale | Size | Line Height | Weight | Use Case |
|---|---:|---:|---:|---|
| Display | 57px | 64px | 400 | Rare hero headlines |
| Headline Large | 32px | 40px | 400 | Page titles |
| Headline Small | 22px | 28px | 500 | Section headers |
| Title | 18px | 24px | 500 | Card titles |
| Body Large | 14px | 20px | 400 | Larger body copy |
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
| Cards | 12px | Standard card shape |
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
| Card (Elevated) | 2dp | Emphasized content |
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

### Component Consistency Rule

All templates must reuse the same underlying components and role rules.
Template variation is allowed through arrangement, visibility, and composition, but not through design-system divergence.

---

## Forms and Pages

This section defines the primary implementation-facing forms and pages in the Mybizz CS application. Each entry states its purpose, architectural boundary, and key design constraints.

### OnboardingForm

**Purpose:** Guided first-run experience for new Owners.  
**Architecture:** Onboarding is a separate form, not part of Settings.  
**Wireframe:** `wireframe-onboarding-OnboardingForm.html` (created).

**Flow structure (6 steps):**
1. Welcome and orientation.
2. Business Profile — links to Settings Business Profile tab for detail editing.
3. Palette Selection — links to Settings Palette tab for detail editing.
4. System Currency — dropdown plus confirm button. Required. Immutable after first transaction. See [[[default:adr-global/adr-system-currency-selection-and-immutability|System Currency, Display Currency, and Immutability]]](C:/dev/project-library-global/adr-global/adr-system-currency-selection-and-immutability.md) ADR.
5. Legal Acknowledgement — two checkboxes plus confirm button. Required. Logged in Mybizz_management. See [[[default:adr-global/adr-legal-policy-responsibility-acknowledgement-and-clause-builder-architecture|Legal Policy Responsibility Acknowledgement and Clause-Builder Architecture]]](C:/dev/project-library-global/adr-global/adr-legal-policy-responsibility-acknowledgement-and-clause-builder-architecture.md) ADR.
6. Completion — onboarding complete.

**Key design rules:**
- Progress indicator must be visible throughout.
- Bidirectional navigation (back and forward) between steps.
- "Go to Settings" links open the corresponding Settings tab and return to onboarding when done.
- Legal acknowledgement is a required action in onboarding, not a mutable Settings configuration.
- Payment gateway configuration is not part of onboarding. Onboarding links to Settings for gateway setup. See [[[default:adr-global/adr-payment-gateway-configuration-is-a-settings-function-and-is-rbac-governed|Payment Gateway Configuration Is a Settings Function and Is RBAC-Governed]]](C:/dev/project-library-global/adr-global/adr-payment-gateway-configuration-is-a-settings-function-and-is-rbac-governed.md) ADR.
- Onboarding state is tracked in `business_profile.onboarding_status`. See [[[default:adr-global/adr-onboarding-resumability|Onboarding Resumability]]](C:/dev/project-library-global/adr-global/adr-onboarding-resumability.md) ADR, [[[default:adr-global/adr-onboarding-finality|Onboarding Finality]]](C:/dev/project-library-global/adr-global/adr-onboarding-finality.md) ADR (deleted).

**Step 5 — Legal Acknowledgement (two checkboxes):**
- `cb_legal_ack` (CheckBox) — "I understand I must configure my own Privacy Policy and Terms & Conditions in Settings → Policy Editor. Mybizz is not responsible for the content of those pages." Sets `business_profile.legal_ack_accepted`.
- `cb_data_governance_ack` (CheckBox) — "I understand that Mybizz retains certain records for legal and compliance purposes as described above." Sets `business_profile.data_governance_ack_accepted`.
- "Confirm Acknowledgement" button (`btn_confirm_legal`) is enabled only when both checkboxes are ticked.
- This acknowledgement is recorded once and cannot be reversed.

**Per-step Continue button enable/disable logic:**

| Step | Continue Button | Logic |
|------|----------------|-------|
| 1 (Welcome) | Always enabled | Informational only |
| 2 (Business Profile) | Always enabled | Optional link step — can be skipped |
| 3 (Palette Selection) | Always enabled | Optional link step — can be skipped |
| 4 (System Currency) | Disabled until confirmed | Enabled only after `btn_confirm_currency` is clicked and `business_profile.system_currency` is set |
| 5 (Legal Acknowledgement) | Disabled until confirmed | Enabled only after both `cb_legal_ack` and `cb_data_governance_ack` are ticked and `btn_confirm_legal` is clicked |
| 6 (Completion) | "Go to Dashboard" | Sets `onboarding_status = "completed"`, `onboarding_completed = True`, navigates to Dashboard |

### SettingsForm

**Purpose:** Single tabbed form for all post-onboarding configuration.  
**Architecture:** Settings is a single form with tab-based navigation using M3 buttons.  
**Wireframe:** `wireframe-settings-SettingsForm.html` (recreated with 11 tabs).

**Tab structure (11 tabs):**

| Tab | Content | RBAC Visibility |
|---|---|---|
| Business Profile | Name, email, phone, address, timezone, entity type | Owner, Manager |
| Palette | Brand colour selection (PaletteSettingsForm — local component) | Owner, Manager |
| System Currency | Currency display (locked after first transaction) | Owner only |
| Legal Acknowledgement | Read-only view of legal acknowledgements | Owner only |
| Display Currency | Optional per-service currency override | Owner, Manager |
| Payment Gateway | Stripe/Paystack configuration (API keys in Vault) | Owner only |
| Services | Service catalogue management | Owner, Manager, Admin |
| Users & Permissions | User management and RBAC | Owner only |
| Email Configuration | Brevo SMTP and API settings | Owner only |
| Theme | M3 theme customisation | Owner, Manager |
| Feature Flags | Feature activation toggles | Owner only |

**Key design rules:**
- Tab visibility is RBAC-governed. Different roles see different tabs.
- "Return to Onboarding" button in header — visible only when onboarding is in progress.
- Vault access (within Payment Gateway tab) requires TOTP step-up per [[[default:adr-global/adr-payment-security-boundary-vault|Payment Security Boundary: Secret Keys Deferred to the Vault]]](C:/dev/project-library-global/adr-global/adr-payment-security-boundary-vault.md) ADR.
- Legal Acknowledgement tab is read-only. It displays acknowledgement history. The acknowledgement action itself belongs to onboarding.
- Payment gateway configuration is a Settings concern, not an onboarding concern. See [[[default:adr-global/adr-payment-gateway-configuration-is-a-settings-function-and-is-rbac-governed|Payment Gateway Configuration Is a Settings Function and Is RBAC-Governed]]](C:/dev/project-library-global/adr-global/adr-payment-gateway-configuration-is-a-settings-function-and-is-rbac-governed.md) ADR.
- PaletteSettingsForm is a local/form-specific component used only within the Palette tab.
- Business Profile is a tab within SettingsForm, not a standalone form.
- Services and Users & Permissions tabs are link-only tabs that navigate to their respective forms.

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
- On failure: Error message "Invalid code. Please try again." with input field cleared. No lockout — unlimited retries.
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

### EmailSetupForm

**Purpose:** Configure Brevo SMTP sender settings for outgoing emails.  
**Architecture:** Standalone form. Per-client configuration stored in `email_config` table. SMTP credentials stored in Vault.

**Schema fields (`email_config`):** `from_email`, `from_name`, `configured`, `configured_at`, `sender_domain_verified`.

**Key design rules:**
- From Email and From Name are the only per-client editable fields.
- SMTP host, port, and encryption are platform constants (smtp-relay.brevo.com:587, TLS) — not user-configurable. See [[[default:adr-global/adr-brevo-email-and-crm|Brevo Replaces All Zoho Products]]](C:/dev/project-library-global/adr-global/adr-brevo-email-and-crm.md) ADR.
- SMTP credentials (API key) stored in Vault, not in the form or database.
- Test Connection button verifies SMTP configuration.
- Success/error result displayed inline after test.
- RBAC: Owner only.

### PaymentGatewayForm

**Purpose:** Configure active payment gateway (Stripe or Paystack).  
**Architecture:** Standalone form. One active gateway per client instance ([[[default:adr-global/adr-payment-gateway-mutability|Payment Gateway Mutability]]](C:/dev/project-library-global/adr-global/adr-payment-gateway-mutability.md) ADR). Publishable key stored in `payment_config` table. Secret key stored in Vault ([[[default:adr-global/adr-payment-security-boundary-vault|Payment Security Boundary: Secret Keys Deferred to the Vault]]](C:/dev/project-library-global/adr-global/adr-payment-security-boundary-vault.md) ADR).

**Schema fields (`payment_config`):** `active_gateway`, `stripe_publishable_key`, `stripe_connected`, `stripe_connected_at`, `paystack_connected`, `paystack_connected_at`, `test_mode`, `configured_at`.

**Key design rules:**
- Gateway selector with Stripe and Paystack options (radio buttons or cards).
- Test Mode toggle for sandbox/live key switching.
- Publishable Key input — safe to expose in client-side code.
- Secret Key input must NOT be present in the form. Secret keys are stored exclusively in The Vault per [[[default:adr-global/adr-payment-security-boundary-vault|Payment Security Boundary: Secret Keys Deferred to the Vault]]](C:/dev/project-library-global/adr-global/adr-payment-security-boundary-vault.md) ADR.
- Vault notice displayed explaining secret key storage.
- RBAC: Owner only.

### ThemeConfigForm

**Purpose:** Customize M3 theme for client-facing public pages.  
**Architecture:** Standalone form. Configuration stored in `theme_config` table.

**Schema fields (`theme_config`):** `primary_color`, `accent_color`, `font_family`, `header_style`, `updated_at`.

**Key design rules:**
- Primary Color and Accent Color with color picker + hex text input.
- Font Family dropdown (Google Fonts selection).
- Header Style dropdown (Standard, Centered, Minimal).
- Live Preview section showing theme applied to sample content.
- Reset to Default button.
- Token-driven theming — palette system integration per DESIGN.md Palette System section.
- RBAC: Owner, Manager.

### UserManagementForm

**Purpose:** Invite team members, assign roles, and manage user access.  
**Architecture:** Standalone form. Users stored in `users` table with role, permissions, account_status fields.

**Key design rules:**
- Role filter dropdown (All, Owner, Manager, Admin, Staff, Customer).
- Invite User button (primary action).
- User table with Name, Email, Role, Status, Actions columns.
- Role badges with semantic color coding per role.
- Status indicators (Active, Inactive, Pending).
- Row actions: Edit (all), Deactivate (active users), Reactivate (inactive users).
- RBAC: Owner only.

### PolicyEditorForm

**Purpose:** Settings-hosted screen for editing Privacy Policy and Terms & Conditions using the ClauseBuilder component.  
**Architecture:** Accessed from Settings. Uses ClauseBuilder as a shared custom component.  
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
└── error_container (ColumnPanel) — max-width: 480px, centred
    ├── txt_error_code (Label) — Display typography (57px), color: primary
    ├── hdg_error_title (Heading) — Headline Large (32px)
    ├── txt_error_message (Text) — Body Large (16px), color: on-surface-variant
    └── btn_go_home (Button) — filled, "Go Home"
```

**Key design rules:**
- All four elements (error code, heading, message, button text) are set programmatically based on error type.
- Error code uses Display typography (`57px/64px 400`), color: `var(--primary)`.
- Heading uses Headline Large (`32px/40px 400`), color: `var(--on-surface)`.
- Message uses Body Large (`16px/24px 400`), color: `var(--on-surface-variant)`, max-width: 480px.
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
**Architecture:** NavigationRailLayout with sidebar + content panel pattern. Uses plain Link components with lambda click handlers (not NavigationLink). HTMLTemplate is banned per the [[[default:adr-global/adr-htmltemplate-use|HTMLTemplate Use]]](C:/dev/project-library-global/adr-global/adr-htmltemplate-use.md) ADR.

**Structure:**
```text
AdminLayout (NavigationRailLayout)
├── app_bar (Container) — "Mybizz CS" title, notifications icon, user menu icon
├── sidebar (ColumnPanel) — 260px fixed width, navigation groups below
└── content_area (ColumnPanel) — forms load here via content panel pattern
```

**Sidebar navigation groups and items:**

| Group | Items | Conditional Visibility |
|-------|-------|----------------------|
| (ungrouped) | Dashboard | Always |
| Sales & Operations | Bookings, Services | Always |
| Customers & Marketing | Contacts, Campaigns, Broadcasts, Segments, Tasks | Contacts: always. Campaigns, Broadcasts, Segments, Tasks: `marketing_enabled` feature flag |
| Content & Website | Blog, Pages | Blog: `blog_enabled` feature flag. Pages: always |
| Finance & Reports | Invoices, Payments, Reports, Analytics, Time Entries | Always |
| (bottom) | Settings | Owner, Manager |
| (bottom) | Vault | Owner only |
| (bottom) | Sign Out | Always — all roles |

**Key design rules:**
- Sidebar uses plain Link components with lambda click handlers. NavigationLink and navigate_to are NOT used.
- Sidebar width: 260px on desktop (>=998px). Collapses to hamburger drawer overlay on mobile (<998px).
- Active link highlighting via M3 primary-container background.
- Sign Out is always visible at sidebar bottom for all roles.
- Vault link is Owner-only. Settings link is Owner/Manager only.
- Content area uses the content panel pattern — forms are swapped in, not navigated to via open_form().
- App bar shows notifications icon and user menu icon (both icon-only buttons).

**Responsive behavior:**
- Desktop (>=998px): Fixed sidebar (260px) + content fills remainder.
- Mobile (<998px): Sidebar collapses to hamburger drawer overlay. Content is full-width.

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

## Custom Components Inventory

### Shared Custom Components (6)

| Component | Where Used | Justification |
|---|---|---|
| VideoDisplayComponent | ServicesForm, HomePage, BlogPostForm, ServiceViewerForm | Display-only video embed. Not in native M3. Supports YouTube (V1). Programmatic visibility — hidden when no valid video URL exists. |
| FooterComponent | All public pages (6+) | Custom footer with logo, credentials, social links, policies, map, contact. Not in native M3. |
| TimeLapseCarouselComponent | HomePage, ServicesForm | Auto-rotating carousel not in native M3. |
| ParallaxComponent | Multiple public pages | Parallax scroll effect not in native M3. |
| ClauseBuilder | PrivacyPolicyEditor, TermsConditionsEditor | Reusable clause editor parameterized by document type. Used in 2+ places. Not in native M3. |
| BlogListTiledTemplate | Public blog listing | Tiled grid blog list view. Not in native M3. |
| BlogListListTemplate | Public blog listing | List-with-thumbnail blog list view. Not in native M3. |
| BlogPostClassicTemplate | Public blog post | Classic centered blog post layout. |
| BlogPostTwoColumnTemplate | Public blog post | Two-column blog post layout with sidebar. |
| BlogPostFullWidthTemplate | Public blog post | Full-width immersive blog post layout. |
| HomePageClassicTemplate | Public homepage | Classic homepage variant. |
| HomePageServicesTemplate | Public homepage | Services-focused homepage variant. |
| HomePageBookingTemplate | Public homepage | Booking-focused homepage variant. |
| HomePageMinimalistTemplate | Public homepage | Minimalist homepage variant. |
| LeadCaptureTemplate | Landing pages | Lead capture landing page. |
| EventRegistrationTemplate | Landing pages | Event registration landing page. |
| VslTemplate | Landing pages | Video sales letter landing page. |

### Local/Form-Specific Components (1)

| Component | Where Used | Reason |
|---|---|---|
| PaletteSettingsForm | SettingsForm Palette tab only | Only used in one form. Does not meet shared criteria. |

### Removed from V1 Scope

| Component | Reason |
|---|---|
| OnboardingPaletteForm | Obsolete. Onboarding palette selection is now part of OnboardingForm. |
| AnimatedButtonComponent | Standard M3 components provide sufficient hover/focus animations via role-based styling. |
| AnimatedCardComponent | Standard M3 components provide sufficient hover/focus animations via role-based styling. |
| AnimatedLinkComponent | Standard M3 components provide sufficient hover/focus animations via role-based styling. |
| AnimatedImageComponent | Standard M3 components provide sufficient hover/focus animations via role-based styling. |

All custom components must remain governed by the same token, typography, spacing, and role system defined in this document.

### VideoDisplayComponent — Architectural Definition

**Purpose:** Display-only video embed component. Renders an iframe/video for a valid video URL. When no valid URL exists, the component is programmatically hidden.

**Design intent:**
- The component is display-only. It does not contain input controls (no source selector, no URL input field).
- Input controls for video source and URL belong in the host forms (BlogEditorForm, ServiceEditorForm, SettingsForm).
- The component receives `video_url` and `video_type` as properties set programmatically by the host form.
- Visibility is programmatic: if `video_url` is empty or invalid, the component's `visible` property is set to `False` in code.
- No user-facing include/exclude switch is required.

**Host contexts and video URL governance:**

| Host Context | Video URL Source | Governed By |
|---|---|---|
| ServicesForm (list page) | `business_profile.services_video_url` | SettingsForm → Business Profile tab |
| HomePage | `business_profile.home_video_url` | SettingsForm → Business Profile tab |
| BlogPostForm (public view) | `blog_posts.video_url` | BlogEditorForm sidebar → Video Embed card |
| ServiceViewerForm (detail view) | `services.video_url` | ServiceEditorForm → video URL field |

**V1 scope:** YouTube only. Multi-source support (Vimeo, Dailymotion, custom URLs) is deferred to V2. The `video_type` field is included in the schema for future extensibility but defaults to `"youtube"` in V1.

**Programmatic visibility rule:**
- The component's `visible` property is controlled in code, not in the Anvil Designer.
- If `video_url` is `None`, empty, or fails URL validation, the component is hidden.
- If a valid URL exists, the component is shown and renders the embed.
- This applies uniformly across all host contexts.

---

## Motion and Interaction Guidance

Motion in V1 should remain subtle and functional.
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

JavaScript should remain minimal and purposeful.
Approved uses include:

- Palette application.
- Parallax scroll effects.
- Social sharing interactions.
- Character counters for SEO fields.
- Back-to-top interactions.

### Performance Guidance

- Use lazy loading for comments and other deferred content.
- Optimize image sizes and dimensions.
- Keep template switching lightweight.
- Avoid unnecessary runtime complexity in presentation layers.

### Storage Guidance

Client-side palette caching may be used where appropriate, but implementation should remain predictable and easy to reason about.

---

## Design-Related Schema Expectations

The design consultation established schema expectations tied to presentation and customization.
These should be treated as approved design-support requirements, subject to alignment with the authoritative schema and implementation plan.

### business_profile additions

Expected fields include:

- `palette`
- `palette_updated_at`
- `blog_list_view`
- `enable_comments`
- `homepage_template`
- `business_logo`
- `accreditations`
- `social_facebook`
- `social_instagram`
- `social_linkedin`
- `social_twitter`
- `google_maps_embed_url`
- `contact_email`
- `contact_phone`
- `home_video_url` — YouTube URL for homepage video display (governed via SettingsForm)
- `home_video_type` — Video source type for homepage (V1: "youtube")
- `services_video_url` — YouTube URL for services page video display (governed via SettingsForm)
- `services_video_type` — Video source type for services page (V1: "youtube")

### blog_posts additions

Expected fields include:

- `template`
- `meta_title`
- `meta_description`
- `keywords`
- `pullquote`
- `video_url` — YouTube URL for embedded video display (V1: YouTube only)
- `video_type` — Video source type (V1: "youtube"; V2: "vimeo", "dailymotion", "custom")

### blog_comments table

Expected fields include:

- `post_id`
- `commenter_name`
- `commenter_email`
- `comment_text`
- `status`
- `created_at`
- `updated_at`

### privacy_policy table

Expected fields include:

- `category`
- `title`
- `clause`
- `sort_order`
- `is_seeded`
- `is_active`
- `created_at`
- `updated_at`

### t_and_c table

Expected fields include:

- `category`
- `title`
- `clause`
- `sort_order`
- `is_seeded`
- `is_active`
- `created_at`
- `updated_at`

---

## Mybizz_management Context

Mybizz_management is **out of scope** for this project. The following is recorded as architectural context only. Mybizz_management is a separate Anvil application for platform operator tooling — client provisioning, health monitoring, billing automation, and update distribution. It is not part of master_template and is built only after the master template reaches launch.

Relevant architectural context is documented in [[[mb-3-cs-project-context:docs-local/platform-architecture|Platform Architecture]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/platform-architecture.md).

---

## Invoice Design

### Tax Handling (V1)

The `invoice` and `invoice_items` schema tables do not contain dedicated tax fields. Tax is handled as a display-time calculation:

- `invoice_items.amount` = `quantity` × `unit_price` (per line item)
- Subtotal = sum of all `invoice_items.amount` values
- Tax = subtotal × tax rate (display calculation)
- `invoice.total_amount` = subtotal + tax (the final stored amount)

For V1, the tax rate is fixed at **15%** (South African VAT). This rate is used for display calculation only and is not stored per invoice. If a configurable tax rate is needed in future, it should be added to the `business_profile` or `config` table.

### Invoice Status Lifecycle

The `invoice.status` field supports five values: `draft`, `sent`, `paid`, `overdue`, `cancelled`.

**Status transitions:**
- `draft` → `sent` (when invoice is emailed to customer)
- `sent` → `paid` (when payment is recorded)
- `sent` → `overdue` (when due date passes without payment)
- `overdue` → `paid` (when payment is recorded)
- `draft` / `sent` / `overdue` → `cancelled` (when invoice is voided)
- `cancelled` and `paid` are terminal states

**Status-based permitted actions in InvoiceViewerForm:**

| Status | Edit | Send | Record Payment | Cancel | Download PDF |
|--------|------|------|----------------|--------|--------------|
| Draft | Yes | Yes | No | Yes | Yes |
| Sent | No | No | Yes | Yes | Yes |
| Paid | No | No | No | No | Yes |
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
- Invoice Date, Due Date, Currency
- Line Items table (description, quantity, unit price, amount)
- Totals: Subtotal, Tax, Total
- Notes (if present on the invoice record)
- Status-appropriate action buttons (per table above)

### InvoiceEditorForm Scope

The editor supports both create and edit workflows:
- **Create:** New invoice with customer selection, dates, currency, line items, and notes
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

The `bookings.status` field supports three values: `pending`, `confirmed`, `cancelled`.

**Status transitions:**
- `pending` → `confirmed` (when payment succeeds or manual confirmation)
- `pending` → `cancelled` (when payment fails or customer cancels)
- `confirmed` → `cancelled` (when booking is cancelled after confirmation)
- `cancelled` and `confirmed` are terminal states for the booking lifecycle

**Status badge colors:**

| Status | Background | Text |
|--------|-----------|------|
| Confirmed | `#e6f7ee` | `#00b958` |
| Pending | `#fff0eb` | `#ff9469` |
| Cancelled | `#ffe6e6` | `#ff000c` |

**Status-based permitted actions in BookingViewerForm:**

| Status | Edit | Cancel | View Invoice | Reschedule |
|--------|------|--------|-------------|------------|
| Pending | Yes | Yes | No | Yes |
| Confirmed | No | Yes | Yes | Yes |
| Cancelled | No | No | No | No |

### Booking Calendar Architecture

The booking calendar uses 4 template subforms for rendering events across three views (Month, Week, Day). View toggle uses a SegmentedButton.

**Template subforms:**

| Subform | Purpose | Used In |
|---------|---------|---------|
| `tmpl_day_event` | Event card in day view | Day view RepeatingPanel |
| `tmpl_month_day_cell` | Day cell in month grid | Month view grid |
| `tmpl_week_event` | Event block in week view | Week view RepeatingPanel |
| `tmpl_week_timeslot` | Time row in week view | Week view time grid |

**Event color coding (left border):**

| Status | Border Color | Opacity |
|--------|-------------|---------|
| Confirmed | `#388e3c` (green) | 1.0 |
| Pending | `#f9a825` (yellow) | 1.0 |
| Cancelled | `#c62828` (red) | 0.6 |

**Day view (`tmpl_day_event`):**
- Event card with 4px left border (color-coded by status), 8px border-radius, subtle shadow.
- Shows: event title (12px, weight 500), time range (11px), description with customer name and meeting type (11px), status badge.
- Clickable — opens BookingViewerForm.

**Month view (`tmpl_month_day_cell`):**
- Day cell in month grid. Today cell has `primary-container` background.
- Other-month cells have `opacity: 0.4`.
- Event chips are 9px font with status color coding.

**Week view (`tmpl_week_event`):**
- Event block with left border color coding (same palette as day view).
- Cancelled events have `opacity: 0.5`.

**Week timeslot (`tmpl_week_timeslot`):**
- Time row: 60px time label column + 1fr content.
- Empty timeslots show a thin spacer line (`height: 1px`, `background: #E7E0EC`, `opacity: 0.5`).
- Events have hover state (`background: #EADDFF`).

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
| 4. Meeting Type | Radio buttons or cards: In-Person, Video Call, Phone Call. Selected option highlights. | None — from RT1 cache |
| 5. Intake Form | Dynamic form fields from `booking_metadata_schemas`. Text inputs, dropdowns, textareas as defined by schema. | None — from RT1 cache |

**Round-trip 3: `confirm_booking(booking_data)`**

| Step | UI Elements | Server Call |
|------|-------------|-------------|
| 6. Payment | Order summary card (service, provider, date, time, meeting type, amount). "Confirm & Pay" button. Processing spinner during RT3. | RT3 |
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
| Actions | View, Edit, Delete | Icon buttons; Edit only for Pending |

**Primary actions:** "+ Add Booking" (filled), "Calendar View" (outlined — toggles to BookingCalendarForm).
**Search/Filter:** Search TextBox + Status DropdownMenu + Date Range DropdownMenu.

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

---

## GStack Workflow Usage

This file exists specifically to satisfy the workflow requirement that `/design-consultation` produces `DESIGN.md`.
For GStack purposes, this file should be treated as the design-system checkpoint output.

### Upstream Position

This file depends on:

- completed `/office-hours`
- completed `/plan-ceo-review`
- completed `/design-consultation`

### Downstream Use

This file is the design authority for:

- wireframe updates where styling implications matter
- `/design-shotgun`
- `/design-html`
- downstream design review and UI implementation decisions

### Operational Rule

Do not rerun `/design-consultation` merely because the original run did not create a file literally named `DESIGN.md`.
If the consultation outputs exist and are approved, consolidate them here and continue.
Only rerun `/design-consultation` if scope, direction, or design requirements materially changed.

---

## Maintenance Rule

This document should be updated when any of the following change:

- visual direction
- design tokens
- palette system
- typography scale
- spacing or radius rules
- template architecture
- component roles
- approved custom components
- design-related schema requirements
- form or page boundaries
- onboarding or settings architecture

When updated, this file remains the canonical design reference and supporting documents remain secondary references.

---

## Source Reference Mapping

This section records how the source files map into this consolidated design authority.

| Source file | Primary contribution to this document |
|---|---|
| [[[mb-3-cs-project-context:docs-local/design-direction\|Design Direction (Canonical)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/design-direction.md) | Core visual direction, tokens, spacing, typography, elevations, role map |
| `design-consultation-plan-workflows.md` | Workflow details, custom components, schema additions, implementation notes |
| `design-consultation-plan-descriprive.md` | Workstreams, palette model, blog template strategy, layout architecture |

---

## Final Instruction for GStack and OpenCode

Treat `DESIGN.md` as the authoritative design system document for Mybizz CS.
If a downstream command requires the design consultation output, use this file first.
Use the supporting design consultation documents only for deeper detail where needed.

---

## MUI Aesthetic Implementation

This section records the MUI aesthetic decisions applied to the Mybizz CS design system.
These decisions are authoritative. The four supporting artifact files listed below are the
implementation deliverables. OpenCode and GStack `/design-consultation` must apply them
exactly as specified — no deviation, no regeneration unless source inputs change.

### Decision Summary

The MUI dashboard (https://mui.com/material-ui/getting-started/templates/dashboard/)
was selected as the aesthetic reference for Mybizz CS.

The following MUI aesthetic variables have been adopted and applied:

| Variable | MUI Value Adopted |
|---|---|
| Surface distinction | Two-level: background vs surface-container-low for panels/nav |
| Card shadow | Soft dual-layer: `hsla(220,30%,5%,0.07) 0px 4px 16px 0px, hsla(220,25%,10%,0.07) 0px 8px 16px -5px` |
| Card border radius | 8px (tightened from M3 default 12px) |
| Typography heading weight | 600 (updated from 400 on headline role) |
| Dividers | outline-variant at 40% opacity — translucent hairlines |
| State hover opacity | 0.08 |
| State pressed/focused opacity | 0.12 |
| App bar | surface token, hairline bottom border, no gradient at rest |
| Left nav | surface-container-low background, hairline right border |

All MUI values are applied through CSS custom properties and Anvil role selectors.
No hardcoded hex values were introduced.
All color values resolve through the palette selector token system.

### Constraints Preserved

The following DESIGN.md rules were not altered by the MUI aesthetic patch:

- Button radius: 100px (pill) — unchanged
- Dialog radius: 28px — unchanged
- Input radius (outlined): 4px — unchanged
- Navigation item radius: 100px (pill) — unchanged
- Data grid radius: 24px — unchanged
- Font family: Roboto — unchanged
- Spacing rhythm: 4px base grid — unchanged
- M3 color role structure — unchanged

### MUI Aesthetic Artifact Files

These four files are located in `docs/` and are the authoritative
implementation artifacts for the MUI aesthetic and palette system.

| File | Location | Purpose | Status |
|---|---|---|---|
| [[[mb-3-cs-project-context:docs-local/design_palette_section\|DESIGN Palette Section]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN_palette_section.md) | `docs/` | Full M3 CSS token sets for all 4 palettes | Authoritative |
| [[[mb-3-cs-project-context:docs-local/palette_selector_tokens\|Token Swap Blocks]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/palette_selector_tokens.md) | `docs/` | Python dict blocks for palette selector | Authoritative |
| `theme_mui_patch.css` | `docs/` | MUI aesthetic patch (reference only) | Reference |
| `theme_patched.css` | `docs/` | Deployment-ready theme.css with MUI patch | Deploy |

### Source Colors (Locked)

| Palette | Source Hex | Algorithm |
|---|---|---|
| Professional Blue | `#2563EB` | M3 HCT TonalSpot |
| Warm Coral | `#FF6F61` | M3 HCT TonalSpot |
| Calm Teal | `#008080` | M3 HCT TonalSpot |
| Sophisticated Purple | `#6B46C1` | M3 HCT TonalSpot |

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
