---
title: "software-requirements-specification"
doc-id: "mb-f157e3fc"
updated: "2026-11-01"
---
# Mybizz CS — Software Requirements Specification (SRS)

The testable requirements the software must meet.

**Standard:** [[[default:docs-standard-global/docstd-software-requirements-specification]]](C:/dev/project-library-global/docs-standard-global/docstd-software-requirements-specification.md)

**Status:** Draft
**Date:** 2026-10-31
**Author:** Mybizz founder
**Derived from:** `product-requirements-document.md`. Every requirement traces to a functional requirement there. The build detail of each feature is `mb-3-cs-architecture-reference.md` section 12; where this document differs from it, the architecture reference is correct.

**Certainty tags:** `[CLEAR]` / `[PARTIAL]` / `[INFERRED]` / `[MISSING]`, as in the BRD and PRD.

---

## 1. Introduction

### 1.1 Purpose
This document states the testable requirements of Mybizz CS for developers, for the agents that build it, and for testing. It is the technical companion of the PRD.

### 1.2 Scope
App 1 (`mb-3-cs-app1`) and what it releases into App 2 (`master_template`) and App 3 (`blank_client_template`) for the client instances, and the interfaces of the client instances with App 5. App 5's own internals are specified in App 5's own run. Product scope: PRD sections 2 and 7.

### 1.3 Definitions and Acronyms

Domain terms are defined in `GLOSSARY.md` once Step 16 produces it; until then, `user-flows.md` (Terms and Actors) holds them. Terms specific to this SRS:

| Term | Meaning |
|---|---|
| Client instance | App 4: one client's Anvil app, cloned from App 3, depending on App 2 |
| Hosting account | A Mybizz-owned Anvil Business account holding up to two client instances |
| Role decorator | The server-side check in `server_auth/rbac` that refuses a call from a role not allowed |
| Payment hold | The 15-minute `pending` state of a booking whose online payment has not yet succeeded |
| Heartbeat | The 15-minute report from a client instance to App 5 |
| Schema fingerprint | A hash of an instance's table and column names, sent in the heartbeat |
| R2 | Cloudflare R2 object storage; one bucket per client |

### 1.4 References

- `business-requirements-document.md`, `product-requirements-document.md`
- `mb-3-cs-concept.md`, `mb-3-cs-architecture-reference.md`, `user-flows.md`
- `scaffold-spec.md`, `globals-contract.md`, `integration-architecture.md`, `observability-requirements.md`, `deployment-procedures.md`, `anvil-ui-build-standard.md`
- `authoritative-schema.md`, `DESIGN.md`, `palette-tokens.md`, `custom-component-requirements-matrix.md`, `calendar-feature-spec.md`, `releases/v2/mybizz-ai-implementation-spec.md`, `mb-3-cs-vault-implementation.md`, `mb-3-cs-test-specification.md`
- [[[default:specifications-global/spec-regulatory-compliance-baseline|Regulatory Compliance Baseline]]](C:/dev/project-library-global/specifications-global/spec-regulatory-compliance-baseline.md), [[[default:specifications-global/spec-security|Security Specification]]](C:/dev/project-library-global/specifications-global/spec-security.md), [[[default:specifications-global/spec-api-specification|API Specification (HTTP Endpoints)]]](C:/dev/project-library-global/specifications-global/spec-api-specification.md)

---

## 2. Overall Description

### 2.1 Product Perspective
Mybizz CS is a dependent-instance application of the five-app model ([[[default:specifications-global/spec-five-app-architecture-model|Five-App Architecture Model (Specification)]]](C:/dev/project-library-global/specifications-global/spec-five-app-architecture-model.md)). App 1 is developed; each release is pushed into App 2; every client instance depends on App 2 and holds only the schema, the startup module and its data. App 5, the Mybizz management app, is a separate application that the instances call through HTTP endpoints. `[CLEAR]`

### 2.2 Product Functions
The 34 features F-01 to F-34 of `mb-3-cs-concept.md` section 7, summarised in PRD section 5. `[CLEAR]`

### 2.3 User Characteristics
- Owners and staff are business people, not technical users; every screen must be usable without training, with tooltips and process-flow videos (F-25).
- Staff use the admin area on desktop and phone; WhatsUp is used on a phone, often on the move.
- Customers and visitors use the public website and portal on any device, mostly phones.
- Users include people who rely on keyboard, screen reader or high contrast (WCAG 2.1 AA). `[CLEAR]`

### 2.4 Constraints
Anvil's platform constraints apply by default: no programmatic Data Table schema API (schema changes are manual, per instance); no self-hosted fallback; one environment and one database per app; the Skulpt client runtime (money and date logic on the server only); no WebSocket push (client polling); lazy-loaded rows. Beyond the platform default:
- A client instance holds no form or server module; one would silently override `master_template` (reference section 2).
- The Hobby plan is never used; every hosting account is a Mybizz-owned Business account.
- The only Anvil Secret is `encryption_key`.
- Agents never read or write `.env`, never write `anvil.yaml`, and never commit. `[CLEAR]`

### 2.5 Assumptions and Dependencies
- Anvil permits the account structure (BRD AC-6). `[MISSING]`
- An app can schedule its dependency's background tasks and serve its dependency's HTTP endpoints (PRD OQ-2). `[MISSING]`
- Outside services as in `integration-architecture.md`. `[CLEAR]`

---

## 3. Specific Requirements

### 3.1 Functional Requirements

| # | Requirement | Traces to PRD | Verification Method | Certainty |
|---|---|---|---|---|
| SR-01 | Every callable server function carries a role decorator; a call from a role not allowed returns `{'success': False}` and writes nothing | FR-01, FR-30 | Access-matrix test (IN-02) over every function and role | [CLEAR] |
| SR-02 | No password is sent by email; new users set a password from a single-use, time-limited link | FR-01 | Test: invitation and provisioning emails contain no password; a used link is refused | [CLEAR] |
| SR-03 | Sign-in is rate-limited per identifier in `rate_limits`; repeated failures lock the account as the Security Specification sets out | FR-01, FR-30 | Test: repeated failed sign-ins are refused | [CLEAR] |
| SR-04 | A session idle for 30 minutes requires sign-in again and then returns the user to the same form | FR-01 | Test with the clock advanced | [CLEAR] |
| SR-05 | Every significant action writes an `activity_log` row: user, action type, description, metadata, time, IP address | FR-01, FR-29 | Test: role change, Vault access, payment, refund and status changes each write a row | [CLEAR] |
| SR-06 | `vault_service` is the only module that reads `encryption_key`; every credential is stored encrypted in `vault` | FR-02 | Code scan: no other module reads the secret; table scan: no value in clear | [CLEAR] |
| SR-07 | Every Vault view requires a valid TOTP code, with no remembered session; every access emails the Owner; 5 wrong codes lock the Vault for 15 minutes and email the Owner | FR-02 | Test of each condition | [CLEAR] |
| SR-08 | Onboarding saves after each step in `business_profile.onboarding_status`; reopening goes to the next unfinished step; steps 4 and 5 cannot be passed until confirmed | FR-03 | Test: leave at each step and return | [CLEAR] |
| SR-09 | The legal acknowledgement is recorded once with its timestamp, in the instance and in App 5, and cannot be reversed | FR-03 | Test: second acknowledgement attempt changes nothing; App 5 record present | [CLEAR] |
| SR-10 | Dashboard and analytics read `daily_summary`, `service_performance`, `provider_performance` and `campaign_metrics` only, never aggregate source tables on load | FR-04 | Code review and query count on dashboard load | [CLEAR] |
| SR-11 | Settings tabs are filtered on the server by role: Owner all 14; Manager Business Profile, Brand Palette, Display Currency, Theme | FR-05 | Access-matrix test | [CLEAR] |
| SR-12 | Public pages are served by Anvil Routing at the addresses of `scaffold-spec.md` (Public routes), each with title and meta description | FR-06 | Route test of every address | [CLEAR] |
| SR-13 | The homepage renders its sections in the order and visibility of the selected template; a section with no content does not render | FR-07, FR-09 | Test each template with and without optional content | [CLEAR] |
| SR-14 | A landing page view and a landing page submission each increment its counters once | FR-08 | Test | [CLEAR] |
| SR-15 | `get_available_slots` returns only slots where the provider is available, any required resource is free, travel buffers for customer-location bookings are respected, and a group session has places left | FR-11 | Unit tests over availability rules, exceptions, resources, buffers and places | [CLEAR] |
| SR-16 | `confirm_booking` re-checks the slot, resource, places and price inside one transaction; a clash returns the nearest free slots and creates nothing | FR-11 | Concurrent-confirmation test | [CLEAR] |
| SR-17 | A booking with an online payment option is `pending` until the payment succeeds; a `pending` booking older than 15 minutes is set to `cancelled` and its slot and resource freed | FR-11, FR-15 | Test with the hold task | [CLEAR] |
| SR-18 | Bookings on pay on the day, invoice terms or a package session are `confirmed` at once | FR-11 | Test each option | [CLEAR] |
| SR-19 | Booking status changes follow `DESIGN.md` (Booking Status Lifecycle); no booking row is ever deleted | FR-11 | State-transition test; no delete call on `bookings` in code | [CLEAR] |
| SR-20 | Marking a booking `completed` issues the invoice (or the deposit's balance invoice), creates the review task and sends the review request, in one server call | FR-11, FR-15, FR-23 | Test | [CLEAR] |
| SR-21 | Each 24-hour and 1-hour reminder is sent once, with the call link for a video call | FR-11 | Test with the reminder task over a booking's life | [CLEAR] |
| SR-22 | Staff calling `get_calendar_items` receive only their own bookings, whatever filter they send | FR-11 | Access test (`calendar-feature-spec.md` section 11) | [CLEAR] |
| SR-23 | An order's status moves only ordered → in progress → delivered; each change emails the customer | FR-12 | State-transition test | [CLEAR] |
| SR-24 | Booking with a package draws one session; cancelling within the allowed window returns it; an expired package's sessions lapse | FR-13 | Test | [CLEAR] |
| SR-25 | Converting time entries to invoice lines marks them invoiced in the same call; an invoiced entry is refused | FR-14 | Test | [CLEAR] |
| SR-26 | Invoice numbers are `INV-{year}-{sequence}`, sequential per instance with no gap or repeat under concurrent creation | FR-15 | Concurrency test | [CLEAR] |
| SR-27 | Tax = subtotal × the rate at issue, stored on the invoice; with rate 0 no tax line is produced | FR-15 | Calculation tests | [CLEAR] |
| SR-28 | The nightly task sets every unpaid invoice past its due date to `overdue` and emails one reminder | FR-15 | Test with the clock advanced | [CLEAR] |
| SR-29 | Every payment and refund, by gateway or manual, is a `payments` row linked to its invoice, booking or order | FR-15 | Test | [CLEAR] |
| SR-30 | A webhook is verified by signature; an `event_id` already in `webhook_log` is refused; the payload is written to R2; HTTP 200 is returned before the event is applied by a background task | FR-15, FR-18, FR-29 | Duplicate and invalid-signature tests | [CLEAR] |
| SR-31 | The system currency cannot change once a booking or invoice exists; all stored amounts are in it | FR-16 | Test | [CLEAR] |
| SR-32 | A booking, order, enquiry or capture with an email that matches a contact updates that contact; otherwise it creates one | FR-17 | Test | [CLEAR] |
| SR-33 | A lead capture creates the `leads` row and the linked `contacts` row in one call and enrols the contact in the welcome sequence | FR-19 | Test | [CLEAR] |
| SR-34 | Transactional email is sent through Brevo SMTP with the client's key and sender; it is unavailable until the sender domain is verified | FR-18 | Test with a Brevo test account | [CLEAR] |
| SR-35 | An unsubscribe received by Brevo webhook stops all marketing email to that contact | FR-18 | Test | [CLEAR] |
| SR-36 | Each automation run is recorded in `automation_runs`; an automation runs once per triggering event | FR-20 | Test | [CLEAR] |
| SR-37 | A blog post with `scheduled_at` in the past is published by the next run of the 15-minute task | FR-21 | Test | [CLEAR] |
| SR-38 | Seeded clauses can be edited and switched off but not deleted; publishing regenerates the public page immediately | FR-22 | Test | [CLEAR] |
| SR-39 | A review is not public until approved; the homepage shows at most 10 testimonials | FR-23 | Test | [CLEAR] |
| SR-40 | Every server function the customer portal calls filters to the signed-in customer's own records | FR-24 | Access test with two customers | [CLEAR] |
| SR-41 | Tooltips and the morning briefing are switched per user in `users.preferences` | FR-25, FR-27 | Test | [CLEAR] |
| SR-42 | Every AI and speech request goes through `server_ai/ai_request_layer` to App 5 with the instance's Bearer token; no AI provider key exists in the instance | FR-26, FR-27 | Code scan; Vault and secrets scan | [CLEAR] |
| SR-43 | An AI or voice failure returns a clear message (spoken for voice) and leaves the form usable | FR-26, FR-27 | Test with App 5 unreachable | [CLEAR] |
| SR-44 | Assists 9 and 10 are hidden until the client's data passes the thresholds in the AI specification (3 months of activity or booking history) | FR-26 | Test with sample data below and above the threshold | [CLEAR] |
| SR-45 | Voice tier 3 questions are read-only; no voice question changes data | FR-27 | Test: every voice query function performs no write | [CLEAR] |
| SR-46 | Wave receives invoice paid, new customer and refund issued only; a customer is created or matched before its payment; nothing is read from Wave | FR-28 | Test with a Wave test account | [CLEAR] |
| SR-47 | The daily backup writes every table to the client's R2 bucket; `activity_log` holds no row older than 30 days after the nightly archive | FR-29 | Test | [CLEAR] |
| SR-48 | Export all data produces a CSV of every table as a background task | FR-29 | Test | [CLEAR] |
| SR-49 | No card number, CVV or full card data is stored in any table or log | FR-30 | Table and log scans | [CLEAR] |
| SR-50 | Every colour on every screen comes from the Colour Scheme; the palette module sets the `--anvil-m3-*` properties from `palette-tokens.md` and follows the device's light or dark setting | FR-31 | UI conformance scans (Step 29); visual check in light and dark | [CLEAR] |
| SR-51 | A Help message is sent to App 5 with the Bearer token and returns a reference number; App 5's replies appear in the Help view | FR-32 | Test against App 5 | [CLEAR] |
| SR-52 | The instance enforces 500,000 rows and 50 GB; usage is reported to App 5 daily | FR-33 | Test with the cap lowered | [CLEAR] |
| SR-53 | The heartbeat reaches App 5 every 15 minutes with release version and schema fingerprint and no customer personal data | FR-34 | Test against App 5; payload inspection | [CLEAR] |
| SR-54 | The usage report carries the last staff sign-in and last booking date, from which App 5 raises the at-risk alerts | FR-34 | Test against App 5 | [CLEAR] |
| SR-55 | A service with pricing model `duration` offers time slots sized by its duration; a service with `unit` offers Order and no slot; only active services appear publicly | FR-10 | Test both pricing models and an inactive service | [CLEAR] |

Every functional requirement FR-01 to FR-34 of the PRD appears in this table at least once.

### 3.2 External Interface Requirements

- **User interfaces:** the Anvil Material 3 theme with the project deviations (`DESIGN.md`); screens built to `anvil-ui-build-standard.md`; the admin area, customer portal and public website as in `scaffold-spec.md` (Navigation Architecture); WhatsUp on the phone's browser.
- **Hardware interfaces:** the device microphone for WhatsUp, through `anvil.js`, with the user's permission.
- **Software interfaces:** Stripe, Paystack, PayPal, Brevo SMTP and API, Cloudflare R2, Wave, and App 5 (for Groq and management), as `integration-architecture.md` sets out.
- **Communication interfaces:** HTTPS only. Inbound HTTP endpoints: one webhook endpoint per gateway and for Brevo; the management endpoints for App 5, with the instance's Bearer token. Outbound: SMTP with STARTTLS to Brevo; HTTPS to every other service.

### 3.3 Performance Requirements

| Requirement | Value | Certainty |
|---|---|---|
| Booking flow server round trips | at most 3 (`get_booking_context`, `get_available_slots`, `confirm_booking`) | [CLEAR] |
| Calendar | one server call per visible period; refresh every 60 seconds while open | [CLEAR] |
| Any server call | progress indicator shown; timeout with retry after 30 seconds | [CLEAR] |
| Long operations | any operation expected to exceed 22 seconds runs as `@anvil.server.background_task` (Anvil platform constraint) | [CLEAR] |
| Webhook handler | returns HTTP 200 before the event is applied | [CLEAR] |
| Morning briefing | 20–30 seconds spoken | [CLEAR] |
| Capacity per client on a shared account | 500,000 rows, 50 GB | [CLEAR] |
| Page response under normal load | screen usable within 3 seconds on a phone connection | [INFERRED] |

### 3.4 Design Constraints

- Five-app model and the no-forms-in-instances rule: reference sections 1 and 2; [[[default:adr-global/adr-client-instance-readme-five-app-system|Mandatory README in Every Client Instance Documenting the Five-App System]]](C:/dev/project-library-global/adr-global/adr-client-instance-readme-five-app-system.md) ADR.
- Form and state architecture: [[[default:adr-global/adr-form-architecture-and-state|Form Architecture and State Management]]](C:/dev/project-library-global/adr-global/adr-form-architecture-and-state.md) ADR; `globals-contract.md`.
- Navigation: [[[default:adr-global/adr-navigation-lambda-link-open-form|Navigation Standard: Lambda/Link/open_form]]](C:/dev/project-library-global/adr-global/adr-navigation-lambda-link-open-form.md) ADR, [[[default:adr-global/adr-htmltemplate-use|HTMLTemplate Use]]](C:/dev/project-library-global/adr-global/adr-htmltemplate-use.md) ADR.
- Webhooks: [[[default:adr-global/adr-webhook-architecture|Webhook Architecture]]](C:/dev/project-library-global/adr-global/adr-webhook-architecture.md) ADR.
- Background tasks and polling: [[[default:adr-global/adr-real-time-and-background-tasks|Real-Time Updates and Background Task Architecture]]](C:/dev/project-library-global/adr-global/adr-real-time-and-background-tasks.md) ADR.
- Data access: [[[default:adr-global/adr-data-access-patterns|Data Access Patterns and Query Limitations]]](C:/dev/project-library-global/adr-global/adr-data-access-patterns.md) ADR.
- Dark mode: [[[default:adr-global/adr-dark-mode|Dark Mode]]](C:/dev/project-library-global/adr-global/adr-dark-mode.md) ADR.
- Platform: [[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR.
- Imports between modules are relative, so that code runs from `master_template`'s package in a client instance (`scaffold-spec.md`).

### 3.5 Non-Functional Requirements

- **Roles:** five roles in `users.role` — `owner`, `manager`, `admin`, `staff`, `customer`. Every callable server function carries one decorator from `server_auth/rbac` naming the roles allowed; the decorator reads the signed-in user from `anvil.users.get_user()` and refuses the call before the function body runs. The interface hides what a role cannot use, from `globals.session_user`, but that is never relied on. `[CLEAR]`
- **Customer isolation:** functions allowed to `customer` take no contact or customer id from the client; they derive it from the signed-in user. `[CLEAR]`
- **Secrets:** `encryption_key` is the only Anvil Secret; credentials are in `vault`, encrypted by `vault_service`; App 5's Bearer token for the instance is held in the Vault. `[CLEAR]`
- **Reliability:** outside-service calls that fail are queued in `server_shared/retry_queue` and retried every 15 minutes; the Owner is notified after repeated failure. Webhook application is idempotent by `event_id`. `[CLEAR]`
- **Recoverability:** daily backups and pre-release and pre-migration backups to the client's R2 bucket; restore by Mybizz from R2 (UF-33). `[CLEAR]`
- **Observability:** structured logging, `health_log`, `error_log` and `activity_log`, heartbeat to App 5, as `observability-requirements.md` sets out. `[CLEAR]`
- **Maintainability:** feature-based packages, never renamed; one module per responsibility (`scaffold-spec.md`); every public function typed and documented; response envelope `{'success', 'data' | 'error'}`. `[CLEAR]`
- **Accessibility:** WCAG 2.1 AA (`anvil-ui-build-standard.md`). `[CLEAR]`

---

## 4. Appendices

- Package and module structure, navigation and routes: `scaffold-spec.md`.
- Tables and fields: `authoritative-schema.md`; the changes that bring App 1 to it: `schema-migration-log.md`.
- Booking and invoice status lifecycles: `DESIGN.md` (Booking Design, Invoice Design).
- Calendar data model and server function: `calendar-feature-spec.md` section 7.
- Background task schedule: `integration-architecture.md` (Background Tasks).
