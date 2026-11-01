---
title: "product-requirements-document"
doc-id: "mb-ad69f2ae"
updated: "2026-11-01"
---
# Mybizz CS — Product Requirements Document (PRD)

What the product does, for whom, and how its success is measured.

**Standard:** [[[default:docs-standard-global/docstd-product-requirements-document]]](C:/dev/project-library-global/docs-standard-global/docstd-product-requirements-document.md)

**Status:** Draft
**Date:** 2026-10-31
**Author:** Mybizz founder
**Derived from:** `business-requirements-document.md`. The features are those of `mb-3-cs-concept.md` section 7 (F-01 to F-34); the flows are those of `user-flows.md` (UF-01 to UF-38); the technical specification of each feature is `mb-3-cs-architecture-reference.md` section 12. Where this document differs from those, they are correct.

**Certainty tags:** `[CLEAR]` / `[PARTIAL]` / `[INFERRED]` / `[MISSING]`, as in the BRD.

---

## 1. Overview

Mybizz CS is an online business platform for consulting businesses and service businesses. Each client receives its own application instance on its own domain: a public website with booking, ordering and lead capture; an admin area for the client's staff to run bookings, orders, packages, time, invoices, customers, marketing, content and settings; a customer portal for the client's customers; an AI helper on every relevant form; and WhatsUp, a voice view on the phone. Mybizz runs every client from its own management app (App 5), which provisions, monitors, bills, supports and updates them.

---

## 2. Goals and Non-Goals

### Goals
- Every need in concept section 4.5 met by a built feature, in one product, at one price (BR-1 to BR-10). `[CLEAR]`
- Every client isolated in its own instance, owning and able to export its data (BR-11, BR-12). `[CLEAR]`
- A client goes from registration to a working business without help (BR-13, BR-17). `[CLEAR]`
- Mybizz operates 100 clients from App 5, built before the first paying client (BR-14 to BR-19). `[CLEAR]`

### Non-Goals
- A marketplace, directory or any shared discovery between clients. `[CLEAR]`
- Retail commerce: catalogue, cart, inventory, shipping. `[CLEAR]`
- Hospitality reservations; memberships and subscription billing for clients' businesses. `[CLEAR]`
- A ticket system for clients' customers; customers use the contact form. `[CLEAR]`
- Two-way accounting sync; Wave receives a one-way push only. `[CLEAR]`
- A native mobile app. `[CLEAR]`

---

## 3. Target Users

| User type | Primary need | Frequency of use |
|---|---|---|
| Owner | Set up and run the whole business; see its performance; protect its credentials | Daily |
| Manager | Run operations and marketing; manage staff | Daily |
| Admin | Handle bookings, orders and customers | Daily |
| Staff (provider) | See and work the provider's own calendar; log time | Daily |
| Customer | Book, order, pay, review; see own bookings, packages and invoices | Occasional |
| Visitor / Campaign Visitor | Learn about the business; enquire; respond to an offer | Occasional |
| Mybizz (founder, App 5) | Register, provision, monitor, bill, support and update every client | Daily |

The project has several user types and needs role-based access control (section 6, Security); the access matrix belongs to the test plan as the project's `access-matrix.yaml` (test IN-02).

---

## 4. User Stories / Use Cases

| # | User Story | Flow | Priority | Certainty |
|---|---|---|---|---|
| US-1 | As a new client, I want to register online and receive a working instance on my own domain, so that I can start without technical help | UF-01 | Must | [CLEAR] |
| US-2 | As an Owner, I want a guided, resumable setup in the natural order, so that I reach a working business in one sitting or several | UF-02 | Must | [CLEAR] |
| US-3 | As an Owner or Manager, I want to invite staff with a role, so that each person sees only what the role allows | UF-03 | Must | [CLEAR] |
| US-4 | As an Owner, I want my payment, email and integration keys in an encrypted Vault that needs a code every time, so that no one else can use them | UF-04 | Must | [CLEAR] |
| US-5 | As a customer, I want to book an appointment at the business, at my location, by video or by phone, and pay by the option the business offers, so that I never have to phone | UF-05, UF-06 | Must | [CLEAR] |
| US-6 | As a customer, I want to book places in a group session, so that I can join a class or workshop | UF-07 | Must | [CLEAR] |
| US-7 | As a customer, I want to be offered only times when the provider and the needed room or equipment are both free, so that my booking is certain | UF-08 | Must | [CLEAR] |
| US-8 | As a customer, I want to order and pay for a fixed-price deliverable with my brief, so that I get the work without an appointment | UF-09 | Must | [CLEAR] |
| US-9 | As a client's staff member, I want to move an order from ordered to in progress to delivered, with files and a message, so that the customer is kept informed | UF-12 | Must | [CLEAR] |
| US-10 | As a customer, I want to buy a package of sessions and book against its balance, so that I pay once for several sessions | UF-10 | Must | [CLEAR] |
| US-11 | As a client's staff member, I want the day's metrics, alerts and calendar in one place, so that I can run the day | UF-11 | Must | [CLEAR] |
| US-12 | As a provider, I want to log the time I work for a customer, and as an Owner turn billable hours into an invoice, so that hourly work is billed | UF-13 | Must | [CLEAR] |
| US-13 | As an Owner, I want invoices with payment terms and my tax setting, payment recording and refunds, so that I am paid professionally | UF-14, UF-15 | Must | [CLEAR] |
| US-14 | As an Owner, I want to edit my website pages and publish blog posts, so that my site stays current | UF-16 | Must | [CLEAR] |
| US-15 | As an Owner, I want to choose and configure my homepage layout, video and testimonials, so that the site presents my business well | UF-17 | Must | [CLEAR] |
| US-16 | As an Owner, I want landing pages that turn campaign visitors into leads and contacts, so that marketing produces customers | UF-18, UF-20 | Must | [CLEAR] |
| US-17 | As a visitor, I want to send the business a message, so that I can ask a question | UF-19 | Must | [CLEAR] |
| US-18 | As an Owner, I want one record per customer, with segments and CSV import and export, so that my customer data is complete | UF-21, UF-22 | Must | [CLEAR] |
| US-19 | As an Owner, I want email sequences and broadcasts to segments, so that I stay in touch with customers | UF-23, UF-24 | Must | [CLEAR] |
| US-20 | As an Owner, I want revenue, bookings, customers and marketing return by period, service and provider, so that I know how the business performs | UF-11, UF-25 | Must | [CLEAR] |
| US-21 | As an Owner, I want tasks and automation rules, so that follow-ups happen without my remembering them | UF-26 | Must | [CLEAR] |
| US-22 | As a customer, I want to leave a review, and as an Owner approve reviews and add testimonials, so that the website shows real trust | UF-27 | Must | [CLEAR] |
| US-23 | As an Owner, I want to build my Privacy Policy and Terms and Conditions from pre-written clauses, so that I have legal pages without a lawyer drafting from nothing | UF-28 | Must | [CLEAR] |
| US-24 | As a customer, I want a portal with my bookings, packages, invoices, reviews and account, so that I can manage my dealings with the business myself | UF-29 | Must | [CLEAR] |
| US-25 | As a client's staff member, I want an AI helper on the form I am using, so that I write faster without leaving the page | UF-30 | Must | [CLEAR] |
| US-26 | As a client's staff member, I want to speak to the app from my phone — create tasks, ask questions, hear a morning briefing — so that I can run the business on the move | UF-31 | Must | [CLEAR] |
| US-27 | As an Owner, I want paid invoices, new customers and refunds sent to my Wave account, so that my books follow without re-typing | UF-32 | Could | [CLEAR] |
| US-28 | As an Owner, I want to view my history, export all my data, and have Mybizz restore from my backups, so that my data is never lost or held hostage | UF-33 | Must | [CLEAR] |
| US-29 | As a client user, I want to send Mybizz a problem or feedback from inside the app, so that I get help quickly | UF-34 | Must | [CLEAR] |
| US-30 | As an Owner, I want to see my usage and move to a dedicated account when I outgrow the shared one, so that my business can grow | UF-35 | Must | [CLEAR] |
| US-31 | As Mybizz, I want every client's usage, health and at-risk signals in one app, so that I can act before a client is harmed or leaves | UF-36 | Must | [CLEAR] |
| US-32 | As Mybizz, I want one release to reach every client, so that every client has every fix at once | UF-37 | Must | [CLEAR] |
| US-33 | As any user, I want a clear path when something fails — payment, session, AI, an integration — so that I am never stuck | UF-38 | Must | [CLEAR] |
| US-34 | As an Owner, I want my app and website in my brand palette, in light or dark following my device, so that the business looks like mine | UF-02 | Must | [CLEAR] |
| US-35 | As a client user, I want tooltips and short videos where I am working, so that I learn the app without asking for support | UF-11 | Must | [CLEAR] |
| US-36 | As an Owner, I want to show a service's price in a second currency, so that foreign customers understand it, while my accounts stay in one currency | UF-05 | Must | [CLEAR] |

---

## 5. Functional Requirements

| # | Requirement | Satisfies User Story | Acceptance Criteria | Certainty |
|---|---|---|---|---|
| FR-01 | F-01 Accounts, roles and access: email and password sign-in, set-password links, five roles enforced on the server, lockout, 30-minute session timeout, audit trail | US-3, US-33 | A Staff user's call to an Owner-only function is refused by the server; no password is ever emailed; after 30 minutes idle the user signs in again and returns to the same place; every role change is in the audit table | [CLEAR] |
| FR-02 | F-02 The Vault: every credential encrypted; Owner only; TOTP on every view; email on every access; 15-minute lock after 5 wrong codes | US-4 | Opening the Vault twice in a row asks for a code twice; each opening emails the Owner; the sixth attempt after 5 wrong codes is refused for 15 minutes | [CLEAR] |
| FR-03 | F-03 Registration and onboarding: registration in App 5 issues the client reference; 8-step resumable onboarding linking to Settings | US-1, US-2 | A registered client has a reference number and an instance on its domain; an Owner who leaves at step 5 returns to step 5; Continue at steps 4 and 5 stays disabled until confirmed | [CLEAR] |
| FR-04 | F-04 Dashboard and analytics: metric cards, trends, activity feed, period comparison, revenue by service and provider, marketing return, PDF and CSV export, scheduled reports, the upgrade view | US-20, US-30 | Dashboard figures match the summary tables; an export opens as PDF and as CSV; a scheduled report arrives by email | [CLEAR] |
| FR-05 | F-05 Settings Hub: one form, 14 tabs, shown per role | US-2, US-3 | A Manager sees no financial or security tab; the Owner sees all 14 | [CLEAR] |
| FR-06 | F-06 Public website on the client's domain: Home, About, Contact, Services, Blog, Privacy Policy, Terms and Conditions, footer, optional map | US-14, US-17 | Every public page loads on the client's domain at its clean address, works at compact and expanded widths, and carries meta tags | [CLEAR] |
| FR-07 | F-07 Homepage templates: Classic, Services, Booking, Minimalist and further layouts from one section set | US-15 | Switching the layout changes section order and visibility only; an optional section with no content does not render | [CLEAR] |
| FR-08 | F-08 Landing pages: Lead Capture, Event Registration, Video Sales Letter, on a blank layout, with views and conversions counted | US-16 | A published landing page has no site header or footer; each visit and each submission increments its counters | [CLEAR] |
| FR-09 | F-09 Visual and media presentation: parallax hero, carousels, one video component for YouTube, Vimeo, Dailymotion and direct addresses | US-15 | A section with no video address is hidden; parallax stops when the device asks for reduced motion | [CLEAR] |
| FR-10 | F-10 Service catalogue: duration or unit pricing, delivery modes, providers, meeting types, intake form, payment option, display currency | US-5 | A `duration` service offers slots; a `unit` service offers Order and no slot | [CLEAR] |
| FR-11 | F-11 Bookings and scheduling: the booking flow, all delivery modes, group sessions, resources, payment options, the 15-minute payment hold, reminders, recurring bookings, statuses, calendar, double-booking prevention | US-5, US-6, US-7, US-11, US-33 | Two customers confirming the same slot at once produce one booking and one offer of the nearest free slots; a booking with failed payment is released after 15 minutes; completing a booking issues its invoice and review request; a full group session cannot be booked | [CLEAR] |
| FR-12 | F-12 Orders for delivered work: ordered → in progress → delivered, files and message, email at each change | US-8, US-9 | Each status change emails the customer; a delivered order carries its files | [CLEAR] |
| FR-13 | F-13 Session packages: definition, purchase, balance, draw-down, return within the cancellation window, expiry | US-10 | Booking with a package reduces its balance by one; cancelling within the window restores it; an expired package offers no sessions | [CLEAR] |
| FR-14 | F-14 Time tracking: manual entries and timer, billable flag, conversion to invoice lines, reports | US-12 | Entries converted to an invoice are marked invoiced in the same call and cannot be invoiced twice | [CLEAR] |
| FR-15 | F-15 Invoicing and payments: automatic and manual invoices, payment terms, client tax setting, lifecycle with nightly overdue, sequential numbering, PDF, manual payments, refunds, Stripe or Paystack plus PayPal behind one payment service, webhooks | US-13, US-5 | With tax rate 0 no tax line appears; an invoice past its due date is `overdue` the next morning and the customer is reminded; a duplicate webhook changes nothing; numbering has no gaps or repeats | [CLEAR] |
| FR-16 | F-16 Dual currency: system currency locked after the first transaction; optional display currency per service | US-36 | The system currency cannot be changed once a booking or invoice exists; every stored amount is in the system currency | [CLEAR] |
| FR-17 | F-17 CRM and contacts: one record per person, automatic creation, timeline, pre-built and custom segments, CSV import and export | US-18, US-17 | A booking, order, enquiry or capture with an existing email updates that contact instead of creating a second | [CLEAR] |
| FR-18 | F-18 Email: system, transactional through the client's Brevo SMTP, marketing through Brevo Campaigns, tracking and unsubscribe by webhook | US-19 | A confirmation email arrives from the client's domain with a calendar invite; an unsubscribed contact receives no further marketing email | [CLEAR] |
| FR-19 | F-19 Lead capture and funnel: lead and contact created together, sequence enrolment, source carried from blog to landing page to booking | US-16 | A capture creates one `leads` row and one linked `contacts` row in one call, and enrols the contact in the welcome sequence | [CLEAR] |
| FR-20 | F-20 Tasks and automation: manual and automatic tasks, automation rules evaluated by background task, voice-created tasks | US-21 | An automation for "booking completed" runs once per completed booking and records its run | [CLEAR] |
| FR-21 | F-21 Blog and content: editor, three post layouts, SEO, statuses including scheduled, categories, comments with moderation | US-14 | A scheduled post is public within 15 minutes of its time; a pending comment is not public | [CLEAR] |
| FR-22 | F-22 Clause Builder: seeded and custom clauses, categories, order, active flag, immediate publishing | US-23 | A seeded clause can be edited but not deleted; publishing updates the public page at once | [CLEAR] |
| FR-23 | F-23 Reviews and testimonials: review request after completion, approval before display, typed-in testimonials, up to 10 shown | US-22 | An unapproved review never appears on the website; no more than 10 testimonials are shown | [CLEAR] |
| FR-24 | F-24 Customer portal: My Dashboard, My Bookings, My Packages, My Invoices, My Reviews, Account | US-24 | A customer never receives another customer's record from any server function | [CLEAR] |
| FR-25 | F-25 In-app help: tooltips with a per-user switch; process-flow videos where the user works | US-35 | Switching tooltips off hides them for that user only | [CLEAR] |
| FR-26 | F-26 AI assistance: one AI helper component, ten assists, context preselection, insert, replace or append, requests through App 5; sample data from the first day, cleared by the Owner with one button | US-25, US-33 | With App 5 unreachable the helper shows a clear message and the form keeps working; assists 9 and 10 give results on the sample data; Clear sample data removes every sample row and leaves every row the client created | [CLEAR] |
| FR-27 | F-27 WhatsUp voice: voice into assists, voice tasks, read-only voice questions with spoken answers, voice form filling, business intelligence by voice, morning briefing, follow-up questions, natural-sounding voice, home screen shortcut | US-26 | "Create a task — call Priya on Thursday" creates the task and confirms it aloud; "New booking — Sarah Johnson, Swedish massage, Friday 2pm" fills in the booking form and saves nothing until the user confirms; "Who are my top 5 clients by spend?" is answered from the instance's own figures and no name reaches the AI layer; a question whose data cannot be read is answered with a clear spoken message | [CLEAR] |
| FR-28 | F-28 Wave: one-way push of invoice paid, new customer, refund issued; credentials in the Vault | US-27 | A paid invoice appears in Wave with its customer; nothing is read back from Wave | [CLEAR] |
| FR-29 | F-29 Data, backups and history: one R2 bucket per client, daily backups, webhook payloads and history in R2, 30 days of audit in the app, history view, restore by Mybizz, CSV export | US-28 | The audit table holds no entry older than 30 days; Settings → Data shows older entries from R2; a full CSV export completes as a background task | [CLEAR] |
| FR-30 | F-30 Security and compliance: server-side role checks, rate limiting, encrypted credentials, audit trail, data isolation, POPIA, GDPR, PCI DSS | US-4, US-24 | No card number is stored in any table; no credential appears in any log; rate-limited sign-in refuses repeated attempts | [CLEAR] |
| FR-31 | F-31 Design system: Anvil M3 theme with the project deviations, four palettes, dark mode following the device, status badges with words | US-34 | Changing the palette changes every screen without reload; switching the device to dark changes every screen; no hardcoded colour is found by the UI scans | [CLEAR] |
| FR-32 | F-32 Help, tickets and feedback: Help form to App 5, replies in the Help view, response targets | US-29 | A sent message shows a reference number and appears in App 5; a reply in App 5 appears in the client's Help view and is emailed | [CLEAR] |
| FR-33 | F-33 Hosting, fair use and upgrade: two instances per Mybizz-owned Business account, cap of 500,000 rows and 50 GB per client, usage reported, upgrade to a dedicated account | US-30, US-31 | An instance reports its usage daily; at 80% of its cap Mybizz is warned; an upgrade moves the instance and its domain with no data lost | [CLEAR] |
| FR-34 | F-34 Mybizz management app: registry, provisioning, usage and health monitoring, billing, update distribution, tickets, AI gateway, at-risk alerts | US-1, US-31, US-32 | App 5 raises an alert for a client with no staff sign-in for 14 days, no bookings for 30 days, or a ticket unanswered for 3 days; every instance's heartbeat shows the current release after a push into App 2 | [CLEAR] |

Every user story US-1 to US-36 is satisfied by at least one functional requirement; every requirement cites stories that exist.

---

## 6. Non-Functional Requirements

| Category | Requirement | Certainty |
|---|---|---|
| Performance | A user-facing flow makes the fewest server calls: the booking flow at most three round trips; dashboards read pre-computed tables; any operation over 22 seconds runs as a background task; a server call shows a progress indicator and times out after 30 seconds with a retry | [CLEAR] |
| Security | Vault for every credential, with TOTP step-up on every view; the only Anvil Secret is `encryption_key`; role checks on the server for every function; customers filtered to their own records; Bearer token per instance for App 5; rate-limited sign-in and lockout | [CLEAR] |
| Accessibility | WCAG 2.1 AA in every palette, light and dark; keyboard operation; visible focus; status never by colour alone; reduced motion respected | [CLEAR] |
| Availability | Each client depends on the Mybizz development account (App 2) and its own hosting account; Anvil is a single point of failure, an accepted risk with the procedures in `deployment-procedures.md`; daily backups to the client's R2 bucket | [CLEAR] |
| Timezone handling | UTC storage, display-time conversion, one IANA time zone per instance | [CLEAR] |
| Currency handling | One system currency, locked after the first booking or invoice; optional display currency per service, display only | [CLEAR] |
| Color/theming compliance | Anvil M3 theme with the project deviations of `DESIGN.md`; no hardcoded colour anywhere; four palettes and dark mode through the `--anvil-m3-*` properties | [CLEAR] |
| Data protection | POPIA, GDPR and PCI DSS per [[[default:specifications-global/spec-regulatory-compliance-baseline\|Regulatory Compliance Baseline]]](C:/dev/project-library-global/specifications-global/spec-regulatory-compliance-baseline.md); no customer personal data sent to App 5 | [CLEAR] |
| Capacity | 500,000 rows and 50 GB per client on a shared account; 100 clients in total | [CLEAR] |
| Support | Problem that stops a client's business acknowledged within 4 business hours; other tickets within 24 hours on business days | [CLEAR] |

---

## 7. Out of Scope

No feature of Mybizz CS is deferred to a later release. Voice tier 4 (voice form filling) and voice tier 5 (business intelligence by voice) are built in this release, on the sample data, as `mybizz-ai-implementation-spec.md` specifies. `[CLEAR]`

---

## 8. Dependencies

- **Multi-instance model:** every client instance depends on `master_template` (App 2), which receives each release from App 1 (Step 42). Every later release is delivered through that dependency; schema changes are applied by hand to App 3 and every instance. `[CLEAR]`
- **App 5** must be built and running before the first paying client: registration, provisioning, the AI and voice gateway, the Help channel and at-risk alerts depend on it. `[CLEAR]`
- **Anvil:** Business plan accounts owned by Mybizz; custom domains; background tasks and scheduled tasks; HTTP endpoints; the Material 3 theme and Routing dependencies. Anvil's confirmation of the account structure is outstanding (BRD AC-6). `[MISSING]`
- **Outside services:** Stripe, Paystack, PayPal, Brevo, Cloudflare R2, Groq, Wave (`integration-architecture.md`). `[CLEAR]`
- **The client's own domain**, required for Brevo sending. `[CLEAR]`

---

## 9. Open Questions

| # | Question | Settled at | Certainty |
|---|---|---|---|
| OQ-1 | Does Anvil permit client business apps on Mybizz-owned Business accounts, and how many small apps does one Business account comfortably carry? | Before the first paying client; David asks Anvil's sales team | [MISSING] |
| OQ-2 | Can an app schedule a background task of its dependency and serve its dependency's HTTP endpoints? | Proved in App 1 before Step 31 | [MISSING] |

---

## 10. Success Metrics

The BRD's KPIs (section 8) apply. For this release in particular:

| Metric | Target | Certainty |
|---|---|---|
| Features built and passing their acceptance criteria | F-01 to F-34, all | [CLEAR] |
| First clients live | 10 | [CLEAR] |
| Average onboarding-to-live time | under 7 days | [CLEAR] |
| Support tickets per client per month in the first 3 months | under 2 | [CLEAR] |
