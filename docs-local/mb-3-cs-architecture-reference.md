---
title: "mb-3-cs-architecture-reference"
doc-id: "mb-7d6be180"
updated: "2026-11-01"
---
# Mybizz CS — Architecture Reference

## 0. Authority

This document is the build standard for the architecture of Mybizz CS. Agents build from this document. Where `mb-3-cs-concept.md` or `mb-3-cs-architecture-explainer.md` differs from this document on a technical fact, this document is correct.

Every feature carries the number and name it has in `mb-3-cs-concept.md` section 7 (F-01 to F-34). Detail that has its own home is linked, not repeated:

| Subject | Home |
|---|---|
| Data tables and fields | `docs-local/authoritative-schema.md` |
| User flows | `docs-local/user-flows.md` |
| Design system | `docs-local/DESIGN.md`; palette colour values in `docs-local/palette-tokens.md` |
| Custom components | `docs-local/custom-component-requirements-matrix.md` |
| AI, voice and sample data | `docs-local/mybizz-ai-implementation-spec.md` |
| Calendar | `docs-local/calendar-feature-spec.md` |
| The Vault | `docs-local/mb-3-cs-vault-implementation.md`, [[[default:specifications-global/spec-vault-system\|The Vault System]]](C:/dev/project-library-global/specifications-global/spec-vault-system.md) |
| Testing | `docs-local/mb-3-cs-test-specification.md` |

---

## 1. The Five-App System

The five-app model is Mybizz-wide ([[[default:specifications-global/spec-five-app-architecture-model|Five-App Architecture Model (Specification)]]](C:/dev/project-library-global/specifications-global/spec-five-app-architecture-model.md)). This section is the Mybizz CS instance of that model.

| App | Role | Anvil app and GitHub repository | Hosted on | Web address |
|---|---|---|---|---|
| App 1 | Development workspace | `mb-3-cs-app1` | Mybizz development account (Business plan) | `mb-3-cs-app1.mybizz.live` |
| App 2 | `master_template` — the published dependency | `mb-3-cs-app2` | Mybizz development account | none |
| App 3 | `blank_client_template` — the provisioning clone source | `mb-3-cs-app3` | Mybizz development account | none |
| App 4 | Client instances, one per client | `[client-name]-cs` | Mybizz-owned client hosting accounts (Business plan), two client instances per account; one account per client on upgrade | the client's own domain |
| App 5 | Mybizz management app | `mb-3-cs-app5` | Mybizz development account | `mb-3-cs-app5.mybizz.live` |

### App 1 — development workspace
- All feature development happens here. Holds every data table (development and test data), every form, every server module and all application code.
- Connected to GitHub as the shared source of truth between Anvil and the local copy agents work in.
- Each release is tagged here. Never a dependency for any client instance.

### App 2 — `master_template`
- The sole source of all server-side logic and all forms for every client instance.
- Receives App 1's tagged release, pushed into its repository by the developer (Step 42).
- Stateless: holds no client data.
- Shared to the client hosting accounts as a dependency by its dependency ID. A client instance calling `anvil.server.call()` runs `master_template`'s server modules; a client instance showing a form shows `master_template`'s form.

### App 3 — `blank_client_template`
- The only clone source for new client instances.
- Holds the full schema (no data), one startup module, and `master_template` as a dependency. Zero server modules, zero forms.
- Updated only when the schema changes. Anvil Secrets are left empty and set per instance at provisioning.
- Anvil cannot create tables programmatically; cloning App 3 is how every instance gets the schema.

### App 4 — client instances
- Each client instance is cloned from App 3 into its assigned client hosting account.
- Holds the schema, the startup module and an active dependency on `master_template`. Nothing else.
- Updated automatically: server logic, forms and UI arrive through the dependency. Schema changes are applied manually.
- Clients never have access to the Anvil editor or to any source code. Clients use their app only.

### App 5 — Mybizz management app
- Built and running before the first paying client (F-34).
- Does not depend on client instances. Communicates with client instances through HTTP endpoints secured with a per-client Bearer token ([[[default:specifications-global/spec-api-specification|API Specification (HTTP Endpoints)]]](C:/dev/project-library-global/specifications-global/spec-api-specification.md), [[[default:adr-global/adr-mybizz-management-visibility|Mybizz_management Visibility and Control]]](C:/dev/project-library-global/adr-global/adr-mybizz-management-visibility.md) ADR).
- Client instances expose the management endpoints from their first day.

---

## 2. Rule: No Forms or Server Modules in a Client Instance

A client instance inherits all logic and UI live from `master_template`. A form or server module added directly to a client instance silently and permanently overrides the dependency for that instance, with no error or warning, and that instance stops receiving updates.

- The startup module is the only code allowed in a client instance.
- A change needed for one client is made in App 1, tested against a private client instance, and released into `master_template` (Step 42); every client instance then receives it.
- Every client instance carries a README stating this rule from provisioning ([[[default:adr-global/adr-client-instance-readme-five-app-system|Mandatory README in Every Client Instance Documenting the Five-App System]]](C:/dev/project-library-global/adr-global/adr-client-instance-readme-five-app-system.md) ADR). An agent working in a client instance without that README stops and locates it first.

---

## 3. Data Isolation

- Each client instance is a separate Anvil app with a separate database. `app_tables` always resolves to the current client's data. Cross-client access is architecturally impossible.
- Two client instances on the same hosting account remain two apps with two databases.
- `master_template` holds no client data. All state lives in the client's own data tables and the client's own R2 bucket.
- Mybizz does not copy a client's customer data into App 5. App 5 holds client-level records only: registry, usage figures, health, billing, tickets and feedback.

---

## 4. Hosting, Capacity and Fair Use (F-33)

- **Plan:** every hosting account is an Anvil Business account owned by Mybizz. The Hobby plan is never used.
- **Shared accounts:** two client instances per client hosting account.
- **Fair-share cap per client on a shared account:** half of the account's Business limits — 500,000 data table rows and 50 GB of data table storage. The cap is enforced by the application.
- **Usage reporting:** each client instance reports its row count, storage, heavy-job activity, last staff sign-in and last booking date to App 5 through the management endpoints, on a schedule.
- **Heavy jobs:** imports, exports, backups, AI generation and briefings run as Anvil background tasks.
- **Upgrade:** the upgrade feature (F-04) requests the move in App 5; Mybizz clones the instance to a dedicated client hosting account, restores its data, moves the domain and retires the shared instance.
- **Capacity:** a hard limit of 100 client instances for Mybizz CS.

---

## 5. Provisioning and Registration (F-03, F-34)

1. The client registers. App 5 creates the client record and the client reference number. As part of registration, the client opens a free Cloudflare account in the client's own name, adds a payment method, switches R2 on, and invites Mybizz's Cloudflare user as a member with the role Cloudflare R2 Admin.
2. App 5 assigns the client a hosting account with a free place (one of two).
3. Mybizz clones App 3 into that account as `[client-name]-cs`.
4. Mybizz sets the instance's Anvil Secret (`encryption_key`), the client's domain and the instance's Bearer token. App 5 accepts the client's Cloudflare invitation, creates the client's R2 bucket and a token for that bucket only, and writes the bucket's keys into the instance's Vault. The Bearer token and the client's Mybizz-side credentials are stored in App 5.
5. The client completes onboarding in the instance (F-03).

Procedure detail: [[[default:specifications-global/spec-client-activation-runbook|Client Instance Activation Runbook]]](C:/dev/project-library-global/specifications-global/spec-client-activation-runbook.md).

---

## 6. Development Workflow

- Agents work in the local copy of App 1; the developer pushes to GitHub; Anvil syncs from GitHub.
- Changes made in the Anvil Designer are pulled into the local copy with Step 18's pull procedure before any agent edits code.
- Designer work always precedes code work for any given form.
- This workflow applies to App 1 only (section 2).

---

## 7. Package Structure

Feature-based organisation. Existing packages are never renamed or merged during active development; new packages are added for new features.

```
client_code/
  analytics/      auth/           blog/           bookings/
  components/     crm/            dashboard/      invoices/
  landing_pages/  layouts/        onboarding/     public_pages/
  services/       settings/       shared/
  orders/         packages/       time_tracking/  reviews/
  portal/         ai/             support/

server_code/
  server_analytics/  server_auth/       server_blog/       server_bookings/
  server_customers/  server_dashboard/  server_marketing/  server_payments/
  server_services/   server_settings/   server_shared/
  server_orders/     server_packages/   server_time/       server_reviews/
  server_automation/ server_ai/         server_storage/    server_integrations/
  server_support/    server_management/
```

- The client-side CRM and marketing package is `crm/`; the server-side package is `server_marketing/` ([[[mb-3-cs-project-context:adr-local/crm-package-replaces-marketing|Package Name: crm/ Replaces marketing/]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/adr-local/crm-package-replaces-marketing.md) ADR).
- `server_payments/` holds invoices, payments, refunds and every gateway.
- `server_storage/` holds all Cloudflare R2 access (F-29).
- `server_integrations/` holds Wave (F-28).
- `server_management/` holds the management HTTP endpoints called by App 5.
- `server_support/` sends Help messages to App 5 (F-32).
- `components/` holds every shared custom component listed in `custom-component-requirements-matrix.md`.
- `ai/` holds the WhatsUp voice view and the client-side AI modules; the AI helper itself is a shared component in `components/`.

---

## 8. Module Architecture

The general three-tier module pattern (forms / client modules / server modules / pure logic modules) is defined in [[[default:specifications-global/spec-anvil-platform-standards|Anvil Platform Mechanics]]](C:/dev/project-library-global/specifications-global/spec-anvil-platform-standards.md) section 3. This project's authority for the pattern: [[[default:adr-global/adr-form-architecture-and-state|Form Architecture and State Management]]](C:/dev/project-library-global/adr-global/adr-form-architecture-and-state.md) ADR.

---

## 9. Navigation

General navigation patterns: [[[default:specifications-global/spec-m3-design-standards|M3 Design & Component Standards]]](C:/dev/project-library-global/specifications-global/spec-m3-design-standards.md) section 7.

| Area | System |
|---|---|
| Admin and customer portal | Native M3 layouts (`NavigationRailLayout` / `NavigationDrawerLayout`) + plain `Link` components + lambda click handlers |
| Public website pages | Anvil Routing dependency (`@router.route`) |

`HTMLTemplate` is banned; all layout shells use the native M3 layouts. Sidebar navigation uses plain `Link` components with lambda click handlers, not `NavigationLink`. `navigate_to` is not used. See [[[default:adr-global/adr-htmltemplate-use|HTMLTemplate Use]]](C:/dev/project-library-global/adr-global/adr-htmltemplate-use.md) ADR, [[[default:adr-global/adr-navigation-lambda-link-open-form|Navigation Standard: Lambda/Link/open_form]]](C:/dev/project-library-global/adr-global/adr-navigation-lambda-link-open-form.md) ADR, [[[default:adr-global/adr-ui-customization-approach|UI Customization Approach]]](C:/dev/project-library-global/adr-global/adr-ui-customization-approach.md) ADR.

### AdminLayout navigation
```
Dashboard                          (always)
▼ Sales & Operations
  Bookings                         (always)
  Services                         (always)
  Orders                           (always)
  Packages                         (always)
▼ Customers & Marketing
  Contacts                         (always)
  Campaigns                        (marketing_enabled)
  Broadcasts                       (marketing_enabled)
  Segments                         (marketing_enabled)
  Tasks                            (always)
  Automations                      (marketing_enabled)
  Reviews                          (always)
▼ Content & Website
  Blog                             (blog_enabled)
  Pages                            (always)
  Landing Pages                    (always)
  Media                            (always)
▼ Finance & Reports
  Invoices                         (always)
  Payments                         (always)
  Time Entries                     (always)
  Reports                          (always)
  Analytics                        (always)
Settings                           (Owner / Manager only)
Vault                              (Owner only)
Help                               (all staff roles)
[Sign out — bottom of sidebar, all roles]
```

### CustomerLayout navigation
```
My Dashboard                       (always)
My Bookings                        (always)
My Packages                        (always)
My Invoices                        (always)
My Reviews                         (always)
Account                            (always)
[Sign out — bottom of sidebar]
```

The AI helper (F-26) is a component placed on forms, not a navigation item. WhatsUp (F-27) opens from a home screen shortcut to the voice view.

---

## 10. Startup and Authentication Routing

The startup form is `auth.LoginForm` (defined in `anvil.yaml`). Its `__init__` checks authentication and routes before `init_components()`:

1. Signed in as Owner, Manager, Admin or Staff → open `DashboardForm` in AdminLayout, return.
2. Signed in as Customer → open the customer portal in CustomerLayout, return.
3. Not signed in → show the sign-in form.

`open_form()` in `__init__` is always followed by `return`. See [[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR.

---

## 11. The Sidesheet

The M3 Sidesheet is available on `NavigationDrawerLayout` and `NavigationRailLayout`, for secondary content or detail panels alongside the main content. `NavigationRailLayout` with a Sidesheet serves step-by-step guided workflows (onboarding, booking): accumulated data passes in `self.item` between steps and is submitted to the server in one call at the final step.

---

## 12. Feature Architecture

Each feature below states its packages, data, rules and links. Tables and fields are in `authoritative-schema.md`; flows are in `user-flows.md`.

### F-01 Accounts, roles and access
- Anvil Users service, email and password; email confirmation required; password reset by email.
- New users (Owner, staff, customers) set their own password from an emailed link; no password is ever sent by email.
- Sessions end after 30 minutes without activity; the user signs in again and returns to the same place.
- Five roles: Owner, Manager, Admin, Staff, Customer, held in the user's `role` field.
- Every server function checks the role with the custom role decorators; the interface hides what a role cannot use, but the server is the enforcement.
- Rate-limited sign-in and account lockout ([[[default:specifications-global/spec-security|Security Specification]]](C:/dev/project-library-global/specifications-global/spec-security.md)).
- Every significant action writes an audit record (F-29 for retention).
- Packages: `auth/`, `server_auth/`. Role limits: [[[default:specifications-global/spec-security-architecture|Security & Secrets Architecture]]](C:/dev/project-library-global/specifications-global/spec-security-architecture.md).

### F-02 The Vault
- One Anvil Secret per instance: `encryption_key`, used only by `vault_service.py`.
- Every other credential (gateway keys, Brevo key, R2 keys, Wave credentials) is encrypted in the Vault's data table with that key.
- Owner-only; every view requires a TOTP code with no remembered session; every access emails the owner; after 5 failed codes the Vault locks for 15 minutes and the owner is emailed.
- Detail: `mb-3-cs-vault-implementation.md`, [[[default:specifications-global/spec-vault-system|The Vault System]]](C:/dev/project-library-global/specifications-global/spec-vault-system.md).

### F-03 Registration, onboarding and setup
- Registration happens in App 5 (section 5); App 5 issues the client reference number.
- Onboarding in the instance is a guided, resumable sequence on `NavigationRailLayout` with a Sidesheet and a progress indicator: business profile → brand palette → system currency → legal acknowledgement (timestamped) → payment gateway → Brevo email.
- Progress is saved after each step; onboarding and Settings link to each other.
- Packages: `onboarding/`, `server_settings/`. Engineering plan: [[[default:specifications-global/spec-onboarding-implementation-plan|Mybizz CS Onboarding System - Engineering Plan]]](C:/dev/project-library-global/specifications-global/spec-onboarding-implementation-plan.md).

### F-04 Dashboard and analytics
- Metric cards (revenue, bookings, customers) with current value, percentage change and trend; trend charts; activity feed; period comparison (week, month, custom range).
- Dashboard figures are read from pre-computed summary tables, refreshed by background task, not computed on page load.
- Advanced analytics: revenue by service and by provider, marketing return on investment per campaign and landing page, PDF and CSV export, scheduled reports emailed by background task.
- Upgrade feature: shows the dedicated account's limits and the client's current usage; the request is sent to App 5 through the management endpoints (section 4).
- Packages: `dashboard/`, `analytics/`, `server_dashboard/`, `server_analytics/`.

### F-05 Settings Hub
- One tabbed form; tabs: Business Profile, Brand Palette, System Currency (read-only), Display Currency, Legal Acknowledgement (read-only), Payment Gateways (test/live), Invoicing (default payment terms, numbering), Users and Permissions, Email Configuration, Theme, Feature Switches (`marketing_enabled`, `blog_enabled`, comments), Integrations (Wave), Legal Pages (Clause Builder, F-22), Data (history view and export, F-29). The Invoicing tab also holds the tax rate (default 0), tax label and tax registration number; the Theme tab holds the public website header style only.
- Tabs are shown per role: Owner sees all; Manager sees operational tabs, not financial or security tabs.
- Packages: `settings/`, `server_settings/`.

### F-06 Public website
- Public pages are served through Anvil Routing: Home, About, Contact, Services listing and detail, Blog listing and post, Privacy Policy, Terms and Conditions, landing pages.
- The contact form creates a contact and a follow-up task (F-17, F-20).
- Google Maps embed on Contact and in the footer, optional.
- Meta tags, semantic structure and clean addresses on every public page; fully responsive.
- The instance runs on the client's own domain: an Anvil custom domain on the instance's environment, with a DNS `A` record at the client's domain provider.
- Packages: `public_pages/`, `layouts/`.

### F-07 Homepage templates
- Four layouts — Classic, Services, Booking, Minimalist — plus further layouts, all composed from the same section components; a layout is a section order and visibility set.
- Optional sections render only when configured: video (a video address is set), services carousel (active services exist).
- Packages: `public_pages/`, `components/`.

### F-08 Landing pages
- Templates: Lead Capture, Event Registration, Video Sales Letter, on a blank layout with no header or footer.
- Each landing page counts views and conversions.
- Packages: `landing_pages/`, `server_marketing/`.

### F-09 Visual and media presentation
- Parallax hero, auto-rotating carousels with arrows and dot indicators.
- One video component for homepage, service pages and blog posts; accepts YouTube, Vimeo, Dailymotion and direct video addresses; hides itself when no address is set.
- Packages: `components/`.

### F-10 Service catalogue
- Pricing model `duration` (price per time block; drives slot calculation) or `unit` (price per deliverable; no slot).
- Per service: name, description, category, pricing model, price, duration, delivery mode, providers, images, video, meeting types, intake form, display currency, active status.
- Delivery modes: at the business, at the customer's location, online live, delivered work, group.
- Packages: `services/`, `server_services/`.

### F-11 Bookings and scheduling
- Booking flow as in `user-flows.md`; one continuous flow with no external redirects.
- Calendar: month, week (seven days across, times down) and day views for Owner, Manager, Admin and Staff; Staff see only their own bookings, enforced on the server; one server call per period, returning only the visible period; colours from status tokens, never hardcoded; refreshed every 60 seconds while open. Build detail: `calendar-feature-spec.md`.
- Availability: business hours per weekday, provider schedules, blocked dates, date exceptions.
- Meeting types: at the business; at the customer's location (address captured; travel buffer before and after, set per service); video call (link generated and sent with confirmation and reminders); phone call.
- Group sessions: one session with a place limit; each place is a booking against the session.
- Resources: rooms and equipment are bookable resources; a service may require a resource, and a slot is available only when provider and resource are both free.
- Payment option per service: pay in full now, pay a deposit now, pay on the day, or invoice with payment terms; a customer with a package can use a package session (F-13, F-15).
- Bookings paid now, or booked on pay on the day or invoice terms, are confirmed at once; a booking whose online payment has not succeeded is held as payment pending for 15 minutes with an emailed payment link, then released.
- Every confirmation and reschedule email carries a calendar invite.
- Resources (rooms and equipment) are managed under Services → Resources.
- Reminders at 24 hours and 1 hour, sent by scheduled background task (F-18).
- Recurring bookings (weekly, every two weeks, monthly); reschedule without cancellation; special requests; statuses pending, confirmed, completed, no_show, cancelled. Marking a booking completed triggers its invoice (or balance invoice) and the review request. Bookings are cancelled, never deleted.
- Double-booking is prevented on the server at the moment of confirmation.
- Packages: `bookings/`, `server_bookings/`.

### F-12 Orders for delivered work
- An order is placed for a `unit`-priced service, paid at ordering, with status ordered → in progress → delivered.
- Delivery can attach files and a message; the customer is emailed at each status change.
- Packages: `orders/`, `server_orders/`.

### F-13 Session packages
- A package defines a service, a number of sessions, a price and an expiry period.
- A purchased package holds a balance; each booking against it draws one session; cancellations within the allowed window return the session.
- Packages: `packages/`, `server_packages/`.

### F-14 Time tracking
- Data: the `time_entries` table — contact, provider, service, optional booking, start, end, calculated duration, hourly rate, calculated total, description, billable flag, invoiced flag.
- Providers log entries manually or with a start/stop timer.
- Billable, uninvoiced entries for a contact are converted into invoice lines (F-15) and marked invoiced in the same server call.
- Reports: hours and value per provider, per customer, per service.
- Packages: `time_tracking/`, `server_time/`.

### F-15 Invoicing and payments
- Invoices are generated automatically on booking completion, and created manually or from time entries, orders and packages.
- Payment terms per invoice (due on receipt, 7 days, 30 days, or set days); the due date is calculated from the issue date; default terms are set in Settings → Invoicing.
- Tax: each client sets a tax rate (default 0), a tax label and a registration number in Settings → Invoicing; the rate at issue is stored on the invoice; no tax line appears when the rate is 0.
- Lifecycle: draft → sent → paid; overdue set by a nightly task, with a reminder email to the customer; cancelled. Sequential numbering per instance (`INV-{year}-{sequence}`).
- Payments received outside the gateways (cash, bank transfer) are recorded manually; refunds are full or partial, through the original gateway or recorded manually.
- PDF generation, email delivery, payment recording, refunds.
- Gateways: one card gateway per instance (Stripe or Paystack), plus PayPal as an option. One gateway-agnostic payment service (`server_payments/`) sits behind all three; switching gateways does not change services or bookings.
- Gateway webhooks are received by HTTP endpoints, verified, applied, and their payloads written to R2 (F-29).
- Test and live modes per gateway. Money logic runs in server modules only.
- Packages: `invoices/`, `server_payments/`. Integration patterns: [[[default:specifications-global/spec-integration|Integration Specification]]](C:/dev/project-library-global/specifications-global/spec-integration.md).

### F-16 Dual currency
- System currency set at onboarding; locked once the first booking or invoice exists.
- Display currency optional per service; display only. All stored amounts, totals and reports are in the system currency.

### F-17 CRM and contacts
- One `contacts` record per person with status Lead, Customer or Inactive; activity timeline from bookings, emails, notes, forms.
- Contacts are created automatically by bookings, enquiries and landing page forms.
- Pre-built segments (New Leads, Active Customers, Inactive Customers, High Value) and a custom segment builder over tags, status, total spend and booking history.
- CSV import and export.
- Packages: `crm/`, `server_customers/`, `server_marketing/`.

### F-18 Email
- System email: Anvil built-in email (sign-in, confirmation, reset).
- Transactional email: Brevo SMTP with the client's Brevo account and the client's own domain as sender.
- Marketing email: Brevo Campaigns — sequences, broadcasts, segment targeting, open and click tracking from Brevo webhooks, unsubscribe handling.
- Brevo webhook payloads are written to R2 (F-29); only current status is kept in the data tables.
- Packages: `crm/`, `server_marketing/`. Patterns: [[[default:specifications-global/spec-integration|Integration Specification]]](C:/dev/project-library-global/specifications-global/spec-integration.md).

### F-19 Lead capture and marketing funnel
- A lead capture submission creates the lead and the contact in one server call, linked, with source recorded.
- Capture analytics per form; automatic enrolment into a sequence.
- Funnel links: blog post call-to-action → landing page → booking flow, each step recording the source.
- Packages: `landing_pages/`, `server_marketing/`.

### F-20 Tasks and automation
- Tasks linked to contacts, with due date, notes and completion timestamp.
- Automatic tasks from events (enquiry without booking, review request after appointment).
- Automation rules: trigger event + conditions + actions (create task, send email, enrol in sequence); evaluated by background task.
- Voice-created tasks arrive through F-27.
- Packages: `crm/`, `server_automation/`.

### F-21 Blog and content
- Rich text editor; post layouts classic centred, two-column with sidebar, full-width immersive; pullquotes; featured image and video.
- SEO title, description, slug and keywords per post; statuses draft, scheduled, published, archived (scheduled posts publish at their set time by background task); categories; view counts.
- Public listing in tiled grid or list-with-thumbnails view.
- Comments with moderation (approve, spam, delete), switchable in Settings.
- Packages: `blog/`, `server_blog/`.

### F-22 Clause Builder
- Clauses for Privacy Policy and Terms and Conditions: seeded clauses, custom clauses, categories, order, active flag.
- Publishing regenerates the public policy page immediately.
- Packages: `settings/`, `server_settings/`.

### F-23 Reviews and testimonials
- After a completed appointment, an automatic task and a review-request email go to the customer.
- A review is a star rating (1–5) and a comment, linked to the contact, booking and provider.
- Reviews are hidden until the client approves them; approved reviews feed the homepage testimonials section.
- The client can also type in testimonials, marked as entered by the business; up to 10 testimonials are shown in total.
- Packages: `reviews/`, `server_reviews/`; the star rating is `StarRatingComponent` in `components/`.

### F-24 Customer portal
- CustomerLayout (section 9): My Dashboard, My Bookings, My Packages, My Invoices, My Reviews, Account.
- Customers set their password from the link in their first booking confirmation.
- Customers book, reschedule, pay invoices and edit their profile; every server function filters to the signed-in customer's own records.
- No customer ticket system; customer contact goes through the contact form (F-06).
- Packages: `portal/`.

### F-25 Ease of use and in-app help
- Tooltips on every feature, with a per-user on/off setting.
- Process-flow videos attached to the screens they explain, using the video component (F-09).
- Packages: `components/`, `shared/`.

### F-26 AI assistance
- Architecture and assists: `mybizz-ai-implementation-spec.md`.
- One reusable AI helper component, `AIHelperComponent` in `components/` (`custom-component-requirements-matrix.md`), with trigger button, floating window, assist selector, dynamic working area, result area, copy, insert/replace/append into the host form.
- Context awareness: the host form passes its context; the helper preselects the matching assist.
- One shared AI request layer (`server_ai/`): prompt composition, model call, result return, error handling. Each assist is defined by identity, input fields, context mapping, prompt template and result handling.
- All ten assists are built. Assists 9 (client sentiment summary) and 10 (booking and availability insights) run on the sample data (section 16) until the client clears it, then on the client's real data; on thin real data each says so and names what is needed.
- AI requests are routed through App 5, which holds Mybizz's AI provider keys and records usage per client. Client instances hold no Mybizz AI keys.
- The core application never depends on the AI: every AI failure returns a clear message and leaves the form usable.

### F-27 WhatsUp voice
- Speech-to-text: audio is sent to the server, transcribed by Groq Whisper (through App 5, as F-26), and passed into the shared AI request layer.
- Text-to-speech: browser SpeechSynthesis by default; Groq TTS for the natural-sounding voice.
- Voice tier 1: voice input to every assist. Tier 2: voice task creation. Tier 3: read-only single-answer queries (schedule and bookings, payments and revenue, tasks, customers, navigation) with spoken answers.
- Morning briefing: a brief-generation server function reads bookings, invoices, tasks and leads; a briefing prompt produces a 20–30 second spoken summary; per-user on/off setting.
- Conversational follow-up: a short session context held in the component state is sent with each request; the session ends on close, timeout or navigation away.
- Mobile voice view, opened from a home screen shortcut; no app install.
- Voice tier 4, voice form filling: one extraction prompt per supported form (booking, contact, invoice, task, quote, deal); extracted values mapped to form fields and matched to existing records; confirmation before save; a partly filled form with the missing fields highlighted, never an error.
- Voice tier 5, business intelligence by voice: the question is mapped to a fixed set of analysis functions that run in the instance's server code against its own tables and summary tables; the figures go to the AI layer with every person's name replaced by a placeholder label, and the instance puts the names back.
- Every voice query fails gracefully with a spoken message.

### F-28 Wave accounting
- One-way, event-driven push from the instance to the client's Wave account on three events: invoice paid, new customer, refund issued.
- Customer records are created or matched in Wave before a payment is pushed.
- Connection once in Settings → Integrations; Wave credentials in the Vault.
- No pull from Wave, no sync back, no mapping screens. Optional per client.
- Packages: `server_integrations/`.

### F-29 Data, backups and history
- Each client has one Cloudflare R2 bucket, in a free Cloudflare account in the client's own name; Mybizz reaches it as an invited member. The bucket's access keys are held in the client's Vault. Detail: `integration-architecture.md` (Client Storage).
- Backups: scheduled background task exports every data table to the bucket daily; the last 7 daily and 4 weekly backups are kept.
- Webhook payloads (Stripe, Paystack, PayPal, Brevo) are written to the bucket on receipt; the data tables keep only the current state they produce.
- History is not kept in the data tables; history records go to the bucket.
- Audit trail: the last 30 days stay in the audit table; a scheduled task moves older entries to the bucket.
- View: Settings → Data reads history from the bucket and displays it. Restore: Mybizz restores data from the bucket into the tables at the Owner's request. Export: all data as CSV at any time.
- Packages: `server_storage/`.

### F-30 Security and compliance
- Server-side enforcement of every role check; rate limiting; encrypted credentials (F-02); audit trail (F-01, F-29); data isolation (section 3).
- POPIA, GDPR and PCI DSS: [[[default:specifications-global/spec-regulatory-compliance-baseline|Regulatory Compliance Baseline (Specification)]]](C:/dev/project-library-global/specifications-global/spec-regulatory-compliance-baseline.md). Card data never touches the instance; gateways handle it.

### F-31 Design system
- The Anvil Material 3 theme (dependency ID `4UK6WHQ6UX7AKELK`) with the project deviations in `DESIGN.md`.
- No hardcoded colours: every colour comes from the Colour Scheme, referenced as `%color:Name%` or `var(--anvil-m3-…)`; translucent colours are derived with `color-mix()`.
- Four palettes, each a complete Anvil M3 Colour Scheme in light and dark, held in `palette-tokens.md`; the palette module sets the `--anvil-m3-*` properties at runtime, so native and custom components change together.
- Dark mode follows the device setting on every screen ([[[default:adr-global/adr-dark-mode|Dark Mode]]](C:/dev/project-library-global/adr-global/adr-dark-mode.md) ADR); the palette module applies the palette's dark block and switches when the device setting changes.
- Status colours `status-success`, `status-pending`, `status-error` are fixed platform colours; status is always shown with its word as well as its colour.
- Two contexts (management interface, public website) from one design system.

### F-32 Help, support tickets and feedback
- A Help item in the admin navigation opens a form: type (problem or feedback), subject, message, optional screenshot.
- `server_support/` sends the message to App 5 through the management endpoint with the instance's Bearer token.
- App 5 stores the ticket or feedback against the client, notifies the founder, and holds the reply thread; replies are shown in the client's Help view.
- Response targets: within 24 hours on business days; a problem that stops a client's business is acknowledged within 4 business hours.
- No customer data is sent with a ticket unless the client includes it.

### F-33 Hosting, fair use and upgrade
- Section 4.

### F-34 Mybizz management app
- App 5, `mb-3-cs-app5`, built before the first paying client.
- Functions: client registry and registration (client reference numbers, credentials), provisioning (section 5), usage monitoring (section 4), health monitoring, billing (monthly invoices to clients), update distribution, support tickets and feedback (F-32), the AI and voice gateway (F-26, F-27).
- At-risk alerts: each client instance reports its last staff sign-in and last booking date with its usage report. App 5 alerts the founder when a client has no staff sign-in for 14 days, no bookings for 30 days, or a support ticket unanswered for 3 days.

---

## 13. Technical Stack

| Layer | Technology | Notes |
|---|---|---|
| Platform | Anvil.works | Full-stack Python, built-in hosting and HTTPS |
| UI | Material Design 3 (M3) | Dependency ID `4UK6WHQ6UX7AKELK` |
| Public routing | Anvil Routing | Dependency ID `3PIDO5P3H4VPEMPL` |
| Database | Anvil Data Tables | PostgreSQL-backed; schema in `authoritative-schema.md` |
| Authentication | Anvil Users service | Role checks by custom decorators |
| Anvil Secrets | `encryption_key` only | Used only by `vault_service.py` |
| Storage, backups, history | Cloudflare R2 | One bucket per client |
| Payments | Stripe, Paystack, PayPal | One gateway-agnostic service |
| Email | Anvil built-in; Brevo SMTP; Brevo Campaigns | Client's own Brevo account |
| AI text | Groq (provider configurable in App 5) | Called through App 5 |
| Speech | Groq Whisper (speech-to-text); browser SpeechSynthesis and Groq TTS (text-to-speech) | Groq called through App 5 |
| Accounting | Wave API | Optional, one-way |
| Video | YouTube, Vimeo, Dailymotion, direct video addresses | Embedded |
| Maps | Google Maps embed | Optional |

---

## 14. Platform Constraints

- **Skulpt client runtime:** not all of the Python standard library works in the browser. Money and date logic lives in server modules only. See [[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR.
- **No native URL routing:** public pages use the routing dependency; the admin area uses lambda/Link/`open_form`.
- **Lazy-loaded rows:** never iterate a table without `.search()` or `.limit_to()`. See [[[default:adr-global/adr-data-access-patterns|Data Access Patterns and Query Limitations]]](C:/dev/project-library-global/adr-global/adr-data-access-patterns.md) ADR.
- **Server call latency:** every `anvil.server.call()` is an HTTP round trip; user-facing flows minimise calls.
- **No WebSocket push:** real-time updates use client-side timer polling. See [[[default:adr-global/adr-real-time-and-background-tasks|Real-Time Updates and Background Task Architecture]]](C:/dev/project-library-global/adr-global/adr-real-time-and-background-tasks.md) ADR.

---

## 15. Related Specifications

| Specification | Relevance |
|---|---|
| [[[default:specifications-global/spec-anvil-platform-standards\|Anvil Platform Mechanics]]](C:/dev/project-library-global/specifications-global/spec-anvil-platform-standards.md) | Platform mechanics, server patterns, data access |
| [[[default:specifications-global/spec-anvil-spec-table\|Anvil Specification Table]]](C:/dev/project-library-global/specifications-global/spec-anvil-spec-table.md) | Anvil component and pattern reference |
| [[[default:specifications-global/spec-five-app-architecture-model\|Five-App Architecture Model (Specification)]]](C:/dev/project-library-global/specifications-global/spec-five-app-architecture-model.md) | Five-app model (Mybizz-wide) |
| [[[default:specifications-global/spec-client-activation-runbook\|Client Instance Activation Runbook]]](C:/dev/project-library-global/specifications-global/spec-client-activation-runbook.md) | Provisioning and activation |
| [[[default:specifications-global/spec-api-specification\|API Specification (HTTP Endpoints)]]](C:/dev/project-library-global/specifications-global/spec-api-specification.md) | Webhook and management endpoints |
| [[[default:specifications-global/spec-integration\|Integration Specification]]](C:/dev/project-library-global/specifications-global/spec-integration.md) | Brevo, Stripe, Paystack patterns |
| [[[default:specifications-global/spec-security-architecture\|Security & Secrets Architecture]]](C:/dev/project-library-global/specifications-global/spec-security-architecture.md) | Role limits, token security |
| [[[default:specifications-global/spec-security\|Security Specification]]](C:/dev/project-library-global/specifications-global/spec-security.md) | Security patterns, rate limiting |
| [[[default:specifications-global/spec-vault-system\|The Vault System]]](C:/dev/project-library-global/specifications-global/spec-vault-system.md) | Vault architecture |
| [[[default:specifications-global/spec-regulatory-compliance-baseline\|Regulatory Compliance Baseline (Specification)]]](C:/dev/project-library-global/specifications-global/spec-regulatory-compliance-baseline.md) | POPIA, GDPR, PCI DSS |
| `spec-nomenclature.md` | Naming conventions |

---

## 16. Sample Data

Specified in `mybizz-ai-implementation-spec.md` (Sample Data).

- Provisioning loads three months of sample data for the client's trade, in the client's system currency and app language, into every business table, and computes the summary tables from it.
- Every sample row is recorded in `sample_rows` (table name, row id).
- Sample contacts use `example.com` addresses. The email, reminder, campaign, payment and Wave services skip every sample record.
- A banner on every screen shows while `sample_rows` holds rows.
- **Clear sample data** (Owner only, on the banner and in Settings, with a confirmation dialog) runs a background task: delete every recorded row, empty the links from real records to sample records, empty `sample_rows`, recompute the summary tables, record the action in `activity_log`.
- Packages: `server_settings/` (`sample_data_service`: loading, the clearing task and the banner state).
