---
title: "platform-architecture"
doc-id: "mb-dc62ff9a"
updated: "2026-10-29"
---
# Mybizz CS — Platform Architecture

**Vertical:** Consulting & Services
**Architecture:** Single vertical, dependency-based (master_template → client instances)
**UI Theme:** Material Design 3 (M3)
**Platform Capacity:** 100 clients (V1.x)
**Schema:** 44 tables (see [[[mb-3-cs-project-context:docs-local/authoritative-schema|Authoritative Database Schema]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/authoritative-schema.md))

**Note:** The five-app dependency model (development workspace / master_template /
blank_client_template / client instances / platform-operator tooling) is Mybizz-wide — see
[[[default:specifications-global/spec-five-app-architecture-model|Five-App Architecture Model (Specification)]]](C:/dev/project-library-global/specifications-global/spec-five-app-architecture-model.md). This document contains only this app's specific
instantiation of that model and its own feature set.

---

## 1. What Mybizz CS Is

Mybizz CS is the Consulting & Services instantiation of the Mybizz platform — a complete online
business management platform delivered as a subscription SaaS product. Each subscriber receives
a fully operational, isolated Anvil application instance — their own online presence and business
management system, from public website through to CRM, bookings, payments, and reporting.

**Target businesses:** salons, spas, therapists, personal trainers, physiotherapists,
consultants, coaches, clinics, studios, and any business that sells services — either as timed
appointments or as fixed-price deliverables.

---

## 2. The Strategic Decision — Single Vertical

Mybizz was originally designed as a multi-vertical open platform supporting four business types:
Consulting & Services, E-commerce, Hospitality, and Membership. The decision was made to narrow
this app to a single vertical — Consulting & Services only.

The entire codebase, database schema, and planning documents for `mb-3-cs` are scoped to
Consulting & Services. Products, orders, shopping carts, shipping, room reservations, membership
tiers, subscription billing, and hospitality-specific features are explicitly out of scope for
this app.

---

## 3. This App's Five-App Instances

Per the Mybizz-wide model ([[[default:specifications-global/spec-five-app-architecture-model|Five-App Architecture Model (Specification)]]](C:/dev/project-library-global/specifications-global/spec-five-app-architecture-model.md)), this app's actual
instance names:

| App | Instance Name |
|---|---|
| App 1 — Development workspace | `mb-3-cs` |
| App 2 — Published dependency | `master_template` |
| App 3 — Provisioning clone source | `blank_client_template` |
| App 4 — Client instances | `[client-name]-cs` |
| App 5 — Platform operator tooling | `Mybizz_management` (deferred, post-launch) |

**Note:** `mb-2-cs` was the original development application and has been deprecated. All
development has migrated to `mb-3-cs`.

---

## 4. Core Features (This App's V1 Scope)

### 4.1 Authentication & Administration
- Email/password login, password reset
- RBAC roles: Owner, Manager, Admin, Staff, Customer
- Rate-limited login, account lockout, full audit trail
- Dashboard: revenue, booking count, customer totals — with period comparison
- Settings: business profile, theme, currency, user & permissions management
- The Vault: encrypted secrets store for all API keys — owner-only access

### 4.2 Public-Facing Website
- Standard pages: Home, About, Contact (with enquiry form), Privacy Policy, Terms & Conditions
- Dynamic pages: Services listing & detail, Blog listing & posts
- Home page: Four configurable templates (Classic, Services, Booking, Minimalist)
- Landing pages: Lead Capture, Event Registration, Video Sales Letter

### 4.3 Service Catalogue
Two pricing models — both fully supported:

| Model | Description | Example |
|---|---|---|
| `duration` | Price tied to a time block | 60-minute massage at R450 |
| `unit` | Price tied to a deliverable | Logo design at R3,500 |

### 4.4 Bookings & Appointments
- Service selection → provider selection → date & time → meeting type → intake form → payment → confirmation
- Calendar view: month, week, day
- Availability management: business hours, blocked dates, provider-specific availability
- Automatic reminders: 24 hours before and 1 hour before

### 4.5 Payments & Invoicing
- Payment gateways: Stripe (global), Paystack (Africa) — one per client instance
- Invoicing: automatic invoice generation on booking completion — PDF and email delivery

### 4.6 CRM & Contacts
- Contact database with Lead/Customer/Inactive status
- Full activity timeline per contact
- Tags and segmentation, CSV import/export

### 4.7 Email & Campaigns
Three distinct email systems — Anvil built-in (system emails), Brevo SMTP (transactional),
Brevo Campaigns (marketing). **See:** [[[default:specifications-global/spec-integration|Integration Specification]]](C:/dev/project-library-global/specifications-global/spec-integration.md) (Integration — Brevo patterns).

### 4.8 Blog & Content, Tasks & Segmentation
Full blog with rich text editor; manual and basic auto-generated tasks; pre-built segments.

---

## 5. Technical Stack

| Layer | Technology | Notes |
|---|---|---|
| Platform | Anvil.works | Full-stack Python, built-in hosting & HTTPS |
| UI | Material Design 3 (M3) | Dependency ID: 4UK6WHQ6UX7AKELK |
| Database | Anvil Data Tables | PostgreSQL-backed, 44 tables |
| Authentication | Anvil Users service | Built-in, RBAC via custom decorators |
| Anvil Secrets | `encryption_key` only | One item only — used by `vault_service.py` |

---

## 6. This App's Business Model

- Maximum clients: 100 (V1.x)
- First 10 clients (Launch): $25/month, grandfathered for life
- Clients 11–50 (Pioneer): $35/month, grandfathered for life
- Clients 51+ (Business): $50/month, price not fixed for life

---

## 7. Platform Constraints Summary

The platform is designed to work within Anvil's specific parameters. Key constraints:

- **Skulpt client-side runtime:** Not all Python 3 stdlib works. Money and date logic lives in
  server modules only. See [[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR.
- **No native URL routing:** Public pages use the community routing library. Admin portal uses
  lambda/Link/`open_form`. See [[[default:adr-global/adr-navigation-lambda-link-open-form|Navigation Standard: Lambda/Link/open_form]]](C:/dev/project-library-global/adr-global/adr-navigation-lambda-link-open-form.md) ADR, [[[default:adr-global/adr-ui-customization-approach|UI Customization Approach]]](C:/dev/project-library-global/adr-global/adr-ui-customization-approach.md) ADR.
- **Lazy-loaded Data Table rows:** Never iterate without `.search()` or `.limit_to()`. See
  [[[default:adr-global/adr-data-access-patterns|Data Access Patterns and Query Limitations]]](C:/dev/project-library-global/adr-global/adr-data-access-patterns.md) ADR.
- **Server call latency:** Every `anvil.server.call()` is an HTTP round-trip. Minimize in
  user-facing flows. See [[[default:adr-global/adr-form-architecture-and-state|Form Architecture and State Management]]](C:/dev/project-library-global/adr-global/adr-form-architecture-and-state.md) ADR.
- **No WebSocket push:** Real-time updates use client-side timer polling. See [[[default:adr-global/adr-real-time-and-background-tasks|Real-Time Updates and Background Task Architecture]]](C:/dev/project-library-global/adr-global/adr-real-time-and-background-tasks.md) ADR.

**See:** [[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR (Anvil Platform Constraints and Design Boundaries)

---

## Related Specifications

| Specification | Relevance |
|---|---|
| [[[default:specifications-global/spec-five-app-architecture-model\|Five-App Architecture Model (Specification)]]](C:/dev/project-library-global/specifications-global/spec-five-app-architecture-model.md) | Five-app architecture model (Mybizz-wide) |
| [[[default:specifications-global/spec-client-activation-runbook\|Client Instance Activation Runbook]]](C:/dev/project-library-global/specifications-global/spec-client-activation-runbook.md) | Client provisioning and activation procedures |
| `spec-nomenclature.md` | Naming conventions for files, forms, components, tables |
| [[[default:specifications-global/spec-integration\|Integration Specification]]](C:/dev/project-library-global/specifications-global/spec-integration.md) | Brevo, Stripe, Paystack integration patterns |
| [[[default:specifications-global/spec-regulatory-compliance-baseline\|Regulatory Compliance Baseline (Specification)]]](C:/dev/project-library-global/specifications-global/spec-regulatory-compliance-baseline.md) | POPIA, GDPR, PCI DSS compliance |
| [[[default:specifications-global/spec-security-architecture\|Security & Secrets Architecture]]](C:/dev/project-library-global/specifications-global/spec-security-architecture.md) | RBAC role limits, token security |

---

*Platform Architecture v3.0 — Five-app model extracted to
[[[default:specifications-global/spec-five-app-architecture-model|Five-App Architecture Model (Specification)]]](C:/dev/project-library-global/specifications-global/spec-five-app-architecture-model.md). This document contains only this app's specific
instances, feature scope, and business model.*
