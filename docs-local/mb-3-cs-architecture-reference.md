---
title: "mb-3-cs-architecture-reference"
doc-id: "mb-7d6be180"
updated: "2026-11-01"
---
# Mybizz CS — Architecture Reference

## 0. Authority

This document is the build standard for the architecture of Mybizz CS. Agents build from this document. Where `mb-3-cs-concept.md` or `mb-3-cs-architecture-explainer.md` differs from this document on a technical fact, this document is correct.

Every feature carries the number and name it has in `mb-3-cs-concept.md` section 7 (F-01 to F-36); the client-facing list of every feature is `mb-3-cs-feature-list.md`. Detail that has its own home is linked, not repeated:

| Subject | Home |
|---|---|
| Data tables and fields | `docs-local/authoritative-schema.md` |
| User flows | `docs-local/user-flows.md` |
| Design system | `docs-local/DESIGN.md`; palette colour values in `docs-local/palette-tokens.md` |
| Custom components | `docs-local/custom-component-requirements-matrix.md` |
| AI, voice and sample data | `docs-local/mybizz-ai-implementation-spec.md` |
| Calendar | `docs-local/calendar-feature-spec.md` |
| Email and the Team Inbox | `docs-local/team-inbox-spec.md` |
| Social media sharing | `docs-local/sm-syndication-spec.md` |
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
| App 4 | Client instances, one per client | `[client-name]-cs` | One Mybizz-owned Anvil Business account per client | the client's own domain |
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
- Each client instance is cloned from App 3 into the client's own Mybizz-owned hosting account.
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

- Each client instance is a separate Anvil app with a separate database, on its own Anvil account. `app_tables` always resolves to the current client's data. Cross-client access is architecturally impossible.
- `master_template` holds no client data. All state lives in the client's own data tables and the client's own R2 bucket.
- Mybizz does not copy a client's customer data into App 5. App 5 holds client-level records only: registry, usage figures, health, billing, tickets and feedback.

---

## 4. Hosting and Capacity (F-33)

- **Plan:** every hosting account is an Anvil Business account owned by Mybizz. The Hobby plan is never used.
- **One account per client:** each client instance is the only client app on its hosting account and has the account's full Business limits: 1,000,000 data table rows and 100,000 Anvil emails a month (unused: email goes through Amazon SES, F-18).
- **Usage reporting:** each client instance reports its row count, storage, email volume, heavy-job activity, last staff sign-in and last booking date to App 5 through the management endpoints, on a schedule.
- **Heavy jobs:** imports, exports, backups, AI generation, briefings, campaign sending and inbound email processing run as Anvil background tasks.
- **Capacity:** a hard limit of 100 client instances for Mybizz CS.

---

## 5. Provisioning and Registration (F-03, F-34)

1. The client registers. App 5 creates the client record and the client reference number. As part of registration, the client opens a free Cloudflare account in the client's own name, adds a payment method, switches R2 on, adds the client's domain to Cloudflare DNS, and invites Mybizz's Cloudflare user as a member with the roles Cloudflare R2 Admin and DNS.
2. Mybizz opens a new Anvil Business account for the client.
3. Mybizz clones App 3 into that account as `[client-name]-cs`.
4. Mybizz sets the instance's Anvil Secret (`encryption_key`) and the instance's Bearer token. App 5 accepts the client's Cloudflare invitation, creates the client's R2 bucket and a token for that bucket only, creates the client's Amazon SES tenant, domain identity, receipt rule, SNS topics and scoped AWS keys (`team-inbox-spec.md`), and writes every key into the instance's Vault. Anvil's Email service in the instance is set to Custom Mail Server with the SES SMTP endpoint. The Bearer token and the client's Mybizz-side credentials are stored in App 5.
5. App 5 loads the sample data for the client's trade (section 16).
6. The client completes the onboarding wizard in the instance (F-03); its domain step has App 5 write the client's DNS records (website, MX, DKIM, MAIL FROM, SPF, DMARC) through the Cloudflare API.

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
  portal/         ai/             support/        inbox/
  sales/          accounts/

server_code/
  server_analytics/  server_auth/       server_blog/       server_bookings/
  server_customers/  server_dashboard/  server_marketing/  server_payments/
  server_services/   server_settings/   server_shared/
  server_orders/     server_packages/   server_time/       server_reviews/
  server_automation/ server_ai/         server_storage/    server_inbox/
  server_support/    server_management/ server_sales/      server_accounts/
```

- The client-side CRM and marketing package is `crm/`; the server-side package is `server_marketing/` ([[[mb-3-cs-project-context:adr-local/crm-package-replaces-marketing|Package Name: crm/ Replaces marketing/]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/adr-local/crm-package-replaces-marketing.md) ADR).
- `server_payments/` holds invoices, payments, refunds and every gateway.
- `server_storage/` holds all Cloudflare R2 access (F-29).
- `server_inbox/` holds the Team Inbox: addresses, received email, conversations and the SES endpoints (F-18).
- `server_shared/email_service` sends every outgoing email through Amazon SES (F-18).
- `server_sales/` holds deals, companies and quotes (F-35).
- `server_accounts/` holds expenses, the accounts reports and the accounting export (F-28).
- `shared/translations/` holds one translation module per app language; every screen text is read from it (F-25).
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
| Admin and customer portal | Native M3 layouts (`NavigationRailLayout` / `NavigationDrawerLayout`) + the layouts' `NavigationLink` components + lambda click handlers |
| Public website pages | Anvil Routing dependency (`@router.route`) |

`HTMLTemplate` is banned; all layout shells use the native M3 layouts. Sidebar navigation uses the layouts' `NavigationLink` components with lambda click handlers that capture their loop variables (`lambda e, fn=form_name: self._nav_click(fn)`); parameterised navigation uses `open_form()`. `navigate_to` is never used. See [[[default:adr-global/adr-htmltemplate-use|HTMLTemplate Use]]](C:/dev/project-library-global/adr-global/adr-htmltemplate-use.md) ADR, [[[default:adr-global/adr-navigation-lambda-link-open-form|Navigation Standard: Lambda/Link/open_form]]](C:/dev/project-library-global/adr-global/adr-navigation-lambda-link-open-form.md) ADR, [[[default:adr-global/adr-ui-customization-approach|UI Customization Approach]]](C:/dev/project-library-global/adr-global/adr-ui-customization-approach.md) ADR.

### AdminLayout navigation
```
Dashboard                          (always)
Inbox                              (every staff role; unread count)
▼ Sales & Operations
  Bookings                         (always)
  Services                         (always)
  Orders                           (always)
  Packages                         (always)
  Deals                            (always)
  Quotes                           (always)
▼ Customers & Marketing
  Contacts                         (always)
  Companies                        (always)
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
  Expenses                         (always)
  Accounts                         (always)
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
My Quotes                          (always)
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
- Every other credential (gateway keys, AWS keys for Amazon SES and S3, the SES SMTP password, R2 keys) is encrypted in the Vault's data table with that key.
- Owner-only; every view requires a TOTP code with no remembered session; every access emails the owner; after 5 failed codes the Vault locks for 15 minutes and the owner is emailed.
- Detail: `mb-3-cs-vault-implementation.md`, [[[default:specifications-global/spec-vault-system|The Vault System]]](C:/dev/project-library-global/specifications-global/spec-vault-system.md).

### F-03 Registration, onboarding and setup
- Registration happens in App 5 (section 5); App 5 issues the client reference number.
- Onboarding in the instance is a resumable wizard on `NavigationRailLayout` with a Sidesheet, a progress indicator and a help video on every step: app language → business profile → brand palette → system currency → legal acknowledgement (timestamped) → payment gateway → connect the client's domain (website and email DNS records, written by App 5 and checked until live) → import existing contacts.
- The contact import reads a CSV file, with ready-made column mappings for the exports of HubSpot, Mailchimp and Acuity and a manual mapping for any other file; it matches existing contacts by email.
- The AI helper's bio/profile writer offers the first website text from the business profile.
- Progress is saved after each step; onboarding and Settings link to each other.
- Packages: `onboarding/`, `server_settings/`. Engineering plan: [[[default:specifications-global/spec-onboarding-implementation-plan|Mybizz CS Onboarding System - Engineering Plan]]](C:/dev/project-library-global/specifications-global/spec-onboarding-implementation-plan.md).

### F-04 Dashboard and analytics
- Metric cards (revenue, bookings, customers) with current value, percentage change and trend; trend charts; activity feed; period comparison (week, month, custom range).
- Dashboard figures are read from pre-computed summary tables, refreshed by background task, not computed on page load.
- Advanced analytics: revenue by service and by provider, marketing return on investment per campaign and landing page, PDF and CSV export, scheduled reports emailed by background task.
- The sample data banner and the Owner's Clear sample data button (section 16).
- Packages: `dashboard/`, `analytics/`, `server_dashboard/`, `server_analytics/`.

### F-05 Settings Hub
- One tabbed form; tabs: Business Profile (including the app language), Brand Palette, System Currency (read-only), Display Currency, Legal Acknowledgement (read-only), Payment Gateways (test/live), Invoicing (default payment terms, numbering, quote validity), Users and Permissions, Email (addresses, desks, members, signatures, auto-replies), Domain (DNS record status), Theme, Feature Switches (`marketing_enabled`, `blog_enabled`, comments), Legal Pages (Clause Builder, F-22), Data (history view, export, sample data, F-29). The Invoicing tab also holds the tax rate (default 0), tax label and tax registration number; the Theme tab holds the public website header style only.
- Tabs are shown per role: Owner sees all; Manager sees operational tabs, not financial or security tabs.
- Packages: `settings/`, `server_settings/`.

### F-06 Public website
- Public pages are served through Anvil Routing: Home, About, Contact, Services listing and detail, Blog listing and post, Privacy Policy, Terms and Conditions, landing pages.
- The contact form creates a contact and a follow-up task (F-17, F-20).
- Google Maps embed on Contact and in the footer, optional.
- Meta tags, semantic structure and clean addresses on every public page; fully responsive. Every public route sets its title, description and Open Graph tags on the server through the Routing dependency's `Route.meta` (`sm-syndication-spec.md`).
- Front-end editing: for an Owner or Manager signed in, every public page shows an Edit button. Edit mode makes each text, image and video element of the page editable in place; the AI helper writes or rewrites the selected text; Save writes the page's content (`pages.components`, `config.home_config`, `landing_pages.config`, `blog_posts.content`) in one server call and publishes it; Cancel discards. Images are uploaded to `files`. Every save is recorded in `activity_log`.
- The instance runs on the client's own domain: an Anvil custom domain on the instance's environment, with a DNS `A` record written by App 5 in the client's Cloudflare DNS (section 5).
- Packages: `public_pages/`, `layouts/`, `components/` (the edit mode).

### F-07 Homepage templates
- Four layouts — Classic, Services, Booking, Minimalist — plus further layouts, all composed from the same section components; a layout is a section order and visibility set.
- Optional sections render only when configured: video (a video address is set), services carousel (active services exist).
- Packages: `public_pages/`, `components/`.

### F-08 Landing pages
- Templates: Lead Capture, Event Registration, Video Sales Letter, on a blank layout with no header or footer.
- A landing page is created in LandingPageEditorForm (template, slug, tracking); its content is edited on the page itself in edit mode (F-06).
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
- Invoices are generated automatically on booking completion and on quote acceptance (F-35), and created manually or from time entries, orders and packages.
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
- One `contacts` record per person with status Lead, Customer or Inactive, optionally linked to a company (F-35); activity timeline from bookings, emails (received and sent, F-18), quotes, deals, notes, forms.
- Contacts are created automatically by bookings, enquiries, landing page forms and Add as lead in the Team Inbox.
- Pre-built segments (New Leads, Active Customers, Inactive Customers, High Value) and a custom segment builder over tags, status, total spend and booking history.
- CSV import and export.
- Packages: `crm/`, `server_customers/`, `server_marketing/`.

### F-18 Email
- Build detail: `team-inbox-spec.md`.
- Every email received and sent goes through Amazon SES in Mybizz's AWS account (`eu-west-1`), one SES tenant per client. Anvil's own email quota is not used.
- Receiving: the client domain's MX record points to SES; SES scans each email, stores it in S3 and notifies the instance's endpoint `/_/api/email/inbound`; a background task stores it in `email_messages`, moves attachments to R2 and links it to a contact.
- Team Inbox (`inbox/TeamInboxForm`): unlimited personal and desk addresses (`email_addresses`); each staff member sees the addresses the staff member belongs to; Inbox, Drafts, Sent, Archive, Spam, Trash; reply, reply all, forward; sort, filter, search; star, mark as unread; bulk actions; conversations by `Message-ID`; Add as lead; AI assist 6 drafts replies.
- Sending: `server_shared/email_service` sends every email through the SES API from the client's domain, DKIM-signed: Team Inbox email, transactional email (confirmations, reminders, invoices, receipts) and marketing email. Anvil's Users service email goes through Anvil's Email service set to Custom Mail Server with the SES SMTP endpoint.
- Marketing email: sequences, broadcasts and newsletters, segment targeting, SES open and click tracking, one-click unsubscribe (`List-Unsubscribe` and `List-Unsubscribe-Post`), suppression of bounces and complaints (`email_suppressions`), sending in batches within the SES rate.
- SES events and inbound notifications are recorded in `webhook_log` (`gateway` = `ses`) with their payloads in R2 (F-29).
- Packages: `inbox/`, `crm/`, `server_inbox/`, `server_marketing/`, `server_shared/`.

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
- New posts are written in BlogEditorForm; published posts are corrected in edit mode on the page itself (F-06).
- Social media sharing: the Share panel, AI captions per platform (`blog_posts.share_captions`), platform share links, `?ref=` tracking, the `/_/api/blog-image/{slug}` endpoint and reader share buttons, as `sm-syndication-spec.md` specifies.
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
- CustomerLayout (section 9): My Dashboard, My Bookings, My Packages, My Quotes, My Invoices, My Reviews, Account.
- Customers set their password from the link in their first booking confirmation.
- Customers book, reschedule, pay invoices, accept or decline quotes and edit their profile; every server function filters to the signed-in customer's own records.
- No customer ticket system; customer contact goes through the contact form (F-06).
- Packages: `portal/`.

### F-25 Ease of use, in-app help and language
- Every feature carries a tooltip; the tooltip opens the feature's short help video in a pop-up, using the video component (F-09); per-user on/off setting.
- Help videos are held per language; the pop-up plays the video in the app language.
- App language: `business_profile.app_language` (`en` / `fr` / `pt` / `es` / `de` / `it`). English is complete at launch; a language is offered once its translation is complete and checked.
- Translation: every piece of screen text is read from `shared/translations/` — one module per language, one entry per phrase key — and is never typed into the Designer; component text is set in code from the phrase key. Email templates are stored per language (`email_templates.language`). AI (Groq) produces each translation; a native speaker checks each language once. No right-to-left language is offered.
- Packages: `components/`, `shared/`.

### F-26 AI assistance
- Architecture and assists: `mybizz-ai-implementation-spec.md`.
- One reusable AI helper component, `AIHelperComponent` in `components/` (`custom-component-requirements-matrix.md`), with trigger button, floating window, assist selector, dynamic working area, result area, copy, insert/replace/append into the host form.
- Context awareness: the host form passes its context; the helper preselects the matching assist.
- One shared AI request layer (`server_ai/`): prompt composition, model call, result return, error handling. Each assist is defined by identity, input fields, context mapping, prompt template and result handling.
- Two further AI functions use the same helper: the website-text assist in edit mode (F-06, F-08, F-21) and the social caption assist (F-21, `sm-syndication-spec.md`).
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

### F-28 Accounts
- Expenses (`expenses`, `expense_categories`): date, supplier, category, amount, tax, payment method, notes and a receipt image stored in R2 (`expenses/receipts/`).
- Reports, computed in server code: profit and loss by month (payments less refunds, against expenses), outstanding invoices, tax collected (invoices) and tax paid (expenses), customer statements (PDF).
- Export: one click produces one CSV file per record type — customers, invoices, invoice lines, payments, refunds, expenses — for a chosen period, in the import columns of Wave, Xero or QuickBooks; dates `YYYY-MM-DD`, plain decimal amounts, no currency symbols, UTF-8; the files are written to R2 (`exports/`) and downloaded.
- Sample data is never exported.
- Packages: `accounts/`, `server_accounts/`.

### F-29 Data, backups and history
- Each client has one Cloudflare R2 bucket, in a free Cloudflare account in the client's own name; Mybizz reaches it as an invited member. The bucket's access keys are held in the client's Vault. Detail: `integration-architecture.md` (Client Storage).
- Folders of the bucket: `backups/daily/`, `backups/weekly/`, `history/{table}/`, `audit/{year}/{month}/`, `webhooks/{service}/{year}/{month}/`, `email/messages/{address}/{year}/{month}/`, `email/attachments/`, `expenses/receipts/`, `exports/`.
- Backups: scheduled background task exports every data table to the bucket daily; the last 7 daily and 4 weekly backups are kept.
- Webhook payloads (Stripe, Paystack, PayPal, Amazon SES) are written to the bucket on receipt; the data tables keep only the current state they produce.
- Emails: bodies older than 90 days move to the bucket nightly and the row stays as an index; attachments are always in the bucket.
- Exit: Mybizz writes the full export, `encryption_key` and the decrypted Vault into the bucket, then leaves the client's Cloudflare account.
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

### F-33 Hosting
- Section 4.

### F-34 Mybizz management app
- App 5, `mb-3-cs-app5`, built before the first paying client.
- Functions: client registry and registration (client reference numbers, credentials), provisioning (section 5) including the Anvil account, R2 bucket, Amazon SES identity and DNS records, usage monitoring (section 4), health monitoring, billing (monthly invoices to clients), update distribution, support tickets and feedback (F-32), the AI and voice gateway (F-26, F-27), the demo apps (F-36).
- App 5 holds Mybizz's AWS account access (SES, S3, SNS) and Mybizz's Cloudflare user.
- At-risk alerts: each client instance reports its last staff sign-in and last booking date with its usage report. App 5 alerts the founder when a client has no staff sign-in for 14 days, no bookings for 30 days, or a support ticket unanswered for 3 days.

### F-35 Sales pipeline, companies and quotes
- Companies (`companies`): a business customer; contacts link to it through `contacts.company_id`.
- Deals (`deals`): title, contact, company, owner, stage (`lead` / `qualified` / `proposal` / `negotiation` / `won` / `lost`), value, expected close date, linked quote; a board view by stage and a list view; Staff see the deals they own, enforced on the server.
- Quotes (`quotes`, `quote_items`): numbered `QT-{year}-{sequence}`, lines, the client's tax rate, validity date, PDF; sent by email with a link to `/quote/{token}`, where the customer accepts or declines; acceptance creates the invoice in the same server call, links it to the quote and moves a linked deal to `won`; a quote past its validity date becomes `expired` nightly.
- Quotes and deals appear on the contact's timeline (F-17).
- Packages: `sales/`, `server_sales/`.

### F-36 Demo apps
- Two instances of Mybizz CS on Mybizz's development account: `demo.mybizz.live` (sample data, section 16) and `try.mybizz.live` (empty), each with `config.demo_mode` set.
- A scheduled task resets both instances every night: every table is emptied, and the demo instance's sample data is loaded again.
- In demo mode, `email_service` sends every email to the signed-in visitor's own address, whatever its recipient; payments use each gateway's test keys and test cards; the Vault, Users and Permissions and Domain tabs are read-only.
- Visitors register for a demo sign-in with their email address; demo sign-ins are removed at the nightly reset.

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
| Email | Amazon SES (sending, receiving, tracking) | Mybizz's AWS account, one tenant per client; Anvil's Email service set to the SES SMTP endpoint |
| AI text | Groq (provider configurable in App 5) | Called through App 5 |
| Speech | Groq Whisper (speech-to-text); browser SpeechSynthesis and Groq TTS (text-to-speech) | Groq called through App 5 |
| Translation | `shared/translations/` | One module per app language |
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
| [[[default:specifications-global/spec-integration\|Integration Specification]]](C:/dev/project-library-global/specifications-global/spec-integration.md) | Stripe, Paystack, PayPal patterns |
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
- Sample contacts use `example.com` addresses. The email, reminder, campaign and payment services and the accounts export skip every sample record.
- A banner on every screen shows while `sample_rows` holds rows.
- **Clear sample data** (Owner only, on the banner and in Settings, with a confirmation dialog) runs a background task: delete every recorded row, empty the links from real records to sample records, empty `sample_rows`, recompute the summary tables, record the action in `activity_log`.
- Packages: `server_settings/` (`sample_data_service`: loading, the clearing task and the banner state).
