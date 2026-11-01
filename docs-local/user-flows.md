---
title: "user-flows"
doc-id: "mb-b1292280"
updated: "2026-11-01"
---
# Mybizz CS — User Flows

**Authority:** Reference — defines the intended behaviour of every user flow in Mybizz CS. Each flow carries the feature number it belongs to (F-01 to F-34, as in `mb-3-cs-concept.md`). Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct.
**Related ADRs:** [[[default:adr-global/adr-form-architecture-and-state|Form Architecture and State Management]]](C:/dev/project-library-global/adr-global/adr-form-architecture-and-state.md) ADR, [[[default:adr-global/adr-webhook-architecture|Webhook Architecture]]](C:/dev/project-library-global/adr-global/adr-webhook-architecture.md) ADR

---

## Terms

- **Client** — a business that uses Mybizz CS. Every "client" in this document is a Mybizz client.
- **Customer** — a client's own customer: the person who books, orders, pays and reviews.
- **Entry points** — public pages have web addresses (`/services`, `/contact`, `/landing/:slug`). The admin area and the customer portal have no web addresses; their entry points are menu paths, written as `Menu → Item`. The admin menu is defined in `mb-3-cs-architecture-reference.md` section 9.

---

## Actors

| Actor | Description |
|---|---|
| New Client | A business registering for Mybizz CS |
| Owner | The client's Owner-role user |
| Manager | Manager-role user — operational management |
| Admin | Admin-role user — bookings and customers |
| Staff | Staff-role user — a provider; own calendar and bookings |
| Customer | The client's customer, signed in to the customer portal or booking on the public site |
| Visitor | An unauthenticated person browsing the client's public website |
| Campaign Visitor | A visitor arriving from a marketing campaign |
| Mybizz | The founder, working in the Mybizz management app (App 5) |

---

## Flow Index

| # | Flow | Feature | Actor | Entry point |
|---|---|---|---|---|
| UF-01 | Client registration and provisioning | F-03, F-34 | New Client, Mybizz | Mybizz website |
| UF-02 | Owner onboarding | F-03 | Owner | First sign-in |
| UF-03 | Staff invitation and sign-in | F-01, F-05 | Owner, Manager, Staff | Settings → Users and Permissions |
| UF-04 | Opening the Vault | F-02 | Owner | Vault |
| UF-05 | Services appointment booking | F-11 | Customer | `/services` |
| UF-06 | Booking at the customer's location | F-11 | Customer | `/services` |
| UF-07 | Group session booking | F-11 | Customer | `/services` |
| UF-08 | Booking that needs a room or equipment | F-11 | Customer | `/services` |
| UF-09 | Ordering delivered work | F-12 | Customer | `/services` |
| UF-10 | Buying and using a session package | F-13 | Customer | `/services`, customer portal |
| UF-11 | Admin daily operations | F-04, F-11 | Owner, Manager, Admin, Staff | Dashboard |
| UF-12 | Managing an order | F-12 | Owner, Admin, Staff | Sales & Operations → Orders |
| UF-13 | Time tracking and invoicing hours | F-14 | Staff, Owner | Finance & Reports → Time Entries |
| UF-14 | Invoices, payment terms and refunds | F-15 | Owner, Manager | Finance & Reports → Invoices |
| UF-15 | Payment webhooks | F-15, F-29 | System | Gateway webhook endpoints |
| UF-16 | Content management | F-06, F-21 | Owner, Admin | Content & Website → Pages, Blog |
| UF-17 | Homepage customisation | F-07, F-09 | Owner | Content & Website → Pages → Homepage |
| UF-18 | Landing page creation | F-08 | Owner | Content & Website → Landing Pages |
| UF-19 | Contact form submission | F-06, F-17, F-20 | Visitor | `/contact` |
| UF-20 | Lead capture via landing page | F-19 | Campaign Visitor | `/landing/:slug` |
| UF-21 | Contact management | F-17 | Owner, Manager, Admin | Customers & Marketing → Contacts |
| UF-22 | Custom segment builder | F-17 | Owner, Manager | Customers & Marketing → Segments |
| UF-23 | Email campaign setup | F-18 | Owner, Manager | Customers & Marketing → Campaigns |
| UF-24 | Email broadcast | F-18 | Owner, Manager | Customers & Marketing → Broadcasts |
| UF-25 | Marketing dashboard | F-04 | Owner, Manager | Finance & Reports → Analytics → Marketing |
| UF-26 | Tasks and automation rules | F-20 | Owner, Manager, Staff | Customers & Marketing → Tasks, Automations |
| UF-27 | Reviews and testimonials | F-23 | Customer, Owner | Review email, Customers & Marketing → Reviews |
| UF-28 | Clause Builder | F-22 | Owner | Settings → Legal Pages |
| UF-29 | Customer portal | F-24 | Customer | Customer sign-in |
| UF-30 | Using the AI helper | F-26 | Owner, Manager, Admin, Staff | AI button on a form |
| UF-31 | WhatsUp voice | F-27 | Owner, Manager, Admin, Staff | Home screen shortcut |
| UF-32 | Connecting Wave | F-28 | Owner | Settings → Integrations |
| UF-33 | Restoring or viewing data from R2 | F-29 | Owner, Mybizz | Settings → Data |
| UF-34 | Help: support tickets and feedback | F-32 | Owner, Manager, Admin, Staff, Mybizz | Help |
| UF-35 | Upgrade to a dedicated account | F-04, F-33 | Owner, Mybizz | Dashboard → Upgrade |
| UF-36 | Mybizz acts on an at-risk alert | F-34 | Mybizz | App 5 → Alerts |
| UF-37 | Release reaches every client | F-34 | Mybizz | App 1 release |
| UF-38 | Error recovery | F-11, F-15, F-01 | All users | Inline |

---

## UF-01 Client registration and provisioning (F-03, F-34)

**New Client:**
1. Registers on the Mybizz website: business name, owner name, owner email, the client's own domain.
2. Receives a confirmation email from Mybizz and confirms the email address.
3. Sets up the client's storage, guided step by step on the registration page with a short video: opens a free Cloudflare account on the client's own email address, adds a payment method, switches R2 on, and invites Mybizz's Cloudflare user as a member with the role Cloudflare R2 Admin. The account is the client's and stays the client's.

**System (App 5):**
4. Creates the client record and issues the client reference number.
5. Assigns a client hosting account with a free place (two clients per account).
6. Accepts the client's Cloudflare invitation, creates the client's R2 bucket, and creates a token that reaches that bucket only.

**Mybizz:**
7. Clones App 3 (`blank_client_template`) into the assigned hosting account as `[client-name]-cs`.
8. Sets the instance's `encryption_key` Anvil Secret, connects the client's domain, and issues the instance's Bearer token; App 5 stores the token and the client's Mybizz-side credentials, and writes the bucket's keys into the instance's Vault.
9. Creates the Owner account in the instance with `business_profile.onboarding_status = "in_progress"`.

**System:**
10. Sends the Owner a welcome email from Mybizz's own Brevo account with a link to set a password. No password is ever sent by email.

---

## UF-02 Owner onboarding (F-03)

Onboarding is a guided, resumable form, separate from Settings, with a progress indicator. Each step saves when completed. See [[[default:specifications-global/spec-onboarding-implementation-plan|Mybizz CS Onboarding System - Engineering Plan]]](C:/dev/project-library-global/specifications-global/spec-onboarding-implementation-plan.md).

1. **Owner sets a password** from the welcome link and signs in → the onboarding form opens with a welcome and the progress indicator.
2. **Business profile** — link to Settings → Business Profile tab.
3. **Brand palette** — link to Settings → Brand Palette tab, with live preview.
4. **System currency** — select from the list and confirm; locked once the first booking or invoice exists.
5. **Legal acknowledgement** — read the critical excerpts, tick, confirm; recorded with a timestamp in the instance and in App 5.
6. **Payment gateway** — link to Settings → Payment Gateways: choose Stripe or Paystack, optionally PayPal; secret keys go into the Vault; test mode on.
7. **Brevo email** — link to Settings → Email Configuration: connect the client's Brevo account; key into the Vault; verify the sender on the client's domain; send a test email.
8. **Onboarding complete** → the Owner is taken to the Dashboard.

- **Bidirectional navigation:** the Settings form shows "Return to Onboarding" while onboarding is in progress.
- **Resume:** an Owner who leaves mid-way returns to the next unfinished step.
- **After onboarding:** Settings is used for all ongoing configuration (tabbed, role-governed). Credentials are changed only in the Vault (UF-04).
- **Optional help:** the Owner can book the included 30-minute guided call, or request assisted setup.

---

## UF-03 Staff invitation and sign-in (F-01, F-05)

1. Owner or Manager opens Settings → Users and Permissions → Invite.
2. Enters name, email and role (Manager, Admin or Staff); for Staff, assigns the services the provider delivers and sets the provider's schedule.
3. The invitee receives an email with a link to set a password.
4. The invitee signs in and lands on the Dashboard, seeing only what the role allows.
5. Owner or Manager can change a role, deactivate or reactivate a user at any time; deactivated users cannot sign in.

---

## UF-04 Opening the Vault (F-02)

1. Owner opens Vault.
2. Enters the current code from an authenticator app (required every time; no remembered session).
3. Views, adds or replaces a credential (gateway keys, Brevo key, R2 keys, Wave credentials).
4. Closes the Vault. The Owner receives an email recording the access.

---

## UF-05 Services appointment booking (F-11)

**Entry:** `/services`

1. Browse service categories.
2. Select a service — description, duration, price (in the display currency if set), providers, delivery mode.
3. Select a provider (a specific provider, or "any available") — photo, specialisations, availability.
4. Select date and time — calendar showing only available slots.
5. Select meeting type — at the business, video call or phone call (UF-06 covers the customer's location). A video call link is generated automatically.
6. Enter customer details (name, email, phone). Signed-in customers are pre-filled.
7. Complete the intake form if the service requires one.
8. Add appointment notes or special requests.
9. Review and pay — the payment option set on the service:
   - **Pay in full now**
   - **Pay a deposit now**, balance invoiced
   - **Pay on the day** — no payment now; invoiced at the appointment
   - **Invoice with payment terms** — no payment now; an invoice is issued with the service's payment terms (for example, 30 days)
   - A customer with a session package can **use a package session** instead (UF-10).
10. Confirmation — immediate confirmation email with a calendar invite attached; reminders 24 hours and 1 hour before, each with the call link where relevant.

- **Post-confirmation:** offer a recurring appointment (weekly, every two weeks, monthly).
- **Status:** a booking paid now, or booked on pay on the day or invoice terms, is confirmed at once. A booking whose online payment has not yet succeeded is pending (UF-38).
- **Clash prevention:** the server checks the slot again at the moment of confirmation; if the slot was taken, the customer is shown the nearest free slots.
- **Performance:** the flow pre-loads data and batches related operations to keep server calls few ([[[default:adr-global/adr-form-architecture-and-state|Form Architecture and State Management]]](C:/dev/project-library-global/adr-global/adr-form-architecture-and-state.md) ADR). Date and money logic runs on the server only ([[[default:adr-global/adr-anvil-platform-constraints|Anvil Platform Constraints and Design Boundaries]]](C:/dev/project-library-global/adr-global/adr-anvil-platform-constraints.md) ADR).
- **System:** the booking creates or updates the customer's contact (F-17) and appears on the provider's calendar.

---

## UF-06 Booking at the customer's location (F-11)

As UF-05, with these differences:

- At step 5 the customer chooses **at my location** and enters the address.
- Available slots at step 4 allow the service's travel time before and after, against the provider's other bookings.
- The address appears on the booking, the confirmation and the provider's calendar entry.

---

## UF-07 Group session booking (F-11)

**Set-up (Owner or Manager):** Services → a group service → add a session: date, time, provider, place limit, location or call link.

**Customer:**
1. Selects the group service and sees the upcoming sessions with places left.
2. Selects a session and the number of places (one per person attending).
3. Enters details, pays by the service's payment option (UF-05 step 9), and is confirmed.
4. When a session is full, it shows as full and cannot be booked.

---

## UF-08 Booking that needs a room or equipment (F-11)

**Set-up (Owner or Manager):** Services → Resources → add a room or piece of equipment; on the service, mark the resource as required.

**Customer:** books as in UF-05. Only slots where both the provider and the required resource are free are offered. The resource is reserved with the booking and freed when the booking is cancelled.

---

## UF-09 Ordering delivered work (F-12)

**Entry:** `/services` → a service priced per deliverable

1. Customer reads the service and selects Order.
2. Enters details and the brief (text and optional attachments).
3. Pays by the service's payment option (in full, deposit, or invoice with payment terms).
4. Receives an order confirmation email. The order's status is **ordered**.

The client then manages the order (UF-12).

---

## UF-10 Buying and using a session package (F-13)

**Buying:**
1. Customer selects a package on the service page (for example, 10 sessions) and pays.
2. The package and its balance appear in the customer portal (My Packages) and on the customer's contact record.

**Using:**
3. When booking the package's service (UF-05), the customer chooses **use a package session** at step 9; one session is drawn from the balance.
4. Cancelling within the allowed window returns the session to the balance.
5. When the package expires, unused sessions lapse and the customer is told before expiry.

---

## UF-11 Admin daily operations (F-04, F-11)

**Navigation:** the admin menu in `mb-3-cs-architecture-reference.md` section 9.

**Morning routine:** Dashboard — today's metrics, alerts and activity feed → process pending items → check today's calendar. (The spoken morning briefing, UF-31, gives the same picture by voice.)

**Using the calendar:** Sales & Operations → Bookings → Calendar. Month, week or day view; Today, previous and next to move through time; filter by provider (Owner, Manager, Admin) or by room or equipment. Staff see only their own bookings. Clicking a booking opens it; clicking an empty slot starts a new booking with the provider, date and time filled in. The calendar refreshes itself every minute. Build detail: `calendar-feature-spec.md`.

**Processing a booking:** open the booking → check details and payment status → confirm, reschedule or cancel. Confirming or rescheduling sends the customer an updated confirmation with a calendar invite. Cancelling frees the slot and any resource and returns a package session where the window allows. Bookings are cancelled, never deleted.

**Completing an appointment:** mark the booking completed → the invoice is generated (or the deposit balance invoiced) → the review request is sent (UF-27).

**No-show:** mark the booking no-show → the booking closes and the no-show is recorded on the customer's contact; any pay-on-the-day amount is not invoiced unless the client invoices a no-show fee manually.

---

## UF-12 Managing an order (F-12)

**Entry:** Sales & Operations → Orders

1. Open the order (status **ordered**) and review the brief.
2. Set it to **in progress** → the customer is emailed.
3. Deliver: attach the files and a message, set it to **delivered** → the customer is emailed with the delivery.
4. Any balance due is invoiced (UF-14).

---

## UF-13 Time tracking and invoicing hours (F-14)

**Entry:** Finance & Reports → Time Entries

**Logging time (Staff):**
1. Start the timer on a customer and service, or add an entry by hand (start, end, service, customer, description).
2. Mark the entry billable or not; the hourly rate comes from the service and can be changed on the entry.
3. Stop the timer; duration and total are calculated.

**Invoicing hours (Owner or Manager):**
4. Filter billable, uninvoiced entries by customer.
5. Select entries → Create Invoice → the entries become invoice lines and are marked invoiced in the same step, so they cannot be invoiced twice.

**Reports:** hours and value per provider, per customer and per service.

---

## UF-14 Invoices, payment terms and refunds (F-15)

**Entry:** Finance & Reports → Invoices

**Creating an invoice manually:**
1. New Invoice → select the customer → add lines (description, quantity, unit price), or pull in time entries, an order balance or a package.
2. Set the payment terms (for example, due on receipt, 7 days, 30 days); the due date is calculated from the issue date. The default terms are set in Settings → Invoicing.
3. Tax is added automatically at the client's tax rate from Settings → Invoicing, with the client's tax label and registration number; with a rate of 0 there is no tax line.
4. Save as draft, or send → PDF generated and emailed to the customer with a payment link.

**Lifecycle:** draft → sent → paid. An unpaid invoice past its due date becomes **overdue** (set by a nightly task) and the customer receives a reminder. Cancelling an invoice voids it.

**Recording payment:** online payments arrive through webhooks (UF-15); payments received another way (cash, bank transfer) are recorded manually → the invoice is marked paid and a receipt is emailed.

**Refunds:** open a paid invoice → Refund (full or part) → the refund is processed through the original gateway, or recorded as a manual refund → the customer is emailed and the refund is sent to Wave if connected (F-28).

---

## UF-15 Payment webhooks (F-15, F-29)

1. Stripe, Paystack or PayPal sends a webhook to the client instance.
2. The handler verifies the webhook, returns HTTP 200 immediately, and defers the work to a background task ([[[default:adr-global/adr-webhook-architecture|Webhook Architecture]]](C:/dev/project-library-global/adr-global/adr-webhook-architecture.md) ADR).
3. The background task applies the result (booking confirmed, invoice paid, refund recorded) and writes the webhook payload to the client's R2 bucket. The data tables keep only the resulting state.

See [[[default:adr-global/adr-real-time-and-background-tasks|Real-Time Updates and Background Task Architecture]]](C:/dev/project-library-global/adr-global/adr-real-time-and-background-tasks.md) ADR for polling intervals and [[[default:adr-global/adr-pdf-invoice-generation|PDF Invoice Generation]]](C:/dev/project-library-global/adr-global/adr-pdf-invoice-generation.md) ADR for invoice generation.

---

## UF-16 Content management (F-06, F-21)

**Updating website pages:** Content & Website → Pages → select a page → edit text and images → Preview → Publish.

**Publishing a blog post:** Content & Website → Blog → New Post → write in the rich text editor → choose the post layout (classic centred, two-column with sidebar, full-width immersive) → set excerpt, category, tags, featured image, video and author → SEO settings (title, description, slug, keywords) → Publish Now, Save Draft, or Schedule (date and time).

**Comments:** Blog → Comments → approve, mark as spam or delete. Comments can be switched off in Settings.

---

## UF-17 Homepage customisation (F-07, F-09)

**Entry:** Content & Website → Pages → Homepage

1. Select a homepage layout — Classic, Services, Booking, Minimalist, or a further layout.
2. Configure the hero — headline, subheadline, hero image, call to action.
3. Configure features — up to 5 items (icon, title, description up to 100 characters).
4. Configure the services showcase — on or off, number shown (1–6), display style.
5. Configure testimonials — approved reviews (UF-27) are offered automatically; the Owner can also type in testimonials (name, text, rating, photo), up to 10 in total, and choose which appear.
6. Configure the video section — a video address (YouTube, Vimeo, Dailymotion or direct); the section hides if empty.
7. Configure the final call to action — headline, subheadline, button text and link.
8. Preview → Save → live immediately.

Changes auto-save every 30 seconds. The configuration is stored in the `config` table with `key = 'home_config'`.

---

## UF-18 Landing page creation (F-08)

**Entry:** Content & Website → Landing Pages

1. Choose a template — Lead Capture, Event Registration or Video Sales Letter.
2. Basic settings — title, address slug (generated, unique), status (draft or published).
3. Configure the template's content.
4. Preview on the blank layout (no header or footer).
5. Publish → validated, saved to `landing_pages`, shareable address shown.
6. Share — copy the address, send a test email, share to social media, embed, add to an email campaign.

**Analytics:** views, conversions, conversion rate, referrer, device.

---

## UF-19 Contact form submission (F-06, F-17, F-20)

**Entry:** `/contact`

**Fields:** name (required), email (required), phone (optional), message (required, up to 1,000 characters).

**On submission:**
1. Saves the submission to `contact_submissions`.
2. Creates the contact, or updates the existing contact with the same email, and adds the enquiry to the contact's timeline.
3. Creates a follow-up task for the business.
4. Notifies the Owner, and sends the visitor an auto-reply if configured.
5. The form resets and shows a thank-you message.

This is how a customer contacts the business with a question or a problem; there is no customer ticket system.

---

## UF-20 Lead capture via landing page (F-19)

**Entry:** `/landing/:slug` — blank layout

1. Visitor arrives from a campaign → page view recorded with the source.
2. Views the content — headline, benefits, image or video, capture form.
3. Submits the form.
4. System: validates the email; creates the `leads` and `contacts` records together and sets `leads.converted_to_contact_id` at once; increments the conversion count; enrols the contact in the welcome sequence through Brevo Campaigns.
5. A thank-you message is shown.
6. The lead receives the welcome email with content, a call to action and an unsubscribe link.

**Owner:** receives a "new lead captured" notification. The lead appears in the CRM with lead status `new`.

**Funnel:** a blog post's call to action links to the landing page, and the landing page's call to action links into the booking flow (UF-05), with the source carried through each step.

---

## UF-21 Contact management (F-17)

**Entry:** Customers & Marketing → Contacts

**Overview:** total contacts, status breakdown (Lead, Customer, Inactive), pre-built segments, search, contact grid.

**Actions:** open a segment; search; open a contact (statistics, activity timeline, active campaigns, packages, open invoices, quick actions); add a contact; bulk actions (add tag, enrol in a campaign, CSV export); CSV import.

**Automatic creation:** contacts are created or updated automatically from bookings, orders, enquiry forms and landing page captures.

---

## UF-22 Custom segment builder (F-17)

**Entry:** Customers & Marketing → Segments → New Segment

1. Add rules — tags, status, total spend, number of bookings, last booking date, lead source.
2. Combine rules with "all of" or "any of".
3. Preview the matching contacts and their count.
4. Name and save the segment. Saved segments update automatically and can be used in campaigns and broadcasts.

---

## UF-23 Email campaign setup (F-18)

**Entry:** Customers & Marketing → Campaigns

1. View the campaigns dashboard — active campaigns with enrolled count and performance.
2. New Campaign → campaign editor.
3. Choose a campaign type — New Client Welcome, Appointment Follow-Up, Re-engagement, Referral Request, or blank.
4. Review the email sequence — preview each email and its delay.
5. Set the trigger — event type, segment and delay before the first email.
6. Activate — confirm settings → Activate Campaign.
7. The campaign runs automatically — contacts are enrolled, emails are sent on schedule, opens and clicks are tracked through Brevo webhooks (payloads written to R2).

**Background process:** an hourly background task processes active enrolments.

---

## UF-24 Email broadcast (F-18)

**Entry:** Customers & Marketing → Broadcasts

1. New Broadcast → choose recipients (a segment or all contacts who have not unsubscribed).
2. Write the email or start from a template; preview; send a test.
3. Send now or schedule.
4. View results — sent, opens, clicks, unsubscribes.

---

## UF-25 Marketing dashboard (F-04)

**Entry:** Finance & Reports → Analytics → Marketing

**Metrics:** total contacts and new this month; revenue (current against previous); email performance (sent, open rate, click rate, conversions); top lead sources by revenue; return on investment per campaign and landing page; actionable alerts.

**Alert logic:** a nightly background task at 02:00 calculates the metrics and generates alerts.

**Actions:** drill into any metric; enrol contacts from an alert in one click; view detailed reports; change the period; export to PDF or CSV; schedule a report by email.

---

## UF-26 Tasks and automation rules (F-20)

**Tasks — Entry:** Customers & Marketing → Tasks
1. View tasks by due date, filtered by owner and status.
2. Add a task linked to a contact (title, due date, notes) or open an automatic task.
3. Complete a task — the completion is timestamped.

**Automation rules — Entry:** Customers & Marketing → Automations
1. New Automation → choose the trigger event (for example, booking completed, enquiry received, invoice overdue, no booking for 90 days).
2. Add conditions (for example, service, segment, tag).
3. Add actions — create a task, send an email, enrol in a sequence, add a tag.
4. Switch the automation on. A background task evaluates automations and records each run.

---

## UF-27 Reviews and testimonials (F-23)

**Customer:**
1. After an appointment is completed, the customer receives a review request email (and an automatic task is created for the business).
2. Opens the link, gives a star rating (1–5) and a comment, and submits.
3. The review also appears in the customer portal under My Reviews.

**Owner — Entry:** Customers & Marketing → Reviews
4. New reviews arrive as **awaiting approval** and are not public.
5. Approve or decline each review. Approved reviews become available as homepage testimonials (UF-17).
6. Typed-in testimonials are added on the Homepage page (UF-17) and are marked as entered by the business.

---

## UF-28 Clause Builder (F-22)

**Entry:** Settings → Legal Pages

1. Choose the document — Privacy Policy or Terms and Conditions.
2. The pre-written clauses load, grouped by category.
3. Edit clauses, add custom clauses, reorder by dragging, switch clauses on or off.
4. Save and publish → the public page updates immediately.

The client is responsible for the final wording being right for the client's business and jurisdiction.

---

## UF-29 Customer portal (F-24)

**Entry:** customer sign-in on the client's website

1. Customer signs in (or sets a password from the link in the first booking confirmation).
2. My Dashboard — upcoming bookings, package balances, invoices due.
3. My Bookings — view, reschedule or cancel within the allowed window; book again.
4. My Packages — balance and expiry.
5. My Invoices — view, download PDF, pay online.
6. My Reviews — reviews written and pending review requests.
7. Account — profile, password, email preferences.

Every view shows only the signed-in customer's own records.

---

## UF-30 Using the AI helper (F-26)

1. On a form where AI helps (for example, Business Profile, a service, a blog post, an invoice), the user presses the AI button.
2. The floating AI window opens over the page with the assist that fits the page already selected; the user can switch assists.
3. The user fills in the assist's fields — typing, or speaking (UF-31).
4. Generate → the result appears in the window.
5. Copy, edit, then Insert, Replace or Append into the form, or Cancel.
6. If the AI is unavailable, the window says so and the form keeps working normally.

Assists 9 (client sentiment summary) and 10 (booking and availability insights) appear once the client's data passes the threshold in `mybizz-ai-implementation-spec.md`.

---

## UF-31 WhatsUp voice (F-27)

**Entry:** the WhatsUp shortcut on the phone's home screen (added once from the app's "Add WhatsUp to home screen" option).

**Asking or creating:**
1. Tap the microphone and speak.
2. The speech is transcribed and understood.
3. Mybizz CS acts:
   - **Voice to an assist** — the spoken request fills the assist and the result is shown (UF-30).
   - **Task** — "Create a task — call Priya on Thursday" → the task is created and confirmed aloud.
   - **Question** — "What is my schedule for tomorrow?" → the answer is shown and spoken.
   - **Navigation** — "Show me unpaid invoices" → the screen opens.
4. **Follow-up** — the user asks a further question in the same session; the answer takes the last answer into account. The session ends on close, after a pause, or on navigating away.
5. If the data cannot be retrieved, Mybizz CS says so clearly.

**Morning briefing:**
- With the briefing switched on, opening WhatsUp (or the app) in the morning plays a 20–30 second spoken summary: today's bookings, outstanding invoices, overdue tasks, new leads, flagged items.
- The briefing is switched on or off per user in Account settings.

---

## UF-32 Connecting Wave (F-28)

**Entry:** Settings → Integrations → Wave

1. Owner selects Connect Wave and signs in to Wave to authorise the connection.
2. The Wave credentials are stored in the Vault.
3. From then on, three events are sent to Wave automatically: invoice paid, new customer, refund issued.
4. The Integrations tab shows the connection status and the last event sent. Disconnect stops all events.

---

## UF-33 Restoring or viewing data from R2 (F-29)

**Viewing history (Owner) — Entry:** Settings → Data
1. Choose what to view — audit history older than 30 days, webhook records, past backups.
2. Choose the period; the records are read from the client's R2 bucket and shown.

**Restoring (Mybizz, at the Owner's request through Help):**
1. Mybizz selects the backup and the tables to restore.
2. The data is restored into the instance from the client's R2 bucket.
3. The Owner is told when the restore is complete.

**Export:** Settings → Data → Export all data as CSV, at any time.

---

## UF-34 Help: support tickets and feedback (F-32)

**Client user — Entry:** Help
1. Choose the type — **problem** or **feedback**.
2. Enter a subject and a message; optionally attach a screenshot.
3. Send → the message goes to App 5 and a reference number is shown.
4. Replies appear in the Help view, and the user is emailed when Mybizz replies.

**Mybizz — Entry:** App 5 → Tickets
1. Open the ticket or feedback and reply.
2. A problem that stops a client's business is acknowledged within 4 business hours; every other ticket is answered within 24 hours on business days.
3. Close the ticket when resolved; feedback is tagged for product planning.

---

## UF-35 Upgrade to a dedicated account (F-04, F-33)

**Owner — Entry:** Dashboard → Upgrade
1. The upgrade view shows the client's current usage and the limits of a dedicated Business account, and the new monthly price.
2. Owner selects Request Upgrade and confirms.

**Mybizz — Entry:** App 5 → Upgrades
3. Schedules the move and tells the Owner the time.
4. Clones the instance to a new dedicated hosting account, restores the data, moves the domain, and retires the shared instance.
5. Confirms to the Owner; billing changes from the next invoice.

---

## UF-36 Mybizz acts on an at-risk alert (F-34)

**Entry:** App 5 → Alerts

1. App 5 raises an alert when a client has no staff sign-in for 14 days, no bookings for 30 days, or a support ticket unanswered for 3 days.
2. Mybizz opens the alert and sees the client's usage history.
3. Mybizz contacts the Owner (email or call) and records the action and outcome on the client record.
4. The alert closes when the condition clears.

---

## UF-37 Release reaches every client (F-34)

1. A change is built and tested in App 1 and tagged as a release.
2. The developer pushes the release into App 2 (`master_template`) (Step 42).
3. Every client instance runs the new release immediately through its dependency.
4. If the release changes the schema, Mybizz applies the schema change to each client instance and records it in `schema-migration-log.md`.

---

## UF-38 Error recovery (F-11, F-15, F-01)

**Payment failure:**
1. Show the error.
2. Allow a retry with the same card.
3. If it still fails, offer another payment method.
4. Hold the booking for 15 minutes as **payment pending** and email the customer a payment link.
5. Release the slot, and any resource, if the hold expires unpaid.

**Session timeout:** after 30 minutes without activity the user is asked to sign in again; the user returns to where they were.

**AI or voice unavailable:** a clear message is shown or spoken; the form keeps working (UF-30, UF-31).

**Integration unavailable (Brevo, Wave, R2):** the action is queued and retried in the background; the Owner is notified if it keeps failing.

---

## Flow Design Principles

- **Progressive disclosure** — show only what each step needs
- **Clear calls to action with progress indication**
- **Real-time input validation**
- **A recovery path for every error** — no dead ends
- **Automatic progress saving**
- **Email confirmation for every transaction**
- **Fewest possible server calls** — pre-load, batch, submit once at the end of a multi-step flow
