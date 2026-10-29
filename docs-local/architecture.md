---
title: "architecture"
doc-id: "mb-7d6be180"
updated: "2026-10-29"
---
# Mybizz CS — Architecture

**Vertical:** Consulting & Services only
**Architecture:** Dependency-based (master_template → client instances)
**Schema:** 44 tables (see [[[mb-3-cs-project-context:docs-local/authoritative-schema|Authoritative Database Schema]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/authoritative-schema.md))

---

## 1. The Five-App System

Mybizz runs on five applications; Apps 1, 2, 3 and 5 are on Mybizz's Anvil account and each client instance is on its client's own account (see [[[default:adr-global/adr-client-instance-readme-five-app-system|Mandatory README in Every Client Instance Documenting the Five-App System]]](C:/dev/project-library-global/adr-global/adr-client-instance-readme-five-app-system.md) ADR):

### mb-3-cs
The development workspace (App 1, `mb-3-cs-app1`). All feature work happens here. Connected to GitHub as the shared
source of truth between Anvil.works and the local copy agents work in. Never a dependency for any client instance.

### master_template
The sole source of all server-side logic and UI forms for every client instance. Receives
App 1's tagged releases, pushed into its repository by the developer (Step 42). A frozen, versioned snapshot of mb-3-cs's proven work — never given to
clients directly. Client instances depend on it entirely; there is no copy to fall out of date
and no separate "deploy" step for a client instance — it simply reflects whatever
`master_template` currently contains.

### blank_client_template
The provisioning clone source. Contains the 44-table schema ([[[mb-3-cs-project-context:docs-local/authoritative-schema]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/authoritative-schema.md)), one startup module, and
`master_template` as a dependency. **Zero server modules, zero forms.** Every new client instance
is cloned from this app, not built from scratch.

### `[client-name]-cs` (one per paying client)
Contains the schema, the startup module, and an active dependency on `master_template`. Nothing
else. **This emptiness is intentional, not incomplete** — see the warning below.

### Mybizz_management
The platform operator's internal tooling — client provisioning, health monitoring, billing
automation, update distribution. A separate Anvil application, never part of `master_template`.
Deferred to post-launch; built only after `master_template` reaches launch and the first client
has been manually onboarded.

Client instances expose management HTTP endpoints from day one so that Mybizz_management can
communicate with them when built. **See:** [[[default:specifications-global/spec-api-specification|API Specification (HTTP Endpoints)]]](C:/dev/project-library-global/specifications-global/spec-api-specification.md) (API Specification — management endpoints),
[[[default:adr-global/adr-mybizz-management-visibility|Mybizz_management Visibility and Control]]](C:/dev/project-library-global/adr-global/adr-mybizz-management-visibility.md) ADR.

**See:** [[[default:adr-global/adr-client-instance-readme-five-app-system|Mandatory README in Every Client Instance Documenting the Five-App System]]](C:/dev/project-library-global/adr-global/adr-client-instance-readme-five-app-system.md) ADR (Mandatory README in Every Client Instance)

---

## ⚠️ Do Not Add Forms or Server Modules to a Client Instance

Because a client instance (App 4) inherits all logic and UI live from `master_template`, adding a
form or server module directly to a client instance does not extend that instance — it silently
and permanently overrides the dependency for that instance only, with no error or warning. That
instance stops receiving any future update to `master_template`. If a change is needed for a
specific client, it belongs in `mb-3-cs`, tested against a private client instance, then released
into `master_template` (Step 42) — after which every client instance, including the
one that prompted the change, receives it automatically. Per [[[default:adr-global/adr-client-instance-readme-five-app-system|Mandatory README in Every Client Instance Documenting the Five-App System]]](C:/dev/project-library-global/adr-global/adr-client-instance-readme-five-app-system.md) ADR, every client instance
carries a README stating this explicitly from the moment of provisioning; if you are working
directly in a client instance app and don't see that README, stop and locate it before proceeding.

---

## 2. Client Instances — Clone Lineage

Each subscriber's client instance is provisioned by cloning `blank_client_template` (App 3), not
by building a fresh app. The clone brings the schema, the startup module, and the
`master_template` dependency — nothing else.

- **Data isolation is structural.** Each client is a separate Anvil app with a separate
  database. `app_tables` always resolves to the current client's data. Cross-client access is
  architecturally impossible.
- **`master_template` is stateless.** It contains zero client data. All state lives in the
  client's own Data Tables.
- **Clients never see source code.** `master_template` is consumed as a compiled dependency.

---

## 3. Development Workflow

Agents work in the local copy of App 1 and the developer pushes to GitHub; Anvil syncs from GitHub. Changes made in the Anvil
Designer are pulled into the local copy with Step 18's pull procedure before any agent edits code. Designer work must always
precede code work for any given form. This workflow applies to `mb-3-cs` only — see the warning
above regarding client instances.

---

## 4. Package Structure

Feature-based organization. Packages are never renamed or merged during active development.

```
client_code/
  auth/          layouts/       dashboard/     settings/
  bookings/      services/      customers/     crm/
  blog/          public_pages/  shared/

server_code/
  server_auth/       server_settings/   server_dashboard/
  server_bookings/   server_services/   server_customers/
  server_marketing/  server_payments/   server_blog/
  server_shared/     server_analytics/  server_emails/
```

**Note:** The client-side CRM and marketing package is named `crm/` — not `marketing/`. The
server-side package remains `server_marketing/`.

**See:** [[[mb-3-cs-project-context:adr-local/crm-package-replaces-marketing|Package Name: crm/ Replaces marketing/]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/adr-local/crm-package-replaces-marketing.md) ADR

---

## 5. Module Architecture

See [[[default:specifications-global/spec-anvil-platform-standards|Anvil Platform Mechanics]]](C:/dev/project-library-global/specifications-global/spec-anvil-platform-standards.md) Section 3 for the general Three-Tier Module Pattern (Forms /
Client Modules / Server Modules / Pure Logic Modules) — not repeated here. This project's specific
authority for the pattern: [[[default:adr-global/adr-form-architecture-and-state|Form Architecture and State Management]]](C:/dev/project-library-global/adr-global/adr-form-architecture-and-state.md) ADR (Form Architecture and State Management).

---

## 6. Navigation Architecture

See [[[default:specifications-global/spec-m3-design-standards|M3 Design & Component Standards]]](C:/dev/project-library-global/specifications-global/spec-m3-design-standards.md) Section 7 for the general authenticated/public navigation patterns
(plain `Link` vs. Routing, lambda loop-capture) — not repeated here. This project's specific
choices and menu structures:

| Area | System |
|---|---|
| Admin & customer portal | Native M3 Layouts (`NavigationRailLayout` / `NavigationDrawerLayout`) + plain Link components + lambda click handlers |
| Public website pages | Anvil Routing dependency (`@router.route`) |

**This project uses native M3 Layouts** (`NavigationRailLayout`, `NavigationDrawerLayout`) for
layout shells, per the [[[default:adr-global/adr-htmltemplate-use|HTMLTemplate Use]]](C:/dev/project-library-global/adr-global/adr-htmltemplate-use.md) ADR. `HTMLTemplate` is banned — all layout-shell cases
use the native M3 Layouts. Sidebar navigation uses plain `Link` components with lambda click
handlers (not `NavigationLink`). `navigate_to` is not used.

**See:** [[[default:adr-global/adr-htmltemplate-use|HTMLTemplate Use]]](C:/dev/project-library-global/adr-global/adr-htmltemplate-use.md) ADR, [[[default:adr-global/adr-navigation-lambda-link-open-form|Navigation Standard: Lambda/Link/open_form]]](C:/dev/project-library-global/adr-global/adr-navigation-lambda-link-open-form.md) ADR, [[[default:adr-global/adr-ui-customization-approach|UI Customization Approach]]](C:/dev/project-library-global/adr-global/adr-ui-customization-approach.md) ADR

### AdminLayout Navigation Structure
```
Dashboard                          (always)
▼ Sales & Operations
  Bookings                         (always)
  Services                         (always)
▼ Customers & Marketing
  Contacts                         (always)
  Campaigns                        (marketing_enabled)
  Broadcasts                       (marketing_enabled)
  Segments                         (marketing_enabled)
  Tasks                            (marketing_enabled)
▼ Content & Website
  Blog                             (blog_enabled)
  Pages                            (always)
  Media                            (always)
▼ Finance & Reports
  Invoices                         (always)
  Payments                         (always)
  Reports                          (always)
  Analytics                        (always)
  Time Entries                     (always)
Settings                           (Owner / Manager only)
Vault                              (Owner only)
[Sign out — bottom of sidebar, all roles]
```

### CustomerLayout Navigation Structure
```
My Dashboard                       (always)
My Bookings                        (always)
My Invoices                        (always)
My Reviews                         (always)
Support Tickets                    (always)
Account                            (always)
[Sign out — bottom of sidebar]
```

**Note:** The CustomerLayout is deferred to V2. Customers book via the public site and the
business manages all bookings from the admin panel in V1.

---

## 7. Startup and Authentication Routing

The Startup Form is `auth.LoginForm` (defined in `anvil.yaml`). The `__init__` method checks
authentication and routes before `init_components()`:

1. If logged in as Owner/Manager/Admin/Staff → open `DashboardForm` with AdminLayout, return
2. If logged in as Customer → open customer portal with CustomerLayout, return
3. If not logged in → proceed with LoginForm display

Calling `open_form()` in `__init__` must always be followed by `return`.

**See:** [[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR (Anvil Platform Constraints — server call latency)

---

## 8. The Sidesheet

The M3 Sidesheet is available on `NavigationDrawerLayout` and `NavigationRailLayout`. It is
appropriate for secondary content or detail panels alongside main content without replacing it.
`NavigationRailLayout` with Sidesheet is well-suited to step-by-step guided workflows: pass
accumulated data via `self.item` between steps; submit to the server in a single call at the
final step.

---

## Related Specifications

| Specification | Relevance |
|---|---|
| [[[default:specifications-global/spec-anvil-platform-standards\|Anvil Platform Mechanics]]](C:/dev/project-library-global/specifications-global/spec-anvil-platform-standards.md) | Platform mechanics, server patterns, data access |
| [[[default:specifications-global/spec-anvil-spec-table\|Anvil Specification Table]]](C:/dev/project-library-global/specifications-global/spec-anvil-spec-table.md) | Comprehensive Anvil component and pattern reference |
| [[[default:specifications-global/spec-five-app-architecture-model\|Five-App Architecture Model (Specification)]]](C:/dev/project-library-global/specifications-global/spec-five-app-architecture-model.md) | Five-app architecture (Mybizz-wide) |
| `spec-nomenclature.md` | Naming conventions for files, forms, components, tables |
| [[[default:specifications-global/spec-security-architecture\|Security & Secrets Architecture]]](C:/dev/project-library-global/specifications-global/spec-security-architecture.md) | RBAC role limits, token security, regulatory posture |
| [[[default:specifications-global/spec-security\|Security Specification]]](C:/dev/project-library-global/specifications-global/spec-security.md) | General security patterns, rate limiting, numeric standards |
| [[[default:specifications-global/spec-vault-system\|The Vault System]]](C:/dev/project-library-global/specifications-global/spec-vault-system.md) | Vault architecture, two-level secrets model |

---

*Architecture v2.0 — Section 1 corrected from three named apps to the actual five-app system
([[[default:adr-global/adr-client-instance-readme-five-app-system|Mandatory README in Every Client Instance Documenting the Five-App System]]](C:/dev/project-library-global/adr-global/adr-client-instance-readme-five-app-system.md) ADR); Section 2 clarified to state the `blank_client_template` clone lineage explicitly; a
warning against modifying client instances added. Sections 5–6 trimmed of content now duplicated
in the Standards Library ([[[default:specifications-global/spec-anvil-platform-standards|Anvil Platform Mechanics]]](C:/dev/project-library-global/specifications-global/spec-anvil-platform-standards.md), [[[default:specifications-global/spec-m3-design-standards|M3 Design & Component Standards]]](C:/dev/project-library-global/specifications-global/spec-m3-design-standards.md)).*

**Outstanding, not fixed here:** [[[default:adr-global/adr-client-instance-readme-five-app-system|Mandatory README in Every Client Instance Documenting the Five-App System]]](C:/dev/project-library-global/adr-global/adr-client-instance-readme-five-app-system.md) ADR states "the 36-table schema" for `blank_client_template`;
[[[mb-3-cs-project-context:docs-local/authoritative-schema|Authoritative Database Schema]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/authoritative-schema.md) confirms 44 tables. That correction belongs in [[[default:adr-global/adr-client-instance-readme-five-app-system|Mandatory README in Every Client Instance Documenting the Five-App System]]](C:/dev/project-library-global/adr-global/adr-client-instance-readme-five-app-system.md) ADR itself.
