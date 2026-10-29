---
title: "build-plan"
doc-id: "mb-e9bc1d1c"
updated: "2026-10-29"
---
---
type: concept
title: Build Plan
ingested_via: put_page
ingested_at: '2026-06-20T09:39:12.527Z'
source_kind: put_page
---

# Mybizz CS — Build Plan (Definitive)

**Vertical:** Consulting & Services
**V1 Scope:** Blog, Landing Pages, Campaigns, Pre-built Segments, Tasks, Homepage Templates, Basic Analytics, Stripe + Paystack
**V2 Deferred:** Client Portal, Advanced Analytics, Complex Task Automation, PayPal, Time Tracking, Custom Segment Builder
**Schema:** See [[[mb-3-cs-project-context:docs-local/authoritative-schema|Authoritative Database Schema]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/authoritative-schema.md) for current table count
**Architecture:** Dependency-based (master_template → client instances)
**Review Status:** CEO + Eng Review Complete (All 5 phases engineering reviews complete)

**Related Specifications:**

| Specification | Relevance |
|---|---|
| [[[default:specifications-global/spec-client-activation-runbook\|Client Instance Activation Runbook]]](C:/dev/project-library-global/specifications-global/spec-client-activation-runbook.md) | Client provisioning procedures |
| [[[default:specifications-global/spec-onboarding-implementation-plan\|Mybizz CS Onboarding System - Engineering Plan]]](C:/dev/project-library-global/specifications-global/spec-onboarding-implementation-plan.md) | Onboarding form, Settings form, legal acknowledgement |
| [[[default:specifications-global/spec-deployment\|Deployment Specification]]](C:/dev/project-library-global/specifications-global/spec-deployment.md) | Anvil environments, release workflow, rollback, schema migration |
| [[[default:specifications-global/spec-integration\|Integration Specification]]](C:/dev/project-library-global/specifications-global/spec-integration.md) | Brevo, Stripe, Paystack integration patterns |
| [[[default:specifications-global/spec-api-specification\|API Specification (HTTP Endpoints)]]](C:/dev/project-library-global/specifications-global/spec-api-specification.md) | HTTP endpoints (webhooks, management) |
| [[[default:specifications-global/spec-testing-methodology-standards\|Testing Methodology]]](C:/dev/project-library-global/specifications-global/spec-testing-methodology-standards.md) | Testing levels, pure logic, Uplink safety protocol |
| [[[mb-3-cs-project-context:docs-local/mb-3-cs-test-specification]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/mb-3-cs-test-specification.md) | Test specifications, E2E flows, RBAC matrix |
| [[[default:specifications-global/spec-regulatory-compliance-baseline\|Regulatory Compliance Baseline (Specification)]]](C:/dev/project-library-global/specifications-global/spec-regulatory-compliance-baseline.md) | POPIA, GDPR, PCI DSS compliance |

---

## Current Status

| Component | Status | Notes |
|-----------|--------|-------|
| **Schema** | 100% Complete | See [[[mb-3-cs-project-context:docs-local/authoritative-schema\|Authoritative Database Schema]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/authoritative-schema.md) |
| **Wireframes** | ✅ 100% Complete | 50 HTML screens created |
| **Design Direction** | ✅ 100% Complete | M3 design approved |
| **ADR Documentation** | 100% Complete | See `adr-global/` in project-library-global (found by GBrain search) for current ADR count and status |
| **Positioning Strategy** | ✅ 100% Complete | Business model documented |
| **Phase 0** | ✅ 100% Complete | All TODOs implemented |
| **Phase 1** | 🔄 In Progress | Foundation implementation started |
| **Phase 2** | ⏸️ Pending | Awaits Phase 1 completion |
| **Phase 3** | ⏸️ Pending | Awaits Phase 2 completion |
| **Phase 4** | ⏸️ Pending | Awaits Phase 3 completion |
| **Phase 5** | ⏸️ Pending | Awaits Phase 4 completion |
| **Implementation** | 🔄 In Progress | Phase 1 Foundation implementation |

---

**Last Updated:** 2026-06-16  
**Next Review:** After Phase 1 implementation completion
**CEO Review:** 2026-06-15 — 11 sections reviewed, 14 TODO items captured (see todo.md section 8), plan changes applied
**Eng Review:** 2026-06-16 — 5 dilemmas reviewed, 4 completable items executed (see todo.md section 9)
**Test Specs:** Testing specifications consolidated into this build plan (see Phase 5.4 Testing & QA, build-plan.md Stage 5.4 completion criteria, and self-review checklists). Separate testing specifications are in [[[default:specifications-global/spec-testing-methodology-standards|Testing Methodology]]](C:/dev/project-library-global/specifications-global/spec-testing-methodology-standards.md) and [[[mb-3-cs-project-context:docs-local/mb-3-cs-test-specification|Test Specification (Canonical)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/mb-3-cs-test-specification.md).
**State Contract:** [[[default:docs-standard-global/docstd-globals-contract|globals.py Contract]]](C:/dev/project-library-global/docs-standard-global/docstd-globals-contract.md) — shared state variables and defensive loading pattern

---

## Overview

This build plan defines the phased implementation sequence for the Mybizz CS V1 MVP. Each phase has clear deliverables, completion criteria, and inter-process dependencies.

**Key Principles:**
- Build sequentially — each phase depends on the prior phase being complete
- No phase is complete until all its stages pass completion criteria
- Security is woven through from Phase 0 — not bolted on later
- The Vault and authentication are foundational — nothing else builds without them
- All review tasks (CEO + Eng) are embedded as completion criteria

---

## Phase Structure

| Phase | Focus | Key Deliverables | Dependencies |
|-------|-------|------------------|--------------|
| 0 | Pre-Implementation | Test coverage, observability, deployment procedures, Vault error handling | None (starting point) |
| 1 | Foundation | Auth, RBAC, Vault, navigation, settings | Phase 0 complete |
| 2 | Transactional Core | Services, bookings, payments, invoicing | Phase 1 complete |
| 3 | CRM + Email | Contacts, campaigns, segments, tasks, landing pages | Phase 2 complete |
| 4 | Content + Website | Blog, public website, homepage templates | Phase 3 complete |
| 5 | Polish + Launch | Analytics, security, compliance, testing | Phase 4 complete |

---

## Implementation Order

1. **Phase 0** — Pre-Implementation Requirements (TODO 1-4)
   - TODO 1: Add Vault Error Handling (P1, critical)
   - TODO 2: Define Test Coverage Requirements (P1, pre-implementation)
   - TODO 3: Define Observability Requirements (P2, pre-implementation)
   - TODO 4: Define Deployment Procedures (P2, pre-implementation)

2. **Phase 1** — Foundation (TODO 11 embedded)
   - Phase 1.1-1.6 with TODO 11 verification in Stage 1.2

3. **Phase 2** — Transactional Core (TODO 10, 12 embedded)
   - Phase 2.1-2.5 with TODO 10, 12 in Stage 2.4 (Time Tracking deferred to V2)

4. **Phase 3** — CRM + Email (TODO 6, 8, 9 embedded)
   - Phase 3.1-3.5 with TODO 6, 8, 9 in Stage 3.1

5. **Phase 4** — Content + Website (TODO 5, 14, 15 embedded)
   - Phase 4.1-4.3 with TODO 5, 14, 15 in Stages 4.1-4.2

6. **Phase 5** — Polish + Launch (TODO 2, 3, 4, 13 embedded)
   - Phase 5.1-5.5 with TODO 2, 3, 4, 13 in Stages 5.4-5.5

---

## Phase 0: Pre-Implementation Requirements

**Goal:** Address critical gaps and define requirements before Phase 1 begins.

### Stage 0.1: Add Vault Error Handling

**Deliverables:**
- `server_shared/vault_service.py` updated with rescue block for `KeyNotFoundError`
- Unit test for `get_vault_secret()` with missing secret

**Completion Criteria:**
- ✅ Rescue block added to `get_vault_secret()` with user-friendly error handling
- ✅ Unit test added and passing
- ✅ No 500 errors when secret not found

### Stage 0.2: Define Test Coverage Requirements

**Deliverables:**
- Test coverage specification document
- Unit test framework configured (100% coverage for pure logic functions)
- Integration test framework configured (all server functions with mock data)
- E2E test framework configured (critical flows: auth, booking, payment, email)
- Test data cleanup procedures defined
- Mock external services configured (Brevo, payment gateways)

**Completion Criteria:**
- ✅ Test coverage requirements documented
- ✅ Test frameworks configured and tested
- ✅ Coverage verification working (coverage.py or similar)

### Stage 0.3: Define Observability Requirements

**Deliverables:**
- Observability specification document
- Structured logging at entry/exit for all server functions
- Metrics defined (bookings created, payments processed, errors, latency)
- Alerts defined (error rates, API failures, rate limits)
- Admin dashboard for platform health
- Log retention documented (2 years for audit)

**Completion Criteria:**
- ✅ Observability requirements documented
- ✅ Logging framework configured
- ✅ Metrics and alerts defined
- ✅ Log retention procedures documented
- ✅ Health thresholds defined: "healthy" (all checks pass), "degraded" (>1 check fails in 15 min), "unhealthy" (>3 consecutive failures)
- ✅ Log retention policy: 2 years for audit logs, 90 days for operational logs, 30 days for debug logs
- ✅ Error rate monitoring: alert when error rate exceeds 5% of requests in any 15-minute window

### Stage 0.4: Define Deployment Procedures

**Deliverables:**
- Deployment procedures document
- Backward-compatible DB migration procedures
- Feature flag strategy defined (10%, 50%, 100% rollout)
- Rollback procedures (Git revert, feature flag disable)
- Post-deploy verification checklist
- Environment parity procedures (staging matches production)
- Rollback procedure tested in staging

**Completion Criteria:**
- ✅ Deployment procedures documented
- ✅ Migration rollback procedures tested
- ✅ Feature flag strategy defined
- ✅ Rollback procedure tested and verified

**See:** [[[default:specifications-global/spec-deployment|Deployment Specification]]](C:/dev/project-library-global/specifications-global/spec-deployment.md) (Deployment — Anvil environments, release workflow, rollback, schema migration)

---

## Phase 1: Foundation

**Goal:** Establish authentication, navigation, settings, and the Vault — the foundation for all subsequent phases.

**Dependency:** Phase 0 must be complete (Pre-Implementation Requirements)

### Stage 1.1: Project Infrastructure

**Deliverables:**
- Anvil app `mb-3-cs` created with correct package structure
- Dependencies installed: M3 Theme (4UK6WHQ6UX7AKELK), Routing (3PIDO5P3H4VPEMPL)
- GitHub repository linked
- VS Code connected to GitHub

**Completion Criteria:**
- ✅ App runs without errors
- ✅ M3 theme applied and rendering correctly
- ✅ Routing dependency installed and functional
- ✅ GitHub sync working (push/pull)
- ✅ Package structure matches specification

### Stage 1.2: Authentication System

**Deliverables:**
- `users` table extended with `role`, `permissions`, `account_status`, `signed_up`, `email_confirmation_key`, `mfa` fields
- `LoginForm` — email/password login with validation
- `SignupForm` — new user registration with business name capture
- `PasswordResetForm` — password reset flow
- `server_auth/service.py` — authentication server functions (email confirmation, rate limiting, session management)
- `server_auth/rbac.py` — role-based access control decorators (full coverage)
- `tests/test_auth_service.py` — auth unit tests
- `tests/test_rbac.py` — RBAC unit tests

**Completion Criteria:**
- ✅ Users can register with email/password
- ✅ Email confirmation flow works (Brevo SMTP, expiration handling, resend capability)
- ✅ Password reset flow works
- ✅ Five roles defined: Owner, Manager, Admin, Staff, Customer
- ✅ RBAC decorators functional on ALL server functions (TODO 11 verified)
- ✅ Rate limiting implemented (10 req/min unauthenticated, 100 req/min authenticated) using `rate_limits` table
- ✅ Session management working (30-minute inactivity timeout, "Remember me" V1 feature)
- ✅ Audit logging for authentication events
- ✅ All unit tests passing

**Implementation Notes:**
- Email confirmation uses Brevo SMTP (configured in Phase 0)
- Rate limiting uses `rate_limits` table for persistence across server restarts
- Session timeout is 30 minutes inactivity, with "Remember me" option for extended sessions (V1 feature)
- Full RBAC coverage required on all server functions, not just critical endpoints

### Stage 1.3: Dashboard & Navigation

**Deliverables:**
- `AdminLayout` — NavigationRailLayout with navigation
- `DashboardForm` — main admin dashboard with metrics
- Navigation structure implemented (Dashboard, Sales & Operations, Customers & Marketing, Content & Website, Finance & Reports, Settings)
- `shared/navigation_helpers.py` — navigation utility functions (lambda click handlers, open_form wrappers)
- `tests/test_navigation.py` — navigation unit tests

**Completion Criteria:**
- ✅ AdminLayout renders with M3 styling
- ✅ Navigation sidebar functional with all items
- ✅ Lambda click handlers working with variable capture
- ✅ Dashboard displays metrics (revenue, bookings, customers — initially static)
- ✅ Navigation visibility respects RBAC (Settings Owner/Manager only)
- ✅ Mobile responsive layout working
- ✅ Sign out link present at bottom of sidebar
- ✅ Empty state: dashboard shows welcome message and onboarding link when no data exists
- ✅ Loading state: dashboard shows skeleton/placeholder while server calls are in progress
- ✅ Error state: dashboard shows retry button and error message on server failure
- ✅ All unit tests passing

**Implementation Notes:**
- Navigation helpers prevent lambda capture bugs and ensure consistent patterns
- AdminLayout uses NavigationRailLayout with plain Link components (not NavigationLink)
- Navigation structure follows the spec in architecture.md
- **See:** [[[default:adr-global/adr-form-architecture-and-state|Form Architecture and State Management]]](C:/dev/project-library-global/adr-global/adr-form-architecture-and-state.md) ADR (Form Architecture and State Management)

### Stage 1.4: Settings & Configuration

**Deliverables:**
- `business_profile` table populated with business information
- `config` table for key-value configuration
- `email_config` table for email settings
- `payment_config` table for payment gateway settings
- `theme_config` table for M3 theme customization
- `SettingsForm` — single tabbed form with 11 tabs (M3 button navigation, RBAC-governed):
  - Business Profile, Palette, System Currency, Legal Acknowledgement, Display Currency
  - Payment Gateway, Services, Users & Permissions, Email Configuration, Theme, Feature Flags
  - "Return to Onboarding" button in header (visible when onboarding in progress)
- `tests/test_settings.py` — settings unit tests

**Completion Criteria:**
- ✅ Business profile can be saved and retrieved
- ✅ Feature flags functional (bookings_enabled, services_enabled, etc.)
- ✅ Theme customization persists and applies correctly
- ✅ Currency selection working (immutable after first transaction)
- ✅ Edge case: first transaction is refunded/voided — currency remains immutable; refund uses the original currency
- ✅ User management functional (invite, edit role, deactivate)
- ✅ All config tables initialized via `create_initial_config()`
- ✅ Settings form uses 11-tab layout with M3 button navigation
- ✅ RBAC governance enforced per tab
- ✅ "Return to Onboarding" button visible only when onboarding in progress
- ✅ Edge case: user closes browser mid-onboarding — onboarding resumable from last completed step ([[[default:adr-global/adr-onboarding-resumability|Onboarding Resumability]]](C:/dev/project-library-global/adr-global/adr-onboarding-resumability.md) ADR); no session expiry for incomplete onboarding
- ✅ All unit tests passing

**Implementation Notes:**
- SettingsForm uses sub-sections for better maintainability
- Currency becomes immutable after first transaction
- Feature flags control navigation item visibility
- **See:** [[[default:adr-global/adr-ui-customization-approach|UI Customization Approach]]](C:/dev/project-library-global/adr-global/adr-ui-customization-approach.md) ADR (UI Customization Approach)

### Stage 1.5: The Vault

**Deliverables:**
- `vault` table created with encrypted secrets storage
- `encryption_key` set in Anvil Secrets
- `server_shared/encryption_service.py` — encryption/decryption functions
- `server_shared/vault_service.py` — Vault CRUD functions
- `server_shared/vault_totp_service.py` — TOTP step-up authentication
- `settings/VaultForm` — owner-only Vault UI with TOTP gate

**Completion Criteria:**
- ✅ `encryption_key` set in Anvil Secrets (only item there)
- ✅ VaultForm requires TOTP step-up on every open
- ✅ No grace period — TOTP required each time
- ✅ Email notification sent to Owner on every Vault access
- ✅ Secret keys stored encrypted in `vault` table
- ✅ `get_vault_secret()` retrieves secrets correctly (TODO 1: Vault error handling implemented)
- ✅ Payment config uses Vault for secret keys (not stored in `payment_config` table)
- ✅ Secret masking in `get_payment_config()` returns `'***'` for secrets

### Stage 1.6: AdminLayout Navigation Refinement

**Deliverables:**
- Finalized navigation structure with feature flag visibility
- CustomerLayout created (deferred to V2 — stub for now)
- Navigation consistency verified across all forms

**Completion Criteria:**
- ✅ All navigation items have correct visibility rules
- ✅ Feature flags control navigation item visibility
- ✅ Navigation works correctly on mobile
- ✅ Active link highlighting working
- ✅ No navigation errors or broken links

### Stage 1.7: Management Endpoints

**Deliverables:**
- `server_shared/management_endpoints.py` — HTTP endpoints for Mybizz_management communication
- Bearer token authentication for all management endpoints
- Per-client management token stored in client instance config

**Endpoints (per [[[default:adr-global/adr-mybizz-management-visibility|Mybizz_management Visibility and Control]]](C:/dev/project-library-global/adr-global/adr-mybizz-management-visibility.md) ADR):**

| Endpoint | Method | Purpose |
|---|---|---|
| `/management/health` | GET | Returns health metrics, last active, error summary |
| `/management/status` | GET | Returns subscription status, feature flags |
| `/management/suspend` | POST | Writes suspended flag to client tables |
| `/management/unsuspend` | POST | Clears suspended flag |
| `/management/push-notification` | POST | Writes notification row to client notifications table |
| `/management/push-update-alert` | POST | Writes update available notice to client tables |

**Authentication:** Each request must include a Bearer token in the `Authorization` header. The token is validated against a value stored in the client instance's management configuration. Requests without a valid token are rejected with HTTP 403.

**Completion Criteria:**
- ✅ All management endpoints respond correctly
- ✅ Bearer token authentication enforced on all endpoints
- ✅ Invalid tokens receive HTTP 403
- ✅ Health endpoint returns current metrics from `health_log` and `error_log` tables
- ✅ Module ships with master_template — all client instances have endpoints from provisioning

**Note:** Mybizz_management (App 5) is deferred. These endpoints exist so that client instances are management-ready from day one. Until Mybizz_management is built, the endpoints are unused but functional.

---

## Phase 2: Transactional Core

**Goal:** Implement services, bookings, payments, and invoicing — the core commercial functions.

**Dependency:** Phase 1 must be complete (Foundation, Vault, Auth, Navigation, Settings)

**Note:** Time Tracking (originally Stage 2.6) has been deferred to V2 per CEO review. It is a separate product concern that does not belong in the core booking/payment pipeline.

### Stage 2.1: Service Catalogue

**Deliverables:**
- `services` table populated with service offerings
- `services` package:
  - `ServiceListForm` — service listing with filters
  - `ServiceEditorForm` — create/edit services
  - `ServiceViewerForm` — read-only service detail
- `server_services/service.py` — service server functions

**Completion Criteria:**
- ✅ Services can be created with both `duration` and `unit` pricing models
- ✅ Service categories functional (create, edit, reorder)
- ✅ Provider assignment working (Staff users)
- ✅ Meeting types configured (In-Person, Video Call, Phone Call)
- ✅ Service images and video URL supported
- ✅ Public service listing functional
- ✅ Service detail page functional

### Stage 2.2: Availability Management

**Deliverables:**
- `availability_rules` table — recurring availability schedules
- `availability_exceptions` table — holiday/special closures
- `availability/AvailabilitySettingsForm` — availability configuration UI

**Completion Criteria:**
- ✅ Business hours configured by day of week
- ✅ Blocked dates (holidays, closures) working
- ✅ Provider-specific availability schedules
- ✅ Buffer time between appointments configurable
- ✅ Advance booking window configurable
- ✅ Availability conflicts detected and blocked

### Stage 2.3: Booking System

**Deliverables:**
- `bookings` table populated
- `booking_metadata_schemas` table for dynamic forms
- `bookings/` package:
  - `BookingCalendarForm` — month/week/day views
  - `BookingListForm` — booking listing
  - `BookingEditorForm` — create/edit bookings
  - `BookingViewerForm` — booking detail
- `server_bookings/service.py` — booking server functions

**Completion Criteria:**
- ✅ Calendar views functional (month, week, day)
- ✅ Booking creation flow working (service → provider → date/time → payment)
- ✅ Recurring appointments working (weekly, bi-weekly, monthly)
- ✅ Video call link generation for Video Call bookings
- ✅ Booking status workflow (PENDING → CONFIRMED → COMPLETED/CANCELLED)
- ✅ Contact auto-created from booking if not exists
- ✅ Booking metadata schemas functional for intake forms
- ✅ Empty state: booking calendar shows "No bookings yet" with "Create Booking" CTA
- ✅ Loading state: calendar shows skeleton grid while data loads
- ✅ Error state: booking form shows inline validation errors and server error retry
- ✅ Edge case: user navigates away mid-payment — booking holds for 15 minutes, then expires (documented behavior)
- ✅ Edge case: slow connection during payment — 30-second timeout with user-visible progress indicator
- ✅ Booking flow completes in under 3 seconds on a standard connection (service selection → confirmation display)
- **Round-trip budget (max 3 server calls):**
  - Call 1: `get_booking_context(service_id)` → service details, available providers, date ranges, intake form template
  - Call 2: `get_available_slots(provider_id, date_range)` → available time slots
  - Call 3: `confirm_booking(booking_data)` → booking confirmation, payment result
  - Meeting type and intake form are client-side selections from pre-loaded data — no server call
  - Confirmation display is client-side — no server call
  - Cache `get_booking_context()` response in `globals.py` for flow duration (see [[[default:docs-standard-global/docstd-globals-contract|globals.py Contract]]](C:/dev/project-library-global/docs-standard-global/docstd-globals-contract.md))
- **See:** [[[default:adr-global/adr-form-architecture-and-state|Form Architecture and State Management]]](C:/dev/project-library-global/adr-global/adr-form-architecture-and-state.md) ADR (Form Architecture — booking flow latency management)
- **See:** [[[default:specifications-global/spec-testing-methodology-standards|Testing Methodology]]](C:/dev/project-library-global/specifications-global/spec-testing-methodology-standards.md) (E2E booking test spec, error path tests, RBAC matrix)

### Stage 2.4: Payment Integration

**Deliverables:**
- Payment gateway integration (Stripe + Paystack):
  - `server_payments/stripe_service.py`
  - `server_payments/paystack_service.py`
  - `server_payments/paypal_service.py` (deferred to V2)
- Webhook handlers for payment verification
- `payment_config` updated to use Vault for secrets

**Completion Criteria:**
- ✅ Stripe payment processing working (test mode)
- ✅ Paystack payment processing working (test mode)
- ✅ Payment gateway selection functional via settings
- ✅ Webhook verification working (TODO 10: Webhook signature verification implemented)
- ✅ Payment status updated on successful payment
- ✅ Payment ID stored in booking
- ✅ Test/Live mode switching working
- ✅ External service failure handling implemented (TODO 12: Retry logic with backoff)
- **See:** [[[default:adr-global/adr-webhook-architecture|Webhook Architecture]]](C:/dev/project-library-global/adr-global/adr-webhook-architecture.md) ADR (Webhook Architecture), [[[default:adr-global/adr-real-time-and-background-tasks|Real-Time Updates and Background Task Architecture]]](C:/dev/project-library-global/adr-global/adr-real-time-and-background-tasks.md) ADR (Real-Time and Background Tasks)
- **See:** [[[default:specifications-global/spec-integration|Integration Specification]]](C:/dev/project-library-global/specifications-global/spec-integration.md) (Integration — Stripe, Paystack, Brevo patterns), [[[default:specifications-global/spec-api-specification|API Specification (HTTP Endpoints)]]](C:/dev/project-library-global/specifications-global/spec-api-specification.md) (API Specification — webhook endpoints)

### Stage 2.5: Invoicing System

**Deliverables:**
- `invoice` and `invoice_items` tables populated
- `server_payments/invoice_service.py` — invoice server functions
- `invoices/` package:
  - `InvoiceListForm` — invoice listing
  - `InvoiceEditorForm` — create/edit invoices
  - `InvoiceViewerForm` — invoice detail with PDF

**Completion Criteria:**
- ✅ Automatic invoice generation on booking completion
- ✅ Manual invoice creation working
- ✅ Invoice PDF generation functional
- ✅ Invoice email delivery via Brevo SMTP
- ✅ Invoice statuses working (Draft, Sent, Paid, Overdue, Cancelled)
- ✅ Payment recording updates invoice status
- ✅ Multi-currency display working
- **See:** [[[default:adr-global/adr-pdf-invoice-generation|PDF Invoice Generation]]](C:/dev/project-library-global/adr-global/adr-pdf-invoice-generation.md) ADR (PDF Invoice Generation)

---

## Phase 3: CRM + Email

**Goal:** Implement contacts, campaigns, segments, tasks, and landing pages — the customer engagement layer.

**Dependency:** Phase 2 must be complete (Services, Bookings, Payments, Invoicing)

### Stage 3.1: Contact Management

**Deliverables:**
- `contacts` table populated (unified person record)
- `contact_events` table — activity timeline
- `contact_counter` table — ID generation
- `customers` table (legacy — migrate to contacts)
- `crm/` package:
  - `ContactListForm` — contact listing with search/filter
  - `ContactEditorForm` — create/edit contacts
  - `ContactViewerForm` — contact detail with activity timeline
- `server_customers/contact_service.py` — contact server functions

**Completion Criteria:**
- ✅ Contacts can be created manually or auto-created from bookings/forms
- ✅ Contact activity timeline functional (bookings, emails, notes)
- ✅ Tags and segmentation working
- ✅ CSV import/export functional
- ✅ Contact lifecycle stages working (new, active, at_risk, lost)
- ✅ Lifetime value and transaction counts cached and updated
- ✅ No duplicate contacts (email-based deduplication)
- ✅ Empty state messages added (TODO 6)
- ✅ Loading state: contact list shows skeleton rows while data loads
- ✅ Error state: contact form shows inline validation errors; list shows retry on server failure
- ✅ Edge case: contact dedup with same email, different names — manual merge dialog when confidence < 90%
- ✅ Edge case: landing page lead submitted with duplicate email — updates existing contact, does not reject
- ✅ Database indexes added (TODO 8: contacts.email, bookings.contact_id, bookings.service_id indexed)
- ✅ Pagination added to large lists (TODO 9: 50 items per page)
- **See:** [[[default:adr-global/adr-data-access-patterns|Data Access Patterns and Query Limitations]]](C:/dev/project-library-global/adr-global/adr-data-access-patterns.md) ADR (Data Access Patterns — pagination, denormalization)

### Stage 3.2: Email Marketing (Brevo Campaigns)

**Deliverables:**
- `email_campaigns` table — campaign definitions
- `contact_campaigns` table — enrollment tracking
- `brevo_campaigns_integration.py` — Brevo API integration
- `marketing/` package:
  - `EmailCampaignListForm` — campaign listing
  - `EmailCampaignEditorForm` — campaign sequence builder
  - `EmailBroadcastForm` — one-off broadcasts
- Hourly background task for campaign processing

**Completion Criteria:**
- ✅ Pre-built campaign sequences functional (New Client Welcome, Appointment Follow-Up, Re-engagement, Referral Request)
- ✅ Campaign editor with configurable delays and triggers
- ✅ Contact enrollment working (event-based and manual)
- ✅ Brevo API integration functional (test mode)
- ✅ Open/click tracking via webhooks working
- ✅ Broadcast emails functional
- ✅ Unsubscribe handling working
- **See:** [[[default:adr-global/adr-real-time-and-background-tasks|Real-Time Updates and Background Task Architecture]]](C:/dev/project-library-global/adr-global/adr-real-time-and-background-tasks.md) ADR (Real-Time and Background Tasks — campaign enrollment task)
- **See:** [[[default:specifications-global/spec-integration|Integration Specification]]](C:/dev/project-library-global/specifications-global/spec-integration.md) (Integration — Brevo Campaigns API, SMTP patterns)

### Stage 3.3: Segmentation

**Deliverables:**
- `segments` table — segment definitions
- Pre-built segments: New Leads, Active Customers, Inactive Customers, High Value
- Segment contact counts accurate and cached
- Segments usable for campaign targeting

**Note:** Custom segment builder (filter by tags, status, spend, booking history) has been deferred to V2 per CEO review. Pre-built segments are sufficient for V1 campaign targeting.

**Completion Criteria:**
- ✅ Pre-built segments working (New Leads, Active Customers, Inactive Customers, High Value)
- ✅ Segment contact counts accurate and cached
- ✅ Segments usable for campaign targeting

### Stage 3.4: Tasks & Automation

**Deliverables:**
- `tasks` table — task definitions
- `crm/TaskListForm` — task management UI
- Background task for auto-generated tasks (daily 03:00)

**Completion Criteria:**
- ✅ Manual task creation against contacts
- ✅ Basic auto-generated tasks (follow-ups, review requests)
- ✅ Task completion tracking
- ✅ Task due dates and reminders working
- ✅ Tasks visible in admin dashboard

### Stage 3.5: Landing Pages

**Deliverables:**
- `landing_pages` table — landing page definitions
- `leads` table — captured leads
- `lead_captures` table — capture form configurations
- `landing_pages/` package:
  - `LandingPageListForm` — landing page listing
  - `LandingPageEditorForm` — landing page builder (3 templates)
- Simultaneous `leads` + `contacts` creation on capture

**Completion Criteria:**
- ✅ Three landing page templates working (Lead Capture, Event Registration, VSL)
- ✅ URL slug management (auto-generated, unique)
- ✅ Draft/Published status working
- ✅ Analytics tracking (views, conversions, conversion rate)
- ✅ Lead capture creates `leads` and `contacts` simultaneously
- ✅ `leads.converted_to_contact_id` set immediately
- ✅ Welcome campaign auto-enrollment working

---

## Phase 4: Content + Website

**Goal:** Implement blog, public website, and homepage templates — the public-facing layer.

**Dependency:** Phase 3 must be complete (CRM, Email, Landing Pages)

### Stage 4.1: Blog System

**Deliverables:**
- `blog_posts` and `blog_categories` tables populated
- `blog/` package:
  - `BlogListForm` — public blog listing
  - `BlogPostForm` — public blog post detail
  - `BlogEditorForm` — admin blog editor with rich text
  - `CategoryManagementForm` — category management
- `server_blog/service.py` — blog server functions

**Completion Criteria:**
- ✅ Blog posts can be created with rich text editor
- ✅ SEO fields functional (title, meta description, URL slug)
- ✅ Draft/Published/Archived statuses working
- ✅ Blog categories functional (create, edit, reorder)
- ✅ Public blog listing and post pages functional
- ✅ View count tracking working
- ✅ Author attribution working
- ✅ Form debounce implemented (TODO 5)

### Stage 4.2: Public Website Pages

**Deliverables:**
- `pages` table — static page definitions
- `public_pages/` package:
  - `HomePage` — home page with routing
  - `AboutPage` — about page
  - `ContactPage` — contact form page
  - `PrivacyPolicyPage` — privacy policy (reads from `privacy_policy` table via ClauseBuilder)
  - `TermsConditionsPage` — terms & conditions (reads from `t_and_c` table via ClauseBuilder)
- `ClauseBuilder` custom component — reusable clause-level editor parameterized by document type
- Policy editor screen in Settings → Website — uses ClauseBuilder for both Privacy Policy and T&Cs
- `privacy_policy` and `t_and_c` tables populated with master template starter clauses
- Routing dependency configured for public pages

**Completion Criteria:**
- ✅ All standard pages functional (Home, About, Contact, Privacy, Terms)
- ✅ Contact form submits and creates `contact_submissions` record
- ✅ Contact form notifications working (owner + auto-reply)
- ✅ ClauseBuilder component functional (category selection, seeded clauses, custom clauses, save)
- ✅ Privacy Policy page renders clauses from `privacy_policy` table
- ✅ Terms & Conditions page renders clauses from `t_and_c` table
- ✅ Policy editor screen functional in Settings → Website
- ✅ Policy version history display functional (publish timestamps, version tracking)
- ✅ Privacy and Terms pages editable from admin via clause builder
- ✅ Mobile responsive
- ✅ SEO meta tags functional
- ✅ Accessibility requirements verified (TODO 14: Keyboard navigation, screen reader support, contrast ratios)
- ✅ Responsive breakpoints verified (TODO 15: Mobile, tablet, desktop tested)

### Stage 4.3: Homepage Templates

**Deliverables:**
- `config` table `home_config` key for template configuration
- `public_pages/HomePage` with four template options:
  - Classic
  - Services
  - Booking
  - Minimalist
- Template configuration UI in Settings

**Completion Criteria:**
- ✅ Four homepage templates rendering correctly
- ✅ Template configuration UI functional
- ✅ Configurable sections (hero, features, services showcase, testimonials, CTA)
- ✅ Auto-save every 30 seconds
- ✅ Preview in new tab working
- ✅ Changes go live immediately on save

---

## Phase 5: Polish + Launch

**Goal:** Implement analytics, security hardening, compliance, and testing — the launch preparation.

**Dependency:** Phase 4 must be complete (Blog, Public Website, Homepage Templates)

### Stage 5.1: Dashboard Analytics

**Deliverables:**
- Enhanced `DashboardForm` with live analytics
- `server_analytics/reporting_service.py` — analytics server functions
- Charts and visualizations using M3 Plot components

**Completion Criteria:**
- ✅ Revenue metrics functional (total, period comparison, trend)
- ✅ Booking metrics functional (count, completion rate, cancellation rate)
- ✅ Customer metrics functional (total, new this month, churn)
- ✅ Period selector working (week, month, quarter, custom)
- ✅ Real-time data refresh working
- ✅ Charts rendering correctly
- **See:** [[[default:adr-global/adr-data-access-patterns|Data Access Patterns and Query Limitations]]](C:/dev/project-library-global/adr-global/adr-data-access-patterns.md) ADR (Data Access Patterns — pre-computed aggregations)

### Stage 5.2: Security Hardening

**Deliverables:**
- Enhanced RBAC review and testing
- Data encryption verification
- Audit logging review
- Rate limiting verification
- Input validation review

**Completion Criteria:**
- ✅ All server functions have correct RBAC decorators
- ✅ No secrets exposed to client code
- ✅ All sensitive data encrypted
- ✅ Audit log comprehensive and accurate
- ✅ Rate limiting functional and tested
- ✅ Input validation on all forms
- ✅ Session timeout working correctly

### Stage 5.3: Compliance Implementation

**Deliverables:**
- Privacy Policy page populated (reads from `privacy_policy` table via ClauseBuilder)
- Terms of Service page populated (reads from `t_and_c` table via ClauseBuilder)
- Cookie consent banner on public pages
- `export_my_data()` function — GDPR/POPIA data export (PDF with Mybizz-held and client-instance data separation)
- Dashboard entry point for Owner data export — RBAC-governed (Owner only)
- `delete_my_account()` function — GDPR/POPIA anonymization
- Consent recording on forms and booking flows

**Completion Criteria:**
- ✅ Privacy Policy and Terms pages accurate and published (clause builder functional)
- ✅ Cookie consent banner functional
- ✅ Data export function working (exports all user data as PDF)
- ✅ Data export UI accessible from Dashboard → Account → Download My Data
- ✅ Data export separates Mybizz-held records from client-instance data
- ✅ Account deletion/anonymization working
- ✅ Consent recording on all data collection points
- ✅ Unsubscribe handling from marketing emails

### Stage 5.4: Testing & QA

**Deliverables:**
- Pure logic tests for business logic
- Integration tests via Uplink
- End-to-end testing of critical flows
- Uplink connection configured
- Test data cleanup procedures

**Completion Criteria:**
- ✅ All pure logic tests passing (TODO 2: Test coverage requirements implemented)
- ✅ All integration tests passing
- ✅ Critical flows tested (booking, payment, email)
- ✅ Test data cleanup working
- ✅ No critical or high-severity issues
- ✅ Performance acceptable under expected load

### Stage 5.5: Launch Preparation

**Deliverables:**
- Documentation (user guides, admin guides, setup guides)
- Video tutorials (platform overview, first booking, payments setup)
- Onboarding checklist
- Production deployment plan
- Monitoring and alerting configured
- Backup/restore procedures implemented

**Completion Criteria:**
- ✅ All documentation complete and accurate
- ✅ Video tutorials created
- ✅ Onboarding checklist functional
- ✅ Production deployment plan documented (TODO 4: Deployment procedures implemented)
- ✅ Monitoring and alerting active (TODO 3: Observability requirements implemented)
- ✅ Backup/restore procedures documented and tested (TODO 13)
- ✅ First beta client ready for onboarding
- ✅ Support channels configured

---

## Inter-Process Dependencies

### Critical Path
```
Phase 0 (Pre-Implementation) → Phase 1 (Foundation) → Phase 2 (Transactional Core) → Phase 3 (CRM + Email) → Phase 4 (Content + Website) → Phase 5 (Polish + Launch)
```

### Cross-Phase Dependencies
- **Phase 1 depends on Phase 0:** Test coverage, observability, deployment procedures must be defined first
- **Phase 2 depends on Phase 1:** Services need users (providers), bookings need contacts (from Phase 3, but contacts table exists in Phase 1), payments need Vault (Phase 1)
- **Phase 3 depends on Phase 2:** Contacts auto-created from bookings, campaigns triggered by booking events
- **Phase 4 depends on Phase 3:** Blog and landing pages feed contacts, public website uses contact forms
- **Phase 5 depends on Phase 4:** Analytics uses data from all phases, security hardening covers all features

### Data Flow Dependencies
- `users` table (Phase 1) → all other tables
- `contacts` table (Phase 3) → bookings, tasks, campaigns
- `services` table (Phase 2) → bookings
- `bookings` table (Phase 2) → invoices, contact_events
- `email_campaigns` table (Phase 3) → contact_campaigns
- `landing_pages` table (Phase 3) → leads → contacts

**See:** [[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR (Anvil Platform Constraints) for platform-specific implementation guidance.

---

## Completion Criteria Summary

| Phase | Complete When |
|-------|---------------|
| 0 | Test coverage, observability, deployment procedures defined; Vault error handling implemented |
| 1 | Auth, dashboard, settings, Vault functional with no errors; RBAC coverage verified |
| 2 | Services, bookings, payments, invoicing operational end-to-end; Webhook verification complete |
| 3 | Contacts, campaigns, segments, tasks, landing pages integrated; Empty states, indexes, pagination implemented |
| 4 | Blog, public website, homepage templates live and functional; Accessibility, responsive breakpoints verified |
| 5 | Analytics, security, compliance, testing complete and passing; Deployment procedures tested |

---

## Risk Mitigation

### Technical Risks (All Addressed)
- **Vault complexity:** Addressed in Phase 0 (TODO 1) — mitigated
- **Brevo API rate limits:** Addressed in Phase 3 — mitigated
- **Payment webhook idempotency:** Addressed in Phase 2 (TODO 10) — mitigated
- **Contact deduplication:** Addressed in Phase 3 — mitigated
- **RBAC coverage:** Addressed in Phase 1 (TODO 11) — mitigated
- **External service failures:** Addressed in Phase 2 (TODO 12) — mitigated
- **Anvil platform constraints:** All phases — [[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR documents Skulpt limits, lazy rows, server latency, background tasks

### Operational Risks (All Addressed)
- **Onboarding friction:** Keep Phase 1 simple and intuitive
- **Data migration:** Have clear rollback procedures (TODO 4) — mitigated
- **Testing gaps:** Addressed in Phase 0 & 5 (TODO 2) — mitigated
- **Observability gaps:** Addressed in Phase 0 (TODO 3) — mitigated
- **Deployment risks:** Addressed in Phase 0 (TODO 4) — mitigated
- **Backup/restore:** Addressed in Phase 5 (TODO 13) — mitigated

---

## Post-Launch

### V2 Planning

After Phase 5 launch, begin V2 development:
- Client portal (customer self-service)
- Advanced analytics (revenue by service/provider, marketing ROI)
- Complex task automation
- PayPal integration
- Additional homepage templates
- Time tracking (logging billable hours against bookings/services)
- Custom segment builder (filter by tags, status, spend, booking history)

---

## Self-Review Checklists

**Source:** CEO review 2026-06-15 Section 5

For a solo developer, self-review is where bugs hide. Complete the following checklist before marking any stage as done.

### Phase 1 Self-Review (Foundation)

- [ ] All server functions have RBAC decorators — grep for `@authenticated_endpoint` or equivalent
- [ ] No secrets in client code — search for hardcoded API keys, passwords, or tokens
- [ ] Settings form tabs respect RBAC visibility — test with each role
- [ ] Vault TOTP gate works on every open — no grace period
- [ ] Currency selection is immutable after first transaction — test the guardrail
- [ ] Navigation links resolve correctly — no dead links or 404s
- [ ] Mobile responsive layout verified at 375px minimum width

### Phase 2 Self-Review (Transactional Core)

- [ ] Payment webhook signature verification rejects invalid signatures
- [ ] Idempotency check prevents duplicate payment processing
- [ ] Booking status transitions are correct (PENDING → CONFIRMED → COMPLETED/CANCELLED)
- [ ] Double-booking prevention works — test concurrent booking attempts
- [ ] Invoice amounts match booking amounts (including tax/currency)
- [ ] External service failures handled gracefully (Brevo, Stripe, Paystack)
- [ ] All financial calculations use server-side functions (not Skulpt)

### Phase 3 Self-Review (CRM + Email)

- [ ] Contact deduplication works — same email updates existing contact
- [ ] Campaign enrollment does not duplicate contacts
- [ ] Unsubscribe handling works — contact removed from future sends
- [ ] Empty states display correctly for all list views
- [ ] Pagination works correctly with 50-item pages
- [ ] Landing page lead capture creates both `leads` and `contacts` simultaneously
- [ ] Database indexes verified on high-query columns

### Phase 4 Self-Review (Content + Website)

- [ ] Public pages render correctly on mobile, tablet, desktop
- [ ] SEO meta tags present on all public pages
- [ ] Keyboard navigation works for all interactive elements
- [ ] Screen reader support verified for key flows
- [ ] Contact form creates `contact_submissions` record
- [ ] ClauseBuilder saves correctly to both `privacy_policy` and `t_and_c` tables
- [ ] Form debounce prevents double-submissions

### Phase 5 Self-Review (Polish + Launch)

- [ ] All critical flows tested end-to-end (booking, payment, email)
- [ ] RBAC coverage matrix verified for all roles and forms
- [ ] Error path tests pass (payment failure, webhook timeout, service down)
- [ ] Backup/restore procedure tested and documented
- [ ] Deployment rollback procedure tested
- [ ] Monitoring and alerting configured and tested
- [ ] No critical or high-severity issues in error_log

---

## Review Task Integration Summary

### CEO Review Tasks (15 total)
- **P1 Critical (4):** TODO 1-4 → Phase 0 Stages 0.1-0.4
- **P2 Edge Cases (11):** TODO 5-15 → Embedded in Phases 1-5 completion criteria

### Engineering Review Tasks (15 total)
- **P1 Critical (4):** TODO 1-4 → Phase 0 Stages 0.1-0.4
- **P2 Edge Cases (11):** TODO 5-15 → Embedded in Phases 1-5 completion criteria

### Task Distribution
| Task | Phase | Stage |
|------|-------|-------|
| TODO 1: Vault Error Handling | 0 | 0.1 |
| TODO 2: Test Coverage | 5 | 5.4 |
| TODO 3: Observability | 5 | 5.5 |
| TODO 4: Deployment Procedures | 5 | 5.5 |
| TODO 5: Form Debounce | 4 | 4.1 |
| TODO 6: Empty States | 3 | 3.1 |
| TODO 7: Partial Failure | 3 | 3.1 |
| TODO 8: DB Indexes | 3 | 3.1 |
| TODO 9: Pagination | 3 | 3.1 |
| TODO 10: Webhook Verification | 2 | 2.4 |
| TODO 11: RBAC Coverage | 1 | 1.2 |
| TODO 12: External Service Failures | 2 | 2.4 |
| TODO 13: Backup/Restore | 5 | 5.5 |
| TODO 14: Accessibility | 4 | 4.2 |
| TODO 15: Responsive Breakpoints | 4 | 4.2 |

---

*End of file — build-plan.md (Definitive)*
