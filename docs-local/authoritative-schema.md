---
title: "authoritative-schema"
doc-id: "mb-8b83e940"
updated: "2026-10-29"
---
# Mybizz CS — Authoritative Database Schema

**Date:** 2026-06-01
**Version:** V1 (Consulting & Services only)
**Total Tables:** 44
**Platform:** Anvil.works
**Source:** Consolidated from `database-schema.md`, [[[mb-3-cs-project-context:docs-standard-local/scaffold-spec|Scaffold Specification]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-standard-local/scaffold-spec.md), and [[[mb-3-cs-project-context:docs-local/build-plan|Build Plan (Definitive)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/build-plan.md)

---

## Table Groups Overview

| Group | Tables | Description |
|-------|--------|-------------|
| **Core & Configuration** | 10 | Users, settings, vault, business profile |
| **Bookings** | 6 | Services, availability, bookings, appointments |
| **CRM** | 9 | Contacts, campaigns, segments, tasks, leads |
| **Email** | 3 | Templates, logs, webhooks |
| **Content** | 5 | Blog, comments, pages, landing pages |
| **Policy** | 2 | Privacy policy, terms & conditions clauses |
| **Finance** | 3 | Invoices, time entries |
| **Summary** | 6 | Pre-computed analytics, health, and errors |
| **Total** | **44** | |

---

## 1. Core & Configuration Tables (10)

### [DONE] 1.1 `activity_log`
User action tracking for audit trail.

| Field | Type | Notes |
|-------|------|-------|
| `user_id` | link → users | User who performed action |
| `action_type` | string | e.g., "login", "booking_created" |
| `description` | string | Human-readable description |
| `metadata` | simpleObject | Additional context |
| `created_at` | datetime | UTC |
| `ip_address` | string | Client IP |

### [DONE] 1.2 `business_profile`
Client business information.

| Field | Type | Notes |
|-------|------|-------|
| `business_name` | string | |
| `description` | string | |
| `logo` | media | |
| `contact_email` | string | |
| `phone` | string | |
| `website_url` | string | |
| `tagline` | string | |
| `address_line_1` | string | |
| `address_line_2` | string | |
| `city` | string | |
| `country` | string | |
| `postal_code` | string | |
| `timezone` | string | IANA timezone (e.g., "Africa/Johannesburg") |
| `system_currency` | string | ISO 4217 (e.g., "ZAR", "USD") — set at onboarding, immutable after first transaction |
| `social_facebook` | string | |
| `social_instagram` | string | |
| `social_x` | string | |
| `social_linkedin` | string | |
| `created_at` | datetime | UTC |
| `updated_at` | datetime | UTC |

**Note:** `system_currency` is set during onboarding and becomes immutable after the first transaction. All internal calculations use this currency. Customer-facing prices can be set in different currencies via service pricing, but all internal work is done in `system_currency`.

#### Onboarding fields (added per plan-tune audit 2026-05-31)

| Field | Type | Notes |
|-------|------|-------|
| `onboarding_status` | string | `"in_progress"` or `"completed"` — tracks onboarding state |
| `onboarding_completed` | bool | Overall completion flag |
| `onboarding_completed_at` | datetime | UTC — when onboarding completed |
| `entity_type` | string | `"Individual"` or `"Business"` |
| `palette` | string | Selected brand palette identifier |
| `legal_compliance_enabled` | bool | Legal Compliances switch — irreversible once True |
| `legal_compliance_enabled_at` | datetime | UTC — when switch was enabled |
| `legal_ack_accepted` | bool | Legal responsibility acknowledgement |
| `legal_ack_accepted_at` | datetime | UTC — when acknowledgement recorded |
| `data_governance_ack_accepted` | bool | Data governance boundary acknowledgement |
| `data_governance_ack_accepted_at` | datetime | UTC — when acknowledgement recorded |

> **See:** [[[default:specifications-global/spec-onboarding-implementation-plan|Mybizz CS Onboarding System - Engineering Plan]]](C:/dev/project-library-global/specifications-global/spec-onboarding-implementation-plan.md) for full onboarding field definitions.

#### Design and website fields (added per DESIGN.md)

| Field | Type | Notes |
|-------|------|-------|
| `palette_updated_at` | datetime | UTC — when palette was last changed |
| `blog_list_view` | string | "tiled" or "list" — blog listing style |
| `enable_comments` | bool | Enable blog comments |
| `homepage_template` | string | "classic", "services", "booking", "minimalist" |
| `business_logo` | media | Separate from logo — used in specific contexts |
| `accreditations` | string | Business accreditations text |
| `google_maps_embed_url` | string | Google Maps embed URL for contact page |

###[DONE] 1.3 `config`
Key-value configuration storage.

| Field | Type | Notes |
|-------|------|-------|
| `key` | string | Indexed, unique |
| `value` | simpleObject | JSON value |
| `category` | string | e.g., "features", "settings" |
| `updated_at` | datetime | UTC |
| `updated_by` | link → users | |

### [DONE]  1.4 `email_config`
Email service configuration (Brevo).

| Field | Type | Notes |
|-------|------|-------|
| `from_email` | string | Brevo verified sender |
| `from_name` | string | |
| `configured` | bool | |
| `configured_at` | datetime | UTC |
| `sender_domain_verified` | bool | SPF/DKIM verified |

### [DONE] 1.5 `files`
Anvil Media storage metadata.

| Field | Type | Notes |
|-------|------|-------|
| `path` | string | |
| `file` | media | |
| `file_version` | string | |

### [DONE] 1.6 `payment_config`
Payment gateway configuration.

| Field | Type | Notes |
|-------|------|-------|
| `active_gateway` | string | "stripe" or "paystack" |
| `stripe_publishable_key` | string | Public key only |
| `stripe_connected` | bool | |
| `stripe_connected_at` | datetime | UTC |
| `paystack_connected` | bool | |
| `paystack_connected_at` | datetime | UTC |
| `paypal_client_id` | string | **V2 ONLY — not used in V1** |
| `paypal_connected` | bool | **V2 ONLY** |
| `paypal_connected_at` | datetime | UTC | **V2 ONLY** |
| `test_mode` | bool | |
| `configured_at` | datetime | UTC |

**Note:** Secret keys (`stripe_secret_key`, `paystack_secret_key`) stored in `vault` table only, not here.

### [DONE] 1.7 `rate_limits`
API rate limiting state.

| Field | Type | Notes |
|-------|------|-------|
| `identifier` | string | Indexed (IP or user ID) |
| `count` | number | Request count |
| `reset_time` | datetime | When counter resets |
| `last_request` | datetime | UTC |

### [DONE] 1.8 `theme_config`
M3 theme customization.

| Field | Type | Notes |
|-------|------|-------|
| `primary_color` | string | Hex color |
| `accent_color` | string | Hex color |
| `font_family` | string | |
| `header_style` | string | |
| `updated_at` | datetime | UTC |

###[DONE] 1.9 `users` (Extended Anvil Users)
System users and staff (extends Anvil Users service).

| Field | Type | Notes |
|-------|------|-------|
| `email` | string | Indexed, unique |
| `enabled` | bool | |
| `last_login` | datetime | UTC |
| `password_hash` | string | Anvil managed |
| `n_password_failures` | number | Anvil managed |
| `confirmed_email` | bool | |
| `role` | string | "owner", "manager", "admin", "staff", "customer" |
| `account_status` | string | "active", "suspended", "deleted" |
| `phone` | string | |
| `permissions` | simpleObject | JSON permission flags |
| `created_at` | datetime | UTC |
| `signed_up` | datetime | UTC |
| `email_confirmation_key` | string | |
| `mfa` | simpleObject | TOTP configuration |

### [DONE] 1.10 `vault`
Encrypted secrets storage.

| Field | Type | Notes |
|-------|------|-------|
| `name` | string | Indexed, unique |
| `value` | string | Encrypted with Fernet |
| `created_at` | datetime | UTC |
| `updated_at` | datetime | UTC |
| `updated_by` | link → users | |

**Stored secrets:**
- `brevo_smtp_key` — Brevo SMTP credential
- `brevo_api_key` — Brevo Campaigns API key
- `stripe_secret_key` — Stripe secret key
- `paystack_secret_key` — Paystack secret key

---

## 2. Bookings Tables (6)

### [DONE] 2.1 `availability_exceptions`
Booking availability overrides (holidays, closures).

| Field | Type | Notes |
|-------|------|-------|
| `resource_id` | link → users | Staff provider |
| `exception_date` | date | |
| `start_time` | string | HH:MM |
| `end_time` | string | HH:MM |
| `is_available` | bool | False = blocked |
| `reason` | string | e.g., "Holiday", "Vacation" |

### [DONE]  2.2 `availability_rules`
Recurring availability schedules.

| Field | Type | Notes |
|-------|------|-------|
| `resource_id` | link → users | Staff provider |
| `day_of_week` | number | 0=Monday, 6=Sunday |
| `start_time` | string | HH:MM |
| `end_time` | string | HH:MM |
| `is_active` | bool | |

### [DONE] 2.3 `booking_metadata_schemas`
Dynamic booking form schemas.

| Field | Type | Notes |
|-------|------|-------|
| `schema_name` | string | e.g., "appointment", "consultation" |
| `booking_type` | string | "appointment" or "service" |
| `field_definitions` | simpleObject | JSON schema for dynamic fields |
| `is_active` | bool | |

### [DONE]  2.4 `bookings`
Service appointments.

| Field | Type | Notes |
|-------|------|-------|
| `booking_id` | string | Unique ID |
| `contact_id` | link → contacts | Person who booked |
| `service_id` | link → services | |
| `resource_id` | link → users | Staff provider |
| `booking_type` | string | "appointment" or "service" |
| `status` | string | "PENDING", "CONFIRMED", "COMPLETED", "CANCELLED" |
| `start_datetime` | datetime | UTC |
| `end_datetime` | datetime | UTC |
| `total_price` | number | |
| `payment_status` | string | "unpaid", "deposit_paid", "paid", "refunded" |
| `payment_id` | string | Gateway transaction ID |
| `special_requests` | string | |
| `metadata` | simpleObject | Per booking_type schema |
| `booking_number` | string | Human-readable (e.g., "BK-2026-001") |
| `created_at` | datetime | UTC |
| `updated_at` | datetime | UTC |

### [DONE] 2.5 `services`
Service offerings.

| Field | Type | Notes |
|-------|------|-------|
| `service_id` | string | Unique ID |
| `name` | string | Public-facing |
| `description` | string | Public-facing |
| `duration_minutes` | number | 0 = variable |
| `price` | number | |
| `display_currency` | string | |
| `category` | string | |
| `provider_id` | link → users | |
| `meeting_type` | string | "In-Person", "Video Call", "Phone Call" |
| `pricing_model` | string | "duration" or "unit" |
| `images` | simpleObject | Array of media URLs |
| `video_url` | string | YouTube/Vimeo URL |
| `is_active` | bool | |
| `created_at` | datetime | UTC |

### [DONE] 2.6 `contact_submissions`
Website contact form submissions.

| Field | Type | Notes |
|-------|------|-------|
| `name` | string | |
| `email` | string | |
| `phone` | string | |
| `message` | string | |
| `submitted_date` | datetime | UTC |
| `status` | string | "new", "responded", "archived" |
| `client_id` | link → users | Business owner |
| `submission_id` | string | Unique ID |
| `auto_reply_sent` | bool | |

---

## 3. CRM Tables (9)

### [DONE] 3.1 `contacts`
Unified contact database (leads and customers).

| Field | Type | Notes |
|-------|------|-------|
| `contact_id` | string | Human-readable (C-{number}) |
| `first_name` | string | |
| `last_name` | string | |
| `email` | string | Indexed, nullable for walk-ins |
| `phone` | string | |
| `status` | string | "new", "active", "inactive", "archived" |
| `source` | string | "Website Form", "Booking Widget", "Manual Entry" |
| `lifecycle_stage` | string | "new", "active", "at_risk", "lost" |
| `tags` | simpleObject | Array of tag strings |
| `total_spent` | number | Cached, updated after bookings |
| `total_transactions` | number | Cached count |
| `average_order_value` | number | Cached |
| `last_contact_date` | datetime | UTC |
| `date_added` | datetime | UTC |
| `created_at` | datetime | UTC |
| `updated_at` | datetime | UTC |
| `internal_notes` | string | |
| `preferences` | simpleObject | User preferences |

### [DONE] 3.2 `contact_campaigns`
Contact enrollment in email campaigns.

| Field | Type | Notes |
|-------|------|-------|
| `id` | string | Unique ID |
| `contact_id` | link → contacts | |
| `campaign_id` | link → email_campaigns | |
| `sequence_day` | number | Current day in sequence |
| `status` | string | "enrolled", "completed", "paused" |
| `enrolled_date` | datetime | UTC |
| `last_email_sent_date` | datetime | UTC |
| `completed_date` | datetime | UTC |

### [DONE] 3.3 `contact_counter`
Counter for contact ID generation.

| Field | Type | Notes |
|-------|------|-------|
| `value` | number | Next contact number |

### [DONE] 3.4 `contact_events`
Contact activity timeline.

| Field | Type | Notes |
|-------|------|-------|
| `event_id` | string | Unique ID |
| `contact_id` | link → contacts | |
| `event_type` | string | "booking", "email", "note", "payment" |
| `event_date` | datetime | UTC |
| `event_data` | simpleObject | Event-specific data |
| `related_id` | string | Related booking/invoice ID |
| `user_visible` | bool | Show in timeline UI |

### [DONE] 3.5 `email_campaigns`
Email marketing campaigns.

| Field | Type | Notes |
|-------|------|-------|
| `campaign_id` | string | Unique ID |
| `campaign_name` | string | |
| `campaign_type` | string | "sequence" or "broadcast" |
| `status` | string | "draft", "active", "paused", "completed" |
| `emails_sent` | number | |
| `opens` | number | |
| `clicks` | number | |
| `conversions` | number | |
| `revenue_generated` | number | |
| `campaign_settings` | simpleObject | Sequence triggers, delays |
| `created_date` | datetime | UTC |
| `last_run_date` | datetime | UTC |

### [DONE] 3.6 `segments`
Contact segmentation.

| Field | Type | Notes |
|-------|------|-------|
| `segment_id` | string | Unique ID |
| `segment_name` | string | |
| `segment_type` | string | "prebuilt" or "custom" |
| `filter_criteria` | simpleObject | JSON filter rules |
| `contact_count` | number | Cached count |
| `is_active` | bool | |
| `created_date` | datetime | UTC |

### [DONE] 3.7 `tasks`
CRM tasks and follow-ups.

| Field | Type | Notes |
|-------|------|-------|
| `task_id` | string | Unique ID |
| `contact_id` | link → contacts | |
| `task_title` | string | |
| `task_type` | string | "manual" or "auto" |
| `due_date` | datetime | UTC |
| `completed` | bool | |
| `completed_date` | datetime | UTC |
| `notes` | string | |
| `auto_generated` | bool | |
| `created_at` | datetime | UTC |

### [DONE] 3.8 `leads`
Captured leads (simultaneous creation with contacts).

| Field | Type | Notes |
|-------|------|-------|
| `lead_id` | string | Unique ID |
| `email` | string | |
| `name` | string | |
| `phone` | string | |
| `source` | string | Landing page name |
| `landing_page_id` | link → landing_pages | |
| `capture_id` | link → lead_captures | |
| `converted_to_contact_id` | link → contacts | Set immediately on capture |
| `status` | string | "new", "converted", "archived" |
| `captured_at` | datetime | UTC |

### [DONE] 3.9 `lead_captures`
Lead capture form configurations.

| Field | Type | Notes |
|-------|------|-------|
| `capture_id` | string | Unique ID |
| `capture_name` | string | |
| `capture_type` | string | "lead_form", "event_registration", "vsl" |
| `trigger_settings` | simpleObject | Form field mappings |
| `form_fields` | simpleObject | JSON schema |
| `offer_text` | string | Lead magnet description |
| `status` | string | "draft", "active", "inactive" |
| `captures_count` | number | Total captures |
| `conversions_count` | number | Converted to contacts |

---

## 4. Email Tables (3)

### [DONE]4.1 `email_templates`
Email message templates.

| Field | Type | Notes |
|-------|------|-------|
| `template_id` | string | Unique ID |
| `client_id` | link → users | |
| `name` | string | |
| `subject` | string | |
| `body_html` | string | HTML content |
| `body_text` | string | Plain text fallback |
| `template_type` | string | "welcome", "confirmation", "reminder" |
| `is_active` | bool | |

### [DONE] 4.2 `email_log`
Email send history.

| Field | Type | Notes |
|-------|------|-------|
| `recipient` | string | Email address |
| `subject` | string | |
| `template_id` | link → email_templates | |
| `sent_at` | datetime | UTC |
| `status` | string | "sent", "failed", "opened", "clicked" |
| `error_message` | string | If failed |

### [DONE]4.3 `webhook_log`
Payment gateway webhook history.

| Field | Type | Notes |
|-------|------|-------|
| `gateway` | string | "stripe" or "paystack" |
| `event_type` | string | e.g., "payment_intent.succeeded" |
| `payload` | simpleObject | Raw webhook payload |
| `signature` | string | Gateway signature |
| `verified` | bool | Signature verified |
| `processed` | bool | Processed successfully |
| `error_message` | string | If processing failed |
| `received_at` | datetime | UTC |

**Note:** [[[default:adr-global/adr-webhook-architecture|Webhook Architecture]]](C:/dev/project-library-global/adr-global/adr-webhook-architecture.md) ADR (Webhook Architecture) defines an alternative column set for this table. The columns above represent the actual implementation. The ADR's `source` maps to `gateway`, `status` maps to `verified` + `processed`, and `idempotency_key` is handled via `event_type` + `gateway` dedup. See [[[default:adr-global/adr-webhook-architecture|Webhook Architecture]]](C:/dev/project-library-global/adr-global/adr-webhook-architecture.md) ADR for the design rationale.

---

## 5. Content Tables (5)

### [DONE] 5.1 `blog_categories`
Blog post categories.

| Field | Type | Notes |
|-------|------|-------|
| `category_id` | string | Unique ID |
| `client_id` | link → users | |
| `name` | string | |
| `slug` | string | URL-friendly |
| `description` | string | |

### [DONE] 5.2 `blog_posts`
Blog content.

| Field | Type | Notes |
|-------|------|-------|
| `post_id` | string | Unique ID |
| `client_id` | link → users | |
| `title` | string | |
| `slug` | string | URL-friendly, unique |
| `content` | string | HTML content |
| `excerpt` | string | Short summary |
| `featured_image` | media | |
| `author_id` | link → users | |
| `category_id` | link → blog_categories | |
| `status` | string | "draft", "published", "archived" |
| `published_at` | datetime | UTC |
| `created_at` | datetime | UTC |
| `updated_at` | datetime | UTC |
| `view_count` | number | |
| `template` | string | "classic_centered", "two_column", "full_width" |
| `meta_title` | string | SEO title override |
| `meta_description` | string | SEO meta description |
| `keywords` | string | SEO keywords (comma-separated) |
| `pullquote` | string | Featured pullquote text |
| `video_url` | string | YouTube URL for embedded video (V1: YouTube only) |
| `video_type` | string | Video source type (V1: "youtube") |

### [DONE] 5.2A `blog_comments`
Blog post comments.

| Field | Type | Notes |
|-------|------|-------|
| `post_id` | link → blog_posts | Blog post |
| `commenter_name` | string | |
| `commenter_email` | string | |
| `comment` | string | Comment text |
| `status` | string | "pending", "approved", "rejected" |
| `created_at` | datetime | UTC |
| `updated_at` | datetime | UTC |

### [DONE] 5.3 `landing_pages`
Marketing landing pages.

| Field | Type | Notes |
|-------|------|-------|
| `page_id` | string | Unique ID |
| `title` | string | |
| `slug` | string | URL-friendly, unique |
| `template_type` | string | "lead_capture", "event_registration", "vsl" |
| `config` | simpleObject | Template configuration |
| `status` | string | "draft", "published" |
| `created_at` | datetime | UTC |
| `published_at` | datetime | UTC |
| `views_count` | number | |
| `conversions_count` | number | Leads captured |
| `updated_at` | datetime | UTC |
| `client_id` | link → users | |

### [DONE] 5.4 `pages`
Static website pages.

| Field | Type | Notes |
|-------|------|-------|
| `name` | string | Internal name |
| `slug` | string | URL-friendly |
| `components` | simpleObject | Page builder components |
| `is_published` | bool | |
| `view_count` | number | |
| `created_at` | datetime | UTC |
| `updated_at` | datetime | UTC |
| `page_name` | string | Public name (e.g., "About Us") |
| `page_title` | string | SEO title |

---

## 5A. Policy Tables (2)

### 5A.1 `privacy_policy`
Privacy policy clause data for the Owner's public-facing privacy policy page.

| Field | Type | Notes |
|-------|------|-------|
| `category` | string | Logical grouping (e.g., "Data Collection", "Cookies", "Third Parties") |
| `title` | string | Clause heading |
| `clause` | string | Body text / rich text |
| `sort_order` | number | Display order within category |
| `is_seeded` | bool | True if from master template, False if custom |
| `is_active` | bool | Whether clause is published |
| `created_at` | datetime | UTC |
| `updated_at` | datetime | UTC |

### 5A.2 `t_and_c`
Terms and conditions clause data for the Owner's public-facing T&Cs page.

| Field | Type | Notes |
|-------|------|-------|
| `category` | string | Logical grouping (e.g., "Service Terms", "Payment Terms", "Liability") |
| `title` | string | Clause heading |
| `clause` | string | Body text / rich text |
| `sort_order` | number | Display order within category |
| `is_seeded` | bool | True if from master template, False if custom |
| `is_active` | bool | Whether clause is published |
| `created_at` | datetime | UTC |
| `updated_at` | datetime | UTC |

> **See:** [[[default:adr-global/adr-legal-policy-responsibility-acknowledgement-and-clause-builder-architecture|Legal Policy Responsibility Acknowledgement and Clause-Builder Architecture]]](C:/dev/project-library-global/adr-global/adr-legal-policy-responsibility-acknowledgement-and-clause-builder-architecture.md) ADR for the clause builder architecture.

---

## 6. Finance Tables (3)

### 6.1 `invoice`
Invoice records.

| Field | Type | Notes |
|-------|------|-------|
| `invoice_id` | string | Unique ID |
| `invoice_number` | string | Human-readable (INV-2026-001) |
| `contact_id` | link → contacts | |
| `invoice_date` | datetime | UTC |
| `due_date` | datetime | UTC |
| `total_amount` | number | |
| `currency` | string | ISO 4217 (e.g., "ZAR", "USD") |
| `paid_at` | datetime | UTC |
| `status` | string | "draft", "sent", "paid", "overdue", "cancelled" |
| `payment_method` | string | "stripe", "paystack" |
| `payment_id` | string | Gateway transaction ID |
| `pdf_url` | string | Generated PDF URL |
| `notes` | string | |
| `created_at` | datetime | UTC |

### 6.2 `invoice_items`
Invoice line items.

| Field | Type | Notes |
|-------|------|-------|
| `invoice_id` | link → invoice | |
| `description` | string | |
| `quantity` | number | |
| `unit_price` | number | |
| `amount` | number | quantity × unit_price |

### 6.3 `time_entries`
Time tracking for services.

| Field | Type | Notes |
|-------|------|-------|
| `entry_id` | string | Unique ID |
| `contact_id` | link → contacts | |
| `staff_id` | link → users | Provider |
| `service_id` | link → services | |
| `booking_id` | link → bookings | Optional |
| `start_time` | datetime | UTC |
| `end_time` | datetime | UTC |
| `duration_hours` | number | Calculated |
| `hourly_rate` | number | |
| `total_amount` | number | duration × hourly_rate |
| `description` | string | |
| `is_billable` | bool | |
| `is_invoiced` | bool | |
| `created_at` | datetime | UTC |

**Note:** Time tracking is deferred to V2 per build-plan.md. This table exists as V2 infrastructure. No V1 UI or server code references it.

---

## 7. Summary Tables (5)

### 7.1 `daily_summary`
Pre-computed daily analytics. Populated by daily background task ([[[default:adr-global/adr-real-time-and-background-tasks|Real-Time Updates and Background Task Architecture]]](C:/dev/project-library-global/adr-global/adr-real-time-and-background-tasks.md) ADR, [[[default:adr-global/adr-data-access-patterns|Data Access Patterns and Query Limitations]]](C:/dev/project-library-global/adr-global/adr-data-access-patterns.md) ADR).

| Field | Type | Notes |
|-------|------|-------|
| `date` | date | Summary date |
| `total_revenue` | number | Revenue for the day |
| `revenue_by_service` | simpleObject | JSON breakdown by service |
| `booking_count` | number | Confirmed bookings |
| `new_contacts` | number | New contacts created |
| `created_at` | datetime | UTC |

### 7.2 `service_performance`
Pre-computed per-service analytics. Populated by daily background task ([[[default:adr-global/adr-data-access-patterns|Data Access Patterns and Query Limitations]]](C:/dev/project-library-global/adr-global/adr-data-access-patterns.md) ADR).

| Field | Type | Notes |
|-------|------|-------|
| `service_id` | link → services | |
| `date` | date | Summary date |
| `revenue` | number | Revenue for the service |
| `booking_count` | number | Bookings for the service |
| `created_at` | datetime | UTC |

### 7.3 `provider_performance`
Pre-computed per-provider analytics. Populated by daily background task ([[[default:adr-global/adr-data-access-patterns|Data Access Patterns and Query Limitations]]](C:/dev/project-library-global/adr-global/adr-data-access-patterns.md) ADR).

| Field | Type | Notes |
|-------|------|-------|
| `provider_id` | link → users | Staff provider |
| `date` | date | Summary date |
| `revenue` | number | Revenue for the provider |
| `booking_count` | number | Bookings for the provider |
| `created_at` | datetime | UTC |

### 7.4 `campaign_metrics`
Pre-computed campaign analytics. Populated by hourly background task ([[[default:adr-global/adr-data-access-patterns|Data Access Patterns and Query Limitations]]](C:/dev/project-library-global/adr-global/adr-data-access-patterns.md) ADR).

| Field | Type | Notes |
|-------|------|-------|
| `campaign_id` | link → email_campaigns | |
| `date` | date | Summary date |
| `opens` | number | Email opens |
| `clicks` | number | Email clicks |
| `conversions` | number | Conversions |
| `created_at` | datetime | UTC |

### 7.5 `health_log`
Instance health metrics. Written every 15 minutes by health heartbeat background task ([[[default:adr-global/adr-real-time-and-background-tasks|Real-Time Updates and Background Task Architecture]]](C:/dev/project-library-global/adr-global/adr-real-time-and-background-tasks.md) ADR).

| Field | Type | Notes |
|-------|------|-------|
| `timestamp` | datetime | UTC — when health check ran |
| `status` | string | "healthy", "degraded", "unhealthy" |
| `metrics` | simpleObject | JSON health metrics |
| `created_at` | datetime | UTC |

### 7.6 `error_log`
Application error tracking. Written by server functions when unhandled exceptions occur or error thresholds are exceeded ([[[default:adr-global/adr-observability-architecture|Observability Architecture]]](C:/dev/project-library-global/adr-global/adr-observability-architecture.md) ADR, [[[default:adr-global/adr-real-time-and-background-tasks|Real-Time Updates and Background Task Architecture]]](C:/dev/project-library-global/adr-global/adr-real-time-and-background-tasks.md) ADR).

| Field | Type | Notes |
|-------|------|-------|
| `timestamp` | datetime | UTC — when error occurred |
| `function_name` | string | Server function where error occurred |
| `error_type` | string | Exception class name |
| `error_message` | string | Error message |
| `severity` | string | "error", "critical" |
| `user_id` | link → users | User who triggered the error (if authenticated) |
| `resolved` | bool | Whether error has been investigated |
| `resolved_at` | datetime | UTC — when resolved |
| `created_at` | datetime | UTC |

---

## Key Architectural Patterns

### UTC Storage & Timezone Display
All `datetime` fields stored in UTC. Display conversion uses `business_profile.timezone` (IANA string).

### Data Isolation

Each client instance is a separate Anvil app with its own isolated database. Data isolation is structural — one client instance cannot access another's tables. See [[[default:adr-global/adr-dependency-based-not-multi-tenant|Mybizz CS is a Dependency-Based Architecture, Not a Multi-Tenant Architecture]]](C:/dev/project-library-global/adr-global/adr-dependency-based-not-multi-tenant.md) ADR.

### Secret Management
Payment gateway secret keys stored in `vault` table only (encrypted at rest). Credentials retrieved via `get_vault_secret()` in server code.

### Lead Capture ([[[default:adr-global/adr-lead-capture-simultaneous-creation|Lead Capture: Simultaneous leads + contacts Creation]]](C:/dev/project-library-global/adr-global/adr-lead-capture-simultaneous-creation.md) ADR)
When lead submitted from landing page: both `leads` and `contacts` rows created simultaneously. `leads.converted_to_contact_id` set immediately. Contact auto-enrolled in welcome campaign.

### Email Architecture ([[[default:adr-global/adr-brevo-email-and-crm|Brevo Replaces All Zoho Products]]](C:/dev/project-library-global/adr-global/adr-brevo-email-and-crm.md) ADR)
- Brevo SMTP — Transactional email
- Brevo Campaigns API — Marketing email
- All clients use identical SMTP host (smtp-relay.brevo.com:587)
- Per-client credentials in vault

### Mybizz_management Amendment Log
Mybizz_management (the platform operator's internal tooling) maintains an **append-only amendment log** of credential and configuration changes. It never overwrites. It always holds the latest values plus the full history. This is outside the client instance scope but is a system-level behaviour required for audit and compliance.

### Onboarding Model
Onboarding is a separate form with required elements (currency selection, legal acknowledgement). Settings is a single tabbed form for all configuration. Bidirectional navigation between Onboarding and Settings. **[[[default:adr-global/adr-onboarding-finality|Onboarding Finality]]](C:/dev/project-library-global/adr-global/adr-onboarding-finality.md) ADR**

**See:** [[[default:specifications-global/spec-onboarding-implementation-plan|Mybizz CS Onboarding System - Engineering Plan]]](C:/dev/project-library-global/specifications-global/spec-onboarding-implementation-plan.md) for full onboarding specification.

---

## V1 vs V2 Scope

### V1 (Current)
- Stripe and Paystack payment gateways only
- PayPal **NOT** included (deferred to V2)
- Basic analytics
- Manual + basic auto-generated tasks
- 4 homepage templates
- Pre-built email campaign sequences
- 4 landing page templates

### V2 (Deferred)
- PayPal integration
- Client portal (customer self-service)
- Advanced analytics (revenue by service/provider)
- Complex task automation
- Additional homepage templates
- Additional landing page templates
- Scheduled reports

---

*End of file — authoritative-schema.md (V1, 44 tables)*
