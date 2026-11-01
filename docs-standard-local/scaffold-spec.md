---
title: "scaffold-spec"
doc-id: "mb-be462593"
updated: "2026-11-01"
---
# Mybizz CS — Scaffold Specification

The structure of App 1: its packages, forms, modules, navigation and startup.

**Standard:** [[[default:specifications-global/spec-anvil-platform-standards]]](C:/dev/project-library-global/specifications-global/spec-anvil-platform-standards.md)

**Authority:** where this document differs from `mb-3-cs-architecture-reference.md` on a technical fact, the architecture reference is correct. Feature numbers (F-01 to F-34) are those of `mb-3-cs-concept.md`. Detail that has its own home is linked, not repeated:

| Subject | Home |
|---|---|
| Tables and fields | `docs-local/authoritative-schema.md` |
| Custom components | `docs-local/custom-component-requirements-matrix.md` |
| Design system | `docs-local/DESIGN.md`, `docs-local/palette-tokens.md` |
| Calendar forms | `docs-local/calendar-feature-spec.md` |
| AI and voice | `docs-local/releases/v2/mybizz-ai-implementation-spec.md` |
| Shared client state | `docs-standard-local/globals-contract.md` |
| Screen building rules | `docs-standard-local/anvil-ui-build-standard.md` |

---

## Package Structure

### Rules

- Feature-based packages. A package that exists is never renamed or merged; a new feature gets a new package.
- A form is a folder holding `form_template.yaml` and `__init__.py`. `form_template.yaml` is changed only in the Anvil editor, never by an agent.
- A server package is a folder holding `__init__.py` and its modules (`.py` files).
- Imports between modules are relative (`from ...shared import formatting_utils`), never by the app's package name: in a client instance the code runs from `master_template`'s package, so an import by App 1's package name fails there.
- All money, date and time logic is in server modules (Skulpt, reference section 14).
- Forms marked **(local)** are used inside one parent form only and live in that parent's folder.

### Client code

```
client_code/
├── auth/                          F-01
│   ├── LoginForm/                 startup form; routes by role (section Startup Configuration)
│   ├── PasswordResetForm/         request a reset link
│   └── SetPasswordForm/           set a password from an emailed link (new Owner, staff, customer, reset)
│
├── onboarding/                    F-03
│   └── OnboardingForm/            8 steps, resumable (DESIGN.md OnboardingForm)
│
├── dashboard/                     F-04
│   ├── DashboardForm/
│   │   ├── MetricCard/            (local)
│   │   └── ActivityFeed/          (local)
│   └── UpgradeForm/               usage, dedicated-account limits, request upgrade (F-33)
│
├── analytics/                     F-04
│   ├── DashboardAnalyticsForm/    revenue by service and provider
│   ├── MarketingDashboardForm/    marketing metrics, return on investment, alerts (UF-25)
│   └── RevenueReportForm/         reports, PDF and CSV export, scheduled reports
│
├── settings/                      F-02, F-05, F-22
│   ├── SettingsForm/              14 tabs (DESIGN.md SettingsForm)
│   │   ├── PaletteSettingsForm/   (local) Brand Palette tab
│   │   ├── BusinessProfileTab/    (local)
│   │   ├── CurrencyTab/           (local) system currency (read-only), display currency
│   │   ├── InvoicingTab/          (local) payment terms, tax, numbering
│   │   ├── FeatureSwitchesTab/    (local)
│   │   ├── IntegrationsTab/       (local) Wave
│   │   └── DataTab/               (local) history from R2, export as CSV
│   ├── VaultForm/                 Owner only, TOTP on every open
│   ├── EmailSetupForm/            Email Configuration tab
│   ├── PaymentGatewayForm/        Payment Gateways tab
│   ├── ThemeConfigForm/           Theme tab: public website header style only
│   ├── UserManagementForm/        Users and Permissions
│   └── PolicyEditorForm/          Legal Pages tab; hosts ClauseBuilder
│
├── layouts/
│   ├── AdminLayout/               NavigationRailLayout; admin menu
│   ├── CustomerLayout/            NavigationDrawerLayout; customer portal menu
│   ├── PublicLayout/              NavigationDrawerLayout; public website header and FooterComponent
│   ├── BlankLayout/               no header, no navigation: sign-in, landing pages, errors
│   └── ErrorLayout/               404, 403, 500 on BlankLayout (DESIGN.md ErrorLayout)
│
├── public_pages/                  F-06, F-07, F-09
│   ├── HomePage/
│   │   ├── HomePageClassicTemplate/     (local)
│   │   ├── HomePageServicesTemplate/    (local)
│   │   ├── HomePageBookingTemplate/     (local)
│   │   └── HomePageMinimalistTemplate/  (local)
│   ├── AboutPage/
│   ├── ContactPage/
│   ├── ServicesPage/              public services listing
│   ├── ServiceDetailPage/         public service detail; start of the booking and order flows
│   ├── PrivacyPolicyPage/
│   ├── TermsConditionsPage/
│   ├── PageListForm/              admin: Content & Website → Pages
│   ├── PageEditorForm/            admin: edit, preview, publish a page
│   ├── HomepageEditorForm/        admin: homepage layout, sections, testimonials (UF-17)
│   └── MediaLibraryForm/          admin: Content & Website → Media
│
├── services/                      F-10, F-11
│   ├── ServicesForm/              admin service list
│   ├── ServiceEditorForm/
│   ├── ServiceViewerForm/
│   ├── ServiceCategoriesForm/
│   ├── ResourceListForm/          rooms and equipment (Services → Resources)
│   └── GroupSessionEditorForm/    sessions of a group service (UF-07)
│
├── bookings/                      F-11
│   ├── BookingCalendarForm/       controller and its view forms and templates: calendar-feature-spec.md section 3
│   ├── BookingListForm/
│   ├── BookingFlowForm/           the customer booking flow, steps 1–7 (DESIGN.md Booking Design)
│   ├── BookingEditorForm/         admin create and edit
│   ├── BookingViewerForm/
│   └── AvailabilitySettingsForm/  business hours, provider schedules, blocked dates, exceptions
│
├── orders/                        F-12
│   ├── OrderFlowForm/             customer order: brief, attachments, payment (UF-09)
│   ├── OrderListForm/
│   └── OrderViewerForm/           status changes, delivery files and message (UF-12)
│
├── packages/                      F-13
│   ├── PackageListForm/
│   └── PackageEditorForm/
│
├── time_tracking/                 F-14
│   ├── TimeEntryListForm/         filter, select billable entries, create invoice
│   └── TimeEntryEditorForm/       manual entry and start/stop timer
│
├── invoices/                      F-15
│   ├── InvoiceListForm/
│   ├── InvoiceEditorForm/
│   ├── InvoiceViewerForm/         includes Record Payment and Refund
│   └── PaymentListForm/           Finance & Reports → Payments
│
├── crm/                           F-17, F-18, F-19, F-20
│   ├── ContactListForm/
│   ├── ContactEditorForm/
│   ├── ContactViewerForm/
│   ├── ContactImportForm/         CSV import
│   ├── SegmentManagerForm/
│   ├── TaskListForm/
│   ├── AutomationListForm/
│   ├── AutomationEditorForm/
│   ├── EmailCampaignListForm/
│   ├── EmailCampaignEditorForm/
│   ├── EmailBroadcastForm/
│   └── LeadCaptureForm/
│
├── landing_pages/                 F-08, F-19
│   ├── LandingPageListForm/
│   ├── LandingPageEditorForm/
│   └── LandingPage/               public, on BlankLayout
│       ├── LeadCaptureTemplate/         (local)
│       ├── EventRegistrationTemplate/   (local)
│       └── VslTemplate/                 (local)
│
├── blog/                          F-21
│   ├── BlogListForm/              public listing
│   │   ├── BlogListTiledTemplate/       (local)
│   │   └── BlogListListTemplate/        (local)
│   ├── BlogPostForm/              public post
│   │   ├── BlogPostClassicTemplate/     (local)
│   │   ├── BlogPostTwoColumnTemplate/   (local)
│   │   └── BlogPostFullWidthTemplate/   (local)
│   ├── BlogAdminListForm/         admin: Content & Website → Blog
│   ├── BlogEditorForm/
│   ├── CategoryManagementForm/
│   └── CommentModerationForm/
│
├── reviews/                       F-23
│   ├── ReviewListForm/            approve or decline
│   ├── ReviewViewerForm/
│   └── ReviewSubmitForm/          public: star rating and comment, opened from the review email
│
├── portal/                        F-24
│   ├── MyDashboardForm/
│   ├── MyBookingsForm/
│   ├── MyPackagesForm/
│   ├── MyInvoicesForm/
│   ├── MyReviewsForm/
│   └── AccountForm/               profile, password, email preferences, morning briefing switch
│
├── ai/                            F-27
│   └── WhatsUpForm/               mobile voice view, opened from the home screen shortcut
│
├── support/                       F-32
│   └── HelpForm/                  problem or feedback, replies from App 5
│
├── components/                    shared custom components: custom-component-requirements-matrix.md
│   ├── VideoDisplayComponent/
│   ├── FooterComponent/
│   ├── TimeLapseCarouselComponent/
│   ├── ParallaxComponent/
│   ├── ClauseBuilder/
│   ├── AIHelperComponent/
│   └── StarRatingComponent/
│
└── shared/
    ├── globals.py                 shared client state: globals-contract.md
    ├── navigation_helpers.py
    ├── validation_utils.py
    ├── formatting_utils.py
    └── palette_utils.py           palette and dark mode: palette-tokens.md
```

The four homepage templates, two blog list templates, three blog post templates and three landing page templates are the 12 page templates of `DESIGN.md` (Template and Layout Architecture). Each is a form in its parent's folder, loaded into the parent's content panel by the parent's template field (`business_profile.homepage_template`, `business_profile.blog_list_view`, `blog_posts.template`, `landing_pages.template_type`).

### Server code

```
server_code/
├── server_auth/          rbac (role decorators), auth_service (invitations, set-password links, lockout)
├── server_dashboard/     dashboard_service, upgrade_service
├── server_analytics/     reporting_service, report_export_service, summary_tasks
├── server_settings/      settings_service, vault_service, vault_totp_service, policy_service, onboarding_service
├── server_services/      catalogue_service, resource_service, group_session_service
├── server_bookings/      booking_service, availability_service, calendar_service, intake_service, booking_tasks
├── server_orders/        order_service
├── server_packages/      package_service, package_tasks
├── server_time/          time_entry_service
├── server_payments/      payment_service, stripe_gateway, paystack_gateway, paypal_gateway,
│                         invoice_service, invoice_pdf, refund_service, webhook_handlers, invoice_tasks
├── server_customers/     contact_service, segment_service, contact_import_export
├── server_marketing/     campaign_service, broadcast_service, lead_capture_service, landing_page_service,
│                         brevo_campaigns, campaign_tasks
├── server_automation/    task_service, automation_service, automation_tasks
├── server_blog/          blog_service, comment_service, blog_tasks
├── server_reviews/       review_service, testimonial_service
├── server_ai/            ai_request_layer, assists, voice_service, briefing_service
├── server_storage/       r2_service, backup_tasks, history_service, audit_archive_tasks
├── server_integrations/  wave_service
├── server_support/       help_service
├── server_management/    management_endpoints, heartbeat_tasks, usage_service
└── server_shared/        config, constants, exceptions, logging_config, validators, time_utils,
                          transactional_email, export_service, retry_queue
```

- `vault_service` is the only module that reads the `encryption_key` Anvil Secret (F-02). It holds the encryption and decryption; no other module encrypts or decrypts.
- `payment_service` is the single gateway-agnostic payment service; client code and other server modules call it, never a gateway module directly (F-15).
- `ai_request_layer` is the single route for every AI and speech request, through App 5 (F-26, F-27).
- `r2_service` is the only module that talks to Cloudflare R2 (F-29).
- `management_endpoints` holds the HTTP endpoints App 5 calls; `webhook_handlers` holds the gateway and Brevo webhook endpoints.
- Every `*_tasks` module holds that package's background tasks; the schedule is in `integration-architecture.md` (Background Tasks).

### Server function pattern

- Every callable function carries a role decorator from `server_auth/rbac` (Owner, Manager, Admin, Staff, Customer); the server is the enforcement, never the interface (F-01).
- A function used by a customer filters every query to the signed-in customer's own records (F-24).
- Every function returns `{'success': True, 'data': result}` or `{'success': False, 'error': message}`.
- Every public function has type hints and a docstring with Args, Returns and Raises, and logs as `observability-requirements.md` requires.
- Any operation expected to run longer than 22 seconds is an `@anvil.server.background_task`.

---

## Custom Component Conventions

A custom component is a reusable element that the Anvil Material 3 theme does not provide and that is used in more than one place. An element used in one place only is a local form in its parent's folder.

- The seven shared custom components, where each is used and its implementation notes: `custom-component-requirements-matrix.md`.
- Every shared custom component lives in `client_code/components/`, takes its settings as properties set by the host form, and uses only the tokens, typography, spacing and roles in `DESIGN.md`.
- Components inside a custom component are only those in [[[default:specifications-global/spec-m3_component_mapping|M3 Component Mapping]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md); browser features are reached through `anvil.js`, never `eval`.
- Not built: animated button, card, link and image components; an onboarding palette form; chip components (matrix, Not built).

---

## Database Schema

The tables, their columns, types and allowed values are in `authoritative-schema.md`: 55 tables. The changes that bring App 1's tables to that schema, and the prompts that apply them, are in `schema-migration-log.md`. Every schema change is made in App 1, recorded in `schema-migration-log.md`, and applied to App 3.

### Rules

- One client instance has one database. No table carries a client or instance column; isolation comes from the instance (reference section 3).
- All datetimes are stored in UTC and shown in the business's IANA time zone (`business_profile.timezone`).
- Amounts are stored in the system currency (F-16).
- Bookings, invoices, orders and payments are never deleted; their status changes.
- History, backups and webhook payloads are kept in the client's R2 bucket, not in the tables (F-29).
- Credentials are kept only in `vault`, encrypted.
- `activity_log` is the audit table.

---

## M3 Component Standards

- Every screen is built to `anvil-ui-build-standard.md` and `DESIGN.md`.
- Components are only those in [[[default:specifications-global/spec-m3_component_mapping|M3 Component Mapping]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md). `HTMLTemplate` and the legacy `Label` are banned.
- Component names follow [[[default:specifications-global/spec-nomenclature|Nomenclature]]](C:/dev/project-library-global/specifications-global/spec-nomenclature.md); `Text` components use the prefix `text_`.
- Inputs use `appearance = "outlined"`, set in the Designer. A failed validation sets the input's `error` property and shows the message in its supporting text; both are cleared when the input is valid.
- One filled button per screen for the primary action; outlined for secondary; text for tertiary.
- Editor forms set `self.item` before `self.init_components()`, with Write Back switched on in the Designer's Data Bindings panel; settings forms that read and write through their own server calls use no data bindings.
- Multi-step flows (onboarding, booking, order) keep the selections in `self.item` or in `globals.py` (`globals-contract.md`) and submit to the server once, at the last step.

---

## Navigation Architecture

| Area | Mechanism |
|---|---|
| Admin area | `AdminLayout` (`NavigationRailLayout`), plain `Link` components with lambda click handlers, `open_form()` |
| Customer portal | `CustomerLayout` (`NavigationDrawerLayout`), the same mechanism |
| Public website | `PublicLayout`, Anvil Routing (`@router.route`) |

- Each page form names its layout in the Designer. Opening a page is `open_form('package.FormName')`; the layout stays.
- Click handlers capture the loop variable: `lambda e, fn=form_name: self._nav_click(fn)`.
- `NavigationLink`, `navigate_to` and `HTMLTemplate` are not used ([[[default:adr-global/adr-navigation-lambda-link-open-form|Navigation Standard: Lambda/Link/open_form]]](C:/dev/project-library-global/adr-global/adr-navigation-lambda-link-open-form.md) ADR, [[[default:adr-global/adr-htmltemplate-use|HTMLTemplate Use]]](C:/dev/project-library-global/adr-global/adr-htmltemplate-use.md) ADR).
- The menu items, their order and who sees them are defined in `mb-3-cs-architecture-reference.md` section 9. The forms behind them:

### AdminLayout menu → forms

| Menu item | Form | Shown to |
|---|---|---|
| Dashboard | `dashboard.DashboardForm` | all staff roles |
| Sales & Operations → Bookings | `bookings.BookingListForm` (Calendar: `bookings.BookingCalendarForm`) | all staff roles |
| Sales & Operations → Services | `services.ServicesForm` | all staff roles |
| Sales & Operations → Orders | `orders.OrderListForm` | all staff roles |
| Sales & Operations → Packages | `packages.PackageListForm` | all staff roles |
| Customers & Marketing → Contacts | `crm.ContactListForm` | all staff roles |
| Customers & Marketing → Campaigns | `crm.EmailCampaignListForm` | when `marketing_enabled` |
| Customers & Marketing → Broadcasts | `crm.EmailBroadcastForm` | when `marketing_enabled` |
| Customers & Marketing → Segments | `crm.SegmentManagerForm` | when `marketing_enabled` |
| Customers & Marketing → Tasks | `crm.TaskListForm` | all staff roles |
| Customers & Marketing → Automations | `crm.AutomationListForm` | when `marketing_enabled` |
| Customers & Marketing → Reviews | `reviews.ReviewListForm` | all staff roles |
| Content & Website → Blog | `blog.BlogAdminListForm` | when `blog_enabled` |
| Content & Website → Pages | `public_pages.PageListForm` | all staff roles |
| Content & Website → Landing Pages | `landing_pages.LandingPageListForm` | all staff roles |
| Content & Website → Media | `public_pages.MediaLibraryForm` | all staff roles |
| Finance & Reports → Invoices | `invoices.InvoiceListForm` | all staff roles |
| Finance & Reports → Payments | `invoices.PaymentListForm` | all staff roles |
| Finance & Reports → Time Entries | `time_tracking.TimeEntryListForm` | all staff roles |
| Finance & Reports → Reports | `analytics.RevenueReportForm` | all staff roles |
| Finance & Reports → Analytics | `analytics.DashboardAnalyticsForm` | all staff roles |
| Settings | `settings.SettingsForm` | Owner, Manager |
| Vault | `settings.VaultForm` | Owner |
| Help | `support.HelpForm` | all staff roles |
| Sign out | — | all roles |

Within each item, what a role may see or do is enforced by the server (F-01).

### CustomerLayout menu → forms

| Menu item | Form |
|---|---|
| My Dashboard | `portal.MyDashboardForm` |
| My Bookings | `portal.MyBookingsForm` |
| My Packages | `portal.MyPackagesForm` |
| My Invoices | `portal.MyInvoicesForm` |
| My Reviews | `portal.MyReviewsForm` |
| Account | `portal.AccountForm` |
| Sign out | — |

### Public routes

| Address | Form |
|---|---|
| `/` | `public_pages.HomePage` |
| `/about` | `public_pages.AboutPage` |
| `/services` | `public_pages.ServicesPage` |
| `/services/:slug` | `public_pages.ServiceDetailPage` |
| `/book/:slug` | `bookings.BookingFlowForm` |
| `/order/:slug` | `orders.OrderFlowForm` |
| `/blog` | `blog.BlogListForm` |
| `/blog/:slug` | `blog.BlogPostForm` |
| `/contact` | `public_pages.ContactPage` |
| `/privacy` | `public_pages.PrivacyPolicyPage` |
| `/terms` | `public_pages.TermsConditionsPage` |
| `/landing/:slug` | `landing_pages.LandingPage` |
| `/review/:token` | `reviews.ReviewSubmitForm` |
| `/whatsup` | `ai.WhatsUpForm` (signed-in staff only) |

---

## Startup Configuration

The startup form is `auth.LoginForm` (`anvil.yaml`: `startup: {module: auth.LoginForm, type: form}`). Its `__init__` routes before `init_components()`:

1. Signed in as Owner, Manager, Admin or Staff → `open_form('dashboard.DashboardForm')` and `return`; while `business_profile.onboarding_status` is `in_progress`, the Owner goes to `onboarding.OnboardingForm` instead.
2. Signed in as Customer → `open_form('portal.MyDashboardForm')` and `return`.
3. A public address → the routing dependency opens the route's form.
4. Not signed in → the sign-in form is shown.

At startup the palette module applies `business_profile.palette` and starts the device light/dark watch (`palette-tokens.md`).

### Users service (set in the Anvil editor; agents never write `anvil.yaml`)

| Setting | Value | Reason |
|---|---|---|
| Sign-in method | Email and password | F-01 |
| Sign-up | Off | Owners are created at provisioning, staff by invitation, customers by their first booking |
| Confirm email | On | F-01 |
| Remember me | 30 minutes | Matches the 30-minute session rule (F-01, UF-38) |
| Anvil two-factor authentication | Off, and `require_mfa` off | Sign-in has no second factor; the Vault's TOTP is the project's own (F-02). `anvil.yaml` today has `require_mfa: true` with `use_mfa: false`: `require_mfa` is switched off |
| Secure passwords | Required | F-01 |
| User table | `users` | |

### Other services

- Data Tables.
- Email: Anvil built-in email, for system email only (sign-in, confirmation, reset). Transactional email goes through Brevo SMTP from `server_shared/transactional_email` (`integration-architecture.md`).

---

## Dependencies

| Dependency | Dependency ID | Version | Package name |
|---|---|---|---|
| Material 3 Theme | `4UK6WHQ6UX7AKELK` | `v1.2.6` | `m3` |
| Routing | `3PIDO5P3H4VPEMPL` | `v0.6.1` | `routing` |

- Versions are pinned. A version change is a release with its own tests.
- App 3 and every client instance depend on `master_template` (App 2), as `mb-3-cs-architecture-reference.md` section 1 sets out.
- **Anvil Secret:** `encryption_key` only, used only by `vault_service`. Set once per instance at provisioning and never changed.
- **Native libraries:** `anvil.yaml` loads `@floating-ui/dom@1` from `cdn.jsdelivr.net` in `head_html`. Its purpose is not recorded. It is checked against the M3 theme's own requirements, then kept with its reason recorded here, or removed. No other external script is loaded.
- **Runtime:** server `python3-full`, client version 3.

---

*End of file — scaffold-spec.md*
