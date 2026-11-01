---
title: "integration-architecture"
doc-id: "mb-af335781"
updated: "2026-11-01"
---
# Mybizz CS — Integration Architecture

Every outside service the application uses, and how it is connected.

**Standard:** [[[default:specifications-global/spec-integration]]](C:/dev/project-library-global/specifications-global/spec-integration.md)

**Authority:** mandatory — every integration is built to this document. Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct. Feature numbers are those of `mb-3-cs-concept.md`.

**Outside services:**

| Service | Used for | Account owner | Credentials kept in |
|---|---|---|---|
| Stripe | Card payments, global (F-15) | Client | Client's Vault |
| Paystack | Card payments, South Africa and Africa (F-15) | Client | Client's Vault |
| PayPal | Optional second payment method (F-15) | Client | Client's Vault |
| Brevo | Transactional email (SMTP) and marketing email (Campaigns) (F-18) | Client | Client's Vault |
| Cloudflare R2 | Backups, history, webhook payloads (F-29) | Client: a free Cloudflare account on the client's own email address; Mybizz is a member with R2 access | Client's Vault |
| Wave | Optional one-way accounting push (F-28) | Client | Client's Vault |
| Groq | AI text, speech-to-text, natural-sounding speech (F-26, F-27) | Mybizz | App 5 only |
| Anvil built-in email | System email (F-18) | Mybizz (Anvil account) | none |
| Google Maps embed | Optional map (F-06) | Client | none (embed address in `business_profile`) |
| Video hosts | YouTube, Vimeo, Dailymotion, direct addresses (F-09) | Client | none |

Every credential a client instance uses is encrypted in the client's Vault (F-02). Mybizz's own AI keys never reach a client instance.

---

## Payment Gateways

### Gateways

- One card gateway per instance: **Stripe** (global) or **Paystack** (South Africa and Africa; Stripe does not accept South African businesses directly).
- **PayPal** is an optional second method beside the card gateway.
- Configured in Settings → Payment Gateways (Owner only); secret keys are entered in the Vault; each gateway has test and live modes (`payment_config.test_mode`).

### One payment service

- Client code and other server modules never call a gateway. Every payment operation goes through `server_payments/payment_service`, which selects the gateway module (`stripe_gateway`, `paystack_gateway`, `paypal_gateway`) from `payment_config`.
- Switching gateways does not change services, bookings or invoices.
- Money logic runs in server modules only; card data never touches the instance (PCI DSS, F-30).
- All payments go to the business's own gateway account. There are no per-provider payment accounts.

### Payment options (F-11, F-12, F-15)

| Option | Taken at booking or ordering | Then |
|---|---|---|
| Pay in full | The full price, online | Booking confirmed when the payment succeeds |
| Pay a deposit | The service's deposit, online | Balance invoiced on completion |
| Pay on the day | Nothing | Confirmed at once; invoiced on completion |
| Invoice with payment terms | Nothing | Confirmed at once; invoice issued with the service's terms |
| Use a package session | Nothing (one session drawn) | Confirmed at once |

A booking whose online payment has not yet succeeded is held as `pending` for 15 minutes with an emailed payment link, then released with its slot and resource (UF-38).

### Recording payments and refunds

- Every payment and refund is a row in `payments`: by gateway, or recorded manually (cash, bank transfer).
- Refunds are full or partial, through the original gateway or recorded manually; a refund is sent to Wave when Wave is connected (F-28).

### Currency

The system currency is set at onboarding and locked once the first booking or invoice exists. A display currency per service is shown to customers only; every stored amount is in the system currency (F-16).

---

## Email

| Tier | Service | Sends | From |
|---|---|---|---|
| System | Anvil built-in email (`anvil.email.send`) | Sign-in, email confirmation, password reset, set-password links | Anvil-hosted address |
| Transactional | Brevo SMTP, the client's Brevo account | Booking confirmations with calendar invite, reminders, invoices, receipts, order updates, review requests, contact form auto-replies, Vault access notices | The client's own domain |
| Marketing | Brevo Campaigns API, the client's Brevo account | Sequences, broadcasts | The client's own domain |

- **Transactional:** `server_shared/transactional_email` connects to `smtp-relay.brevo.com`, port `587`, STARTTLS, with the SMTP key from the Vault (`brevo_smtp_key`); sender name and address from `email_config`.
- **Marketing:** `server_marketing/brevo_campaigns` uses the Brevo API key from the Vault (`brevo_api_key`), a separate credential from the SMTP key.
- **Sender domain:** the client's own domain is verified in Brevo during onboarding (UF-02 step 7); `email_config.sender_domain_verified` records it. Transactional email stays unavailable until it is verified.
- **Mybizz's own email:** the welcome email at registration (UF-01) and client billing are sent from Mybizz's own Brevo account by App 5, never from a client's account.
- The client starts on Brevo's free plan and moves to a paid Brevo plan when the client's sending grows beyond it (concept section 9.5).

---

## CRM

- The CRM is Mybizz CS's own: the `contacts` table and its timeline (`contact_events`). It is the single record of each person (F-17).
- A booking, order, enquiry or landing page capture creates or updates the contact in the same server call (`server_customers/contact_service`).
- For a campaign or broadcast, the recipients are sent to the client's Brevo account as a Brevo contact list. Brevo holds a copy for sending only; the CRM stays the record.
- Unsubscribes, opens and clicks come back by Brevo webhook and update the contact's email preference and the campaign metrics.

### Credential risk

A client's Brevo keys can send email as the client. They are kept in the Vault, which needs a TOTP code on every view and emails the Owner on every access (F-02). The client secures the client's own Brevo account, and the Owner replaces the keys in the Vault at any time.

---

## Client Storage (R2)

- **The account is the client's.** Each client has a free Cloudflare account in the client's own name, on the client's own email address, with one R2 bucket in it. Mybizz owns no Cloudflare account for any client, and no bucket or account is shared between clients.
- **The client's part, at registration (UF-01):** the client opens the Cloudflare account, adds a payment method (Cloudflare switches R2 on only for an account with one, even within the free allowance), switches R2 on, and invites Mybizz's Cloudflare user as a member with the role **Cloudflare R2 Admin**. The registration page guides each step, with a short video.
- **Mybizz's part, automated by App 5:** App 5 accepts the invitation through Cloudflare's API, creates the bucket, and creates an API token for Mybizz's Cloudflare user with Object Read & Write on that one bucket only. The bucket's S3 credentials come from that token (Access Key ID = the token's id; Secret Access Key = the SHA-256 hash of the token's value). App 5 writes them into the instance's Vault through the management endpoints at provisioning, and keeps no copy.
- **Cost:** Cloudflare's free allowance is 10 GB of storage, 1 million write operations and 10 million read operations a month; downloads are free. Usage above the allowance is billed by Cloudflare to the client's payment method; storage above 10 GB costs $0.015 per GB per month (concept section 9.5).
- The bucket's access keys are in the client's Vault; they never appear in code, in Anvil Secrets or in any document.
- `server_storage/r2_service` is the only module that reads or writes the bucket.

| Object | Written | Kept in the tables |
|---|---|---|
| Backup of every data table | Daily, by `backup_tasks`; also before every release and every schema migration. The last 7 daily and the last 4 weekly backups are kept; the release and migration backups are kept for 30 days | nothing |
| Webhook payloads (Stripe, Paystack, PayPal, Brevo) | On receipt | `webhook_log` row: gateway, event id, type, received time, processed flag, error, R2 key |
| Audit entries older than 30 days | Nightly, by `audit_archive_tasks` | the last 30 days of `activity_log` |
| History records | When the history is created | current state only |

- Settings → Data reads history from the bucket and shows it (UF-33). Mybizz restores from the bucket at the Owner's request. All data can be exported as CSV at any time.
- If R2 is unavailable, the write is queued and retried (Background Tasks); the Owner is told if it keeps failing.

---

## Wave

- Optional, one-way, event-driven: the instance pushes three events to the client's Wave account — invoice paid, new customer, refund issued (F-28).
- A customer is created or matched in Wave before a payment for that customer is pushed.
- Connected once in Settings → Integrations; Wave credentials in the Vault.
- No pull from Wave, no sync back, no mapping screens.
- `server_integrations/wave_service`; each push runs as a background task and is retried on failure.

---

## AI and Speech

- Every AI text, speech-to-text and natural-sounding speech request goes from `server_ai/ai_request_layer` to App 5, with the instance's Bearer token. App 5 holds Mybizz's Groq keys, calls Groq and records each client's usage (F-26, F-27).
- Browser speech synthesis is the default spoken voice; Groq TTS is used for the natural-sounding voice.
- An AI failure returns a clear message and leaves the form working.
- Detail: `releases/v2/mybizz-ai-implementation-spec.md`.

---

## Webhooks

- Each gateway and Brevo has its own HTTP endpoint in `server_payments/webhook_handlers` ([[[default:adr-global/adr-webhook-architecture|Webhook Architecture]]](C:/dev/project-library-global/adr-global/adr-webhook-architecture.md) ADR).
- The handler verifies the signature, refuses an event whose `event_id` is already in `webhook_log`, writes the `webhook_log` row and the payload to R2, returns HTTP 200 at once, and starts a background task to apply the event.
- The background task applies the result (booking confirmed, invoice paid, refund recorded, unsubscribe set) and sets `processed`. A duplicate delivery never creates a duplicate record.
- Management endpoints for App 5 are in `server_management/management_endpoints`, secured with the instance's Bearer token ([[[default:specifications-global/spec-api-specification|API Specification (HTTP Endpoints)]]](C:/dev/project-library-global/specifications-global/spec-api-specification.md)).

---

## Background Tasks

Any operation expected to run longer than 22 seconds is an `@anvil.server.background_task` ([[[default:adr-global/adr-real-time-and-background-tasks|Real-Time Updates and Background Task Architecture]]](C:/dev/project-library-global/adr-global/adr-real-time-and-background-tasks.md) ADR).

### Scheduled

| Task | Module | Schedule | Purpose |
|---|---|---|---|
| Heartbeat to App 5 | `server_management/heartbeat_tasks` | Every 15 minutes | Health, release version, schema fingerprint, task status (`observability-requirements.md`) |
| Usage report to App 5 | `server_management/heartbeat_tasks` | Daily | Row count, storage, heavy-job activity, last staff sign-in, last booking date (F-33, F-34) |
| Payment hold expiry | `server_bookings/booking_tasks` | Every 5 minutes | Release `pending` bookings older than 15 minutes, with their slot and resource |
| Appointment reminders | `server_bookings/booking_tasks` | Every 15 minutes | Send the 24-hour and 1-hour reminders that fall due, each once |
| Scheduled blog posts | `server_blog/blog_tasks` | Every 15 minutes | Publish posts whose `scheduled_at` has passed |
| Automation rules | `server_automation/automation_tasks` | Every 15 minutes | Evaluate automations, act, record each run |
| Integration retry queue | `server_shared/retry_queue` | Every 15 minutes | Retry queued Brevo, Wave and R2 calls; notify the Owner after repeated failure |
| Campaign enrolment | `server_marketing/campaign_tasks` | Hourly | Process active enrolments, send the next email |
| Dashboard summaries | `server_analytics/summary_tasks` | Hourly | Refresh `daily_summary`, `service_performance`, `provider_performance` |
| Marketing metrics and alerts | `server_analytics/summary_tasks` | Daily 02:00 | Campaign metrics, marketing alerts (UF-25) |
| Lifecycle stages | `server_customers` via `summary_tasks` | Daily 02:00 | Recalculate contact status and segments |
| Overdue invoices | `server_payments/invoice_tasks` | Daily 03:00 | Mark unpaid invoices past due as `overdue`; email the reminder |
| Automatic tasks | `server_automation/automation_tasks` | Daily 03:00 | Follow-up tasks from enquiries without a booking |
| Package expiry | `server_packages/package_tasks` | Daily 04:00 | Notify customers before expiry; lapse expired sessions |
| Backup to R2 | `server_storage/backup_tasks` | Daily 01:00 | Export every table to the client's bucket |
| Audit archive to R2 | `server_storage/audit_archive_tasks` | Daily 01:30 | Move `activity_log` entries older than 30 days to the bucket |
| Scheduled reports | `server_analytics/summary_tasks` | Daily 06:00 | Email the reports each user has scheduled (F-04) |

Times are in the business's time zone.

### On event

| Task | Started by |
|---|---|
| Apply a webhook | `webhook_handlers` |
| Push to Wave | Invoice paid, new customer, refund issued |
| Import or export contacts (CSV) | Contacts import and export |
| Export all data (CSV) | Settings → Data |
| AI generation, morning briefing | `server_ai` |

### Where the schedule lives

Scheduled tasks belong to the app that runs them. They are set up in App 3 (`blank_client_template`), calling the background tasks in `master_template`, so that every cloned instance carries the same schedule; App 1 carries the same schedule for development. Before the scaffold plan (Step 31) relies on it, App 1 proves once that an app can schedule a background task of its dependency and serve its dependency's HTTP endpoints.

---

## Timezones

- Each instance sets its IANA time zone at onboarding (`business_profile.timezone`).
- All datetimes are stored in UTC and converted to the business's time zone at display time, in server functions (`server_shared/time_utils`).
- A customer booking from another time zone sees the times in the business's time zone, labelled with it.

---

## Domain and Provisioning

- Every client instance runs on the client's own domain, which the client registers and owns. The domain is required, because Brevo needs it to send the client's email reliably (concept section 9.7).
- Mybizz connects the domain at provisioning: an Anvil custom domain on the instance's environment, with a DNS `A` record at the client's domain provider (F-06).
- `mybizz.live` is Mybizz's own website. Client instances are not given `mybizz.live` subdomains.
- Business email accounts are not provided by Mybizz.
- Provisioning steps: `mb-3-cs-architecture-reference.md` section 5, UF-01, [[[default:specifications-global/spec-client-activation-runbook|Client Instance Activation Runbook]]](C:/dev/project-library-global/specifications-global/spec-client-activation-runbook.md).

---

## Offboarding

When a client leaves ([[[default:standard-operating-procedures-global/sop-offboarding|Offboarding SOP]]](C:/dev/project-library-global/standard-operating-procedures-global/sop-offboarding.md)):

1. A full export of every data table, the instance's `encryption_key`, and the Vault's contents in decrypted form are written into the client's R2 bucket, beside the backups, history and webhook payloads it already holds.
2. The client confirms the export is in the bucket.
3. Mybizz's Cloudflare user leaves the client's Cloudflare account, which also ends Mybizz's bucket token. The account and everything in it stay the client's.
4. The client's domain is disconnected from the instance, and the instance is deleted. App 5 keeps only the records Mybizz is legally required to keep.

Mybizz keeps none of the client's data after the client leaves.

---

*End of file — Integration Architecture*
