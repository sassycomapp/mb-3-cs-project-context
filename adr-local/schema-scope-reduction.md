---
title: "schema-scope-reduction"
doc-id: "mb-d146fc2b"
updated: "2026-11-04"
---
# Schema Scope — Tables In and Out of a Mybizz CS Client Instance

**Status:** Accepted
**Date:** 2026-10-31

---

## Context

A Mybizz CS client instance holds only the tables its features need (F-01 to F-36 of `mb-3-cs-concept.md`). Every table is a maintenance cost: Anvil cannot change a schema by code, so each table and column has to be kept by hand in App 1, App 3 and every client instance (`deployment-procedures.md`, Schema Migration). Some data a business might hold is either out of the product's scope or kept elsewhere: in App 5, in the client's R2 bucket, or in an existing table.

---

## Decision

**A client instance holds exactly the tables listed in `authoritative-schema.md`, and no others.** That document is the single home of the table list.

### Tables that are not part of a client instance

| Table | Reason |
|---|---|
| `tickets`, `ticket_messages` | Support tickets and their replies are held by App 5, the Mybizz management app (F-32); the instance sends a Help message and reads replies through the management endpoints. Clients' own customers have no ticket system; they use the contact form |
| `kb_articles`, `kb_categories` | A knowledge base is out of scope (concept section 14); in-app help is tooltips that open help videos (F-25), held in `master_template` |
| `events`, `event_registrations` | Events management is out of scope (concept section 14); a class or workshop is a group session (`group_sessions`, F-11) and an event sign-up is an Event Registration landing page (F-08) |
| `backups` | Backups are written to the client's own R2 bucket (F-29); no table records them |
| `translations` | Screen text in six languages is code in `master_template` (`shared/translations/`), so every client receives new and corrected translations with each release (F-25) |
| `customers` | Every person is one row of `contacts` ([[[default:adr-global/adr-single-contacts-table\|Single Contacts Table]]](C:/dev/project-library-global/adr-global/adr-single-contacts-table.md) ADR) |

### Tables that are part of a client instance for these reasons

| Table | Reason |
|---|---|
| `reviews`, `testimonials` | Reviews and testimonials are a feature (F-23) |
| `resources` | Rooms and equipment are booked with an appointment where a service needs them (F-11). Providers are not resources: a provider is a user with `is_provider` set, linked as `provider_id` |
| `expenses`, `expense_categories` | Built-in accounts are a feature (F-28) |
| `companies`, `deals`, `quotes`, `quote_items` | Sales is a feature (F-35) |
| `email_addresses`, `email_messages`, `email_suppressions` | The Team Inbox and email are a feature (F-18) |
| `sample_rows` | The record of the sample data, so that clearing it removes only sample rows |
| `cookie_policy`, `consents` | The Cookie Policy is the Clause Builder's third legal document, and every cookie choice and acceptance of the legal documents is recorded (F-22, F-30) |
| `notifications`, `user_preferences`, `delivery_logs` | The notification engine every client instance inherits (`spec-mastertemplate-notification-system`) |

### Rule for any new table

A table is added to a client instance only when a feature in `mb-3-cs-concept.md` needs it, and only through `authoritative-schema.md` and `schema-migration-log.md`.

---

## Consequences

- The schema holds 70 tables, each traceable to a feature.
- Support data never enters a client's database, and a client's customer data never enters App 5 (reference section 3).
- Adding a table is a deliberate schema change with a migration plan, applied by hand to App 1, App 3 and every client instance.

---

*End of `schema-scope-reduction` ADR*
