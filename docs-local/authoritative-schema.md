---
title: "authoritative-schema"
doc-id: "mb-8b83e940"
updated: "2026-11-01"
---
# Mybizz CS — Authoritative Database Schema

Every table and column of a Mybizz CS client instance: name, type and allowed values.

**Authority:** this document is the single home of the schema. Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct. Feature numbers (F-01 to F-34) are those of `mb-3-cs-concept.md`. The changes that bring App 1's tables to this schema are in `schema-migration-log.md`.

**Total tables:** 55
**Platform:** Anvil Data Tables. Every table has `client: none` and `server: full`: the browser never reads a table directly; server functions return rows and results.

---

## Rules

- **One instance, one client.** No table carries a client or instance column; isolation comes from the instance (reference section 3).
- **UTC.** Every `datetime` is stored in UTC and shown in `business_profile.timezone` (an IANA time zone).
- **One currency.** Every amount is in `business_profile.system_currency` (F-16).
- **Never deleted.** Rows of `bookings`, `orders`, `invoice` and `payments` are never deleted; their status changes.
- **History outside the tables.** Backups, history, webhook payloads and audit entries older than 30 days are kept in the client's R2 bucket (F-29).
- **Credentials only in the Vault**, encrypted.
- **Names** follow [[[default:specifications-global/spec-nomenclature|Nomenclature]]](C:/dev/project-library-global/specifications-global/spec-nomenclature.md). A link column is named `{target}_id`, or in the plural for a multiple link. A link to a provider (a staff user who delivers services) is always `provider_id`. A link to a bookable room or piece of equipment is always `resource_id`.
- **Allowed values** are lowercase strings, written in this document as `value1` / `value2`. Code compares against these exact strings.

### Column types

| Type | Holds |
|---|---|
| `string` | Text |
| `number` | A number |
| `bool` | True or False |
| `date` | A date without time |
| `datetime` | A date and time, UTC |
| `media` | A file |
| `simpleObject` | A list or dictionary (JSON) |
| `link → table` | One row of `table` (`link_single`) |
| `links → table` | Several rows of `table` (`link_multiple`) |

---

## Table Groups

| Group | Tables | Count |
|---|---|---|
| Core and configuration | `activity_log`, `business_profile`, `config`, `email_config`, `files`, `payment_config`, `rate_limits`, `theme_config`, `users`, `vault` | 10 |
| Services and bookings | `services`, `resources`, `group_sessions`, `availability_rules`, `availability_exceptions`, `booking_metadata_schemas`, `bookings`, `recurring_series` | 8 |
| Orders and packages | `orders`, `packages`, `customer_packages` | 3 |
| Finance | `invoice`, `invoice_items`, `payments`, `time_entries` | 4 |
| CRM and marketing | `contacts`, `contact_counter`, `contact_events`, `contact_submissions`, `segments`, `tasks`, `leads`, `lead_captures`, `email_campaigns`, `contact_campaigns`, `automation_rules`, `automation_runs` | 12 |
| Email and webhooks | `email_templates`, `email_log`, `webhook_log` | 3 |
| Content and policy | `blog_categories`, `blog_posts`, `blog_comments`, `landing_pages`, `pages`, `privacy_policy`, `t_and_c` | 7 |
| Reviews | `reviews`, `testimonials` | 2 |
| Summary and health | `daily_summary`, `service_performance`, `provider_performance`, `campaign_metrics`, `health_log`, `error_log` | 6 |
| **Total** | | **55** |

---

## 1. Core and Configuration

### 1.1 `activity_log`
The audit trail (F-01, F-29). The last 30 days stay here; older rows are moved to R2 nightly.

| Column | Type | Notes |
|---|---|---|
| `user_id` | link → users | Who acted; empty for system actions |
| `action_type` | string | e.g. `login`, `role_changed`, `vault_opened`, `payment_recorded`, `booking_cancelled` |
| `description` | string | Readable description |
| `metadata` | simpleObject | Row ids and context; never credentials or personal data beyond ids |
| `created_at` | datetime | |
| `ip_address` | string | |

### 1.2 `business_profile`
One row: the client's business.

| Column | Type | Notes |
|---|---|---|
| `business_name` | string | |
| `description` | string | |
| `tagline` | string | |
| `logo` | media | Admin area and invoices |
| `business_logo` | media | Public website |
| `contact_email` | string | |
| `phone` | string | |
| `website_url` | string | |
| `address_line_1` | string | |
| `address_line_2` | string | |
| `city` | string | |
| `country` | string | |
| `postal_code` | string | |
| `timezone` | string | IANA time zone, e.g. `Africa/Johannesburg` |
| `system_currency` | string | ISO 4217; set at onboarding; locked once a booking or invoice exists |
| `entity_type` | string | `individual` / `business` |
| `social_facebook` | string | |
| `social_instagram` | string | |
| `social_x` | string | |
| `social_linkedin` | string | |
| `accreditations` | string | |
| `google_maps_embed_url` | string | Map on Contact and in the footer; empty hides the map |
| `home_video_url` | string | Homepage video; empty hides the section |
| `home_video_type` | string | `youtube` / `vimeo` / `dailymotion` / `direct`, detected on save |
| `services_video_url` | string | Services page video; empty hides the section |
| `services_video_type` | string | As `home_video_type` |
| `palette` | string | `professional_blue` / `warm_coral` / `calm_teal` / `sophisticated_purple`; default `professional_blue` |
| `palette_updated_at` | datetime | |
| `homepage_template` | string | `classic` / `services` / `booking` / `minimalist` |
| `blog_list_view` | string | `tiled` / `list` |
| `enable_comments` | bool | Blog comments on or off |
| `tax_rate` | number | Percentage; default `0` (no tax) |
| `tax_label` | string | e.g. `VAT`, `GST`, `Sales Tax` |
| `tax_registration_number` | string | Shown on invoices when set |
| `default_payment_terms_days` | number | Default for new invoices; `0` = due on receipt |
| `cancellation_window_hours` | number | How long before a booking a customer may cancel and have a package session returned |
| `onboarding_status` | string | `in_progress` / `completed` |
| `onboarding_completed` | bool | |
| `onboarding_completed_at` | datetime | |
| `legal_compliance_enabled` | bool | Legal Compliances switch; cannot be switched off once on |
| `legal_compliance_enabled_at` | datetime | |
| `legal_ack_accepted` | bool | Recorded once, cannot be reversed |
| `legal_ack_accepted_at` | datetime | |
| `data_governance_ack_accepted` | bool | Recorded once, cannot be reversed |
| `data_governance_ack_accepted_at` | datetime | |
| `created_at` | datetime | |
| `updated_at` | datetime | |

### 1.3 `config`
Key–value settings.

| Column | Type | Notes |
|---|---|---|
| `key` | string | Unique |
| `value` | simpleObject | |
| `category` | string | `features` / `content` / `sequence` / `integration` |
| `updated_at` | datetime | |
| `updated_by` | link → users | |

**Keys:**

| Key | Category | Value |
|---|---|---|
| `marketing_enabled` | features | bool |
| `blog_enabled` | features | bool |
| `home_config` | content | Homepage sections (UF-17) |
| `invoice_sequence` | sequence | Last invoice number used this year: `{"year": 2026, "last": 41}` |
| `booking_sequence` | sequence | As above, for `BK-` numbers |
| `order_sequence` | sequence | As above, for `ORD-` numbers |
| `r2_bucket` | integration | Bucket name and endpoint; the keys are in the Vault |
| `app5_endpoint` | integration | App 5's base address |
| `wave_connection` | integration | Wave business id and last event sent; the tokens are in the Vault |

Sequence keys are read and incremented inside a transaction, so that no number repeats or is skipped.

### 1.4 `email_config`
Brevo sender settings (F-18).

| Column | Type | Notes |
|---|---|---|
| `from_email` | string | Address on the client's own domain |
| `from_name` | string | |
| `configured` | bool | |
| `configured_at` | datetime | |
| `sender_domain_verified` | bool | Transactional email stays unavailable until True |

### 1.5 `files`
Files held by the instance: the media library, order briefs and deliveries.

| Column | Type | Notes |
|---|---|---|
| `path` | string | |
| `file` | media | |
| `file_version` | string | |
| `created_at` | datetime | |

### 1.6 `payment_config`
One row: payment gateways (F-15). Secret keys are in the Vault.

| Column | Type | Notes |
|---|---|---|
| `active_gateway` | string | `stripe` / `paystack` — the card gateway |
| `test_mode` | bool | Test or live, for the card gateway |
| `stripe_publishable_key` | string | Public key |
| `stripe_connected` | bool | |
| `stripe_connected_at` | datetime | |
| `paystack_public_key` | string | Public key |
| `paystack_connected` | bool | |
| `paystack_connected_at` | datetime | |
| `paypal_client_id` | string | Public client id |
| `paypal_test_mode` | bool | Test or live, for PayPal |
| `paypal_connected` | bool | |
| `paypal_connected_at` | datetime | |
| `configured_at` | datetime | |

### 1.7 `rate_limits`
Counters for rate limiting, sign-in lockout and the Vault lock.

| Column | Type | Notes |
|---|---|---|
| `identifier` | string | e.g. `signin:{email}`, `ip:{address}`, `vault:{user row id}` |
| `count` | number | Attempts in the current window |
| `reset_time` | datetime | When the counter or lock ends; for `vault:` 15 minutes after the 5th wrong code |
| `last_request` | datetime | |

### 1.8 `theme_config`
One row: the public website's header style (Settings → Theme). Colours come from the palette.

| Column | Type | Notes |
|---|---|---|
| `header_style` | string | |
| `updated_at` | datetime | |

### 1.9 `users`
Every person who signs in: Owner, staff and customers. Extends the Anvil Users service.

| Column | Type | Notes |
|---|---|---|
| `email` | string | Unique; Anvil-managed |
| `enabled` | bool | Anvil-managed |
| `password_hash` | string | Anvil-managed |
| `n_password_failures` | number | Anvil-managed |
| `confirmed_email` | bool | Anvil-managed |
| `email_confirmation_key` | string | Anvil-managed |
| `signed_up` | datetime | Anvil-managed |
| `last_login` | datetime | Anvil-managed |
| `remembered_logins` | simpleObject | Anvil-managed |
| `mfa` | simpleObject | Anvil-managed; unused (Anvil two-factor sign-in is off) |
| `full_name` | string | |
| `phone` | string | |
| `role` | string | `owner` / `manager` / `admin` / `staff` / `customer` |
| `account_status` | string | `active` / `deactivated`; a deactivated user cannot sign in |
| `is_provider` | bool | Delivers services and has a calendar (Staff, and an Owner or Manager who also delivers) |
| `photo` | media | Provider photo, shown in the booking flow |
| `specialisations` | string | Provider specialisations, shown in the booking flow |
| `contact_id` | link → contacts | For a customer: the customer's contact record |
| `permissions` | simpleObject | Extra permission flags within the role |
| `preferences` | simpleObject | `{"tooltips": bool, "morning_briefing": bool}` |
| `created_at` | datetime | |

### 1.10 `vault`
Every credential, encrypted with `encryption_key` by `vault_service` (F-02).

| Column | Type | Notes |
|---|---|---|
| `name` | string | Unique |
| `value` | string | Encrypted |
| `created_at` | datetime | |
| `updated_at` | datetime | |
| `updated_by` | link → users | |

**Names:** `stripe_secret_key`, `stripe_webhook_secret`, `paystack_secret_key`, `paypal_client_secret`, `paypal_webhook_id`, `brevo_smtp_key`, `brevo_api_key`, `brevo_webhook_secret`, `r2_access_key_id`, `r2_secret_access_key`, `wave_access_token`, `wave_refresh_token`, `app5_bearer_token`, `vault_totp_secret`.

---

## 2. Services and Bookings

### 2.1 `services`
The service catalogue (F-10).

| Column | Type | Notes |
|---|---|---|
| `service_id` | string | Unique id |
| `name` | string | |
| `slug` | string | Unique; public address `/services/{slug}` |
| `description` | string | |
| `category` | string | |
| `pricing_model` | string | `duration` (time slots) / `unit` (delivered work, ordered, no slot) |
| `price` | number | |
| `duration_minutes` | number | For `duration` services |
| `hourly_rate` | number | Default rate for time entries |
| `display_currency` | string | ISO 4217; empty = none |
| `providers` | links → users | Providers who deliver the service |
| `meeting_types` | simpleObject | List of `at_business` / `at_customer` / `video` / `phone` |
| `travel_buffer_minutes` | number | Before and after a booking at the customer's location |
| `is_group` | bool | Booked by places in a group session |
| `required_resources` | links → resources | A booking needs one of these free; empty = none needed |
| `payment_option` | string | `full` / `deposit` / `on_the_day` / `invoice` |
| `deposit_amount` | number | For `deposit` |
| `payment_terms_days` | number | For `invoice`; empty = `business_profile.default_payment_terms_days` |
| `intake_schema_id` | link → booking_metadata_schemas | Intake form; empty = none |
| `images` | simpleObject | List of image addresses |
| `video_url` | string | Empty hides the video |
| `video_type` | string | `youtube` / `vimeo` / `dailymotion` / `direct` |
| `is_active` | bool | Only active services are public |
| `created_at` | datetime | |

### 2.2 `resources`
Rooms and equipment that can be booked (F-11).

| Column | Type | Notes |
|---|---|---|
| `name` | string | |
| `resource_type` | string | `room` / `equipment` |
| `description` | string | |
| `is_active` | bool | |
| `created_at` | datetime | |

### 2.3 `group_sessions`
Sessions of a group service (F-11, UF-07).

| Column | Type | Notes |
|---|---|---|
| `service_id` | link → services | A service with `is_group` |
| `provider_id` | link → users | |
| `resource_id` | link → resources | Optional |
| `start_datetime` | datetime | |
| `end_datetime` | datetime | |
| `place_limit` | number | |
| `places_taken` | number | Updated in the same call as each booking and cancellation |
| `location` | string | For an in-person session |
| `call_link` | string | For an online session |
| `status` | string | `scheduled` / `completed` / `cancelled` |
| `created_at` | datetime | |

### 2.4 `availability_rules`
Weekly hours. A row with no provider is the business's opening hours.

| Column | Type | Notes |
|---|---|---|
| `provider_id` | link → users | Empty = the business |
| `day_of_week` | number | `0` Monday to `6` Sunday |
| `start_time` | string | `HH:MM`, business time zone |
| `end_time` | string | `HH:MM` |
| `is_active` | bool | |

### 2.5 `availability_exceptions`
Blocked dates and one-off changes. A row with no provider applies to the whole business.

| Column | Type | Notes |
|---|---|---|
| `provider_id` | link → users | Empty = the business |
| `exception_date` | date | |
| `start_time` | string | `HH:MM`; empty = the whole day |
| `end_time` | string | `HH:MM` |
| `is_available` | bool | False = blocked |
| `reason` | string | Shown on the calendar as the block's title |

### 2.6 `booking_metadata_schemas`
Intake forms.

| Column | Type | Notes |
|---|---|---|
| `schema_name` | string | |
| `booking_type` | string | `appointment` / `order` |
| `field_definitions` | simpleObject | Fields, types, labels, required flags |
| `is_active` | bool | |

### 2.7 `bookings`
Appointments and group places (F-11).

| Column | Type | Notes |
|---|---|---|
| `booking_id` | string | Unique id |
| `booking_number` | string | `BK-{year}-{sequence}` |
| `contact_id` | link → contacts | |
| `service_id` | link → services | |
| `provider_id` | link → users | |
| `resource_id` | link → resources | The room or equipment reserved; empty = none |
| `group_session_id` | link → group_sessions | For a group booking |
| `places` | number | Places booked in a group session; `1` otherwise |
| `customer_package_id` | link → customer_packages | When a package session is used |
| `recurring_series_id` | link → recurring_series | When part of a recurring booking |
| `booking_type` | string | `appointment` / `group` |
| `meeting_type` | string | `at_business` / `at_customer` / `video` / `phone` |
| `customer_address` | simpleObject | For `at_customer` |
| `call_link` | string | For `video` |
| `status` | string | `pending` / `confirmed` / `completed` / `no_show` / `cancelled` |
| `start_datetime` | datetime | |
| `end_datetime` | datetime | |
| `travel_before_minutes` | number | For `at_customer` |
| `travel_after_minutes` | number | For `at_customer` |
| `total_price` | number | |
| `payment_option` | string | `full` / `deposit` / `on_the_day` / `invoice` / `package` |
| `deposit_amount` | number | For `deposit` |
| `payment_status` | string | `unpaid` / `deposit_paid` / `paid` / `pay_on_day` / `invoiced` / `package` / `refunded` |
| `hold_expires_at` | datetime | For `pending`: 15 minutes after creation |
| `special_requests` | string | |
| `metadata` | simpleObject | Intake form answers |
| `source` | string | Where the customer came from (blog, landing page, social, direct) |
| `reminder_24h_sent_at` | datetime | Set when sent, so it is sent once |
| `reminder_1h_sent_at` | datetime | Set when sent |
| `cancelled_at` | datetime | |
| `created_at` | datetime | |
| `updated_at` | datetime | |

Status lifecycle: `DESIGN.md`, Booking Status Lifecycle.

### 2.8 `recurring_series`
A recurring booking's pattern (F-11).

| Column | Type | Notes |
|---|---|---|
| `contact_id` | link → contacts | |
| `service_id` | link → services | |
| `provider_id` | link → users | |
| `pattern` | string | `weekly` / `fortnightly` / `monthly` |
| `start_date` | date | |
| `end_date` | date | Empty = until cancelled |
| `status` | string | `active` / `ended` / `cancelled` |
| `created_at` | datetime | |

---

## 3. Orders and Packages

### 3.1 `orders`
Orders for delivered work (F-12).

| Column | Type | Notes |
|---|---|---|
| `order_number` | string | `ORD-{year}-{sequence}` |
| `contact_id` | link → contacts | |
| `service_id` | link → services | A `unit` service |
| `provider_id` | link → users | Who delivers; optional |
| `brief` | string | |
| `brief_files` | links → files | Customer's attachments |
| `status` | string | `ordered` / `in_progress` / `delivered` / `cancelled` |
| `total_price` | number | |
| `payment_option` | string | `full` / `deposit` / `invoice` |
| `payment_status` | string | `unpaid` / `deposit_paid` / `paid` / `invoiced` / `refunded` |
| `delivery_message` | string | |
| `delivery_files` | links → files | |
| `ordered_at` | datetime | |
| `started_at` | datetime | |
| `delivered_at` | datetime | |
| `created_at` | datetime | |
| `updated_at` | datetime | |

### 3.2 `packages`
Package definitions (F-13).

| Column | Type | Notes |
|---|---|---|
| `name` | string | |
| `description` | string | |
| `service_id` | link → services | |
| `session_count` | number | |
| `price` | number | |
| `expiry_days` | number | Days from purchase |
| `is_active` | bool | |
| `created_at` | datetime | |

### 3.3 `customer_packages`
A customer's purchased package (F-13).

| Column | Type | Notes |
|---|---|---|
| `package_id` | link → packages | |
| `contact_id` | link → contacts | |
| `sessions_total` | number | |
| `sessions_remaining` | number | Changed in the same call as each booking and cancellation |
| `purchased_at` | datetime | |
| `expires_at` | datetime | |
| `expiry_notice_sent_at` | datetime | |
| `status` | string | `active` / `used` / `expired` / `cancelled` |

---

## 4. Finance

### 4.1 `invoice`
Invoices (F-15).

| Column | Type | Notes |
|---|---|---|
| `invoice_id` | string | Unique id |
| `invoice_number` | string | `INV-{year}-{sequence}` |
| `contact_id` | link → contacts | |
| `booking_id` | link → bookings | When issued for a booking |
| `order_id` | link → orders | When issued for an order |
| `customer_package_id` | link → customer_packages | When issued for a package |
| `invoice_date` | datetime | Issue date |
| `payment_terms_days` | number | `0` = due on receipt |
| `due_date` | datetime | `invoice_date` + `payment_terms_days` |
| `subtotal` | number | Sum of `invoice_items.amount` |
| `tax_rate` | number | The rate at issue |
| `tax_amount` | number | `subtotal` × `tax_rate` |
| `total_amount` | number | `subtotal` + `tax_amount` |
| `amount_paid` | number | Sum of succeeded payments less refunds |
| `currency` | string | The system currency |
| `status` | string | `draft` / `sent` / `paid` / `overdue` / `cancelled` |
| `sent_at` | datetime | |
| `paid_at` | datetime | |
| `overdue_reminder_sent_at` | datetime | |
| `pdf_url` | string | R2 key of the generated PDF |
| `notes` | string | |
| `created_at` | datetime | |
| `updated_at` | datetime | |

Status lifecycle: `DESIGN.md`, Invoice Status Lifecycle.

### 4.2 `invoice_items`
Invoice lines.

| Column | Type | Notes |
|---|---|---|
| `invoice_id` | link → invoice | |
| `description` | string | |
| `quantity` | number | |
| `unit_price` | number | |
| `amount` | number | `quantity` × `unit_price` |
| `time_entry_id` | link → time_entries | When the line came from a time entry |
| `sort_order` | number | |

### 4.3 `payments`
Every payment and refund (F-15).

| Column | Type | Notes |
|---|---|---|
| `kind` | string | `payment` / `refund` |
| `invoice_id` | link → invoice | |
| `booking_id` | link → bookings | |
| `order_id` | link → orders | |
| `customer_package_id` | link → customer_packages | |
| `contact_id` | link → contacts | |
| `method` | string | `stripe` / `paystack` / `paypal` / `cash` / `bank_transfer` / `other` |
| `amount` | number | |
| `currency` | string | The system currency |
| `status` | string | `pending` / `succeeded` / `failed` |
| `gateway_reference` | string | The gateway's transaction id; empty for manual |
| `refund_of` | link → payments | For a refund: the payment refunded |
| `recorded_by` | link → users | For a manual payment or refund |
| `received_at` | datetime | |
| `notes` | string | |
| `created_at` | datetime | |

### 4.4 `time_entries`
Time worked (F-14).

| Column | Type | Notes |
|---|---|---|
| `entry_id` | string | Unique id |
| `contact_id` | link → contacts | |
| `provider_id` | link → users | |
| `service_id` | link → services | |
| `booking_id` | link → bookings | Optional |
| `start_time` | datetime | |
| `end_time` | datetime | Empty while the timer runs |
| `duration_hours` | number | Calculated on save |
| `hourly_rate` | number | From the service; changeable per entry |
| `total_amount` | number | `duration_hours` × `hourly_rate` |
| `description` | string | |
| `is_billable` | bool | |
| `is_invoiced` | bool | Set in the same call that creates the invoice line |
| `invoice_id` | link → invoice | |
| `created_at` | datetime | |

---

## 5. CRM and Marketing

### 5.1 `contacts`
One record per person (F-17).

| Column | Type | Notes |
|---|---|---|
| `contact_id` | string | `C-{number}` |
| `first_name` | string | |
| `last_name` | string | |
| `email` | string | Matching key for automatic creation; may be empty for walk-ins |
| `phone` | string | |
| `address` | simpleObject | Shown on invoices |
| `status` | string | `lead` / `customer` / `inactive` |
| `lifecycle_stage` | string | `new` / `active` / `at_risk` / `lost`; recalculated nightly |
| `source` | string | `booking` / `order` / `enquiry` / `landing_page` / `import` / `manual` |
| `tags` | simpleObject | List of tags |
| `total_spent` | number | Updated with each payment |
| `total_transactions` | number | |
| `average_order_value` | number | |
| `last_contact_date` | datetime | |
| `email_opt_out` | bool | Unsubscribed from marketing email |
| `internal_notes` | string | |
| `preferences` | simpleObject | |
| `date_added` | datetime | |
| `created_at` | datetime | |
| `updated_at` | datetime | |

### 5.2 `contact_counter`
One row: the next contact number.

| Column | Type | Notes |
|---|---|---|
| `value` | number | Read and incremented inside a transaction |

### 5.3 `contact_events`
The contact timeline.

| Column | Type | Notes |
|---|---|---|
| `event_id` | string | Unique id |
| `contact_id` | link → contacts | |
| `event_type` | string | `booking` / `order` / `payment` / `email` / `enquiry` / `note` / `review` / `no_show` |
| `event_date` | datetime | |
| `event_data` | simpleObject | |
| `related_id` | string | Id of the related row |
| `user_visible` | bool | Shown on the timeline |

### 5.4 `contact_submissions`
Contact form messages (F-06, UF-19).

| Column | Type | Notes |
|---|---|---|
| `submission_id` | string | Unique id |
| `name` | string | |
| `email` | string | |
| `phone` | string | |
| `message` | string | Up to 1,000 characters |
| `status` | string | `new` / `responded` / `archived` |
| `auto_reply_sent` | bool | |
| `submitted_date` | datetime | |

### 5.5 `segments`
Contact segments (F-17).

| Column | Type | Notes |
|---|---|---|
| `segment_id` | string | Unique id |
| `segment_name` | string | |
| `segment_type` | string | `prebuilt` / `custom` |
| `filter_criteria` | simpleObject | Rules and `all` / `any` |
| `contact_count` | number | Recalculated nightly |
| `is_active` | bool | |
| `created_date` | datetime | |

### 5.6 `tasks`
Tasks (F-20).

| Column | Type | Notes |
|---|---|---|
| `task_id` | string | Unique id |
| `contact_id` | link → contacts | |
| `assigned_to` | link → users | |
| `task_title` | string | |
| `task_type` | string | `manual` / `auto` |
| `source` | string | `manual` / `automation` / `voice` / `system` |
| `due_date` | datetime | |
| `completed` | bool | |
| `completed_date` | datetime | |
| `notes` | string | |
| `auto_generated` | bool | |
| `created_at` | datetime | |

### 5.7 `leads`
Captured leads (F-19).

| Column | Type | Notes |
|---|---|---|
| `lead_id` | string | Unique id |
| `email` | string | |
| `name` | string | |
| `phone` | string | |
| `source` | string | |
| `landing_page_id` | link → landing_pages | |
| `capture_id` | link → lead_captures | |
| `converted_to_contact_id` | link → contacts | Set in the same call that creates the lead |
| `status` | string | `new` / `converted` / `archived` |
| `captured_at` | datetime | |

### 5.8 `lead_captures`
Lead capture forms.

| Column | Type | Notes |
|---|---|---|
| `capture_id` | string | Unique id |
| `capture_name` | string | |
| `capture_type` | string | `lead_form` / `event_registration` / `vsl` |
| `trigger_settings` | simpleObject | |
| `form_fields` | simpleObject | |
| `offer_text` | string | |
| `status` | string | `draft` / `active` / `inactive` |
| `captures_count` | number | |
| `conversions_count` | number | |

### 5.9 `email_campaigns`
Sequences and broadcasts (F-18).

| Column | Type | Notes |
|---|---|---|
| `campaign_id` | string | Unique id |
| `campaign_name` | string | |
| `campaign_type` | string | `sequence` / `broadcast` |
| `status` | string | `draft` / `scheduled` / `active` / `paused` / `completed` |
| `segment_id` | link → segments | Recipients; empty = all contacts not opted out |
| `brevo_campaign_id` | string | |
| `campaign_settings` | simpleObject | Emails, delays, trigger |
| `emails_sent` | number | |
| `opens` | number | |
| `clicks` | number | |
| `conversions` | number | |
| `unsubscribes` | number | |
| `revenue_generated` | number | |
| `created_date` | datetime | |
| `last_run_date` | datetime | |

### 5.10 `contact_campaigns`
Enrolment of a contact in a sequence.

| Column | Type | Notes |
|---|---|---|
| `id` | string | Unique id |
| `contact_id` | link → contacts | |
| `campaign_id` | link → email_campaigns | |
| `sequence_day` | number | |
| `status` | string | `enrolled` / `completed` / `paused` |
| `enrolled_date` | datetime | |
| `last_email_sent_date` | datetime | |
| `completed_date` | datetime | |

### 5.11 `automation_rules`
Automations (F-20).

| Column | Type | Notes |
|---|---|---|
| `name` | string | |
| `trigger_event` | string | `booking_completed` / `enquiry_received` / `invoice_overdue` / `no_booking_90_days` / `lead_captured` |
| `conditions` | simpleObject | Service, segment, tag |
| `actions` | simpleObject | List of `create_task` / `send_email` / `enrol_sequence` / `add_tag`, with settings |
| `is_active` | bool | |
| `last_run_at` | datetime | |
| `created_at` | datetime | |
| `updated_at` | datetime | |

### 5.12 `automation_runs`
Each run of an automation.

| Column | Type | Notes |
|---|---|---|
| `automation_rule_id` | link → automation_rules | |
| `trigger_reference` | string | The triggering event, e.g. `booking:{row id}`; one run per rule and reference |
| `contact_id` | link → contacts | |
| `result` | string | `success` / `skipped` / `failed` |
| `detail` | simpleObject | |
| `ran_at` | datetime | |

---

## 6. Email and Webhooks

### 6.1 `email_templates`
Transactional email templates.

| Column | Type | Notes |
|---|---|---|
| `template_id` | string | Unique id |
| `name` | string | |
| `subject` | string | |
| `body_html` | string | |
| `body_text` | string | |
| `template_type` | string | `welcome` / `confirmation` / `reminder` / `invoice` / `receipt` / `order_update` / `review_request` / `auto_reply` |
| `is_active` | bool | |

### 6.2 `email_log`
Emails sent.

| Column | Type | Notes |
|---|---|---|
| `recipient` | string | |
| `subject` | string | |
| `template_id` | link → email_templates | |
| `brevo_message_id` | string | |
| `sent_at` | datetime | |
| `status` | string | `sent` / `failed` / `opened` / `clicked` |
| `error_message` | string | |

### 6.3 `webhook_log`
Index of webhook events received. The payloads are in R2.

| Column | Type | Notes |
|---|---|---|
| `gateway` | string | `stripe` / `paystack` / `paypal` / `brevo` |
| `event_id` | string | The sender's event id; an id already present is refused |
| `event_type` | string | |
| `r2_key` | string | Where the payload is stored |
| `verified` | bool | Signature verified |
| `processed` | bool | Applied successfully |
| `error_message` | string | |
| `received_at` | datetime | |
| `processed_at` | datetime | |

---

## 7. Content and Policy

### 7.1 `blog_categories`

| Column | Type | Notes |
|---|---|---|
| `category_id` | string | Unique id |
| `name` | string | |
| `slug` | string | |
| `description` | string | |

### 7.2 `blog_posts`

| Column | Type | Notes |
|---|---|---|
| `post_id` | string | Unique id |
| `title` | string | |
| `slug` | string | Unique |
| `content` | string | |
| `excerpt` | string | |
| `featured_image` | media | |
| `author_id` | link → users | |
| `category_id` | link → blog_categories | |
| `tags` | simpleObject | List of tags |
| `template` | string | `classic` / `two_column` / `full_width` |
| `pullquote` | string | |
| `video_url` | string | Empty hides the video |
| `video_type` | string | `youtube` / `vimeo` / `dailymotion` / `direct` |
| `meta_title` | string | |
| `meta_description` | string | |
| `keywords` | string | |
| `status` | string | `draft` / `scheduled` / `published` / `archived` |
| `scheduled_at` | datetime | For `scheduled` |
| `published_at` | datetime | |
| `view_count` | number | |
| `created_at` | datetime | |
| `updated_at` | datetime | |

### 7.3 `blog_comments`

| Column | Type | Notes |
|---|---|---|
| `post_id` | link → blog_posts | |
| `commenter_name` | string | |
| `commenter_email` | string | |
| `comment_text` | string | |
| `status` | string | `pending` / `approved` / `spam` / `deleted`; only `approved` is public |
| `created_at` | datetime | |
| `updated_at` | datetime | |

### 7.4 `landing_pages`

| Column | Type | Notes |
|---|---|---|
| `page_id` | string | Unique id |
| `title` | string | |
| `slug` | string | Unique; public address `/landing/{slug}` |
| `template_type` | string | `lead_capture` / `event_registration` / `vsl` |
| `config` | simpleObject | Template content |
| `status` | string | `draft` / `published` |
| `views_count` | number | |
| `conversions_count` | number | |
| `published_at` | datetime | |
| `created_at` | datetime | |
| `updated_at` | datetime | |

### 7.5 `pages`
Website pages (About, Contact and the others managed under Content & Website → Pages).

| Column | Type | Notes |
|---|---|---|
| `name` | string | Internal name |
| `page_name` | string | Public name |
| `page_title` | string | SEO title |
| `slug` | string | |
| `components` | simpleObject | Page content |
| `is_published` | bool | |
| `view_count` | number | |
| `created_at` | datetime | |
| `updated_at` | datetime | |

### 7.6 `privacy_policy` and 7.7 `t_and_c`
Clauses of the Privacy Policy and of the Terms and Conditions (F-22). Both tables have the same columns.

| Column | Type | Notes |
|---|---|---|
| `category` | string | |
| `title` | string | |
| `clause` | string | |
| `sort_order` | number | Order within the category |
| `is_seeded` | bool | Seeded clauses can be edited and switched off, not deleted |
| `is_active` | bool | Published |
| `created_at` | datetime | |
| `updated_at` | datetime | |

---

## 8. Reviews

### 8.1 `reviews`
Customer reviews (F-23).

| Column | Type | Notes |
|---|---|---|
| `contact_id` | link → contacts | |
| `booking_id` | link → bookings | |
| `provider_id` | link → users | |
| `service_id` | link → services | |
| `request_token` | string | Unique; public address `/review/{token}` |
| `rating` | number | `1` to `5` |
| `comment` | string | |
| `status` | string | `requested` / `awaiting_approval` / `approved` / `declined`; only `approved` can be public |
| `show_on_homepage` | bool | |
| `requested_at` | datetime | |
| `submitted_at` | datetime | |
| `approved_at` | datetime | |

### 8.2 `testimonials`
Testimonials typed in by the business (F-23).

| Column | Type | Notes |
|---|---|---|
| `author_name` | string | |
| `text` | string | |
| `rating` | number | `1` to `5`; optional |
| `photo` | media | Optional |
| `show_on_homepage` | bool | |
| `sort_order` | number | |
| `created_at` | datetime | |

At most 10 reviews and testimonials together have `show_on_homepage` set.

---

## 9. Summary and Health

Written by background tasks only (`integration-architecture.md`, Background Tasks); dashboards read them and never aggregate source tables.

### 9.1 `daily_summary`

| Column | Type | Notes |
|---|---|---|
| `date` | date | |
| `total_revenue` | number | |
| `revenue_by_service` | simpleObject | |
| `booking_count` | number | |
| `new_contacts` | number | |
| `created_at` | datetime | |

### 9.2 `service_performance`

| Column | Type | Notes |
|---|---|---|
| `service_id` | link → services | |
| `date` | date | |
| `revenue` | number | |
| `booking_count` | number | |
| `completed_count` | number | For the completion rate |
| `created_at` | datetime | |

### 9.3 `provider_performance`

| Column | Type | Notes |
|---|---|---|
| `provider_id` | link → users | |
| `date` | date | |
| `revenue` | number | |
| `booking_count` | number | |
| `created_at` | datetime | |

### 9.4 `campaign_metrics`

| Column | Type | Notes |
|---|---|---|
| `campaign_id` | link → email_campaigns | |
| `date` | date | |
| `emails_sent` | number | |
| `opens` | number | |
| `clicks` | number | |
| `conversions` | number | |
| `unsubscribes` | number | |
| `created_at` | datetime | |

### 9.5 `health_log`
Kept 30 days.

| Column | Type | Notes |
|---|---|---|
| `timestamp` | datetime | |
| `status` | string | `healthy` / `degraded` / `unhealthy` |
| `metrics` | simpleObject | Release version, schema fingerprint, background task status, queued retries, error count |
| `created_at` | datetime | |

### 9.6 `error_log`
Kept 90 days.

| Column | Type | Notes |
|---|---|---|
| `timestamp` | datetime | |
| `function_name` | string | |
| `error_type` | string | |
| `error_message` | string | |
| `severity` | string | `error` / `critical` |
| `user_id` | link → users | |
| `resolved` | bool | |
| `resolved_at` | datetime | |
| `created_at` | datetime | |

---

*End of file — authoritative-schema.md (55 tables)*
