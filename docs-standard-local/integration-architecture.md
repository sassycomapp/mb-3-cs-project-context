---
title: "integration-architecture"
doc-id: "mb-af335781"
updated: "2026-11-02"
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
| Amazon SES, SNS and S3 | Every email received and sent: Team Inbox, transactional, marketing and system email (F-18) | Mybizz; one SES tenant per client | Client's Vault (scoped keys); App 5 (account access) |
| Cloudflare R2 | Backups, history, webhook payloads, old emails, attachments, receipts, exports (F-29) | Client: a free Cloudflare account on the client's own email address; Mybizz is a member with R2 access | Client's Vault |
| Cloudflare DNS | The client's domain records for the website and email (F-03, F-18) | Client, in the same Cloudflare account; Mybizz is a member with the DNS role | App 5 only |
| Groq | AI text, speech-to-text, natural-sounding speech (F-26, F-27) | Mybizz | App 5 only |
| Google Maps embed | Optional map (F-06) | Client | none (embed address in `business_profile`) |
| Video hosts | YouTube, Vimeo, Dailymotion, direct addresses (F-09) | Client | none |

Every credential a client instance uses is encrypted in the client's Vault (F-02). Mybizz's own AI keys and AWS account access never reach a client instance.

---

## Payment Gateways

### Gateways

- One card gateway per instance: **Stripe** (global) or **Paystack** (South Africa and Africa; Stripe does not accept South African businesses directly).
- **PayPal** is an optional second method beside the card gateway, offered only when the system currency is one of PayPal's supported currencies (not ZAR); Settings shows PayPal as unavailable otherwise.
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

### PayPal

PayPal's standard checkout, as the [[[default:specifications-global/spec-integration|Integration Specification]]](C:/dev/project-library-global/specifications-global/spec-integration.md) sets out in section 5:

1. **Setup:** the Owner enters the PayPal client ID in Settings and the secret in the Vault. The instance registers its webhook with PayPal (`/_/api/webhooks/paypal`) and stores the webhook ID in the Vault.
2. **Buttons:** `PayPalButtonComponent` (in `components/`) loads PayPal's JavaScript SDK with the client ID, the system currency and the app language, on the booking flow, order flow and pay page.
3. **Create:** the button calls `server_payments/paypal_gateway`, which reads the amount of the booking, order, invoice or package from the database and creates the order (`POST /v2/checkout/orders`, `invoice_id` = the record's identity).
4. **Capture:** after the buyer approves, the server captures the order with a `PayPal-Request-Id` header, and the payment service records the payment and PayPal's fee.
5. **Cancel or error:** the record stays unpaid; a pending booking keeps its 15-minute hold.
6. **Webhooks:** `CHECKOUT.ORDER.APPROVED` (captures an order whose browser closed), `PAYMENT.CAPTURE.COMPLETED`, `PENDING`, `DENIED`, `REFUNDED`, `CUSTOMER.DISPUTE.CREATED`.
7. **Refunds:** full or partial, from the app through the payment service.
8. **Invoices:** the app's own invoices and PDFs; pay links open the instance's pay page, with PayPal beside the card option. PayPal's invoicing is not used.

### Recording payments and refunds

- Every payment and refund is a row in `payments`: by gateway, or recorded manually (cash, bank transfer).
- The gateway's fee for each payment is recorded on its row (`gateway_fee`), from the gateway's own response: Stripe's balance transaction, Paystack's `fees`, PayPal's `paypal_fee`. The accounts show the gross payment as income and the fee as an expense, and the accounting export carries both (F-28).
- Refunds are full or partial, through the original gateway or recorded manually; payments and refunds feed the accounts reports and export (F-28).

### Currency

The system currency is set at onboarding and locked once the first booking or invoice exists. A display currency per service is shown to customers only; every stored amount is in the system currency (F-16).

---

## Email

Every email of a client instance, received and sent, goes through **Amazon SES** in Mybizz's AWS account, region `eu-west-1`, with one SES tenant per client. Build detail: `team-inbox-spec.md`.

| Email | Path | From |
|---|---|---|
| Received, to any address on the client's domain | MX → SES → S3 (client prefix) → SNS → `/_/api/email/inbound` → `inbound_tasks` → `email_messages` | — |
| Team Inbox email: new, reply, forward | `server_shared/email_service` → SES API | The user's address or desk on the client's domain |
| Transactional: booking confirmations with calendar invite, reminders, invoices, quotes, receipts, order updates, review requests, contact form auto-replies, Vault access notices | `server_shared/email_service` → SES API | `email_config.default_from_address_id` |
| Marketing: sequences, broadcasts, newsletters | `server_shared/email_service` → SES API, in batches within the SES sending rate | The campaign's sender address |
| System: sign-in, email confirmation, password reset, set-password links | Anvil Users service → Anvil Email service, Custom Mail Server = SES SMTP (`email-smtp.eu-west-1.amazonaws.com`, port 587, STARTTLS) | The default sender address |

- **Domain:** App 5 writes the client domain's MX, three DKIM CNAMEs, the MAIL FROM MX, SPF and DMARC records in the client's Cloudflare DNS at onboarding step 8 (`team-inbox-spec.md` section 3). Every email is DKIM-signed and aligned for DMARC.
- **Credentials:** the instance's AWS keys (`aws_access_key_id`, `aws_secret_access_key`) allow sending only from the client's domain and reading only the client's S3 prefix; `ses_smtp_password` is derived from them. All three are written by App 5 into the Vault.
- **Events:** SES reports deliveries, bounces, complaints, opens and clicks to `/_/api/email/events` through SNS. A hard bounce or a complaint adds the address to `email_suppressions` (scope `all`); an unsubscribe adds it with scope `marketing`.
- **Unsubscribe:** every marketing email carries an unsubscribe link and the `List-Unsubscribe` and `List-Unsubscribe-Post` headers, served by `/_/api/email/unsubscribe`.
- **Demo mode:** with `config.demo_mode` on, `email_service` sends every email to the signed-in visitor's own address (F-36).
- **Mybizz's own email:** the welcome email at registration (UF-01) and client billing are sent by App 5 from Mybizz's own domain through SES, never from a client's domain.
- **Cost:** $0.10 per 1,000 emails sent or received, absorbed by Mybizz (concept section 9.5).

---

## CRM

- The CRM is Mybizz CS's own: the `contacts` and `companies` tables and the contact timeline (`contact_events`). It is the single record of each person (F-17, F-35).
- A booking, order, enquiry, landing page capture or received email creates or updates the contact in the same server call (`server_customers/contact_service`).
- Every email to and from a contact is linked to the contact and shown on the timeline.
- Unsubscribes, opens and clicks reported by SES update the contact's email preference and the campaign metrics.
- No outside CRM or email platform holds a copy of the client's contacts.

---

## Client Storage (R2)

- **The account is the client's.** Each client has a free Cloudflare account in the client's own name, on the client's own email address, with one R2 bucket in it. Mybizz owns no Cloudflare account for any client, and no bucket or account is shared between clients.
- **The client's part, at registration (UF-01):** the client opens the Cloudflare account, adds a payment method (Cloudflare switches R2 on only for an account with one, even within the free allowance), switches R2 on, adds the client's domain to Cloudflare DNS, and invites Mybizz's Cloudflare user as a member with the roles **Cloudflare R2 Admin** and **DNS**. The registration page guides each step, with a short video.
- **Mybizz's part, automated by App 5:** App 5 accepts the invitation through Cloudflare's API, creates the bucket, and creates an API token for Mybizz's Cloudflare user with Object Read & Write on that one bucket only. The bucket's S3 credentials come from that token (Access Key ID = the token's id; Secret Access Key = the SHA-256 hash of the token's value). App 5 writes them into the instance's Vault through the management endpoints at provisioning, and keeps no copy.
- **Cost:** Cloudflare's free allowance is 10 GB of storage, 1 million write operations and 10 million read operations a month; downloads are free. Usage above the allowance is billed by Cloudflare to the client's payment method; storage above 10 GB costs $0.015 per GB per month (concept section 9.5).
- The bucket's access keys are in the client's Vault; they never appear in code, in Anvil Secrets or in any document.
- `server_storage/r2_service` is the only module that reads or writes the bucket.
- Every app runs on Python 3.14. R2 and Amazon SES are reached with `boto3`, installed from PyPI in the Python environment of App 1, App 2 and App 3 (Settings → Python versions, `requirements.txt`), so every client instance cloned from App 3 carries it. The private test instance proves both before the first release.

| Object | Folder | Written | Kept in the tables |
|---|---|---|---|
| Backup of every data table | `backups/daily/`, `backups/weekly/` | Daily, by `backup_tasks`; also before every release and every schema migration. The last 7 daily and the last 4 weekly backups are kept; the release and migration backups are kept for 30 days | nothing |
| History records | `history/{table}/` | When the history is created | current state only |
| Audit entries older than 30 days | `audit/{year}/{month}/` | Nightly, by `audit_archive_tasks` | the last 30 days of `activity_log` |
| Webhook payloads (Stripe, Paystack, PayPal, SES) | `webhooks/{service}/{year}/{month}/` | On receipt | `webhook_log` row: gateway, event id, type, received time, processed flag, error, R2 key |
| Email bodies older than 90 days | `email/messages/{address}/{year}/{month}/` | Nightly, by `inbound_tasks` | the `email_messages` row as an index |
| Email attachments | `email/attachments/` | On receipt or send | their R2 keys in `email_messages.attachments` |
| Expense receipts | `expenses/receipts/` | On save | `expenses.receipt_r2_key` |
| CSV and accounting exports, the exit export | `exports/` | On request | nothing |

- Settings → Data reads history from the bucket and shows it (UF-33). Mybizz restores from the bucket at the Owner's request. All data can be exported as CSV at any time.
- If R2 is unavailable, the write is queued and retried (Background Tasks); the Owner is told if it keeps failing.

---

## AI and Speech

- Every AI text, speech-to-text and natural-sounding speech request goes from `server_ai/ai_request_layer` to App 5, with the instance's Bearer token. App 5 holds Mybizz's Groq keys, calls Groq and records each client's usage (F-26, F-27).
- App 5 passes each AI and speech request to Groq and returns the result without storing or logging its content; it records only each client's usage counts. Mybizz's Groq organisation has Zero Data Retention switched on, so Groq keeps no copy either.
- Browser speech synthesis is the default spoken voice; Groq TTS is used for the natural-sounding voice.
- An AI failure returns a clear message and leaves the form working.
- Detail: `mybizz-ai-implementation-spec.md`.

---

## Webhooks

- Each gateway has its own HTTP endpoint in `server_payments/webhook_handlers`; SES's notifications arrive at `server_inbox/inbound_endpoint` and `server_inbox/events_endpoint`, verified by their SNS signature ([[[default:adr-global/adr-webhook-architecture|Webhook Architecture]]](C:/dev/project-library-global/adr-global/adr-webhook-architecture.md) ADR).
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
| Usage report to App 5 | `server_management/heartbeat_tasks` | Daily | Row count, storage, email volume, heavy-job activity, last staff sign-in, last booking date (F-33, F-34) |
| Payment hold expiry | `server_bookings/booking_tasks` | Every 5 minutes | Release `pending` bookings older than 15 minutes, with their slot and resource |
| Appointment reminders | `server_bookings/booking_tasks` | Every 15 minutes | Send the 24-hour and 1-hour reminders that fall due, each once |
| Scheduled blog posts | `server_blog/blog_tasks` | Every 15 minutes | Publish posts whose `scheduled_at` has passed |
| Automation rules | `server_automation/automation_tasks` | Every 15 minutes | Evaluate automations, act, record each run |
| Integration retry queue | `server_shared/retry_queue` | Every 15 minutes | Retry queued SES and R2 calls; notify the Owner after repeated failure |
| Campaign enrolment | `server_marketing/campaign_tasks` | Hourly | Process active enrolments, send the next email |
| Dashboard summaries | `server_analytics/summary_tasks` | Hourly | Refresh `daily_summary`, `service_performance`, `provider_performance` |
| Marketing metrics and alerts | `server_analytics/summary_tasks` | Daily 02:00 | Campaign metrics, marketing alerts (UF-25) |
| Lifecycle stages | `server_customers` via `summary_tasks` | Daily 02:00 | Recalculate contact status and segments |
| Overdue invoices | `server_payments/invoice_tasks` | Daily 03:00 | Mark unpaid invoices past due as `overdue`; email the reminder |
| Expired quotes | `server_sales/quote_tasks` | Daily 03:00 | Mark sent quotes past `valid_until` as `expired` |
| Automatic tasks | `server_automation/automation_tasks` | Daily 03:00 | Follow-up tasks from enquiries without a booking |
| Package expiry | `server_packages/package_tasks` | Daily 04:00 | Notify customers before expiry; lapse expired sessions |
| Backup to R2 | `server_storage/backup_tasks` | Daily 01:00 | Export every table to the client's bucket |
| Audit archive to R2 | `server_storage/audit_archive_tasks` | Daily 01:30 | Move `activity_log` entries older than 30 days to the bucket |
| Email archive and Trash | `server_inbox/inbound_tasks` | Daily 01:45 | Move email bodies older than 90 days to the bucket; delete Trash older than 30 days |
| Demo reset (demo apps only) | `server_shared/demo_service` | Daily 00:30 | Empty every table, reload the demo's sample data, remove demo sign-ins (F-36) |
| Scheduled reports | `server_analytics/summary_tasks` | Daily 06:00 | Email the reports each user has scheduled (F-04) |

Times are in the business's time zone.

### On event

| Task | Started by |
|---|---|
| Apply a webhook | `webhook_handlers` |
| Store a received email | `inbound_endpoint` |
| Apply an SES event (bounce, complaint, open, click) | `events_endpoint` |
| Send a campaign or broadcast | `campaign_service`, `broadcast_service` |
| Write social captions | Publishing a blog post (`share_service`) |
| Clear sample data | Settings → Data, the sample data banner (`sample_data_service`) |
| Accounting export | Finance & Reports → Accounts → Export |
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

- Every client instance runs on the client's own domain, which the client registers and owns; the domain carries the website and every email address of the client (concept section 9.7).
- At onboarding step 8, App 5 writes the website `A` record and the email records in the client's Cloudflare DNS (or the Owner copies them into another domain provider), and the instance's environment is switched to the custom domain (F-06, F-18).
- `mybizz.live` is Mybizz's own website and holds the demo apps (`demo.mybizz.live`, `try.mybizz.live`). Client instances are not given `mybizz.live` subdomains.
- Every client has unlimited email addresses on its own domain, in the Team Inbox (F-18).
- Provisioning steps: `mb-3-cs-architecture-reference.md` section 5, UF-01, [[[default:specifications-global/spec-client-activation-runbook|Client Instance Activation Runbook]]](C:/dev/project-library-global/specifications-global/spec-client-activation-runbook.md).

---

## Offboarding

When a client leaves (step by step: `docs-local/offboarding-procedure.md`):

1. A full export of every data table, every email, the instance's `encryption_key`, and the Vault's contents in decrypted form are written into the client's R2 bucket (`exports/`), beside the backups, history, emails and webhook payloads it already holds.
2. The client confirms the export is in the bucket.
3. Mybizz's Cloudflare user leaves the client's Cloudflare account, which also ends Mybizz's bucket token. The account and everything in it stay the client's.
4. The client's domain is disconnected from the instance; App 5 removes the client's DNS records for SES at the client's request, deletes the client's SES tenant, identity, receipt rule and AWS keys, and empties the client's S3 prefix; the instance and its Anvil account are closed. App 5 keeps only the records Mybizz is legally required to keep.

Mybizz keeps none of the client's data after the client leaves.

---

*End of file — Integration Architecture*
