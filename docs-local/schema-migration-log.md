---
title: "schema-migration-log"
doc-id: "mb-dde289cd"
updated: "2026-11-01"
---
# Schema Migration Log

Every schema change, recorded at Step 17 and read at Steps 41 and 42. The schema itself is `authoritative-schema.md`.

| Release | Date | Table | Change | Migration plan |
|---|---|---|---|---|
| pre-release | 2026-10-31 | 11 new tables: `resources`, `group_sessions`, `recurring_series`, `packages`, `customer_packages`, `orders`, `payments`, `automation_rules`, `automation_runs`, `reviews`, `testimonials` | Additive: tables created | App 1 only, prompts P1–P5 below. App 3 and the client instances do not exist yet; App 3 is cloned from App 1's schema |
| pre-release | 2026-10-31 | `error_log` | Breaking: `tomestamp` renamed `timestamp` | App 1 only, by hand (Part A) |
| pre-release | 2026-10-31 | `blog_comments` | Breaking: `comment` renamed `comment_text` | App 1 only, by hand (Part A) |
| pre-release | 2026-10-31 | `availability_rules`, `availability_exceptions` | Breaking: `resource_id` renamed `provider_id` | App 1 only, by hand (Part A) |
| pre-release | 2026-10-31 | `bookings` | Breaking: `resource_id` renamed `provider_id`; then a new `resource_id` added as a link to `resources` | App 1 only, by hand (Part A), then prompt P6 |
| pre-release | 2026-10-31 | `time_entries` | Breaking: `staff_id` renamed `provider_id` | App 1 only, by hand (Part A) |
| pre-release | 2026-10-31 | `services`, `bookings`, `invoice`, `invoice_items`, `time_entries` | Additive: columns added | App 1 only, prompt P6 |
| pre-release | 2026-10-31 | `business_profile`, `payment_config`, `users`, `files`, `contacts`, `tasks`, `email_campaigns`, `email_log`, `campaign_metrics`, `service_performance`, `blog_posts`, `webhook_log` | Additive: columns added | App 1 only, prompt P7 |
| pre-release | 2026-10-31 | 19 tables with `instance_id` or `client_id`; `theme_config`; `webhook_log`; `bookings`; `invoice`; `services` | Breaking: columns removed (listed in P8) | App 1 only, prompt P8, last. App 1 holds development data only; the data in the removed columns is not needed |

---

## Applying this migration

The migration is applied to App 1 only. Every schema change is applied by David in the Anvil editor; the Anvil agent only prepares the schema. Anvil's rule: the agent can change the Data Tables schema, but the change reaches the tables only when it is applied in the Data Tables view, through **Resolve** (`C:\dev\project-library-global\anvil-docs\ai-agent\anvil-ai-agent.md`).

### Part A — by hand, before any prompt (Anvil editor → Data Tables)

Renaming a column in the Data Tables view keeps its data. Rename:

1. `error_log`: `tomestamp` → `timestamp`
2. `blog_comments`: `comment` → `comment_text`
3. `availability_rules`: `resource_id` → `provider_id`
4. `availability_exceptions`: `resource_id` → `provider_id`
5. `bookings`: `resource_id` → `provider_id`
6. `time_entries`: `staff_id` → `provider_id`

### Part B — one prompt per conversation (Anvil editor → Agent chat)

For each prompt, in order P1 to P8:

1. Start a new conversation in the Agent chat.
2. Paste the prompt.
3. Check the agent's list against the prompt. Confirm only when the two match.
4. When the agent has finished, open the Data Tables view, click **Resolve**, check each difference against the prompt, and apply.
5. Then go to the next prompt.

**P1 — services and scheduling tables**

```text
Create these new Data Tables in this app, with exactly these columns and types:

- Table `resources`: `name` (string), `resource_type` (string), `description` (string), `is_active` (bool), `created_at` (datetime)
- Table `group_sessions`: `service_id` (link to one row of `services`), `provider_id` (link to one row of `users`), `resource_id` (link to one row of `resources`), `start_datetime` (datetime), `end_datetime` (datetime), `place_limit` (number), `places_taken` (number), `location` (string), `call_link` (string), `status` (string), `created_at` (datetime)
- Table `recurring_series`: `contact_id` (link to one row of `contacts`), `service_id` (link to one row of `services`), `provider_id` (link to one row of `users`), `pattern` (string), `start_date` (date), `end_date` (date), `status` (string), `created_at` (datetime)

Change only the Data Tables schema. Do not change any form, module, server module, theme, CSS, dependency or service. Do not change or delete any data. For every table you create, set access to "No access" for forms and "Full access" for server code. Before making any change, list exactly what you will create or change and wait for my confirmation.
```

**P2 — orders and packages**

```text
Create these new Data Tables in this app, in this order, with exactly these columns and types:

- Table `packages`: `name` (string), `description` (string), `service_id` (link to one row of `services`), `session_count` (number), `price` (number), `expiry_days` (number), `is_active` (bool), `created_at` (datetime)
- Table `customer_packages`: `package_id` (link to one row of `packages`), `contact_id` (link to one row of `contacts`), `sessions_total` (number), `sessions_remaining` (number), `purchased_at` (datetime), `expires_at` (datetime), `expiry_notice_sent_at` (datetime), `status` (string)
- Table `orders`: `order_number` (string), `contact_id` (link to one row of `contacts`), `service_id` (link to one row of `services`), `provider_id` (link to one row of `users`), `brief` (string), `brief_files` (link to multiple rows of `files`), `status` (string), `total_price` (number), `payment_option` (string), `payment_status` (string), `delivery_message` (string), `delivery_files` (link to multiple rows of `files`), `ordered_at` (datetime), `started_at` (datetime), `delivered_at` (datetime), `created_at` (datetime), `updated_at` (datetime)

Change only the Data Tables schema. Do not change any form, module, server module, theme, CSS, dependency or service. Do not change or delete any data. For every table you create, set access to "No access" for forms and "Full access" for server code. Before making any change, list exactly what you will create or change and wait for my confirmation.
```

**P3 — payments**

```text
Create this new Data Table in this app, with exactly these columns and types:

- Table `payments`: `kind` (string), `invoice_id` (link to one row of `invoice`), `booking_id` (link to one row of `bookings`), `order_id` (link to one row of `orders`), `customer_package_id` (link to one row of `customer_packages`), `contact_id` (link to one row of `contacts`), `method` (string), `amount` (number), `currency` (string), `status` (string), `gateway_reference` (string), `refund_of` (link to one row of `payments`), `recorded_by` (link to one row of `users`), `received_at` (datetime), `notes` (string), `created_at` (datetime)

Change only the Data Tables schema. Do not change any form, module, server module, theme, CSS, dependency or service. Do not change or delete any data. For every table you create, set access to "No access" for forms and "Full access" for server code. Before making any change, list exactly what you will create or change and wait for my confirmation.
```

**P4 — automations**

```text
Create these new Data Tables in this app, in this order, with exactly these columns and types:

- Table `automation_rules`: `name` (string), `trigger_event` (string), `conditions` (simpleObject), `actions` (simpleObject), `is_active` (bool), `last_run_at` (datetime), `created_at` (datetime), `updated_at` (datetime)
- Table `automation_runs`: `automation_rule_id` (link to one row of `automation_rules`), `trigger_reference` (string), `contact_id` (link to one row of `contacts`), `result` (string), `detail` (simpleObject), `ran_at` (datetime)

Change only the Data Tables schema. Do not change any form, module, server module, theme, CSS, dependency or service. Do not change or delete any data. For every table you create, set access to "No access" for forms and "Full access" for server code. Before making any change, list exactly what you will create or change and wait for my confirmation.
```

**P5 — reviews and testimonials**

```text
Create these new Data Tables in this app, with exactly these columns and types:

- Table `reviews`: `contact_id` (link to one row of `contacts`), `booking_id` (link to one row of `bookings`), `provider_id` (link to one row of `users`), `service_id` (link to one row of `services`), `request_token` (string), `rating` (number), `comment` (string), `status` (string), `show_on_homepage` (bool), `requested_at` (datetime), `submitted_at` (datetime), `approved_at` (datetime)
- Table `testimonials`: `author_name` (string), `text` (string), `rating` (number), `photo` (media), `show_on_homepage` (bool), `sort_order` (number), `created_at` (datetime)

Change only the Data Tables schema. Do not change any form, module, server module, theme, CSS, dependency or service. Do not change or delete any data. For every table you create, set access to "No access" for forms and "Full access" for server code. Before making any change, list exactly what you will create or change and wait for my confirmation.
```

**P6 — new columns: services, bookings, invoices, time**

```text
Add these columns to these existing Data Tables, with exactly these names and types:

- Table `services`: add `slug` (string), `hourly_rate` (number), `providers` (link to multiple rows of `users`), `meeting_types` (simpleObject), `travel_buffer_minutes` (number), `is_group` (bool), `required_resources` (link to multiple rows of `resources`), `payment_option` (string), `deposit_amount` (number), `payment_terms_days` (number), `intake_schema_id` (link to one row of `booking_metadata_schemas`), `video_type` (string)
- Table `bookings`: add `resource_id` (link to one row of `resources`), `group_session_id` (link to one row of `group_sessions`), `places` (number), `customer_package_id` (link to one row of `customer_packages`), `recurring_series_id` (link to one row of `recurring_series`), `meeting_type` (string), `customer_address` (simpleObject), `call_link` (string), `travel_before_minutes` (number), `travel_after_minutes` (number), `payment_option` (string), `deposit_amount` (number), `hold_expires_at` (datetime), `source` (string), `reminder_24h_sent_at` (datetime), `reminder_1h_sent_at` (datetime), `cancelled_at` (datetime)
- Table `invoice`: add `booking_id` (link to one row of `bookings`), `order_id` (link to one row of `orders`), `customer_package_id` (link to one row of `customer_packages`), `payment_terms_days` (number), `subtotal` (number), `tax_rate` (number), `tax_amount` (number), `amount_paid` (number), `sent_at` (datetime), `overdue_reminder_sent_at` (datetime), `updated_at` (datetime)
- Table `invoice_items`: add `time_entry_id` (link to one row of `time_entries`), `sort_order` (number)
- Table `time_entries`: add `invoice_id` (link to one row of `invoice`)

Change only the Data Tables schema. Do not change any form, module, server module, theme, CSS, dependency or service. Do not change or delete any data. For every table you create, set access to "No access" for forms and "Full access" for server code. Before making any change, list exactly what you will create or change and wait for my confirmation.
```

**P7 — new columns: everything else**

```text
Add these columns to these existing Data Tables, with exactly these names and types:

- Table `business_profile`: add `home_video_url` (string), `home_video_type` (string), `services_video_url` (string), `services_video_type` (string), `tax_rate` (number), `tax_label` (string), `tax_registration_number` (string), `default_payment_terms_days` (number), `cancellation_window_hours` (number)
- Table `payment_config`: add `paystack_public_key` (string), `paypal_client_id` (string), `paypal_test_mode` (bool), `paypal_connected` (bool), `paypal_connected_at` (datetime)
- Table `users`: add `full_name` (string), `is_provider` (bool), `photo` (media), `specialisations` (string), `contact_id` (link to one row of `contacts`), `preferences` (simpleObject)
- Table `files`: add `created_at` (datetime)
- Table `contacts`: add `address` (simpleObject), `email_opt_out` (bool)
- Table `tasks`: add `assigned_to` (link to one row of `users`), `source` (string)
- Table `email_campaigns`: add `segment_id` (link to one row of `segments`), `brevo_campaign_id` (string), `unsubscribes` (number)
- Table `email_log`: add `brevo_message_id` (string)
- Table `campaign_metrics`: add `emails_sent` (number), `unsubscribes` (number)
- Table `service_performance`: add `completed_count` (number)
- Table `blog_posts`: add `tags` (simpleObject), `scheduled_at` (datetime)
- Table `webhook_log`: add `event_id` (string), `r2_key` (string), `processed_at` (datetime)

Change only the Data Tables schema. Do not change any form, module, server module, theme, CSS, dependency or service. Do not change or delete any data. For every table you create, set access to "No access" for forms and "Full access" for server code. Before making any change, list exactly what you will create or change and wait for my confirmation.
```

**P8 — removals (last)**

```text
Remove these columns from these existing Data Tables. Remove nothing else:

- Table `blog_categories`: remove `client_id`
- Table `blog_posts`: remove `client_id`
- Table `contact_submissions`: remove `client_id`
- Table `email_templates`: remove `client_id`
- Table `landing_pages`: remove `instance_id` and `client_id`
- Table `blog_comments`: remove `instance_id`
- Table `campaign_metrics`: remove `instance_id`
- Table `contacts`: remove `instance_id`
- Table `daily_summary`: remove `instance_id`
- Table `email_campaigns`: remove `instance_id`
- Table `health_log`: remove `instance_id`
- Table `lead_captures`: remove `instance_id`
- Table `leads`: remove `instance_id`
- Table `privacy_policy`: remove `instance_id`
- Table `provider_performance`: remove `instance_id`
- Table `segments`: remove `instance_id`
- Table `service_performance`: remove `instance_id`
- Table `t_and_c`: remove `instance_id`
- Table `tasks`: remove `instance_id`
- Table `theme_config`: remove `primary_color`, `accent_color`, `font_family`
- Table `webhook_log`: remove `payload`, `signature`
- Table `bookings`: remove `payment_id`
- Table `invoice`: remove `payment_method`, `payment_id`
- Table `services`: remove `provider_id`, `meeting_type`

Change only the Data Tables schema. Do not change any form, module, server module, theme, CSS, dependency or service. Do not change or delete any data. For every table you create, set access to "No access" for forms and "Full access" for server code. Before making any change, list exactly what you will create or change and wait for my confirmation.
```

### Part C — check (back to Claude)

Send Claude the new `anvil.yaml`. Claude compares its `db_schema` with `authoritative-schema.md`, table by table and column by column; the migration is complete when nothing differs.
