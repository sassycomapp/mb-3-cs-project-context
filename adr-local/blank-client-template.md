---
title: "blank-client-template"
doc-id: "mb-8829bc8a"
updated: "2026-11-01"
---
# `blank-client-template` ADR — App 3 as the Provisioning Clone Source

**Status:** Accepted
**Date:** 2026-10-31
**Authority:** [[[default:adr-global/adr-client-instance-architecture|Client Instance Architecture]]](C:/dev/project-library-global/adr-global/adr-client-instance-architecture.md) ADR, [[[default:adr-global/adr-dependency-update-model|Dependency Update Model]]](C:/dev/project-library-global/adr-global/adr-dependency-update-model.md) ADR, [[[default:specifications-global/spec-five-app-architecture-model|Five-App Architecture Model (Specification)]]](C:/dev/project-library-global/specifications-global/spec-five-app-architecture-model.md). Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct.

---

## Context

Every client instance needs the full schema of `authoritative-schema.md` before `master_template`'s server modules can work. Anvil cannot create Data Tables by code, and a new Anvil app has no tables. A client instance must also never hold its own forms or server modules: one would silently override `master_template` for that instance and stop it receiving updates (reference section 2).

Three ways of giving a new instance its tables exist:

1. **Clone `master_template`.** The clone gets its own copy of every form and server module, which override the dependency and break updates.
2. **Create the tables by hand for each client.** Every table and column by hand, for every client; error-prone and not workable at 100 clients.
3. **Clone a dedicated blank app** that holds the schema, a startup module and the dependency, and nothing else.

---

## Decision

**App 3, `mb-3-cs-app3` (`blank_client_template`), is the only clone source for new client instances.** It is held on the Mybizz development account.

### Contents of App 3

| Component | Present | Notes |
|---|---|---|
| Every table of `authoritative-schema.md` | Yes | The released schema; no data |
| One startup module | Yes | Opens `master_template`'s startup form |
| `master_template` (App 2) as a dependency | Yes | Inherited by every clone |
| Scheduled tasks | Yes | The schedule of `integration-architecture.md` (Background Tasks), calling `master_template`'s background tasks; inherited by every clone |
| README stating the five-app rule | Yes | [[[default:adr-global/adr-client-instance-readme-five-app-system\|Mandatory README in Every Client Instance Documenting the Five-App System]]](C:/dev/project-library-global/adr-global/adr-client-instance-readme-five-app-system.md) ADR |
| Server modules | No | Never added |
| Forms | No | Forms live in `master_template` |
| Anvil Secrets | No | `encryption_key` is set per instance at provisioning |
| Custom domain | No | The client's own domain is connected per instance at onboarding step 8 |
| Email service setting | No | Anvil's Email service is set to Custom Mail Server with the SES SMTP endpoint per instance at provisioning |
| Data | No | The sample data for the client's trade is loaded per instance at provisioning, and recorded in `sample_rows` |

### The startup module

The startup module opens `master_template`'s startup form, `auth.LoginForm`, which routes by role (reference section 10):

```python
from anvil import open_form
from {app2_package}.auth.LoginForm import LoginForm

open_form(LoginForm())
```

`{app2_package}` is the `package_name` in App 2's `anvil.yaml`. It is the only place where code imports by package name; every module inside `master_template` uses relative imports (`scaffold-spec.md`).

### Provisioning

1. Mybizz opens a new Mybizz-owned Anvil Business account for the client.
2. App 3 is cloned into that account and named `[client-name]-cs`.
3. The clone has the schema, the startup module, the scheduled tasks, the README and the dependency on `master_template`.
4. The remaining steps follow `mb-3-cs-architecture-reference.md` section 5 and the [[[default:specifications-global/spec-client-activation-runbook|Client Instance Activation Runbook]]](C:/dev/project-library-global/specifications-global/spec-client-activation-runbook.md).

### Schema responsibility

App 3 changes only when the schema changes. Every schema change is made in App 1, recorded in `schema-migration-log.md`, applied to App 3, and applied by hand to every existing client instance. No release that changes the schema is pushed into App 2 (Step 42) without that migration plan (`deployment-procedures.md`).

---

## Consequences

- A new client instance is correctly structured in minutes.
- Server logic, forms and UI reach every instance through the dependency from its first day.
- Every instance starts with the same schema and the same schedule.
- App 3 needs schema maintenance only.
- Schema changes to running instances are manual, which limits how freely the schema changes after launch.

---

*End of `blank-client-template` ADR*
