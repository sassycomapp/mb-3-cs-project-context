---
title: "deployment-procedures"
doc-id: "mb-f46b55a9"
updated: "2026-11-01"
---
# Mybizz CS — Deployment Procedures

How a release moves from App 1 through App 2 to the client instances, and how it is rolled back.

**Standard:** [[[default:specifications-global/spec-deployment]]](C:/dev/project-library-global/specifications-global/spec-deployment.md)

**Authority:** [[[default:adr-global/adr-dependency-update-model|Dependency Update Model]]](C:/dev/project-library-global/adr-global/adr-dependency-update-model.md) ADR, [[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR. Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct. Where the global standard describes client instances on the client's own Anvil account, this project differs: every client instance runs on a Mybizz-owned Anvil Business account (reference section 4).

---

## Deployment Model

| App | Role in deployment | Hosted on |
|---|---|---|
| App 1 — `mb-3-cs-app1` | Development and testing; each release is tagged here (`vMAJOR.MINOR.PATCH`) | Mybizz development account |
| App 2 — `mb-3-cs-app2`, `master_template` | The release: holds exactly App 1's latest released tag; every client instance depends on it | Mybizz development account |
| App 3 — `mb-3-cs-app3`, `blank_client_template` | The released schema, no data, the scheduled tasks; new instances are cloned from it | Mybizz development account |
| App 4 — `[client-name]-cs` | Each client instance: schema, startup module, dependency on App 2 | One Mybizz-owned client hosting account (Business plan) per client |
| Demo apps — `demo.mybizz.live`, `try.mybizz.live` | Two instances of the release for prospects (F-36), reset nightly | Mybizz development account |
| App 5 — `mb-3-cs-app5` | Receives heartbeats and usage reports; records releases; sends release notes | Mybizz development account |

### The update flow

```
App 1 (tagged release, e.g. v1.4.0)
    ↓ the developer pushes the tag into App 2's repository (Step 42)
App 2 — master_template
    ↓ automatically, through the dependency
Every client instance
```

- Server logic, forms and UI reach every client instance through the dependency; there is no per-client update action.
- The schema does not travel through the dependency. A schema change is applied to App 3 and to each existing instance by hand (Schema Migration).
- No form or server module is ever added to a client instance; one would silently override `master_template` for that instance (reference section 2).
- Testing before release happens on the local Anvil App Server and on a private test client instance on its own hosting account. There is no staging environment.

### Release steps

1. **Feature complete** in App 1 and signed off (`build-verified`).
2. **Release ready:** every test passes and no High or Critical finding is open (Step 41).
3. **Tag** the release in App 1.
4. **Back up** every client instance to its own R2 bucket.
5. **Apply** any schema change to App 3 and to every client instance, additive changes first (Schema Migration).
6. **Push** the tag into App 2's repository (Step 42). This is the production action; only the developer performs it.
7. **Verify** (Post-Deploy Verification).
8. **Notify:** App 5 records the release and sends each client the release note by email. The note is informational: the update is already live.

### Staged rollout (high-risk releases)

1. Pin every client instance except the private test instance and a small first group to the previous release tag of App 2.
2. Push the new release into App 2 (Step 42).
3. Watch the first group's heartbeats and error logs for 24 hours.
4. Unpin the remaining instances so that they follow App 2's current release.

Each instance's dependency setting is changed in the hosting account that holds it. App 5's client registry records which account holds which instance.

---

## Schema Migration

Anvil has no programmatic API for creating or changing Data Tables. Every schema change is a manual operation in the Anvil editor, in App 1, in App 3 and in every client instance.

### Rules

1. No schema change is released without a migration plan recorded in `schema-migration-log.md`.
2. Every schema change is made first in App 1 (Step 17), then in App 3, then in every existing client instance.
3. Code that reads a new column handles rows where it is empty.
4. Every client instance is backed up to its R2 bucket before its schema is changed.

### Migration types

| Type | Definition | Order |
|---|---|---|
| **Additive** | A new table or column; no existing data changes | 1. Add in App 1 and test. 2. Add to App 3. 3. Add to every client instance. 4. Release the code (Step 42) |
| **Breaking** | A column or table removed or renamed | 1. Release code that no longer uses the old column. 2. Confirm every instance runs that release. 3. Remove the column from every client instance. 4. Remove it from App 3 and App 1 |
| **Modified** | A column's type changed | 1. Add a new column of the new type. 2. Release code that reads the new column, falling back to the old. 3. Copy the data in each instance with a background task. 4. Release code that reads the new column only. 5. Remove the old column (as Breaking) |

### Migration checklist

- [ ] Change and type recorded in `schema-migration-log.md`
- [ ] Release note describes it
- [ ] App 3 updated
- [ ] Procedure for existing instances written down
- [ ] Code handles the old and the new schema
- [ ] Rollback written down
- [ ] Tested on the private test instance before any client instance
- [ ] App 5's client registry records, per instance, that the migration is done; the heartbeat's schema fingerprint confirms it

---

## Post-Deploy Verification

After every push into App 2:

1. App 2 loads without errors.
2. The private test instance shows the new release and passes its smoke test: sign-in, a booking with payment in test mode, an invoice, a transactional email through SES, an email received in the Team Inbox. The demo apps show the new release.
3. Every instance's next heartbeat reports the new release version and the expected schema fingerprint (`observability-requirements.md`).
4. `error_log` across instances shows no new error type in the first hour.
5. Background task status in the heartbeats shows every task running.
6. App 5's dashboard shows no instance missing a heartbeat.

---

## Rollback

### Code rollback

Rollback is re-pointing App 2 to the last good release tag, not a code revert.

1. Identify the last good release tag.
2. Push that tag into App 2's repository again (Step 42 procedure).
3. Every client instance runs the last good release through the dependency.
4. Verify on the private test instance and in the heartbeats.
5. App 5 sends each client a short note.

### Schema rollback

Anvil cannot roll back a schema change automatically.

1. Roll the code back first, if the code depends on the change.
2. Remove an added column or table from every affected client instance by hand.
3. Restore modified data from the R2 backup taken before the migration, if a column was modified.
4. Bring App 3 back to the released schema.
5. Record the rollback in `schema-migration-log.md`.

---

## Rollback Triggers

**Immediate rollback** (do not wait for investigation):
- Error rate above 10% across instances
- Critical functionality broken: sign-in, booking, payment, invoicing
- Data corruption detected
- A security vulnerability introduced

**Considered rollback** (investigate, then decide):
- Error rate above 5% across instances
- Response times more than 50% slower
- A rising number of support tickets about the release

---

## Deployment Checklist

### Before the release
- [ ] Every feature of the release has passed `build-verified`
- [ ] The whole test suite passes ([[[default:security-global/requirements/sec-requirements-pre-merge-master-template|Pre-Merge Requirements for master_template]]](C:/dev/project-library-global/security-global/requirements/sec-requirements-pre-merge-master-template.md))
- [ ] No High or Critical finding open (Step 41)
- [ ] Release note written
- [ ] Schema migration plan recorded, if the schema changes
- [ ] Vault and integration changes documented, if any
- [ ] Every client instance backed up to its R2 bucket

### The release
1. [ ] Schema changes applied (App 3, every instance), if any
2. [ ] Tag pushed into App 2 (Step 42)
3. [ ] Post-deploy verification passed
4. [ ] Error rates watched for 1 hour
5. [ ] Release note sent from App 5

### After the release
- [ ] Background task health checked in App 5
- [ ] Support tickets about the release reviewed after 24 hours
- [ ] Problems found recorded against the release in App 5

---

## Anvil Account-Level Failures

The account structure has two levels of risk:

- **The Mybizz development account** holds App 1, App 2, App 3 and App 5. Every client instance depends on App 2, so this account is the single point of failure for every client.
- **Each client hosting account** holds one client instance. A failure of one hosting account affects only its own client.

| Scenario | Detection | Recovery | Prevention |
|---|---|---|---|
| Development account suspended | Email from Anvil; App 2 and App 5 unavailable; heartbeats stop arriving | Contact Anvil support at once; there is no self-service recovery | Billing kept current with a backup payment method; Anvil terms followed; Anvil's answer on Mybizz's account structure kept on file |
| Hosting account suspended | Heartbeats from its instance stop; App 5 raises a critical alert | Contact Anvil support; if the account cannot be restored, clone the instance into a new hosting account from App 3, restore its data from R2, and move the domain | Billing kept current; usage watched in App 5 (F-33) |
| Mybizz's AWS account or SES sending suspended | SES errors on every send; App 5 raises a critical alert | Contact AWS support; outgoing email is queued in each instance's retry queue and sent when sending returns; senders' mail servers retry the delivery of received email for several days | Bounce and complaint rates watched per client; marketing paused for a client above the thresholds (`observability-requirements.md`); two-factor authentication on the AWS account |
| Any account compromised | Unexpected commits in GitHub, unknown collaborators, unknown sessions | Change the account's credentials; contact Anvil support; audit Git history and revert unauthorised commits; audit data tables; rotate every Bearer token and Vault credential that may be exposed; tell affected clients under POPIA and GDPR | Strong unique passwords and two-factor authentication on every Anvil and GitHub account; no collaborators beyond Mybizz |
| Anvil platform outage | Every app unreachable; Anvil status page | None possible; wait for Anvil; tell clients through the Mybizz website | Recovery expectations stated in the client terms |

This is an accepted risk of the platform decision; no self-hosted fallback is planned ([[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR).

---

*End of file — Deployment Procedures*
