---
title: "observability-requirements"
doc-id: "mb-4a65dade"
updated: "2026-11-01"
---
# Mybizz CS — Observability Requirements

What the application logs, measures and alerts on, and how long logs are kept.

**Standard:** [[[default:specifications-global/spec-observability]]](C:/dev/project-library-global/specifications-global/spec-observability.md)

**Authority:** [[[default:adr-global/adr-observability-architecture|Observability Architecture]]](C:/dev/project-library-global/adr-global/adr-observability-architecture.md) ADR, [[[default:adr-global/adr-mybizz-management-visibility|Mybizz_management Visibility and Control]]](C:/dev/project-library-global/adr-global/adr-mybizz-management-visibility.md) ADR. Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct.

**Model:** each client instance records its own health, errors and audit trail in its own data tables and pushes a heartbeat and a usage report to App 5 on a schedule. App 5 never polls instances; its HTTP endpoints on each instance are for control and on-demand checks. No external monitoring tool is used. Anvil's App Logs, CPU and memory views, and profiling remain in use for investigating a single instance.

---

## Logging

### Structured logging

Every server function logs through `logging.getLogger(__name__)`, configured once in `server_shared/logging_config`, as one JSON object per message with these fields:

| Field | Content |
|---|---|
| `timestamp` | ISO 8601, UTC |
| `level` | DEBUG, INFO, WARNING, ERROR, CRITICAL |
| `function` | The calling server function |
| `error_type` | Exception class name (empty when no error) |
| `error_message` | Exception message (empty when no error) |
| `user_id` | The signed-in user's row id, or empty |
| `request_id` | A UUID generated per request, carried through every log line of that request |

```python
logger = logging.getLogger(__name__)
logger.info("payment_recorded", extra={"function": "record_payment", "invoice_id": invoice.get_id(), "request_id": request_id})
```

### Rules

- Function entry and exit at DEBUG, with argument names and row ids only, never values.
- Every call to an outside service (gateway, Brevo, R2, Wave, App 5) at INFO, with its duration.
- Every caught and re-raised exception at ERROR, and written to `error_log`.
- Never logged: credentials, keys, tokens, card data, passwords, TOTP codes, the content of Vault entries, or customer personal data beyond row ids (POPIA, GDPR, F-30).

### Log levels

| Level | Use |
|---|---|
| DEBUG | Detail for development; off in client instances |
| INFO | Normal operation and outside-service calls |
| WARNING | Unexpected but handled: a retry, a refused duplicate webhook |
| ERROR | A failed operation the user or Owner must know about |
| CRITICAL | A failure that stops the instance working: Vault unreadable, database unavailable |

---

## Metrics

### The log tables

| Table | Written by | Holds |
|---|---|---|
| `health_log` | `server_management/heartbeat_tasks`, every 15 minutes | Status, release version, schema fingerprint, background task status (last run, success or failure per task), queued retries |
| `error_log` | Every server module, through `server_shared/logging_config` | Uncaught and re-raised exceptions: function, type, message, severity, user, resolved flag |
| `activity_log` | Every server module | The audit trail: who did what, when (sign-in and sign-out, role changes, Vault access, payments and refunds, status changes of bookings, invoices and orders, settings changes, data exports) |

All three are written by server functions only; no client code writes to them. `activity_log` is this project's audit table: the standard calls it `audit_log`.

### Business metrics (pre-computed)

Dashboards and reports read summary tables, never compute on page load ([[[default:adr-global/adr-data-access-patterns|Data Access Patterns and Query Limitations]]](C:/dev/project-library-global/adr-global/adr-data-access-patterns.md) ADR). The tasks and their schedules are in `integration-architecture.md` (Background Tasks).

| Table | Holds |
|---|---|
| `daily_summary` | Revenue, revenue by service, booking count, new contacts, per day |
| `service_performance` | Revenue, booking count and completion rate per service, per day |
| `provider_performance` | Revenue and booking count per provider, per day |
| `campaign_metrics` | Emails sent, opens, clicks, conversions, unsubscribes, per campaign, per day |

---

## Alerts

### In the instance (to the Owner)

| Condition | Alert |
|---|---|
| Every Vault access | Email to the Owner (F-02) |
| 5 failed Vault codes | Vault locked for 15 minutes; email to the Owner |
| An integration call (Brevo, Wave, R2) still failing after its retries | Email to the Owner and a notice on the Dashboard |
| A webhook that could not be applied | Notice on the Dashboard; the event stays unprocessed in `webhook_log` |

### In App 5 (to Mybizz)

App 5 shows every client instance on one dashboard: last heartbeat, release version, background task health, error count and last error, usage against the fair-use cap, billing status, open tickets.

**Critical — act now:**
- No heartbeat from an instance for 45 minutes (three missed heartbeats).
- An instance's error count in one hour above its threshold.
- A background task failing on three consecutive runs.
- Payment failures above 10% of payment attempts in one day.
- A support ticket of type problem that stops a client's business, unacknowledged after 4 business hours (F-32).

**Warning — watch:**
- An instance's release version differs from `master_template`'s current release.
- Usage above 80% of the fair-use cap of 500,000 rows or 50 GB (F-33).
- A background task running longer than twice its usual time.

**At-risk (F-34):** no staff sign-in for 14 days; no bookings for 30 days; a support ticket unanswered for 3 days.

---

## Heartbeat to App 5

- Sent every 15 minutes by `server_management/heartbeat_tasks`, as an HTTP POST to App 5 with the instance's Bearer token ([[[default:specifications-global/spec-api-specification|API Specification (HTTP Endpoints)]]](C:/dev/project-library-global/specifications-global/spec-api-specification.md)).
- Contents: instance status, release version, schema fingerprint, background task status, error count since the last heartbeat, last error, queued retries.
- The daily usage report adds: row count, data table storage, heavy-job activity, last staff sign-in, last booking date.
- No customer personal data is sent to App 5. Neither the heartbeat nor the usage report contains names, emails, or the content of any record.
- A heartbeat that cannot be delivered is written to `health_log` and sent with the next one.

---

## Log Retention

| Record | Kept in the instance | Then |
|---|---|---|
| `activity_log` (audit) | 30 days | Moved to the client's R2 bucket nightly; kept there for the life of the client (F-29) |
| `health_log` | 30 days | Deleted |
| `error_log` | 90 days | Deleted |
| `webhook_log` rows | 90 days | Deleted; the payloads stay in R2 |
| Webhook payloads | — (never in the tables) | R2, for the life of the client |
| Anvil App Logs | As Anvil keeps them | — |

Retention clean-up runs with the nightly audit archive task. A client's R2 records are in the client's own Cloudflare account and stay there when the client leaves (`integration-architecture.md`, Offboarding).

---

*End of file — Observability Requirements*
