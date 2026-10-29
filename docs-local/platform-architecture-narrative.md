---
title: "platform-architecture-narrative"
doc-id: "mb-daf7b766"
updated: "2026-10-29"
---
# Mybizz — Comprehensive Application Treatise

> **Status:** Written before PDLF. The product intent is current; figures and the build approach will be set by the PDLF build.

**mybizz.live**
Version 1.0 | 2026
Developer Reference Document

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [The Problem](#2-the-problem)
   - 2.1 [Disconnected Tools and Fragmented Costs](#21-disconnected-tools-and-fragmented-costs)
   - 2.2 [The Technical Burden of Going It Alone](#22-the-technical-burden-of-going-it-alone)
   - 2.3 [The Hidden Operational Cost](#23-the-hidden-operational-cost)
3. [The Solution](#3-the-solution)
4. [Ease of Use](#4-ease-of-use)
   - 4.1 [Intuitive User Flows](#41-intuitive-user-flows)
   - 4.2 [Tooltips and Contextual Help](#42-tooltips-and-contextual-help)
   - 4.3 [Guided Process-Flow Videos](#43-guided-process-flow-videos)
5. [Core Capabilities](#5-core-capabilities)
   - 5.1 [Onboarding and Setup](#51-onboarding-and-setup)
   - 5.2 [Dashboard and Business Analytics](#52-dashboard-and-business-analytics)
   - 5.3 [Bookings and Scheduling](#53-bookings-and-scheduling)
   - 5.4 [Service Catalogue Management](#54-service-catalogue-management)
   - 5.5 [Customer Relationship Management](#55-customer-relationship-management)
   - 5.6 [The Three Email Systems](#56-the-three-email-systems)
   - 5.7 [Invoicing and Payments](#57-invoicing-and-payments)
   - 5.8 [Content and Blog](#58-content-and-blog)
   - 5.9 [Public Website and Landing Pages](#59-public-website-and-landing-pages)
   - 5.10 [Homepage Templates](#510-homepage-templates)
   - 5.11 [Landing Page Templates](#511-landing-page-templates)
   - 5.12 [Settings Hub](#512-settings-hub)
   - 5.13 [Tasks and Productivity](#513-tasks-and-productivity)
   - 5.14 [Lead Capture and Marketing Automation](#514-lead-capture-and-marketing-automation)
   - 5.15 [Integrated Marketing Funnel](#515-integrated-marketing-funnel)
   - 5.16 [The Clause Builder](#516-the-clause-builder)
   - 5.17 [Dual Currency System](#517-dual-currency-system)
   - 5.18 [Visual and Media Presentation Features](#518-visual-and-media-presentation-features)
   - 5.19 [Security and Data Protection](#519-security-and-data-protection)
6. [Design and User Experience](#6-design-and-user-experience)
   - 6.1 [Material 3 Foundation](#61-material-3-foundation)
   - 6.2 [MUI-Inspired Refinements](#62-mui-inspired-refinements)
   - 6.3 [Dual-Context Design](#63-dual-context-design)
   - 6.4 [Brand Customization Without Design Work](#64-brand-customization-without-design-work)
   - 6.5 [Consistency Across Every Touchpoint](#65-consistency-across-every-touchpoint)
7. [Who It's For](#7-whos-it-for)
   - 7.1 [Health and Wellness](#71-health-and-wellness)
   - 7.2 [Beauty and Lifestyle](#72-beauty-and-lifestyle)
   - 7.3 [Professional and Business Consulting](#73-professional-and-business-consulting)
   - 7.4 [Coaching and Advisory Services](#74-coaching-and-advisory-services)
   - 7.5 [Creative and Technical Services](#75-creative-and-technical-services)
   - 7.6 [Freelance and Gig-Style Service Providers](#76-freelance-and-gig-style-service-providers)
   - 7.7 [Trades and Skilled Services](#77-trades-and-skilled-services)
   - 7.8 [Business Size and Geography](#78-business-size-and-geography)
8. [Competitive Positioning](#8-competitive-positioning)
   - 8.1 [Where Mybizz Sits in the Market](#81-where-mybizz-sits-in-the-market)
   - 8.2 [The Ownership Differentiator](#82-the-ownership-differentiator)
   - 8.3 [The Real Cost Comparison](#83-the-real-cost-comparison)
   - 8.4 [The Simplicity Advantage](#84-the-simplicity-advantage)
   - 8.5 [The Africa Advantage](#85-the-africa-advantage)
   - 8.6 [Note on Competitive Data](#86-note-on-competitive-data)
9. [Why Mybizz CS](#9-why-mybizz-cs)
10. [V2 — Future Roadmap](#10-v2--future-roadmap)
   - 10.1 [WhatsApp Task Creation](#101-whatsapp-task-creation)
   - 10.2 [AI Assistant Layer](#102-ai-assistant-layer)
   - 10.3 [Client Portal](#103-client-portal)
   - 10.4 [Advanced Analytics](#104-advanced-analytics)
   - 10.5 [PayPal Integration](#105-paypal-integration)
   - 10.6 [Time Tracking](#106-time-tracking)
   - 10.7 [Custom Segment Builder](#107-custom-segment-builder)
   - 10.8 [Additional Homepage Templates](#108-additional-homepage-templates)
   - 10.9 [Optional Marketplace Presence](#109-optional-marketplace-presence)
   - 10.10 [Dark Mode Support](#1010-dark-mode-support)
   - 10.11 [Customer Portal Self-Service](#1011-customer-portal-self-service)
   - 10.12 [Multi-Source Video Support](#1012-multi-source-video-support)
   - 10.13 [Mybizz Management Platform](#1013-mybizz-management-platform)
   - 10.14 [Platform Capacity Expansion](#1014-platform-capacity-expansion)
11. The Five-App Structure
   - App 1: mb-3-cs — Development Workspace
   - App 2: master_template — The Published Dependency
   - App 3: blank_client_template — Provisioning Clone Source
   - App 4: [client-name]-cs — Client Instances (one per client)
   - App 5: Mybizz_management — Platform Operator Tooling
12. [Closing Summary](#11-closing-summary)

---

## 1. Executive Summary

Mybizz is a complete online business platform built specifically for consulting and services businesses. It replaces the fragmented collection of separate tools — website builders, booking systems, payment processors, CRMs, email platforms, and invoicing software — with a single, unified platform at a single price.

Every Mybizz subscriber receives their own fully operational business platform: a professional website, a booking and scheduling system, integrated payment processing, a customer database, email marketing, invoicing, a blog, and business analytics. Everything works together from day one. Nothing needs to be connected, configured separately, or paid for as an add-on.

The platform is built on Google's Material 3 design system, enhanced with additional visual refinements inspired by modern SaaS interfaces, giving every business a clean, professional, and trustworthy online presence without requiring any design work from the business owner.

---

## 2. The Problem

### 2.1 Disconnected Tools and Fragmented Costs

Running a professional service business online today means stitching together five or six separate tools — each with its own subscription, its own learning curve, and its own way of storing data. A typical setup might look like this:

- **A website builder** — one subscription, one login, one set of design decisions
- **A booking system** — another subscription, another integration to figure out
- **A payment gateway** — another signup, another configuration process
- **A CRM or contact spreadsheet** — yet another tool, or worse, a manual process
- **An email marketing platform** — another account to manage, another list to keep in sync
- **An invoicing tool** — one more service, one more monthly fee

Each tool costs money on its own. None of them share data cleanly. Client information ends up entered three times in three different places. A booking in one system does not automatically create an invoice in another. An enquiry on the website does not automatically appear in the CRM. And the business owner ends the day with six browser tabs open, manually moving information between tools that should have been designed to work together.

### 2.2 The Technical Burden of Going It Alone

Beyond the cost of subscriptions, the technical burden of running a professional online presence without a platform like Mybizz is significant and often underestimated:

- **Website hosting subscriptions** — separate hosting fees for the website, often with confusing tier structures and renewal pricing that increases after the first year
- **DNS management** — configuring and maintaining domain name system records so the website, email, and other services all resolve correctly; one misconfigured record can take the site offline or break email delivery
- **CDN management** — setting up and maintaining a content delivery network to ensure the website loads quickly for visitors in different geographic regions
- **DDoS and security management** — protecting the website from automated attacks, bot traffic, and malicious requests; most small business owners have no expertise in this area and rely on whatever their hosting provider offers by default
- **Responsive front-end design** — ensuring the website looks professional and functions correctly on desktops, tablets, and phones of every screen size; a site that looks broken on mobile loses customers immediately
- **SSL certificate management** — maintaining HTTPS certificates so the site is secure and browsers do not display warning messages to visitors
- **Database setup and integration** — if the business wants a booking system or CRM that actually stores data, someone has to set up a database, connect it to the front end, and maintain it over time
- **Backup and recovery** — ensuring business data is backed up regularly and can be restored if something goes wrong
- **Software updates** — keeping every component updated to avoid security vulnerabilities and compatibility issues

### 2.3 The Hidden Operational Cost

Most small service businesses never get all of it working. They settle for a partial setup — perhaps a booking link and a social media page — and manage everything else by hand. The cost is not just financial. It is time, missed opportunities, unprofessional client experiences, and no reliable view of how the business is actually performing. The business owner becomes a part-time IT administrator instead of focusing on the service they actually deliver.

---

## 3. The Solution

Mybizz brings everything into one place. A business owner signs up once, sets up their business once, and then runs everything from a single platform.

Here is what a typical customer journey looks like on Mybizz:

1. A potential client finds the business through a Google search or social media link
2. They land on the business's professional Mybizz website
3. They browse the services, read the blog, or watch a video introduction
4. They decide to book — selecting a service, choosing a provider, picking a date and time
5. They select whether they want to meet in person, by video call, or by phone
6. They complete any intake form the business requires
7. They pay — either the full amount or a deposit, depending on how the business configured it
8. They receive an immediate confirmation email with all the details
9. The booking appears automatically on the business's calendar
10. The client's details are captured in the CRM — no manual entry needed
11. An invoice is generated automatically
12. Twenty-four hours before the appointment, a reminder email goes out — with the video call link included if applicable
13. One hour before, another reminder goes out
14. The business owner logs in and sees everything — revenue, bookings, new customers, upcoming appointments — on a single dashboard

There is nothing to connect. Nothing to sync. Nothing to troubleshoot across multiple platforms. The website, the booking system, the payment processing, the customer records, the email communications, the invoices, and the analytics all share the same data because they are all part of the same platform.

One setup. One platform. One price. Everything included.

---

## 4. Ease of Use

### 4.1 Intuitive User Flows

Every workflow in Mybizz is designed to follow the natural sequence a business owner would think through. Booking a client follows the order: what service, who is the provider, when, where, how to pay. Setting up the business follows the order: who are you, what do you offer, how do you want to look, how do you get paid. Each screen presents the next logical step, with clear labels, sensible defaults, and obvious primary actions.

The navigation is organized into groups that match how business owners think about their operations: Sales and Operations (bookings and services), Customers and Marketing (contacts, campaigns, segments), Content and Website (blog and pages), Finance and Reports (invoices and analytics), and Settings. A business owner who learns one area of the platform finds the rest familiar immediately because every screen follows the same patterns.

### 4.2 Tooltips and Contextual Help

Every feature in Mybizz can have tooltips turned on — small, plain-language explanations that appear when the user hovers over or taps a feature for the first time. These explain what the feature does and why it matters, without assuming technical knowledge. The tooltips are optional: once a business owner is comfortable, they can turn them off and work at full speed.

### 4.3 Guided Process-Flow Videos

Contextual process-flow guidance videos are available throughout the platform, directed at exactly where the user is currently working. A business owner setting up their booking system sees a short video walkthrough specific to booking configuration. A business owner creating their first email campaign sees a video guide specific to that workflow. The videos are brief, focused, and placed exactly where the user needs them — not buried in a separate help centre that requires searching.

---

## 5. Core Capabilities

### 5.1 Onboarding and Setup

Mybizz onboarding is designed to get a business live as quickly as possible, without requiring technical knowledge.

- **Guided first-run experience** — a step-by-step walkthrough that covers every essential setup task in the correct order, with a visible progress indicator showing how far along the business owner is
- **Business profile setup** — business name, description, contact details, address, logo, social media links, accreditations, and entity type (individual or business)
- **Brand palette selection** — four professionally designed colour palettes to choose from, each suited to different business types (consultants, salons, wellness studios, premium services), with a live preview of how the choice affects the entire platform
- **System currency selection** — choose the business's primary accounting currency at setup (see section 5.17 for the full dual currency system)
- **Legal compliance acknowledgement** — guided acknowledgement of data governance and legal policy responsibilities, recorded with timestamps for audit purposes
- **Payment gateway connection** — connect either Stripe (for global payments) or Paystack (for South African and African clients) with simple key entry
- **Email configuration** — connect the business's own Brevo account for transactional and marketing emails
- **Bidirectional navigation** — the onboarding flow links directly to Settings for detailed configuration, and Settings links back to onboarding if setup is not yet complete
- **Resumability** — if a business owner starts onboarding and gets interrupted, they can return later and pick up exactly where they left off; no progress is lost
- **30-minute guided setup call** — included with every subscription; the Mybizz founder personally walks each new client through their initial configuration
- **Premium done-for-you setup** — for business owners who prefer it, Mybizz will configure the entire platform on their behalf for a one-time fee

### 5.2 Dashboard and Business Analytics

The dashboard is the first thing a business owner sees after logging in. It provides an immediate, at-a-glance view of business performance.

- **Revenue overview** — total revenue for the current period with comparison to the previous period, showing percentage change and trend direction
- **Booking count** — number of bookings in the current period with trend comparison
- **Customer totals** — total active customers and new customers in the current period
- **Activity feed** — recent activity across the platform: new bookings, payments received, new contacts, campaign activity
- **Metric cards** — individual KPI cards for revenue, bookings, and customer counts, each showing the current value, percentage change, and trend direction
- **Period comparison** — all metrics support comparison against the previous period (week, month, or custom range)
- **Visual charts** — simple, clear revenue and booking trend charts for quick pattern recognition

### 5.3 Bookings and Scheduling

The booking system is the operational heart of any service business. Mybizz handles the complete booking lifecycle.

- **Full booking flow** — customers select a service, choose a provider, pick a date and time, select a meeting type, complete any required intake forms, and pay — all in one continuous flow with no page jumps or external redirects
- **Calendar views** — month, week, and day calendar views for managing the business schedule at different levels of detail; each view shows bookings colour-coded by status (confirmed, pending, cancelled)
- **Availability management** — set business hours by day of the week, configure provider-specific schedules, and block out dates for holidays or closures
- **Availability exceptions** — override regular hours for specific dates (e.g., a provider taking a day off, extended hours for a special event)
- **Meeting types** — every service can offer one or more meeting formats: in-person, video call, or phone call; video call links are generated automatically for video bookings
- **Payment at booking** — collect full payment or a deposit at the time of booking, configurable per service
- **Automatic reminders** — email reminders sent 24 hours and 1 hour before each appointment, including video call links where applicable
- **Recurring appointments** — support for weekly, bi-weekly, and monthly recurring bookings
- **Booking viewer** — detailed view of any booking showing client information, service details, payment status, and available actions (edit, cancel, reschedule, view invoice)
- **Booking status management** — bookings move through clear statuses: pending (awaiting payment or confirmation), confirmed (paid and scheduled), and cancelled — with appropriate actions available at each stage
- **Reschedule support** — confirmed bookings can be rescheduled without cancellation; the client receives an updated confirmation automatically
- **Multi-provider support** — each provider (staff member) has their own calendar, their own services, and their own availability; revenue is tracked per provider for reporting
- **Special requests** — customers can add notes or special requests when booking, visible to the provider

### 5.4 Service Catalogue Management

Mybizz supports two pricing models to cover the full range of service businesses.

- **Time-based pricing** — services priced by duration (e.g., a 60-minute massage at R450); the system uses the duration to calculate available time slots and prevent double-booking
- **Deliverable pricing** — services priced as a fixed deliverable (e.g., a logo design at R3,500); these are not time-slot-dependent and can be booked as tasks or consultations
- **Service editor** — full creation and editing of services with name, description, category, pricing model, price, duration, images, video URL, meeting type (in-person, video call, phone call), and active/inactive status
- **Service categories** — organize services into categories for easy browsing on the public website
- **Provider assignment** — assign one or more providers to each service; customers can choose their preferred provider or be assigned to any available one
- **Per-service intake forms** — optionally require customers to complete a custom intake form when booking a specific service (e.g., a health questionnaire before a physiotherapy session)
- **Video embeds** — add video content to individual service pages (e.g., a walkthrough of what to expect during a treatment); videos are linked from the business's own YouTube channel
- **Service viewer** — detailed read-only view of any service showing all configuration, assigned providers, and related bookings

### 5.5 Customer Relationship Management

The CRM is a unified system for managing every person who interacts with the business — leads, active customers, and past customers.

- **Unified contact database** — a single, centralised record for every person: leads, active customers, and inactive customers, all in one place with a clear status indicator
- **Contact lifecycle tracking** — every contact has a status (Lead, Customer, or Inactive) that reflects their current relationship with the business
- **Full activity timeline** — see every interaction a contact has had with the business: bookings made, emails received, notes added, forms submitted — all in chronological order
- **Contact detail fields** — name, email, phone, source (how they found the business), tags, internal notes, preferences, lifetime value, total bookings, average transaction value, and last contact date
- **Automatic contact creation** — contacts are created automatically when someone makes a booking, submits an enquiry form, or fills in a landing page form; no manual data entry required
- **Tags and segmentation** — tag contacts manually or let the system categorize them automatically based on their behaviour
- **Pre-built segments** — ready-to-use customer segments: New Leads (recently captured, not yet booked), Active Customers (booked at least once), Inactive Customers (no booking in a defined period), and High Value (above a spend threshold)
- **Custom segment builder** — create custom segments using filters for tags, status, total spend, and booking history
- **CSV import and export** — import existing customer lists from spreadsheets and export the full database at any time; the business always has access to its data in a portable format
- **Contact list** — searchable, filterable list of all contacts with quick access to their profile, activity history, and actions
- **Contact editor and viewer** — full create, edit, and read-only views for every contact record

### 5.6 The Three Email Systems

Mybizz includes three distinct email systems, each serving a different purpose. Understanding how they work together is important because they cover the full spectrum of business email communication.

#### 5.6.1 System Emails (Automatic, No Configuration Needed)

These are built-in operational emails that work automatically from day one with no setup required:

- **Login and account emails** — password reset links, email confirmations, account notifications
- **How they work** — the platform sends these automatically whenever a user requests a password reset, confirms their email, or performs other account-level actions
- **No configuration needed** — these use the platform's built-in email capability and work immediately upon signup

#### 5.6.2 Brevo-Powered Transactional Emails (Business-Triggered, Automatic)

These are business-event-triggered emails that go out automatically once the business connects their Brevo account:

- **Booking confirmations** — sent immediately when a client books an appointment, including all booking details
- **Appointment reminders** — sent 24 hours and 1 hour before the appointment, including video call links where applicable
- **Invoice delivery** — sent when an invoice is emailed to a customer, including the invoice as a PDF attachment
- **Payment receipts** — sent when a payment is recorded against an invoice
- **How they work** — these are triggered automatically by business events (a booking is made, an invoice is sent, a payment is recorded). The business owner does not need to send them manually
- **Configuration** — the business connects their Brevo account once during setup, configures their sender name and email address, and the system handles the rest
- **Branding** — all transactional emails are branded with the business's name and email address, not Mybizz branding

#### 5.6.3 Brevo-Powered Marketing Campaigns (Planned, Targeted)

These are marketing emails that the business owner creates and manages:

- **Campaign sequences** — automated email sequences triggered by events: a new client welcome series (sent over several days after first booking), an appointment follow-up (sent after an appointment), a re-engagement campaign (sent to customers who have not booked in a while), and a referral request (sent after positive engagement)
- **Email broadcasts** — one-time email announcements or promotions sent to selected segments or the full contact list
- **Campaign editor** — create and configure email campaigns with configurable delays between emails and event-based enrollment triggers
- **Segment-based targeting** — send campaigns to specific customer segments (e.g., a re-engagement campaign only to customers who have not booked in 90 days, or a special offer only to high-value customers)
- **Open and click tracking** — track which emails are opened and which links are clicked, with results visible in the campaign dashboard
- **Campaign metrics** — view campaign-level performance: emails sent, opens, clicks, conversions, and revenue generated
- **Unsubscribe handling** — automatic unsubscribe management so contacts can opt out of marketing emails while still receiving transactional ones

### 5.7 Invoicing and Payments

The invoicing and payments system handles the full financial transaction lifecycle, from booking to payment to receipt.

- **Automatic invoice generation** — when a booking is completed, an invoice is generated automatically, including all service details, line items, and totals
- **Manual invoice creation** — create invoices manually for ad-hoc services, packages, or custom work that was not booked through the system
- **Invoice editor** — full invoice creation with customer selection, date fields, line item entry (description, quantity, unit price), notes, and currency
- **Invoice viewer** — detailed invoice view showing invoice number, status badge, bill-to information, line items table, subtotal, tax, and total
- **Invoice lifecycle management** — invoices move through clear statuses: draft (being prepared), sent (delivered to customer), paid (payment received), overdue (past due date without payment), and cancelled (voided) — with appropriate actions available at each stage
- **PDF generation** — invoices are available as downloadable PDF documents for professional presentation
- **Email delivery** — send invoices directly to customers via email with a single action
- **Payment recording** — record payments against invoices, automatically updating the invoice status
- **Payment gateway integration** — payments processed through Stripe (global) or Paystack (South Africa and Africa), integrated directly into the booking flow
- **Gateway-agnostic design** — both payment gateways use the same underlying service, so the experience is identical regardless of which gateway the business uses; switching gateways later does not require reconfiguring services or bookings
- **Invoice list** — searchable, filterable list of all invoices with status filtering and quick actions
- **Invoice numbering** — automatic sequential invoice numbering (e.g., INV-2026-001) for professional documentation

### 5.8 Content and Blog

The blog system provides a full content publishing platform for businesses that want to build authority, share expertise, or drive organic traffic.

- **Rich text editor** — write and format blog posts with a full-featured editor supporting headings, lists, images, links, and embedded video
- **Blog post templates** — three layout options for each post:
  - **Classic centred** — a clean single-column layout, best for long-form articles and editorial content
  - **Two-column with sidebar** — main article plus a sidebar showing author bio, related posts, categories, and newsletter signup; best for engagement and internal linking
  - **Full-width immersive** — a wide visual treatment with featured image, pullquotes, and generous line height; best for visual storytelling and photography-heavy content
- **SEO control per post** — set a custom SEO title, meta description, URL slug, and keywords for every post
- **Featured images and video** — add a featured image and embed video content (YouTube) in individual posts
- **Pullquotes** — highlight key quotes with featured pullquote styling in full-width posts
- **Post statuses** — manage posts as Draft, Published, or Archived; only published posts appear on the public site
- **Blog categories** — organize posts into categories with management tools for creating, editing, and ordering categories
- **View count tracking** — see how many times each post has been viewed
- **Blog list views** — the public blog listing supports two view modes:
  - **Tiled grid view** — a multi-column card-based layout for visual browsing, with featured images, category badges, titles, and excerpts
  - **List with thumbnails** — a row-based layout with thumbnail images and denser text, better for editorial scanning
- **Comment moderation** — manage blog comments with approval, spam, and deletion controls; comments can be enabled or disabled globally from Settings
- **Public blog pages** — automatically generated blog listing and individual post pages, fully integrated with the public website

### 5.9 Public Website and Landing Pages

Every Mybizz subscriber gets a complete, professional, mobile-responsive public website — managed entirely from the admin panel with no coding required.

- **Homepage** — choose from four professionally designed homepage layouts (see section 5.10)
- **About page** — tell the business's story with a dedicated, professionally laid out about page
- **Contact page** — a public contact form that captures enquiries directly into the CRM, with Google Maps integration for showing the business location
- **Services page** — an automatically generated listing of all active services with detail pages, images, and booking links
- **Privacy Policy page** — a professionally structured privacy policy page, managed through the Clause Builder (see section 5.16)
- **Terms and Conditions page** — a professionally structured terms page, managed through the Clause Builder
- **Landing pages** — create dedicated landing pages for specific marketing campaigns (see section 5.11)
- **Landing page analytics** — track views and conversions for each landing page
- **Footer** — a consistent, professional footer across all public pages showing business credentials, social links, contact information, policy links, and optional Google Maps embed
- **Mobile responsive** — every public page is fully responsive and looks professional on desktop, tablet, and mobile
- **SEO optimised** — all public pages include proper meta tags, semantic structure, and clean URLs for search engine visibility
- **Custom domain support** — businesses can connect their own domain name (e.g., bookings.their-business.com), or use a Mybizz subdomain at no additional cost

### 5.10 Homepage Templates

Mybizz offers four homepage layout options, each designed for a different business style and conversion strategy. All four use the same underlying components — they differ only in layout, section ordering, and emphasis.

- **Classic** (default, balanced) — a general-purpose layout with all major sections visible: hero, about summary, services showcase, video introduction, testimonials, trust strip, call-to-action band, and footer. Best for businesses that want a complete, well-rounded homepage.

- **Services** (services-first) — the services showcase section moves directly below the hero, making it the first thing visitors see after the headline. Best for businesses whose services are their primary selling point and want to show them immediately.

- **Booking** (conversion-focused) — the call-to-action section appears twice near the top of the page, and the trust strip is moved up to support conversion. Best for businesses that want to drive visitors toward booking as quickly as possible.

- **Minimalist** (clean, minimal) — uses fewer sections and more whitespace: hero, brief about, compact trust strip, call-to-action, and footer. Hides the carousel, full services grid, testimonials, and video. Best for businesses that prefer a clean, understated online presence.

All templates support optional sections that appear automatically when configured: the video section only shows when a YouTube URL is provided, and the services carousel only shows when services are active.

### 5.11 Landing Page Templates

Landing pages are standalone pages designed for specific marketing campaigns — they are separate from the main website and have no navigation header or footer, keeping the visitor focused on one action.

- **Lead Capture** — designed to collect contact information from visitors. Includes a headline, description, an offer (e.g., "Download our free guide"), and a form capturing name, email, and phone. Best for building the contact list through content marketing or advertising.

- **Event Registration** — designed to promote and register attendees for events, workshops, or webinars. Includes event details, date, time, location or link, and a registration form. Best for businesses that run group events or workshops.

- **Video Sales Letter (VSL)** — designed for video-driven conversion. Features a prominent video embed (from the business's YouTube channel) followed by a call-to-action button. Best for businesses that use video to explain their service and want the viewer to take action immediately after watching.

Each landing page tracks its own views and conversion count, so the business owner can see which campaigns are working. Landing pages can be linked from blog posts, social media, email campaigns, or paid advertising.

### 5.12 Settings Hub

Settings is the central control panel for the entire business configuration. It is a single, tabbed form organized by concern, with different tabs visible to different user roles.

- **Business Profile** — manage business name, description, email, phone, address, timezone, entity type (individual or business), social media links (Facebook, Instagram, X/Twitter, LinkedIn), accreditations, and logo
- **Brand Palette** — switch between four professionally designed colour palettes at any time; the change applies instantly across the entire platform, from the public website to the admin interface
- **System Currency** — view the business's primary accounting currency (set during onboarding; immutable after the first transaction to ensure financial consistency)
- **Legal Acknowledgement** — a read-only view of the legal acknowledgements made during onboarding, with timestamps; the acknowledgement action itself is part of onboarding, not settings
- **Display Currency** — optionally configure a different display currency for specific services or regions (see section 5.17)
- **Payment Gateway** — configure and manage the active payment gateway (Stripe or Paystack); toggle between test mode and live mode for sandbox testing before going live
- **Services** — a direct link to the service catalogue management area
- **Users and Permissions** — invite team members, assign roles (Manager, Admin, Staff, Customer), manage access, activate and deactivate accounts; role-based visibility means different users see different parts of the platform
- **Email Configuration** — set up and verify the business's Brevo email sending identity; test the connection to verify that transactional and marketing emails will be delivered correctly
- **Theme** — fine-tune the public website's visual presentation with colour and font options beyond the brand palette
- **Feature Flags** — enable or disable platform features like marketing tools and the blog, so the interface only shows what the business actually uses; this keeps the admin panel clean and focused for businesses that do not need every feature

Different roles see different tabs. The Owner sees everything. A Manager sees operational tabs but not financial or security tabs. A Staff member sees only their own schedule. This ensures each team member has exactly the access they need — no more, no less.

### 5.13 Tasks and Productivity

Mybizz includes a task management system to help business owners stay on top of follow-ups and operational actions.

- **Manual task creation** — create tasks linked to specific contacts with a title, due date, and notes
- **Auto-generated tasks** — the system automatically creates follow-up tasks based on business activity (e.g., following up with a new lead who enquired but did not book, requesting a review after an appointment)
- **Task list** — a centralized task list with completion tracking, filtering, and due date visibility
- **Task completion** — mark tasks as completed with automatic timestamping for audit trail

### 5.14 Lead Capture and Marketing Automation

Beyond email campaigns, Mybizz includes tools specifically designed to capture and convert leads.

- **Lead capture forms** — create dedicated lead capture forms for landing pages with customizable fields and offer text
- **Automatic contact creation** — when a lead submits a form, both the lead record and the contact record are created simultaneously; the lead is immediately linked to the contact with no manual conversion step
- **Lead tracking** — track leads from their source (landing page, enquiry form, manual entry) through to conversion as a paying customer
- **Capture analytics** — see how many leads each capture form has generated and how many have converted
- **Campaign enrollment** — automatically enroll new leads into welcome sequences or nurture campaigns

### 5.15 Integrated Marketing Funnel

Mybizz supports a connected marketing journey that links content, landing pages, and the booking flow into a seamless funnel. Here is how it works in practice:

1. **A business owner writes a blog post** — for example, "5 Signs You Need a Business Coach" — and publishes it on their Mybizz blog
2. **The blog post includes a call-to-action** — a button or link that says "Book a Free Consultation" or "Learn More About Our Coaching Packages"
3. **The call-to-action links to a landing page** — a dedicated Lead Capture or Video Sales Letter landing page that presents the offer in focused, distraction-free detail
4. **The landing page links to the booking flow** — the visitor clicks "Book Now" and is taken directly into the Mybizz booking system, where they select a service, choose a time, and pay
5. **The lead is captured at every step** — if the visitor submits the landing page form, they become a lead in the CRM. If they book, they become a customer automatically. If they only read the blog post, the visit is tracked

This same pattern works from social media: a post on Instagram, LinkedIn, or Facebook can link directly to a landing page, which links to the booking flow. The business owner creates the content once and it works as a conversion funnel indefinitely.

### 5.16 The Clause Builder

The Clause Builder is a tool that helps business owners create and maintain their Privacy Policy and Terms and Conditions pages without needing legal expertise or technical knowledge.

**What it is:** A structured, clause-based editor where the business owner builds their legal documents by selecting, editing, and organizing pre-written clauses into a complete policy. Think of it as building a document from blocks rather than writing from scratch.

**How it works:**

1. The business owner opens the Clause Builder from Settings and selects which document to work on: Privacy Policy or Terms and Conditions
2. The system loads a set of pre-seeded clauses — common, professionally written clauses covering typical topics (data collection, cookies, third-party services, payment terms, liability, etc.)
3. The business owner can:
   - **Edit seeded clauses** — customize the wording to match their specific business
   - **Add custom clauses** — create entirely new clauses for topics specific to their business
   - **Organize by category** — group clauses under logical headings (e.g., "Data Collection," "Payment Terms," "Cancellation Policy")
   - **Reorder clauses** — drag clauses into the order they want them to appear
   - **Activate or deactivate clauses** — choose which clauses appear on the published page
4. When the business owner saves and publishes, the policy page on their public website updates immediately
5. The seeded clauses provide a professional starting point; the business owner is responsible for ensuring the final content is appropriate for their business and jurisdiction

**Why it matters:** Most small business owners either skip having a Privacy Policy and Terms and Conditions entirely (which creates legal risk), or they copy-paste generic text from the internet (which may not apply to their situation). The Clause Builder gives them a structured, guided way to produce professional legal pages that are actually relevant to their business — without hiring a lawyer for the initial draft.

### 5.17 Dual Currency System

Mybizz includes a dual currency system designed to let businesses operate accounting in one currency while presenting prices in another for international customers.

**System Currency (the business's home currency):**
- Set once during onboarding
- Used for all internal accounting, reporting, invoicing totals, and revenue calculations
- Immutable after the first transaction — once a booking or invoice is created, the system currency cannot be changed, because changing it would break the consistency of all historical financial records
- Example: a South African business sets ZAR (South African Rand) as its system currency

**Display Currency (optional, per-service):**
- An optional setting on individual services that presents the customer-facing price in a different currency
- Used only for display purposes — the customer sees the price in the display currency, but all internal calculations still use the system currency
- Example: the same South African business offers consulting services to international clients and sets USD (US Dollars) as the display currency on those services. The client sees "$150" on the booking page, but the business's reports and invoices track the equivalent in ZAR

**The benefit:** A business can reach and appeal to different international markets with currency-appropriate pricing presentation, without complicating their underlying accounting. A physiotherapist in Johannesburg can show Rands to local clients and Dollars to international consulting clients, while their financial reporting stays clean in a single home currency.

### 5.18 Visual and Media Presentation Features

Mybizz includes several visual presentation features that give the public website a modern, engaging feel without requiring any design or technical work from the business owner.

#### 5.18.1 Parallax Visual Effect

The homepage features a parallax scrolling effect — a subtle visual treatment where the background image moves at a different speed than the foreground content as the visitor scrolls. This creates a sense of depth and modern polish on the hero section of the homepage. It works automatically on all devices and requires no configuration from the business owner.

#### 5.18.2 Auto-Rotating Display Carousels

The homepage and the Services page include auto-rotating carousels — visual showcases that cycle through multiple items (services, featured content, or testimonials) automatically, without the visitor needing to click through manually. The visitor can also navigate manually using arrows or dot indicators if they want to jump to a specific item. This keeps the homepage feeling dynamic and alive while presenting multiple pieces of content in a compact space.

#### 5.18.3 Video Presentation

Video content can be displayed in three contexts across the platform:

- **On the homepage** — a video introduction to the business (e.g., the business owner explaining their approach, a tour of the premises, or client testimonials)
- **On individual service listings** — a video walkthrough of what to expect during a specific service (e.g., "What happens during your first physiotherapy session")
- **Within blog posts** — embedded video content that complements the written article

Videos are linked from the business's own YouTube channel — the business owner simply provides the YouTube URL in Settings or when editing a service or blog post, and the video displays directly within the relevant page. No separate video hosting is needed. The video section is automatically hidden on any page where no video URL is configured, so pages without video look clean and complete.

### 5.19 Security and Data Protection

Mybizz is built with security as a foundational requirement, not an afterthought. Every aspect of data protection is designed to give business owners confidence that their business data — and their customers' data — is safe.

- **Bank-level credential protection** — all sensitive credentials (payment gateway keys, email passwords, API tokens) are encrypted at rest using industry-standard encryption. They are never visible to anyone — not even to Mybizz support — after they are saved. The system that manages these credentials (called The Vault) stores everything encrypted and requires a two-factor authentication code from an authenticator app every single time the business owner wants to view or manage them

- **Two-factor authentication for sensitive operations** — accessing stored credentials requires a time-based verification code from an authenticator app (such as Google Authenticator), entered every single time with no "remember me" shortcut. An email notification is sent to the business owner on every access, so they always know when their credentials have been viewed

- **Five-tier access control** — the platform supports five distinct access levels:
  - **Owner** — full control over everything, including financial data, credentials, and user management
  - **Manager** — operational management of bookings, customers, marketing, and reporting; cannot access credentials or financial configuration
  - **Admin** — booking and customer management; cannot access financial reporting, settings, or credentials
  - **Staff** — access to their own calendar and bookings only; cannot see other staff data or business settings
  - **Customer** — access to their own bookings, invoices, and account information only

- **Server-enforced permissions** — access control is enforced at the server level, not just in the visible interface. A user cannot access data or perform actions outside their assigned role, regardless of how they interact with the platform

- **Rate limiting** — the platform limits the number of requests any user or IP address can make, protecting against automated attacks and abuse

- **Full audit trail** — every significant action on the platform is logged with the user, timestamp, and description for accountability and compliance

- **Data isolation** — every business's data is completely isolated from every other business on the platform. No business can see another business's clients, bookings, revenue, or any other information. This isolation is built into the platform's fundamental structure, not achieved through filters or configuration. Each business's data is held completely privately and separately — never mixed with or visible to any other business

- **Data ownership and portability** — every business owns its data completely. Client lists, booking history, contact details, and transaction records belong exclusively to the subscribing business. Data can be exported at any time in standard formats (CSV). If a business cancels, their data does not remain accessible to anyone else — it is theirs to take

- **Compliance alignment** — the platform is designed to support POPIA (South Africa), GDPR (international), and PCI DSS (payment card industry) compliance requirements

---

## 6. Design and User Experience

### 6.1 Material 3 Foundation

The Mybizz interface is built on Google's Material 3 (M3) design system — the same design technology used by Google's own products and trusted by leading professional software platforms worldwide. Material 3 provides the foundational design language: the colour system, typography hierarchy, spacing rules, component shapes, interaction patterns, and navigation conventions.

The practical benefit for Mybizz users is that the interface feels immediately familiar and professional. Buttons behave as expected. Navigation is where you expect it. Forms follow consistent patterns. The visual hierarchy makes it clear what is important, what is actionable, and what is informational. This is not a custom design that might look dated in a year — it is built on an evolving, industry-standard design foundation.

### 6.2 MUI-Inspired Refinements

On top of the Material 3 foundation, Mybizz applies additional visual refinements inspired by the MUI (Material-UI) design approach used in modern SaaS products. These refinements enhance the interface with:

- **Softer, more sophisticated card shadows** — giving depth without heaviness
- **Tighter card corners** — a more contemporary, polished feel
- **Stronger heading weights** — clearer visual hierarchy and easier scanning
- **Subtle dividers** — refined separation between sections without harsh lines
- **Refined surface distinction** — different background tones for different areas (navigation, content panels, cards) that make the layout easier to read at a glance
- **Consistent hover and press states** — every interactive element responds to touch and mouse in the same predictable way

The result is a consistently clean, professional, and modern look and feel across every screen — from the public website that customers see to the admin panel that the business owner uses daily. The interface looks like it was designed by a professional agency, not like a template.

### 6.3 Dual-Context Design

Mybizz has two distinct but related presentation contexts:

- **The management interface** — designed for clarity and speed. Higher contrast, clearer hierarchy, more compact layouts. This is where the business owner works every day, so efficiency matters.
- **The public-facing website** — designed for warmth and trust. Softer visual rhythm, more generous whitespace, palette-driven brand expression. This is where customers form their first impression.

Both contexts use the same underlying design system — the same typography, spacing, component shapes, and interaction rules. The difference is in emphasis and density, not in two separate designs. This means the business owner's admin experience and the customer's website experience feel like they belong to the same brand.

### 6.4 Brand Customization Without Design Work

Every business can select from four professionally designed colour palettes — each crafted for a specific business personality:

- **Professional Blue** — trust, professionalism, stability. Best for consultants, clinics, therapists.
- **Warm Coral** — warmth, creativity, approachability. Best for salons, spas, lifestyle businesses.
- **Calm Teal** — health, balance, growth. Best for fitness, wellness, physiotherapy.
- **Sophisticated Purple** — premium, refined, exclusive. Best for executive coaching, high-end consulting.

The palette applies consistently across the entire platform, from the public website to the booking pages to the admin interface, creating a cohesive brand experience without the business owner needing to make a single design decision.

### 6.5 Consistency Across Every Touchpoint

Whether a customer is browsing the website on a phone, booking an appointment on a tablet, or the business owner is checking reports on a desktop, the experience is the same: clean, professional, fast, and intuitive. The platform handles responsive layout automatically, so there is nothing to configure for mobile — it just works.

---

## 7. Who It's For

Mybizz is built for any business that sells consulting or services — online or in person. The common thread is simple: they sell time or deliverables, they book clients, they need to get paid, and they need a professional online presence.

### 7.1 Health and Wellness

Physiotherapists, chiropractors, psychologists, nutritionists, personal trainers, yoga instructors, massage therapists, occupational therapists, speech therapists, and other health practitioners who need appointment booking, client records, and payment processing.

### 7.2 Beauty and Lifestyle

Salons, spas, nail technicians, aestheticians, hair stylists, barbers, tattoo artists, makeup artists, and other beauty professionals who need scheduling, client management, and a professional online presence.

### 7.3 Professional and Business Consulting

Management consultants, strategy advisors, financial consultants, IT consultants, marketing consultants, HR consultants, legal consultants, and other professional advisors who need client engagement tools, project-based invoicing, and a credible online presence that reflects their expertise.

### 7.4 Coaching and Advisory Services

Life coaches, business coaches, executive coaches, career coaches, health coaches, wellness coaches, mentors, and other advisory professionals who need booking systems, client progress tracking, and email communication tools.

### 7.5 Creative and Technical Services

Photographers, videographers, web designers, graphic designers, copywriters, content creators, UX designers, branding consultants, and other creative professionals who need to showcase their work, manage client bookings, and invoice for deliverables.

### 7.6 Freelance and Gig-Style Service Providers

Independent contractors, freelancers, and gig workers across any service category — the kind of independent service offerings found on platforms like Fiverr or Upwork, but operating under their own brand rather than a marketplace. Mybizz gives these providers their own standalone platform where they own their client relationships, their brand, and their data, instead of competing for visibility inside a marketplace algorithm.

### 7.7 Trades and Skilled Services

Plumbers, electricians, carpenters, painters, gardeners, tutors, driving instructors, caterers, event planners, and other skilled service providers who need scheduling, client management, and invoicing.

### 7.8 Business Size and Geography

Mybizz is optimized for solo practitioners and small practices of 2 to 10 providers. The platform supports team-based businesses from day one — each team member gets their own calendar, their own services, and their own access level — while remaining simple enough for a one-person operation to manage alone.

Mybizz is built for global operation. It supports international payment processing through Stripe, with native Paystack integration for South African and African markets where Stripe does not operate. Multi-currency support and timezone-aware scheduling make it viable for businesses anywhere in the world.

---

## 8. Competitive Positioning

### 8.1 Where Mybizz Sits in the Market

Mybizz occupies a distinct position in the market: it is a complete, standalone business platform — not a marketplace, not a point solution, and not a website builder with a booking plugin bolted on. It competes on completeness, ownership, and simplicity rather than on marketplace discovery or lowest sticker price.

The three most commonly compared platforms in this space are Booksy, Vagaro, and Mindbody. Each operates as a marketplace: service businesses list inside a shared discovery environment where their competitors appear alongside them, and the platform takes a commission or charges add-on fees for features that Mybizz includes by default.

### 8.2 The Ownership Differentiator

On any marketplace platform, the business's clients are effectively the platform's clients. The platform's algorithm decides visibility. Competitors appear next to the listing. If the business builds its client base through the marketplace and then leaves, the clients remain in the marketplace's ecosystem.

On Mybizz, the business has its own standalone platform. No competitors appear on the business's pages. No algorithm affects visibility. No commission is taken on bookings. The client database belongs exclusively to the business — structurally isolated, privately hosted, and accessible only by the business owner. Clients are the business's clients on day one and remain the business's clients permanently.

### 8.3 The Real Cost Comparison

Marketplace platforms advertise low base prices. The full cost tells a different story.

| | Booksy | Vagaro | Mindbody | **Mybizz** |
|---|---|---|---|---|
| Base price | $29.99/mo | $23.99/mo | $99–$699/mo | **$50/mo** |
| Per-staff fee | +$20/person | +per calendar | included | **included** |
| Processing fee | 2.49–2.69% | 2.2–3.5% | 3.5% | **gateway rate only** |
| Marketplace commission | up to $49.99/mo (Boost) | none | 20% new client | **none** |
| Add-ons required | none listed | forms, SMS, storage | many | **none** |
| Website included | no | add-on | no | **yes** |
| Hosting included | no | no | no | **yes** |
| Client owns data | no | no | no | **yes** |
| Competitors on your page | yes | yes | yes | **no** |

A solo practitioner on Booksy with one staff member pays $49.99/month before processing fees, with no website and no ownership of their client data. A three-person practice on Booksy pays $70–90/month before processing fees. Mybizz at $50/month is all-in. The price is the price.

### 8.4 The Simplicity Advantage

Marketplace platforms are focused on one thing: booking and discovery. They do not include a website, a blog, landing pages, a CRM with email marketing, or invoicing. Businesses using these platforms still need to set up and pay for all of those separately — adding cost, complexity, and the burden of keeping everything in sync.

Mybizz replaces all of those tools. Website, booking, payments, CRM, email marketing, invoicing, blog, and analytics — all in one platform, all working together, all included in the price.

### 8.5 The Africa Advantage

Stripe does not operate in South Africa. Booksy and Vagaro were not built for the African market. Mybizz integrates Paystack natively — clients pay in Rands, the money lands in the business's bank account, no workarounds. This positions Mybizz as the only complete business platform built specifically for African service businesses.

### 8.6 Note on Competitive Data

The competitive comparison above is based on information from the project's positioning strategy document and publicly known characteristics of Booksy, Vagaro, and Mindbody. Some details (specific pricing, add-on structures, commission rates) may have changed since the last review and should be verified by the developer for factual accuracy before being used in external-facing materials.

---

## 9. Why Mybizz CS

### The Ownership Argument

On marketplace platforms, clients belong to the platform. On Mybizz, clients belong to the business. This is the single most important differentiator and the one that matters most to business owners who have experienced the frustration of building a client base inside someone else's ecosystem.

### The Total Cost Argument

When you add up per-staff fees, processing commissions, marketplace cuts, add-ons for features like SMS or forms, and the cost of a separate website hosted elsewhere — a typical service business on a marketplace platform pays significantly more than the headline price suggests, and still does not own its data or have a complete integrated platform.

### The Simplicity Argument

Set up the business once. Website, booking, payments, CRM, email, invoicing — all connected, all working, all in one place. Then run the business.

### The Founder Story

Mybizz was not designed by a product team optimizing metrics. It was built by a founder who spent years trying to establish an online service business and encountered exactly the fragmentation, cost, and complexity described above. After speaking with other small business owners, the pattern was consistent: the challenge was not ambition or effort — it was the sheer fragmentation of the tools required to operate a modern service business online.

Mybizz is the product the founder needed and could not find. That origin is the product's most credible differentiator: it was designed by someone who lived the problem, not someone who observed it from the outside.

---

## 10. V2 — Future Roadmap

The following features and capabilities are planned for a future version of Mybizz. They are not currently available. Each item is described briefly — enough to recognize what it is and what value it would provide.

### 10.1 WhatsApp Task Creation

Users will be able to create tasks in the task management system by sending a WhatsApp message. This is one-directional only — the system does not send task information back via WhatsApp. The business owner sends a message like "Follow up with Mrs. Ndaba about her booking" and it appears as a task in the system.

*Source: User request / project planning notes.*

### 10.2 AI Assistant Layer

A reusable AI helper component that provides intelligent assistance across the platform. The AI assistant will offer ten capabilities:

1. **Bio/profile writer** — generates professional business descriptions and owner bios during onboarding
2. **Service description generator** — writes compelling service descriptions from brief input
3. **FAQ generator** — creates frequently asked questions from service descriptions
4. **Proposal generator** — produces professional proposals for consulting engagements
5. **Blog post drafts** — generates blog post outlines and drafts for content marketing
6. **Email response suggestions** — suggests reply text for customer communications
7. **Invoice message generator** — writes professional invoice cover messages
8. **Terms and conditions plain language explainer** — explains legal clauses in simple terms
9. **Client sentiment summary** — analyses client communication patterns (requires accumulated data)
10. **Booking and availability insights** — identifies patterns and optimization opportunities in booking data (requires accumulated data)

The AI assistant is designed as a contextual floating helper — it opens on the current page without navigating away, and it preselects the most relevant assist based on where the user is working.

*Source: [[[mb-3-cs-project-context:docs-local/releases/v2/mybizz-ai-implementation-spec]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/releases/v2/mybizz-ai-implementation-spec.md)*

### 10.3 Client Portal

A self-service portal where customers can manage their own bookings, view invoices, update their profile, and access their history without contacting the business owner. This shifts routine administrative tasks from the business owner to the customer.

*Source: positioning-strategy.md, platform-architecture.md*

### 10.4 Advanced Analytics

Extended reporting beyond the V1 dashboard: revenue breakdown by service and by provider, marketing ROI tracking, export to PDF and CSV, and scheduled report delivery via email.

*Source: positioning-strategy.md, platform-architecture.md*

### 10.5 PayPal Integration

Adding PayPal as a third payment gateway option alongside Stripe and Paystack, expanding payment method coverage for international customers.

*Source: positioning-strategy.md, platform-architecture.md*

### 10.6 Time Tracking

Logging billable hours against bookings and services, with the ability to generate invoices from tracked time. Useful for consultants and advisors who bill by the hour.

*Source: platform-architecture.md, authoritative-schema.md*

### 10.7 Custom Segment Builder

An advanced segment builder that allows more complex filtering by tags, status, spend, booking history, and other criteria beyond the pre-built segments available in V1.

*Source: positioning-strategy.md, platform-architecture.md*

### 10.8 Additional Homepage Templates

Expanding beyond the four V1 homepage templates with additional layout options for businesses that want more variety in their public presentation.

*Source: positioning-strategy.md*

### 10.9 Optional Marketplace Presence

An opt-in marketplace feature where Mybizz businesses can choose to be listed for discovery exposure — a choice, not the default model. This addresses the marketplace advantage (discovery) while preserving the Mybizz ownership model.

*Source: positioning-strategy.md*

### 10.10 Dark Mode Support

A dark colour scheme for the management interface, reducing eye strain during extended use. Deferred from V1 design direction.

*Source: design-direction.md*

### 10.11 Customer Portal Self-Service

A dedicated customer-facing layout where end customers can view their own bookings, invoices, reviews, support tickets, and account settings. This is the front-end for the Client Portal (10.3).

*Source: scaffold-spec.md (CustomerLayout), architecture.md*

### 10.12 Multi-Source Video Support

Extending video embed support beyond YouTube to include Vimeo, Dailymotion, and custom video URLs. In V1, only YouTube is supported.

*Source: scaffold-spec.md, DESIGN.md*

### 10.13 Mybizz Management Platform

A separate internal platform for Mybizz operators to manage client provisioning, health monitoring, billing automation, update distribution, and consolidated visibility across all client businesses.

*Source: platform-architecture.md*

### 10.14 Platform Capacity Expansion

Expanding beyond the V1 capacity of 100 client businesses, enabling the platform to scale to a larger client base as the business grows.

*Source: positioning-strategy.md*

##  11. The Five-App Structure
All five apps live on a single mybizz Anvil account on the Business plan (~$89/month). Each has a distinct, non-overlapping role.

App 1: mb-3-cs — Development Workspace
Role: All feature development happens here.
Contains: All 44 Data Tables (for dev/testing), all UI forms, all server modules, all application code.
Updated via: Daily development; each release is tagged here.
Key rule: This is the authoring environment, not the publishing environment. It is never a dependency for any client instance.

App 2: master_template — The Published Dependency
Role: Sole source of all server-side logic AND all UI forms for every client instance.
Contains: All server modules + all forms + all UI.
Updated via: the developer pushes App 1's tagged release into its repository (Step 42).
Key rule: When a client instance calls anvil.server.call('function_name'), the function executes from master_template's server modules. When a client instance displays a form, that form is served from master_template via the dependency. master_template is never given to clients directly — they depend on it.

App 3: blank_client_template — Provisioning Clone Source
Role: The sole clone source for creating new client instances.
Contains: All 44 Data Tables (correct schema, no data) + one startup module + master_template as dependency. Zero server modules. Zero forms.
Updated via: Only when schema changes occur.
Key rule: Anvil cannot create tables programmatically, so cloning this app gives each new client the required 44-table schema without copying any server code or forms (which would break the update model). Anvil Secrets are left empty — set per instance at provisioning.

App 4: [client-name]-cs — Client Instances (one per client)
Role: Each paying client gets a dedicated, isolated app instance.
Contains: 44 Data Tables + one startup module + master_template dependency (active). That's it — nothing else.
Updated via: Automatic — server logic, UI, and forms all propagate via the dependency. Schema changes are manual only.
Critical rules:
Must never contain server modules — Anvil runs the instance's own code instead of the dependency, permanently breaking update propagation.
Must never contain forms — same precedence problem applies to UI.
The startup module is the only client-side code allowed.

App 5: Mybizz_management — Platform Operator Tooling
Role: Internal tooling for mybizz as the platform operator.
Contains: Client registry, monitoring dashboard, billing status, update distribution controls.
Status: Deferred — built post-launch.
Key rule: This app does NOT depend on client instances (inverted architecture). It communicates with clients via bidirectional HTTP endpoints secured with per-client Bearer tokens.

---

## 12. Closing Summary

Mybizz represents a fundamentally different approach to running a service business online. Instead of forcing business owners to assemble a patchwork of disconnected tools, it delivers everything they need in a single platform — designed from the ground up to work together, priced transparently, and built to give the business owner complete ownership of their data, their clients, and their brand.

The platform is built on Google's Material 3 design system with MUI-inspired refinements, giving every business a clean, professional, and modern online presence without requiring any design work. The four brand palettes, four homepage templates, three landing page templates, and three blog post templates provide genuine flexibility within a consistent, high-quality design framework.

Every feature — from the booking system to the email marketing engine to the Clause Builder for legal policies — is designed to be operated without technical knowledge, with tooltips, contextual help videos, and a guided onboarding flow that gets the business live as quickly as possible.

Mybizz is live at **mybizz.live**. The platform is built. The market is waiting.


---

*Document owner: Mybizz founder*
*Status: Comprehensive developer reference treatise — covers all V1 features, user flows, and V2 roadmap*
*Based on: positioning-strategy.md, platform-architecture.md, scaffold-spec.md, DESIGN.md, [[[mb-3-cs-project-context:docs-local/releases/v2/mybizz-ai-implementation-spec]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/releases/v2/mybizz-ai-implementation-spec.md), and other authoritative project documents*
*To be updated as features are added or product positioning evolves*
