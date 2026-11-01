---
title: "mb-3-cs-concept"
doc-id: "mb-f23e1822"
updated: "2026-11-01"
---
# Mybizz CS — Concept

**mybizz.live**

---

## Purpose of this document

This document is the concept of Mybizz CS: what the business is, who it serves, what the product does, how it is positioned, how it is priced and how it goes to market. Every statement in it is a decision.

Every feature of Mybizz CS is named and numbered in section 7. The client-facing list of every feature, by category, is `mb-3-cs-feature-list.md`. The same feature number and name are used in `mb-3-cs-architecture-explainer.md`, which explains how each feature is built, and in `mb-3-cs-architecture-reference.md`, which specifies each feature for the build. Where a technical fact is stated, `mb-3-cs-architecture-reference.md` is the standard.

This document serves three purposes:
- Strategic guidance for AI-assisted planning and development
- Foundation for all web content, marketing and business development
- Reference for investor or partner conversations

---

## 1. What Mybizz CS Is

Mybizz CS is a complete, standalone online business platform for consulting businesses and for service businesses, delivered as a subscription SaaS product. Mybizz CS is one of the Mybizz businesses.

Every client receives their own fully operational, isolated application instance. The instance includes, among many other features, a website, booking system, payment processing, CRM with a sales pipeline, a team email inbox, email marketing, invoicing, built-in accounts, time tracking, a blog, analytics, reviews, AI assistance and voice access — in a single platform, at a single monthly price, with nothing to integrate. The costs a client pays outside the monthly price are listed in section 9.5.

Mybizz CS is not a booking app, not a CRM and not a website builder. It is all of them at once: a complete, private, owned operating system for a consulting or service business, at one monthly price, with no add-ons, no commissions and no data sharing. Mybizz CS is therefore compared with competitors as a different category of product — an integrated operating system against single-purpose tools — and never feature by feature against a booking app.

**Web addresses:** the Mybizz company website is at mybizz.live. Each client's application instance runs on the client's own domain.
**Platform:** Anvil.works (full-stack Python, hosted)
**UI theme:** Material Design 3
**Capacity:** a hard limit of 100 clients for Mybizz CS. Each Mybizz business has its own hard limit of 100 clients.

---

## 2. The Problem Mybizz CS Solves

Setting up an online business presence is unnecessarily complex, expensive and fragmented. A consulting or service business trying to operate professionally online faces:

- A website builder and hosting — one subscription, one learning curve
- A booking system — another subscription, another integration
- A payment gateway — another signup, another configuration
- A CRM — another tool, another import/export
- An email marketing platform — another account, another connection
- An invoicing tool — yet another service

Beyond the subscriptions sits the technical burden most business owners underestimate: hosting, domain and DNS records, security certificates, protection from attacks, a site that works on every screen size, a database for bookings and contacts, backups, and software updates.

Each of these is a web of decisions, technical requirements and monthly fees. Any one of them can take weeks to set up correctly. Together, they cost one person well over $100 a month, and a team several hundred, before anyone pays to set them up — and they still do not talk to each other. Most small businesses never get all of it working. They end up with a partial setup — perhaps a website and a booking link — and manage everything else by hand. The business owner becomes a part-time IT administrator instead of doing the work they are good at.

The problem has five parts that compound each other:

- **Money:** five to seven separate subscriptions, each with its own fee, before any transaction fees.
- **Integration:** the tools were not designed to work together. A booking in one does not create a contact in another; a payment in one does not create an invoice in another. The business owner becomes the human link between them.
- **Technical:** each tool has its own interface, learning curve and support. A business owner who is excellent at their craft has to become competent at five software products.
- **Data:** client information lives in three or four systems at once, none of them complete or authoritative.
- **Ownership:** on marketplace platforms, the business's most valuable asset — its customer relationships — is effectively leased from the platform.

Mybizz CS solves all five at once.

The result is what Mybizz CS was built to eliminate: bookings that take far too long, several browser tabs open at once, client data typed into more than one system, and no reliable view of how the business is actually performing.

Mybizz CS connects the client's payment accounts into one system, and provides the client's email, email marketing and accounts itself. The client signs up once with a payment provider and never has to integrate anything.

**Mybizz CS is one setup. One platform. One price.**

---

## 3. The Founder Story

Mybizz was not built from a whiteboard. It was built from personal experience.

The founder spent years attempting to establish an online business and encountered exactly the problem described above — not as an observer, but as the person sitting in front of the screen, overwhelmed, wasting money, and never quite getting to a complete, working system. Every service provider sold a piece of the puzzle. Nobody sold the complete picture.

After speaking with other small business owners, the pattern was consistent. The challenge was not ambition or effort — it was the sheer fragmentation of the tools required to operate a modern business online. Most people eventually gave up on parts of it, paid someone else to figure it out, or settled for less than they needed.

Mybizz CS is the product the founder needed and could not find. That origin is the product's most credible differentiator: it was designed by someone who lived the problem, not by a product team optimising metrics.

---

## 4. Target Market

### 4.1 Who Mybizz CS serves

Mybizz CS serves two kinds of business:

- **Consulting businesses** — businesses that sell expertise and advice.
- **Service businesses** — businesses that sell a service performed for the customer.

Either kind may work on the ground, in person, or in the cloud, online — or both.

**Consulting businesses include but are not limited to:**
- Management consultants, strategy advisors, business advisors
- Financial consultants, accountants, attorneys, legal consultants
- IT consultants, marketing consultants, HR consultants, branding consultants
- Life coaches, business coaches, executive coaches, career coaches, health and wellness coaches, mentors
- Psychologists, nutritionists

**Service businesses include but are not limited to:**
- Health and wellness: physiotherapists, chiropractors, personal trainers, yoga instructors, massage therapists, occupational therapists, speech therapists
- Beauty and lifestyle: salons, spas, nail technicians, aestheticians, hair stylists, barbers, tattoo artists, makeup artists
- Creative and technical: photographers, videographers, web designers, graphic designers, copywriters, content creators, UX designers
- Trades and skilled services: plumbers, electricians, carpenters, painters, gardeners, tutors, driving instructors, caterers, event planners
- Freelance and independent providers: the kind of service offered on Fiverr or Upwork, run under the provider's own brand instead of inside a marketplace
- Cloud-based services: online tutoring, virtual assistance, remote design, website and IT support

**The common thread:** they sell time, expertise or deliverables, they book clients, they need to get paid, and they need a professional online presence.

### 4.2 Business size

Mybizz CS is built for:
- Teams of every size, with unlimited staff at no extra cost: owners, managers, administrators, providers and salespeople
- Solo practitioners, who receive the full platform at the same price

Each team member gets their own sign-in, access level and email address; each provider gets their own calendar and services; each salesperson works their own deals. A team grows without the price changing.

### 4.3 How services are delivered

Mybizz CS supports every way a consulting or service business delivers its work. One business may offer any mix of these:

1. **At the business's premises** — a salon, a practice, a clinic, an office, a studio. The customer books a time slot with a provider and comes in. Where the business needs a room or a piece of equipment as well as a provider, the room or equipment is booked with the appointment.
2. **At the customer's location** — a plumber, a mobile therapist, a tutor or consultant who visits. The customer books a time slot and gives an address; Mybizz CS allows travel time between appointments.
3. **Online, live** — consulting, coaching, therapy or training by video or phone call. The customer books a time slot, and Mybizz CS sends the call link with the confirmation.
4. **Online, as delivered work** — a logo, a report, a website, a plan. There is no appointment. The customer orders and pays for a fixed-price deliverable, and Mybizz CS tracks the order from ordered, to in progress, to delivered.
5. **To groups** — a class, a workshop, a training session. Several customers book the same session, up to a set number of places.

Work billed by the hour is recorded with time tracking (F-14) and invoiced from the hours worked.

### 4.4 Geography

Mybizz CS is global from launch:
- Stripe for global payment processing, and PayPal for customers who prefer it
- Paystack for South African and African clients (Stripe does not accept South African businesses directly)
- Two currencies: a system currency for all accounting, and an optional display currency per service
- UTC storage with display in each user's time zone
- Six app languages: English, French, Portuguese, Spanish, German and Italian, chosen by each client; English at launch, the other languages added one at a time

South Africa is the founder's home market and the primary focus for direct, personal client relationships. International clients are acquired through content and digital marketing.

### 4.5 The needs Mybizz CS meets

Ranked by how acutely the target market feels each need:

| # | Need | How acute | Mybizz CS answer |
|---|---|---|---|
| 1 | Online booking without technical setup | Very high | F-11 Bookings and scheduling |
| 2 | Get paid professionally | Very high | F-15 Invoicing and payments |
| 3 | A professional website without a web designer | High | F-06 Public website, F-07 Homepage templates |
| 4 | Client records without a spreadsheet | High | F-17 CRM and contacts |
| 5 | Professional invoices | High | F-15 Invoicing and payments |
| 6 | Email addresses, an inbox and marketing email without a separate tool | Medium-high | F-18 Email |
| 7 | Understand how the business is performing | Medium | F-04 Dashboard and analytics, F-28 Accounts |
| 8 | Follow up every sale | Medium | F-35 Sales pipeline, companies and quotes |
| 9 | Blog content for search engines and trust, shared to social media | Medium | F-21 Blog and content |
| 10 | Landing pages for campaigns | Medium | F-08 Landing pages |
| 11 | Control who sees what | Medium | F-01 Accounts, roles and access |
| 12 | Protect credentials | Medium | F-02 The Vault |
| 13 | Legal pages | Medium-low | F-22 Clause Builder |
| 14 | AI help with content and proposals | Emerging | F-26 AI assistance |
| 15 | Running the business by voice from a phone | Emerging | F-27 WhatsUp voice |

The first five are felt by every service business owner. The rest make Mybizz CS more complete than anything else at its price.

### 4.6 Why now

- **Digital service businesses are normal.** The share of service businesses operating online rose sharply after 2020 and has not fallen back.
- **AI is expected.** Small business owners are looking for AI-powered tools, and Mybizz CS includes AI and voice in the price.
- **Marketplace fatigue.** A growing group of service businesses want to own their customer relationships instead of paying commissions to a marketplace.
- **Africa's digital service economy is growing.** Professional services and wellness businesses in South Africa, Nigeria, Kenya and Ghana are moving online, and many global tools do not serve them because of payment gaps.

---

## 5. What Mybizz CS Is Not

Mybizz CS is not a marketplace.

Booksy, Vagaro and Mindbody are platforms where businesses compete for customers inside a shared discovery environment. Competitors appear on the business's listing. The customers are effectively platform customers — if the business leaves the platform, the customer relationship stays behind. New customer acquisition through a marketplace attracts commissions of up to 20–23.5% per transaction.

Mybizz CS does not list a business alongside competitors. It does not take a commission on bookings. It does not own the business's customer relationships. Every customer record, booking history, contact detail and transaction belongs exclusively to the client — isolated in the client's own application instance, inaccessible to anyone else. Mybizz never collects the client's customers' details for its own use.

---

## 6. Competitive Positioning

### 6.1 Where Mybizz CS sits

The market has three segments:

- **Marketplace platforms** (Booksy, Vagaro, Mindbody, Fresha): discovery first. The business appears among its competitors, pays commissions, competes for visibility and does not own its customer relationships.
- **Single-purpose tools** (Calendly, Acuity, Square Appointments, Wave, Mailchimp): each does one thing well; together they create the fragmentation problem.
- **Enterprise software** (Salesforce, HubSpot's higher tiers): built for companies with operations teams and large budgets; unsuited to a solo practitioner or small team.

Mybizz CS fills the gap between them: a complete, integrated operating system at a price a solo practitioner or small team can afford, with full data ownership and no marketplace.

### 6.2 The real cost comparison

Mybizz CS replaces a set of separate tools. The fair comparison is the full set of tools a business needs to match what Mybizz CS includes.

**A team of six, separate tools (list prices, 2026):** an owner, a manager, an administrator, one provider and two salespeople.

| Need | Typical tool | Per month |
|---|---|---|
| Website, hosting, security certificate, blog | Squarespace Core (annual billing) | $23 |
| Online booking with reminders, intake forms and payment at booking | Acuity Scheduling Standard (up to 6 calendars) | $34 |
| CRM, sales pipeline and email marketing | HubSpot Starter Customer Platform, 6 seats at $20 | $120 |
| Invoicing and time tracking | FreshBooks Lite, plus 5 team members at $11 | $78 |
| AI writing assistant | Jasper Pro, 1 seat | $69 |
| **Total** | | **$324** |

That total does not include a team email inbox, voice access, backups, legal pages, reviews, a customer portal, accounts, or the cost of having it all set up, and every further staff member adds more per-seat fees.

**All-in-one platforms, for the same team of six:**

| | HoneyBook Premium | GoHighLevel Starter | **Mybizz CS** |
|---|---|---|---|
| Price per month | $129, plus a website at $23 | $97, plus $97 for unlimited AI | **$150** |
| Same price at any team size | no — 10 people maximum | yes | **yes** |
| Payments through any gateway, no processor markup | no — 2.9% + $0.25 through HoneyBook | — | **yes** |
| Paystack built in, for Africa | — | — | **yes** |
| No usage billing (SMS, calls, AI) | — | no — billed per use | **yes** |
| Voice assistant and spoken morning briefing | no | no | **yes** |
| Backups in the client's own storage | no | no | **yes** |
| Leave at any time with all data and backups | — | — | **yes** |
| Month-to-month, no contract | — | — | **yes** |
| Support straight from the founder | no | no | **yes** |

**Booking platforms with a marketplace:**

| | Booksy | Vagaro | Mindbody | **Mybizz CS** |
|---|---|---|---|---|
| Base price | $29.99/mo | about $30/mo | $99–$699/mo | **$150/mo** |
| Per-staff fee | +$20/person | +$10/calendar | included | **included** |
| Processing fee | 2.49–2.69% | 2.2–3.5% | 3.5% | **gateway rate only** |
| Marketplace commission | up to $49.99/mo (Boost) | none | 20% new customer | **none** |
| Add-ons required | none listed | forms, SMS, storage | many | **none** |
| Website included | no | add-on | no | **yes** |
| CRM, team inbox, email marketing, invoicing, accounts, blog included | no | partly, with add-ons | partly | **yes** |
| AI assistance and voice included | no | no | no | **yes** |
| Hosting included | no | no | no | **yes** |
| Client owns data | no | no | no | **yes** |
| Competitors on the business's page | yes | yes | yes | **no** |

Mybizz CS costs less than half of the separate tools it replaces for a team of six, and the gap grows with every staff member, because Mybizz CS charges no per-staff fee.

### 6.3 The ownership argument

On any marketplace platform, the business's customers are the platform's customers. The platform's algorithm decides the business's visibility. Competitors appear next to the business's listing. If a business builds its customer base through Booksy and then leaves, the customers remain in Booksy's ecosystem.

On Mybizz CS, the client's customer database is structurally isolated. It exists only in the client's application instance. No other business can access it. No algorithm affects the client's visibility. The client's customers are the client's customers on day one and remain the client's permanently.

Every client's data is also backed up to the client's own Cloudflare R2 bucket, and can be exported as CSV at any time. There is no contract: billing is month to month, and the client can take the client's data, emails and backups and leave at any time.

### 6.4 The simplicity advantage

Marketplace platforms do one thing: booking and discovery. Businesses using them still set up and pay for a website, a blog, landing pages, a CRM, email addresses and an inbox, email marketing, invoicing and accounts separately. Mybizz CS replaces all of those tools — all in one platform, all working together, all included in the price.

### 6.5 The Africa advantage

Stripe does not accept South African businesses directly, and Booksy and Vagaro were not built for the African market. Mybizz CS integrates Paystack natively — customers pay in Rands and the money lands in the business's bank account, with no workarounds.

---

## 7. The Platform — Feature Set

Mybizz CS is built with every feature below. Each feature carries the same number in the architecture explainer and the architecture reference.

### The client's day-to-day business

**F-01 Accounts, roles and access**
- Email and password sign-in, password reset, rate-limited sign-in and account lockout
- Five roles: Owner, Manager, Admin, Staff, Customer — each sees only what the role allows, enforced on the server
- Full audit trail of every significant action

**F-02 The Vault**
- Encrypted store for every credential (payment keys, email service keys, storage keys) — owner-only
- Every view requires a code from an authenticator app, with no "remember me", and the owner is emailed on every access

**F-03 Registration, onboarding and setup**
- Self-registration: registration creates the client's reference number, records the client in the Mybizz management app and connects the client's Cloudflare R2 storage
- An onboarding wizard with a progress indicator and a help video on every step, in this order: app language, business profile, brand palette, system currency, legal acknowledgement, payment gateway, connect the client's domain (website and email), import existing contacts
- The import brings in contacts from HubSpot, Mailchimp, Acuity or any CSV file, with ready-made column matching
- AI writes the first website text from the business profile
- Setup can be left and resumed without losing progress; onboarding and Settings link to each other
- Assisted setup, where Mybizz configures the instance, costs $200 once-off

**F-04 Dashboard and analytics**
- Revenue, bookings and customer totals with period comparison, metric cards, trend charts and an activity feed
- Advanced analytics: revenue by service and by provider, marketing return on investment, PDF and CSV export, scheduled reports by email
- While sample data is present, a banner on every screen says so, with the Owner's **Clear sample data** button (section 8)

**F-05 Settings Hub**
- One tabbed control panel: business profile (including the app language), brand palette, system currency (view), display currency, legal acknowledgement (view), payment gateways with test and live modes, invoicing defaults, users and permissions, email (addresses, desks and signatures), domain, theme, feature switches, legal pages, data
- Each role sees only the tabs its role allows

### The client's public presence

**F-06 Public website**
- Home, About, Contact (enquiry form into the CRM, with an optional Google Maps location), Services listing and detail, Blog, Privacy Policy, Terms and Conditions
- A consistent footer with credentials, social links, contact details, policy links and optional map
- Mobile-responsive, search-engine optimised, clean addresses
- Owners and Managers edit website pages on the page itself: an Edit button makes every text, image and video editable where it sits, AI writes or rewrites any text on request, and Save publishes the changes immediately
- Runs on the client's own domain

**F-07 Homepage templates**
- Four layouts — Classic, Services, Booking, Minimalist — plus further layouts, all built from the same sections
- Optional sections appear only when configured (video, services carousel)

**F-08 Landing pages**
- Three templates — Lead Capture, Event Registration, Video Sales Letter — with no site header or footer
- New landing pages are set up in the landing page editor, where the template and tracking are chosen; Owners and Managers edit a landing page's text, images and video on the page itself, with AI on request
- Views and conversions tracked per landing page

**F-09 Visual and media presentation**
- Parallax hero, auto-rotating carousels with manual controls
- Video on the homepage, on service pages and in blog posts, from YouTube, Vimeo, Dailymotion or a direct video address; video sections hide when no video is set

### Services, bookings and billing

**F-10 Service catalogue**
- Time-based pricing (a 60-minute session at R450) and deliverable pricing (a logo design at R3,500)
- Per service: name, description, category, pricing model, price, duration, delivery mode, provider assignment, images, video, meeting types, intake form, active status

**F-11 Bookings and scheduling**
- One continuous booking flow: service → provider → date and time → meeting type → intake form → payment → confirmation
- Calendar in month, week and day views, colour-coded by status
- Availability: business hours, provider schedules, blocked dates and date exceptions
- Meeting types: at the business, at the customer's location (address captured, travel time allowed), video call (link generated and sent), phone call
- Group sessions with a set number of places; rooms and equipment booked with the appointment where needed
- Payment options, set per service: pay in full at booking, pay a deposit at booking, pay on the day, or receive an invoice with payment terms (for example, 30 days); a customer with a session package uses a package session
- Reminders 24 hours and 1 hour before, with the call link where relevant
- Recurring appointments (weekly, every two weeks, monthly), rescheduling, special requests, booking statuses (pending, confirmed, completed, no-show, cancelled); bookings are cancelled, never deleted
- Each provider has their own calendar, services and availability; revenue is tracked per provider

**F-12 Orders for delivered work**
- Fixed-price deliverables ordered and paid online, with no appointment
- Order status: ordered, in progress, delivered

**F-13 Session packages**
- Prepaid bundles of sessions (for example, 10 sessions paid up front)
- Sessions are drawn down as they are booked; the customer and the client see the remaining balance

**F-14 Time tracking**
- Providers log time worked for a customer: start and end, service, hourly rate, billable or not, description
- Billable, uninvoiced time is turned into invoice lines
- Hours reported per provider, per customer and per service

**F-15 Invoicing and payments**
- Automatic invoice on booking completion; manual invoices for any other work; invoices from time entries, orders, packages and accepted quotes
- Payment terms on every invoice (for example, due on receipt or 30 days), with a default set by the client; overdue invoices trigger a reminder to the customer
- Tax set by each client — rate, label (VAT, GST, Sales Tax) and registration number — with no tax by default
- Invoice lifecycle: draft, sent, paid, overdue, cancelled; automatic sequential numbering
- PDF invoices, email delivery, payment recording, refunds
- Stripe (global) or Paystack (Africa) as the card gateway, plus PayPal as an option; test and live modes; one payment service behind all gateways

**F-16 Dual currency**
- A system currency set at onboarding, used for all accounting and fixed once the first transaction exists
- An optional display currency per service, shown to customers while all accounting stays in the system currency

**F-28 Accounts**
- Built-in accounts: invoices, payments, refunds and expenses in one place, with a photo of every receipt
- Reports: profit and loss by month, outstanding invoices, tax collected and tax paid, and customer statements
- One-click export of customers, invoices, invoice lines, payments, refunds and expenses, in the import formats of Wave, Xero and QuickBooks, or for the client's accountant
- No accounting subscription needed

### Customers and marketing

**F-17 CRM and contacts**
- One contact record per person: Lead, Customer or Inactive
- Full activity timeline, including every email to and from the contact; name, email, phone, company, source, tags, notes, preferences, lifetime value, total bookings, average transaction value, last contact date
- Contacts created automatically from bookings, enquiries, landing page forms and emails
- Pre-built segments (New Leads, Active Customers, Inactive Customers, High Value) and a custom segment builder (tags, status, spend, booking history)
- CSV import and export

**F-18 Email**
- Unlimited email addresses on the client's own domain: personal (`john@abc.com`) and desk (`help@`, `marketing@`, `accounts@`)
- A Team Inbox inside Mybizz CS: each staff member sees their own address and the desks the staff member belongs to; Inbox, Drafts, Sent, Archive, Spam and Trash; reply, reply all and forward; sort, filter and search; star and mark as unread; act on many emails at once; conversations grouped; an AI-drafted reply on request; works in the phone browser
- Every email from a known contact appears on the contact's timeline; an unknown sender becomes a lead in one click
- Spam and virus filtering on every incoming email
- Confirmations, reminders, invoices, receipts, sign-in and password emails sent from the client's own domain
- Marketing email: sequences (New Client Welcome, Appointment Follow-Up, Re-engagement, Referral Request), broadcasts and newsletters, segment targeting, open and click tracking, campaign metrics, one-click unsubscribe, bounces and complaints blocked automatically
- Every email signed with DKIM; Mybizz sets up the client's domain automatically
- Emails older than 90 days kept in the client's own storage, searchable, and part of the client's data at exit
- No email account to set up and no email bill: every email travels through Amazon SES, paid by Mybizz (`team-inbox-spec.md`)

**F-19 Lead capture and marketing funnel**
- Lead capture forms that create the lead and the contact together
- Leads tracked from source to paying customer, with capture analytics
- Automatic enrolment of new leads into sequences
- Blog post → landing page → booking flow as one connected funnel, also from social media links

**F-20 Tasks and automation**
- Manual tasks linked to contacts, with due dates and notes
- Automatic tasks from business activity (follow up an enquiry that did not book, request a review after an appointment)
- Automation rules: when an event happens and conditions are met, Mybizz CS creates tasks, sends emails or enrols contacts in sequences
- Tasks created by voice (F-27)

**F-21 Blog and content**
- Rich text editor, three post layouts (classic centred, two-column with sidebar, full-width immersive), pullquotes, featured image and video
- SEO per post, statuses (draft, scheduled, published, archived), categories, view counts
- New posts are written in the blog editor; Owners and Managers fix published posts on the page itself with the Edit button
- Public listing in tiled grid or list-with-thumbnail view
- Comment moderation, switchable on or off
- A Share button on every published post sends the post to Facebook, LinkedIn, WhatsApp or Instagram in one tap, with an AI-written caption in the client's language and a full preview; readers share posts too; visits and new contacts from every share are counted per platform (`sm-syndication-spec.md`)

**F-22 Clause Builder**
- Privacy Policy and Terms and Conditions built from pre-written clauses: edit, add, group, reorder, switch on or off, publish
- The published page updates immediately; the client remains responsible for the final wording

**F-23 Reviews and testimonials**
- After an appointment, the customer is invited by email to leave a review: a star rating and a comment
- The client approves each review before it appears as a testimonial on the client's website
- The client can also type in testimonials the business already has

**F-24 Customer portal**
- Each customer of the client signs in to: My Dashboard, My Bookings, My Packages, My Invoices, My Reviews, Account
- Customers book, reschedule, pay invoices, accept quotes and update their profile themselves
- Customers contact the business through the contact form; there is no ticket system for customers

**F-35 Sales pipeline, companies and quotes**
- Deals pipeline: sales stages from lead to won or lost, with value, owner and expected close date
- Companies: a business customer with several contacts
- Quotes: a priced quote with lines and tax, sent by email, accepted by the customer online, and turned into an invoice on acceptance
- Each salesperson sees and works the deals assigned to the salesperson

### Help, AI and voice

**F-25 Ease of use, in-app help and language**
- Every workflow follows the natural order of the task
- Every feature has a plain-language tooltip that opens a short help video in a pop-up; tooltips are switchable on or off
- The client chooses the app's language: English, French, Portuguese, Spanish, German or Italian; screens, emails and help videos follow that choice; English at launch, the other languages added one at a time

**F-26 AI assistance**
- One AI helper on every relevant page: a button opens a floating window, so the user never leaves the page; the helper preselects the assist that fits the page
- Ten assists: bio/profile writer, service description generator, FAQ generator, proposal generator, blog post drafts, email response suggestions, invoice message generator, terms and conditions plain-language explainer, client sentiment summary, booking and availability insights
- AI also writes website text in edit mode (F-06, F-08, F-21) and the social media captions of every published post (F-21)
- Every result can be copied, edited and inserted into the form it belongs to
- Client sentiment summary and booking insights work from the client's first day on sample data, and on the client's real data once the sample data is cleared
- The core application never depends on the AI

**F-27 WhatsUp voice**
- Voice access to Mybizz CS from any phone browser, saved as a home screen shortcut — no app to install, no WhatsApp
- Speak to any AI assist instead of typing
- Create tasks by voice from anywhere
- Ask questions and hear the answers: schedule and bookings, payments and revenue, tasks, customers, and navigation ("Show me unpaid invoices")
- Spoken morning briefing (20–30 seconds): today's bookings, outstanding invoices, overdue tasks, new leads, flagged items — switchable on or off
- Follow-up questions in one voice session, each answered in the context of the last
- Voice form filling: the user speaks a complete record ("New booking — Sarah Johnson, Swedish massage, Friday 2pm") and Mybizz CS fills in the form for the user to confirm
- Business intelligence by voice: revenue, client and marketing questions and advice ("Which days of the week are my busiest?"), answered from the client's own figures
- A natural-sounding voice, used where the browser's own voice is not good enough
- Voice questions fail gracefully: if the data cannot be retrieved, Mybizz CS says so clearly

### Data, security and design

**F-29 Data, backups and history**
- Every client's backups go to the client's own Cloudflare R2 bucket: 7 daily and 4 weekly backups kept
- The bucket keeps each kind of record in its own folder: backups, history, the activity log, payment and email service messages, emails, attachments, receipts and exports
- Webhook responses (the messages Stripe, Paystack, PayPal and Amazon SES send back) go to the R2 bucket
- History is not kept in the app's data tables; history goes to the R2 bucket
- The last 30 days of the audit trail stay in the app; older entries go to the R2 bucket; emails older than 90 days and every attachment go to the R2 bucket
- The client can view history from the R2 bucket in Settings, and Mybizz restores data from the bucket on request
- All data can be exported as CSV at any time
- On leaving, the client receives a full export and the decrypted Vault in the client's own R2 bucket

**F-30 Security and compliance**
- POPIA, GDPR and PCI DSS alignment
- Server-side role enforcement, rate limiting, encrypted credentials, full audit trail
- Complete data isolation between clients, built into the structure of the platform

**F-31 Design system**
- Material Design 3 with refinements: softer shadows, tighter corners, stronger headings, subtle dividers, distinct surfaces, consistent hover and press states
- Two contexts from one design system: the management interface (clear, compact, fast) and the public website (warm, generous, trustworthy)
- Four brand palettes chosen at onboarding and changeable at any time: Professional Blue, Warm Coral, Calm Teal, Sophisticated Purple
- Dark mode on every screen, following the device's light or dark setting
- Typography: Roboto on the M3 type scale; fully responsive on every screen size

### Mybizz running the business

**F-32 Help, support tickets and feedback**
- A Help button in every client's app sends the client's message to Mybizz as a support ticket or as feedback
- Tickets and feedback are handled in the Mybizz management app; the founder is notified; response target within 24 hours on business days
- A problem that stops a client's business is acknowledged within 4 business hours, and the fix reaches every client through `master_template` as soon as it is proven

**F-33 Hosting**
- Each client runs on its own Mybizz-owned Anvil Business account, in its own app with its own database, on the client's own domain
- Each client's app has the full Business limits of its account: 1,000,000 data table rows; heavy jobs run as background tasks
- Improvements reach every client automatically, with nothing for the client to install

**F-34 Mybizz management app**
- Built and running before the first paying client
- Client registry and registration, provisioning of new client instances (the Anvil account, the client's R2 bucket, the client's Amazon SES identity and the client's DNS records), client reference numbers and credentials
- Usage monitoring per client, health monitoring, billing, update distribution
- At-risk alerts: Mybizz is alerted when a client has no staff sign-in for 14 days, no bookings for 30 days, or a support ticket unanswered for 3 days, so the founder can act before the client leaves
- Support tickets and feedback (F-32)

**F-36 Demo apps**
- `demo.mybizz.live`: a full working business loaded with sample data; `try.mybizz.live`: an empty Mybizz CS for exploring setup
- Both apps reset every night, so visitors can change or break anything
- Every email the demo apps send goes to the visitor's own email address; payments run in the gateways' test mode with test cards, so no real money moves

---

## 8. Sample Data

Every new client starts with three months of sample data for a business in the client's own trade, so the dashboard, reports, AI assists and voice questions show real-looking results from the first day. A banner on every screen says the client is viewing sample data. The Owner clears all of it with one button, **Clear sample data**, and everything the client created stays. Sample data never sends an email, takes a payment or appears in the client's accounts export. Specified in `mybizz-ai-implementation-spec.md` (Sample Data).

---

## 9. Pricing Strategy

### 9.1 Price

**$150 per client per month.** One price for every client. Unlimited staff are included. There is no commission on bookings or payments, and no add-on: AI, voice, email, accounts and hosting are all in the price.

### 9.2 Billing model

Billing is monthly, month to month, with no contract. Mybizz invoices each client at the start of each month. A client can leave at any time, taking the client's R2 bucket with every backup, email and export in it.

There is no free trial and no money-back period. A prospective client sees the whole product before paying in the two demo apps (F-36): `demo.mybizz.live`, a working business with sample data, and `try.mybizz.live`, an empty Mybizz CS for trying setup.

### 9.3 Hosting

- Mybizz owns and pays for the Anvil Business account that runs each client instance: one account per client.
- Each client runs in its own app with its own database, on the client's own domain, with the full limits of its account.
- Anvil's Hobby plan is not used, because Anvil licenses the Hobby plan for personal projects only.

### 9.4 Assisted setup

The onboarding wizard (F-03) takes a client from registration to a working business without help, with a help video on every step. A client who wants Mybizz to do the setup pays **$200 once-off** for assisted setup.

### 9.5 Costs outside the monthly price

New costs are added to this list as they are identified.

**Paid by the client:**
- **Stripe, Paystack and PayPal:** the connection is free; the client pays the provider's own fees on each payment. Mybizz adds no markup.
- **Domain name:** the client registers and owns the client's own domain name.
- **Cloudflare R2:** the client's backups and history are kept in a free Cloudflare account in the client's own name, set up at registration. Cloudflare asks for a payment method; storage above the free 10 GB a month is billed by Cloudflare to the client, at $0.015 per GB a month.

**Absorbed by Mybizz:**
- **The Anvil Business account** that runs the client's application instance.
- **Email:** every email received and sent, through Amazon SES, at $0.10 per 1,000 emails.
- **AI voice:** speech-to-text and natural-sounding speech (Groq).
- **AI text:** the AI writing service behind the assists (Groq).

### 9.6 Revenue model

| | Monthly | Yearly |
|---|---|---|
| Revenue at capacity (100 clients × $150) | $15,000 | $180,000 |
| Hosting (100 Business accounts at $89, billed annually) | $8,900 | $106,800 |
| **Gross margin before other costs** | **$6,100** | **$73,200** |

Email and AI cost a few dollars per client per month and are recorded per client in the Mybizz management app (F-34).

### 9.7 Domain

Every client's application instance runs on the client's own domain, and every email address of the client is on that domain. The onboarding wizard connects the client's domain to the client's application instance and to the client's email in one step (F-03).

---

## 10. Go-to-Market Strategy

### 10.1 Principles

Mybizz CS launches with no marketing budget. The strategy must therefore be:
- Founder-led and personal
- Built on authentic story rather than paid reach
- South Africa-first, then international by content

This is not a weakness. A founder who lived the problem, built the solution, and can speak about both honestly is a more credible voice than any ad campaign.

### 10.2 Phase 1 — First clients (10 clients)

The goal of Phase 1 is not revenue — it is proof. Ten real businesses using a real product and willing to say so publicly.

**Tactics:**
- Personal network first: identify 10 consulting or service business owners in the founder's direct network or warm referrals
- WhatsApp and direct outreach: brief, honest, personal — "I built this, you are exactly who I built it for, I want you to be one of ten founding members"
- LinkedIn: the founder's own profile, sharing the story of building Mybizz — not promotional content but honest building-in-public posts
- Local South African business communities: Facebook groups, local networking events, BNI chapters, professional associations

**What to offer:** Mybizz CS at the standard price, plus genuine involvement in shaping the product. The first clients' feedback comes in through the Help channel (F-32).

**What not to do:** paid advertising, cold email blasts, or any approach that requires pretending the platform has more clients than it does.

### 10.3 Phase 2 — Proven platform (clients 11 to 50)

With 10 clients using the platform, the narrative shifts from "founding member" to "proven platform." Phase 2 is about systematic outreach using real evidence.

**Tactics:**
- Case studies from the first clients: one or two detailed write-ups of how a real business uses Mybizz CS (with client permission)
- LinkedIn content: founder posts sharing lessons, client results and product updates — consistent, weekly
- SEO content: blog posts targeting high-intent searches such as "how to set up online booking for a physiotherapy practice" or "best booking system for personal trainers South Africa"
- Referral programme: a client who refers a paying client receives one month free
- Industry-specific outreach: identify professional associations and communities for target industries (consulting, beauty, wellness, trades) and engage authentically — not as a sponsor, but as a member

### 10.4 Phase 3 — Full capacity (clients 51 to 100)

By this stage, Mybizz CS has 50 real clients, documented case studies, visible reviews and an established content presence. Phase 3 introduces paid acquisition supported by organic proof.

**Tactics:**
- Targeted LinkedIn and Facebook advertising to consulting and service business owners in South Africa and internationally
- Google Search advertising targeting booking software and business management keywords
- Partnership outreach: complementary providers (accountants, business coaches, web designers) who serve the same businesses and can refer
- Content marketing: continued SEO blog content now supported by a track record

### 10.5 International strategy

International clients are acquired primarily through content and search. A consulting or service business owner in the UK, Australia or the US searching for "complete booking and website platform for consultants" should find Mybizz CS. SEO content targeting these search terms, combined with Stripe and PayPal for global payments, makes international acquisition possible without a local presence.

The Paystack integration is a specific advantage in African markets where Stripe does not accept businesses directly. This positions Mybizz CS as the only complete platform built for African consulting and service businesses.

---

## 11. Brand Identity

### 11.1 Name and domain

**Brand name:** Mybizz
**Domain:** mybizz.live

The domain is the brand statement: *my business, live.* Online. Operational. Running. The .live TLD does functional work — it communicates that a business on Mybizz is active, present and open.

### 11.2 Brand story

Mybizz exists because its founder needed it and it did not exist.

After years of attempting to build an online business presence and encountering the same fragmented, expensive, technically demanding landscape that every small business faces, the founder built a single platform that solves the whole problem. Not part of it. All of it.

That story — "I built what I needed" — is the brand's most durable asset. It is specific, honest and verifiable. It connects directly with the target client who has had the same experience.

### 11.3 Brand position statement

*Mybizz CS is the complete online business platform for consulting businesses and service businesses. One platform. One price. You own your data, your customers and your business.*

### 11.4 Tone of voice

- Direct and honest — no marketing language that overpromises
- Empathetic — speaks to the real frustration of the target client, not a fictional ideal
- Confident but not arrogant — the product works, the founder knows it, there is no need to oversell
- Human — written by someone who ran the same race, not a corporate marketing department

### 11.5 Visual identity

The visual identity is the design system in F-31: the Anvil Material 3 theme with refinements, two contexts, four brand palettes, dark mode following the device, and Roboto typography.

---

## 12. Key Messages

### Primary message
*One platform. One price. You own it.*

### Supporting messages

**On fragmentation:**
*Most consulting and service businesses are running their online operations across five or six separate tools. Each one costs money. None of them talk to each other properly. Mybizz CS replaces all of them.*

**On ownership:**
*Your business. Your customers. Your data. Nobody else's. On Booksy, your customers are Booksy's customers. On Mybizz CS, your customer database belongs to you — structurally isolated, privately hosted, backed up to your own storage, accessible only by you.*

**On identity:**
*You are not a listing. You are a business. On a marketplace you are one of hundreds in your area. On Mybizz CS you have your own website, your own brand, your own booking flow, your own customer database. You compete on your terms, not theirs.*

**On leaving:**
*What happens to your customers when you leave? On marketplace platforms, they stay behind. On Mybizz CS your customer list is yours on day one and yours on the last day. Take it anywhere. No one else has a copy.*

**On pricing:**
*A website, a booking system, a CRM, email marketing, invoicing and an AI writer, bought separately for a team of six, cost over $300 a month — and every staff member adds more. Mybizz CS is $150 a month for all of it and more, for your whole team, with email, accounts, AI and voice included. No contract, no add-ons.*

**On simplicity:**
*Set up your business once. Website, booking, payments, CRM, sales, email, invoicing, accounts — all connected, all working, all in one place. Then run your business.*

**On voice:**
*Ask your business a question and hear the answer. "What is my schedule for tomorrow?" "Which invoices are outstanding?" From your phone, wherever you are.*

**On Africa:**
*Stripe does not accept South African businesses directly. Mybizz CS integrates Paystack natively. Your customers pay in Rands. Your money arrives in your account. No workarounds.*

**The founder message:**
*I built this because I needed it. I could not find a single platform that did everything a business needs without costing a fortune and requiring a technical degree to set up. So I built one. That is Mybizz.*

---

## 13. Support Model

### 13.1 Self-service first

Mybizz CS is designed to be operated without technical knowledge. Every feature carries a plain-language tooltip that opens a short help video, in the client's app language (F-25). The platform is the support system.

### 13.2 Direct support

Clients reach Mybizz through the Help button in their app (F-32). A client's message becomes a support ticket or feedback in the Mybizz management app and notifies the founder. Response target: within 24 hours on business days. A problem that stops a client's business is acknowledged within 4 business hours. The support channel is operational before the first paying client is onboarded.

### 13.3 Onboarding

| Option | Onboarding | Cost |
|---|---|---|
| Self-setup | The client registers and sets up their own instance through the onboarding wizard (F-03), with a help video on every step. Registration creates the client's reference number and records the client in the Mybizz management app. | Included |
| Assisted setup | Mybizz configures the client's instance on their behalf | $200 (one-time) |

Onboarding is streamlined and self-serving: the wizard takes a client from registration to a working business without help. There is no compulsory setup fee. Assisted setup is there for clients who want it.

---

## 14. What Is Explicitly Out of Scope

The following are not part of Mybizz CS:

- Product catalogue, shopping cart, inventory management, shipping
- Hotel and guest room reservations, and hospitality table bookings
- Membership tiers, subscription access control, recurring billing for client businesses
- A multi-vertical feature toggle system
- A marketplace or shared directory of Mybizz clients
- A support-ticket system for the client's own customers
- A knowledge base, events management

---

## 15. Success Metrics

| Metric | Target |
|---|---|
| First clients onboarded | 10 |
| Total clients at capacity | 100 |
| Annual recurring revenue at capacity | $180,000 |
| Client retention (year 1 to year 2) | >90% |
| Average onboarding-to-live time | <7 days |
| Support tickets per client per month | <2 |

---

## 16. Risks and How Mybizz CS Answers Them

**Founder capacity.** Every support ticket lands with the founder; at 100 clients, three tickets per client per month is 300 tickets. Mybizz CS answers with self-service help on every screen (F-25), built before launch, a support target of under two tickets per client per month, and at-risk alerts (F-34) that show where attention is needed first.

**Early churn.** A client who does not see value in the first weeks leaves quietly. Mybizz CS answers with the at-risk alerts (F-34), a streamlined self-serving setup with a help video on every step (F-03), sample data that shows every report working from the first day (section 8), and the demo apps that show the product before the client pays (F-36).

**Competitors copying.** Features can be copied. Mybizz CS's defence is what cannot be copied easily: the Africa and Paystack positioning, founder-direct support, and an ownership architecture that marketplace platforms cannot offer.

**Cost per client.** Hosting, AI and voice have a real cost per client. The Mybizz management app records each client's hosting, email and AI usage (F-33, F-34), each client has an Anvil account of its own, and the cost per client is reviewed every month.

**A problem with no name.** Business owners feel overwhelmed by tools and costs, but do not name the problem. Mybizz CS marketing names the problem — the five-tool problem — before it sells the features.

---

*Document owner: Mybizz founder*
