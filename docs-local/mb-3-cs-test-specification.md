---
title: "mb-3-cs-test-specification"
doc-id: "mb-d8dcf41a"
updated: "2026-11-01"
---
# Mybizz CS — Test Specification (Canonical)

**Date:** 2026-06-16
**Source:** CEO review 2026-06-15 Section 6, Engineering review 2026-06-16
**Authority:** Build plan test framework, [[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR, [[[default:adr-global/adr-form-architecture-and-state|Form Architecture and State Management]]](C:/dev/project-library-global/adr-global/adr-form-architecture-and-state.md) ADR, [[[default:adr-global/adr-webhook-architecture|Webhook Architecture]]](C:/dev/project-library-global/adr-global/adr-webhook-architecture.md) ADR, [[[default:adr-global/adr-dependency-based-not-multi-tenant|Dependency-Based Architecture, Not Multi-Tenant]]](C:/dev/project-library-global/adr-global/adr-dependency-based-not-multi-tenant.md) ADR

**Note:** General testing methodology (the Level 1/2/3 model, pure-function characteristics, the
Uplink safety protocol, generic test structure/naming, common test patterns) lives in
[[[default:specifications-global/spec-testing-methodology-standards|Testing Methodology Standards]]](C:/dev/project-library-global/specifications-global/spec-testing-methodology-standards.md) and is not repeated here. This document contains only this
project's specific flows, roles, and data — the actual test plan.

---

## 1. E2E Booking Flow Test Spec

**Covers:** CEO review Section 6 — "No end-to-end test spec covering the full happy path from
service selection to payment confirmation"

### Happy Path: Service Selection to Confirmation

**Preconditions:**
- Client instance provisioned with all tables
- At least one service configured in `services` table
- At least one provider (Staff user) assigned to the service
- Availability rules configured for the provider
- Payment gateway configured (Stripe or Paystack in test mode)
- Vault contains payment gateway secret keys

**Steps:**

| Step | Action | Expected Result |
|------|--------|-----------------|
| 1 | Navigate to booking form | Booking form loads with service list from Call 1 (`get_booking_context`) |
| 2 | Select a service | Providers assigned to this service are displayed |
| 3 | Select a provider | Available date range is displayed |
| 4 | Select a date | Available time slots are displayed (from Call 2 `get_available_slots`) |
| 5 | Select a time slot | Meeting type options are displayed (from pre-loaded data) |
| 6 | Select meeting type | Intake form is displayed (from pre-loaded data) |
| 7 | Fill intake form | Review/confirmation step is displayed |
| 8 | Confirm booking | Call 3 (`confirm_booking`) processes payment and creates booking |
| 9 | Confirmation screen | Booking confirmation displayed with booking ID, date, time, service, provider |
| 10 | Check `bookings` table | New row exists with status `confirmed`; a `payments` row holds the gateway reference |
| 11 | Check `contacts` table | Contact auto-created (if new) or linked (if existing) |
| 12 | Check `invoice` table | Invoice auto-generated with correct amount |

**Performance gate:** Steps 1-9 complete in under 3 seconds on a standard connection.

**Data validation:**
- Booking amount matches service price
- Currency matches `business_profile.system_currency`
- Timezone conversion is correct (stored UTC, displayed in client timezone)
- Contact has correct email, phone, name from intake form

### Edge Case: Existing Contact Books

Same as happy path, but the contact email matches an existing `contacts` row.

**Expected:** No duplicate contact created. Booking links to existing contact.

### Edge Case: Provider Unavailable

**Steps:**
1. Start booking flow for a service
2. Select a provider
3. Select a date where the provider has no availability

**Expected:** "No available time slots" message. User can select a different date or provider.

### Edge Case: Double-Booking Prevention

**Steps:**
1. Two users simultaneously book the same provider at the same time slot
2. First booking succeeds
3. Second booking attempts the same slot

**Expected:** Second booking gets "Slot no longer available" error. No duplicate booking created.

---

## 2. Error Path Test Cases

**Covers:** CEO review Section 6 — "Build plan specifies happy-path tests but not error-path
tests"

### 2.1 Payment Failure

**Scenario:** Stripe/Paystack returns `payment_intent.payment_failed`

**Steps:**
1. Start booking flow through to payment step
2. Use a test card that triggers payment failure (Stripe: `4000000000000002`)
3. Submit payment

**Expected:**
- User sees "Payment failed" error with retry option
- `bookings` row created with status `pending` (not `confirmed`)
- `webhook_log` row has `processed=True`, `error_message` set
- No invoice generated
- User can retry payment without re-entering booking details

### 2.2 Webhook Signature Failure

**Scenario:** Webhook arrives with invalid signature

**Steps:**
1. Send a POST request to `/api/webhooks/stripe` with a valid payload but incorrect signature

**Expected:**
- HTTP 400 returned
- No `webhook_log` row created
- No background task dispatched
- Error logged in `error_log`

### 2.3 Webhook Dispatch Failure

**Scenario:** `launch_background_task()` raises an exception

**Steps:**
1. Simulate task queue full condition
2. Webhook arrives with valid signature

**Expected:**
- `webhook_log` row created with `processed=False`
- HTTP 200 returned to gateway (gateway won't retry)
- Reconciliation task picks up the orphaned row within 5 minutes
- Background task eventually processes the webhook

### 2.4 Amazon SES Throttling

**Scenario:** Amazon SES refuses a campaign email with a throttling error

**Steps:**
1. Campaign enrollment task attempts to send email
2. SES returns a throttling error

**Expected:**
- Contact skipped in this run (not marked as failed)
- Task continues with next contact
- Failed contact retried on next hourly run
- Error logged in `error_log`

### 2.5 Vault Secret Not Found

**Scenario:** Payment gateway key missing from Vault

**Steps:**
1. Attempt to process a payment
2. `get_vault_secret('stripe_secret_key')` raises `KeyNotFoundError`

**Expected:**
- User sees "Unable to retrieve credential. Please contact support."
- Error logged in `health_log` with status `"unhealthy"`
- No 500 error exposed to user
- Mybizz_management alerted (if operational)

### 2.6 Contact Deduplication Conflict

**Scenario:** Same email, different names submitted from landing page

**Steps:**
1. Contact exists with email `jane@example.com`, name `Jane Smith`
2. Landing page form submitted with email `jane@example.com`, name `J. Smith`

**Expected:**
- Existing contact updated (not rejected)
- `leads` row created with `converted_to_contact_id` pointing to existing contact
- Welcome campaign does NOT re-enroll (contact already exists)
- Name discrepancy logged for manual review

### 2.7 Server Call Timeout During Booking

**Scenario:** `get_available_slots()` takes longer than 30 seconds

**Steps:**
1. Start booking flow
2. Select provider and date
3. Server call times out

**Expected:**
- User sees "Connection timed out. Please try again." with retry button
- No partial booking data persisted
- `globals.booking_flow_state` preserved — user can retry without starting over

---

## 3. RBAC Coverage Matrix

**Covers:** CEO review Section 6 — "No test matrix showing which roles can access which
forms/tabs"

### Roles

| Role | Description |
|------|-------------|
| **Owner** | Full access to all forms and settings tabs |
| **Manager** | Most forms and settings; some tabs restricted |
| **Admin** | Operational forms; limited settings access |
| **Staff** | Own bookings, own schedule, limited contacts |
| **Customer** | Own bookings, packages, quotes, invoices and reviews, in the customer portal |

### Form Access Matrix

| Form | Owner | Manager | Admin | Staff | Customer |
|------|-------|---------|-------|-------|----------|
| DashboardForm | ✅ | ✅ | ✅ | ✅ | ❌ |
| BookingCalendarForm | ✅ | ✅ | ✅ | ✅ (own only) | ❌ |
| BookingEditorForm | ✅ | ✅ | ✅ | ✅ (own only) | ❌ |
| BookingViewerForm | ✅ | ✅ | ✅ | ✅ (own only) | ❌ |
| ContactListForm | ✅ | ✅ | ✅ | ❌ | ❌ |
| ContactEditorForm | ✅ | ✅ | ✅ | ❌ | ❌ |
| ContactViewerForm | ✅ | ✅ | ✅ | ❌ | ❌ |
| InvoiceListForm | ✅ | ✅ | ❌ | ❌ | ❌ |
| InvoiceEditorForm | ✅ | ✅ | ❌ | ❌ | ❌ |
| ServiceListForm | ✅ | ✅ | ✅ | ❌ | ❌ |
| ServiceEditorForm | ✅ | ✅ | ❌ | ❌ | ❌ |
| EmailCampaignListForm | ✅ | ✅ | ❌ | ❌ | ❌ |
| EmailCampaignEditorForm | ✅ | ✅ | ❌ | ❌ | ❌ |
| TaskListForm | ✅ | ✅ | ✅ | ✅ (own only) | ❌ |
| BlogEditorForm | ✅ | ✅ | ❌ | ❌ | ❌ |
| LandingPageListForm | ✅ | ✅ | ❌ | ❌ | ❌ |
| LandingPageEditorForm | ✅ | ✅ | ❌ | ❌ | ❌ |
| TeamInboxForm | ✅ (every address) | ✅ (own addresses and desks) | ✅ (own addresses and desks) | ✅ (own addresses and desks) | ❌ |
| DealBoardForm | ✅ | ✅ | ✅ | ✅ (own deals) | ❌ |
| QuoteEditorForm | ✅ | ✅ | ✅ | ❌ | ❌ |
| CompanyListForm | ✅ | ✅ | ✅ | ❌ | ❌ |
| ExpenseEditorForm | ✅ | ✅ | ❌ | ❌ | ❌ |
| AccountsForm | ✅ | ✅ | ❌ | ❌ | ❌ |
| Edit mode on public pages | ✅ | ✅ | ❌ | ❌ | ❌ |
| SharePanelComponent | ✅ | ✅ | ❌ | ❌ | ❌ |
| VaultForm | ✅ | ❌ | ❌ | ❌ | ❌ |
| OnboardingForm | ✅ | ❌ | ❌ | ❌ | ❌ |

### Settings Tab Access Matrix

| Settings Tab | Owner | Manager | Admin | Staff |
|--------------|-------|---------|-------|-------|
| Business Profile | ✅ | ✅ | ❌ | ❌ |
| Palette | ✅ | ✅ | ❌ | ❌ |
| System Currency | ✅ | ❌ | ❌ | ❌ |
| Legal Acknowledgement | ✅ | ❌ | ❌ | ❌ |
| Display Currency | ✅ | ✅ | ❌ | ❌ |
| Payment Gateway | ✅ | ❌ | ❌ | ❌ |
| Services | ✅ | ✅ | ❌ | ❌ |
| Users & Permissions | ✅ | ❌ | ❌ | ❌ |
| Email | ✅ | ✅ | ❌ | ❌ |
| Domain | ✅ | ❌ | ❌ | ❌ |
| Data (including Clear sample data) | ✅ | ❌ | ❌ | ❌ |
| Theme | ✅ | ✅ | ❌ | ❌ |
| Feature Flags | ✅ | ❌ | ❌ | ❌ |

### Test Procedure

For each form/tab in the matrix:
1. Log in as the specified role
2. Attempt to navigate to the form/tab via direct URL or navigation link
3. If access granted: verify form loads correctly
4. If access denied: verify redirect to dashboard or "Access Denied" message
5. Verify the navigation link is hidden (not just disabled) for restricted roles

### RBAC Decorator Pattern

Every server function must have an RBAC decorator. The test verifies:

```python
# Expected pattern on every server function
@authenticated_endpoint(roles=['Owner', 'Manager', 'Admin'])
def get_contacts():
    ...
```

**Test:** Grep all server functions in `server_*/` for `@authenticated_endpoint` or equivalent
decorator. Any function without one is a bug.

---

## 4. Feature Tests

Each test runs against App 1 with the test suite's mail catcher in place of Amazon SES, unless it states otherwise.

| # | Feature | Test | Expected |
|---|---|---|---|
| 4.1 | F-18 receiving | Send an email to `help@` (a desk with two members) | One `email_messages` row in `help@`'s Inbox, visible to both members and to no other Staff user; attachments in R2; linked to the contact with the sender's email |
| 4.2 | F-18 unknown address | Send an email to `nobody@` | The email lands in Unassigned, visible to Owner and Manager only |
| 4.3 | F-18 spam and virus | Deliver an SES notification with a failed virus verdict, then one with a failed spam verdict | The first is never stored; the second lands in Spam |
| 4.4 | F-18 suppression | Report a hard bounce for an address, then send it a confirmation and a campaign | Both are refused; `email_suppressions` holds the address with scope `all` |
| 4.5 | F-18 unsubscribe | Post to the one-click unsubscribe endpoint for a contact | The contact receives no further marketing email and still receives confirmations |
| 4.6 | F-35 quote acceptance | Accept a sent quote at `/quote/{token}` | In one server call the quote is `accepted`, an invoice exists with the quote's lines and total, and a linked deal is `won`; a second acceptance changes nothing |
| 4.7 | F-35 deal ownership | A Staff user lists deals | Only deals the Staff user owns are returned by the server |
| 4.8 | F-27 voice tier 4 | Speak "New booking — Sarah Johnson, Swedish massage, Friday 2pm" | The booking form is filled in; no booking row exists until the user confirms |
| 4.9 | F-27 voice tier 5 | Ask "Who are my top 5 clients by spend?" | The answer matches the instance's own figures; the request sent to App 5 contains no contact name |
| 4.10 | Sample data | Create a real booking for a sample service, then Clear sample data | Every row in `sample_rows` is gone, the real booking stays with its service link empty, `sample_rows` is empty, the banner is gone, `activity_log` records the action |
| 4.11 | Sample data | Run the reminder, campaign and accounts export tasks with sample data present | No email is sent to an `example.com` address and the export contains no sample record |
| 4.12 | F-06 edit mode | As Manager, edit a page's heading and save; as Admin, open the same page | The heading is published; the Admin sees no Edit button and the save function refuses an Admin |
| 4.13 | F-21 preview tags | Fetch a published post's address without running scripts | The first response carries the post's `og:title`, `og:description`, `og:image` and `og:url` |
| 4.14 | F-28 export | Export one month for Xero | One UTF-8 CSV per record type, dates `YYYY-MM-DD`, plain decimal amounts, no void expense |
| 4.15 | F-36 demo mode | With `demo_mode` on, send a booking confirmation to a customer address | The email reaches the signed-in visitor's own address only |
| 4.16 | F-25 language | Switch `app_language` to `fr` | Every screen of the admin menu shows no English phrase key or English text; emails use the French templates |

---

*Test Specification (Canonical) v2.0 — consolidated from test-specifications.md into
spec-testing.md. testing-standards.md's content is superseded by testing-methodology-standards.md
(Standards Library) and is not duplicated here.*
