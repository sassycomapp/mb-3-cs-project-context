---
title: "mybizz-ai-implementation-spec"
doc-id: "mb-c145dbbc"
updated: "2026-11-01"
---
# Mybizz AI Implementation Specification

This document specifies the AI and voice layer of Mybizz CS, its sample data and the Wave accounting integration, all built as part of Mybizz CS. Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct.

---

## Purpose

This document defines the architecture and rollout plan for the reusable AI assistance layer of the Mybizz application, including a fully integrated voice module and the Wave accounting integration. The goal is to introduce AI and voice in a way that increases user value, minimises duplicate build effort, and preserves the existing workflow structure of the app.

The strategy is to build one reusable AI helper component that serves multiple AI features through a shared interaction model, rather than building separate standalone AI tools inside each page. The AI and voice layers are post-core enhancements: they are implemented after the core application has been coded and tested.

---

## Objectives

The AI and voice feature set achieves six outcomes:

- Deliver high perceived value to end users early, especially during onboarding and setup.
- Reuse one architectural pattern across all AI assists so that the first implementation does the heavy lifting for the rest.
- Keep users in context by offering AI assistance in a floating window instead of forcing separate navigation flows.
- Make future AI expansion simple by allowing additional assists to be added through the same shared framework.
- Show every data-dependent AI feature working from a client's first day, through sample data the client clears with one button.
- Provide a complete voice interaction loop (speak → understand → respond) that works on any browser, any device, with no additional cost to the client.

---

## Architectural Model

### Core concept

The architecture is a single reusable AI helper custom component used across the app. Users interact with one trigger button placed in relevant forms, which opens a floating AI window containing a menu of available assists, a dynamic working area, and an output area for generated results.

This architecture is preferable to embedding separate AI implementations into individual pages because it centralises the integration point, reduces maintenance effort, and allows the same component to serve all ten assists. It also keeps the user on the current page, which preserves workflow continuity and reduces interface disruption.

Voice input is an additional input modality wired into the same component: the user speaks instead of typing, but the text flows through the same shared AI request pipeline. Voice does not require a separate component or a separate integration point.

### Component responsibilities

The AI helper component contains the following responsibilities:

- A reusable trigger button that can be dropped into any relevant interface.
- A floating modal or panel that opens without forcing page navigation.
- A menu or selector listing the available AI assists.
- A dynamic content region that loads the selected assist's fields, instructions, and result view.
- A shared API request function used by every assist.
- A copy-to-clipboard action on every result so users can immediately reuse generated output.
- A microphone button wired to the Groq Whisper API for voice input.
- A text-to-speech output function: browser SpeechSynthesis by default, and Groq TTS for the natural-sounding voice.

### Context awareness

The helper detects the page or workflow where it was opened and preselects the most relevant AI assist automatically. For example, the invoicing area opens with the invoice message generator preselected, while a profile or onboarding context defaults to the bio/profile writer.

The user is not locked into one assist. The menu still allows switching, but the initial default feels intelligent and reduces friction.

---

## Voice Module — WhatsUp

### What it is

WhatsUp is the Mybizz mobile voice interaction feature. The business owner opens Mybizz on their phone browser, taps a microphone button, speaks a command or query, and Mybizz responds — in text and optionally spoken aloud.

No WhatsApp. No Meta. No additional integration. No cost to the client.

### Why WhatsUp and not WhatsApp

WhatsApp integration requires a Meta Business account, a verified phone number registered with Meta, a paid API tier, and ongoing compliance with Meta's policies — a real integration, real ongoing cost, and dependency on a third party the platform does not control. WhatsUp delivers the same underlying capability (quick voice capture away from the desk) through the browser, within the existing architecture, at zero additional cost.

WhatsUp can be bookmarked as a home screen shortcut on the user's phone for one-tap access — effectively an app-like experience without a separate app.

### Technical stack

| Component | Technology | Cost |
|---|---|---|
| Speech to text | Groq Whisper API | Mybizz absorbs — negligible at scale (about $0.02 per client per month at typical usage) |
| Text to speech | Browser SpeechSynthesis API | Zero — browser-native |
| Intent processing | The shared AI request pipeline | No additional cost |
| Data retrieval | Existing Anvil server functions | No additional cost |

### The complete voice loop

```
User speaks →
Groq Whisper transcribes (server-side, private) →
AI layer interprets intent →
Mybizz retrieves data / creates task / navigates →
Response text generated →
Browser text-to-speech speaks the response back to the user
```

### Browser compatibility

Groq Whisper handles transcription server-side, so speech-to-text has no browser dependency. Browser SpeechSynthesis for text-to-speech is supported across Chrome, Edge, Safari, and Firefox on mobile. The voice module works on any modern browser without requiring Chrome specifically.

---

## Voice Tiers

Mybizz CS is built with voice tiers 1 to 5.

### Tier 1 — Voice input to existing AI assists

The simplest and most immediate value. Instead of typing into the AI helper fields, the user speaks. The spoken text is transcribed by Groq Whisper and passed into the AI helper as if typed. No additional AI logic is required beyond wiring the microphone.

**Examples:**
- "Write a bio for my yoga studio in Cape Town, focused on mindfulness and corporate wellness"
- "Generate a service description for a 60-minute deep tissue massage, R450, includes aromatherapy"
- "Draft a blog post about the top five benefits of regular reflexology"
- "Write an invoice message for Naledi's outstanding balance — professional but warm"
- "Create an FAQ for my legal consulting service"

All ten AI assists are voice-addressable.

### Tier 2 — Voice task creation (WhatsUp core)

The user speaks a task from anywhere — at their desk or on their phone. Mybizz creates the task directly. This is the WhatsUp core use case.

**Examples:**
- "Create a task — call Priya to confirm her appointment on Thursday"
- "Remind me to follow up with David about his proposal"
- "Add a task to update the services page before end of week"
- "Task — send Naledi her invoice before Friday"
- "Follow up with the new lead from the website"

### Tier 3 — Voice queries with spoken response

The user asks Mybizz a question and receives a spoken answer. These are read-only, single-data-point queries against the core application's data. The response is spoken back via browser text-to-speech.

**Schedule and bookings:**
- "What is my schedule for tomorrow?"
- "What bookings do I have this week?"
- "What time is my next appointment?"
- "Who is my first client today?"
- "Do I have any bookings on Friday?"

**Payments and revenue:**
- "What payments came in today?"
- "What is my revenue for this week?"
- "Which invoices are outstanding?"
- "Has Naledi paid her invoice?"

**Tasks:**
- "What tasks do I have due today?"
- "What is on my to-do list?"
- "Do I have any overdue tasks?"

**Clients:**
- "When did Sarah last book?"
- "How many clients do I have?"
- "What is Thabo's contact number?"

**Navigation shortcuts:**
- "Open my bookings"
- "Show me unpaid invoices"
- "Take me to my email campaigns"
- "Open the blog editor"

### Tier 4 — Voice form filling

The user speaks a complete record. Mybizz parses the spoken input into structured data and fills in the relevant form for the user to review and confirm. The user does not type anything: the user speaks, reviews and confirms.

**Examples:**
- "New booking — Sarah Johnson, Swedish massage, Friday 2pm" → the booking form is filled in; the user confirms
- "New contact — Thabo Nkosi, thabo at gmail dot com, referred by Naledi" → the contact form is filled in
- "Create invoice for 3 hours consulting at R800 per hour for Makena Ltd" → the invoice is filled in

Tier 2 (task creation) fills one field for one action. Tier 4 extracts several values from natural speech and maps each to a specific form field, so each supported form has its own extraction prompt.

**Supported forms:** booking, contact, invoice, task, quote and deal.

**Build requirements:**
- One extraction prompt per supported form, returning the form's fields as structured data
- Fill-in logic that maps each extracted value to its form field and matches names to existing records (a spoken customer name to the contact, a spoken service name to the service)
- A confirmation step before saving: the user always reviews before anything is saved
- A partly filled form, never an error, when the speech does not give every field; the missing fields are highlighted

### Tier 5 — Business intelligence by voice

The user asks questions that need analysis across the client's data: comparisons, trends, rankings and advice. Mybizz answers in text and speech.

**Revenue intelligence:**
- "What is my best performing service by revenue this month?"
- "How does this month's revenue compare to last month?"
- "Which service has the most cancellations?"
- "What is my average booking value?"

**Client intelligence:**
- "Who are my top 5 clients by spend?"
- "Which clients haven't booked in more than 60 days?"
- "What is my client retention rate?"
- "Which clients came from my landing page?"

**Marketing intelligence:**
- "How many leads came in this week?"
- "Which campaign had the best open rate?"
- "How many blog posts have I published this month?"

**Predictive and advisory:**
- "Am I on track to hit last month's revenue?"
- "Which days of the week are my busiest?"
- "What time slots are most popular?"
- "Should I open more availability on Thursdays?"

**How an answer is produced:**
1. The AI layer maps the question to one of a fixed set of analysis functions (revenue by period, revenue by service, top clients, lapsed clients, retention, lead sources, campaign results, busiest days, popular time slots, month-to-date progress).
2. The analysis function runs in the client instance's server code against the client's own tables and the pre-computed summary tables, and returns figures.
3. The figures go to the AI layer with every person's name replaced by a placeholder label; the AI layer writes the answer in plain language; the instance puts the names back before the answer is shown and spoken.
4. A question that matches no analysis function receives a clear spoken message naming the kinds of questions Mybizz answers.

When the client's real data is too thin for a credible answer, the answer says so and names what is needed ("Mybizz needs at least three months of bookings to compare weekdays").

## Deeper Voice Features

Three features go beyond single question-and-answer voice.

### Proactive voice briefings

Mybizz speaks to the user without being asked. On opening the app — or via the WhatsUp mobile shortcut — Mybizz delivers a short spoken morning brief covering what matters right now. No query is needed. The system decides what is worth saying based on the user's data.

**Brief content:**
- Today's booking schedule
- Outstanding invoices
- Overdue tasks
- New leads received since yesterday
- Any flagged items requiring action

**Design principle:** the brief is concise — 20 to 30 seconds spoken. Not a data dump. Curated, prioritised, actionable.

**Build requirements:**
- A brief-generation server function that queries across bookings, invoices, tasks, and leads
- A morning brief prompt that converts the data into a natural-sounding spoken summary
- Text-to-speech delivery via browser SpeechSynthesis, or Groq TTS where the natural-sounding voice is switched on
- A user setting to enable or disable the automatic brief

### Conversational follow-up (multi-turn voice)

Tiers 1 to 3 are single-shot — one question, one answer. Conversational follow-up adds multi-turn conversation within a voice session. The user can ask a follow-up question in the context of the previous answer.

**Example:**
- "What payments came in today?" → *[Mybizz answers: R2,400 from three clients]*
- "And what's still outstanding from last week?" → *[Mybizz answers in context]*
- "Send Naledi a reminder" → *[Mybizz creates the task]*

**Build requirements:**
- Session memory within the voice interaction — a short context window held in the component state
- The AI layer receives the prior exchange as context when composing each new response
- The session terminates on explicit close, timeout, or navigation away from the current form

### Natural-sounding voice

Browser SpeechSynthesis is the default voice — functional but robotic. The natural-sounding voice uses Groq TTS and is built as part of Mybizz CS. Mybizz switches a client to the natural-sounding voice where the browser's own voice is a barrier to using voice.

**Cost:** Groq TTS pricing is in the same negligible range as Groq Whisper and is absorbed by Mybizz.

---

## AI Feature Inventory

The ten AI assists and their implementation order:

| Priority | Service | Rationale |
|---|---|---|
| 1 | Bio/profile writer | Highest perceived value, easiest to build, strong onboarding impact |
| 2 | Service description generator | Useful during setup, saves client time, straightforward prompt pattern |
| 3 | FAQ generator | Naturally derived from service descriptions with little extra client effort |
| 4 | Proposal generator | Strong direct revenue value, obvious premium-feeling feature |
| 5 | Blog post drafts | Strong fit with content publishing and social/blog workflows already present in the app |
| 6 | Email response suggestions | Everyday utility feature that keeps users inside the application |
| 7 | Invoice message generator | Small but polished feature that improves professionalism |
| 8 | Terms and conditions plain language explainer | Good strategic fit with clause-builder functionality |
| 9 | Client sentiment summary | Works on sample data from the client's first day; on real data once the client clears the sample data |
| 10 | Booking and availability insights | Works on sample data from the client's first day; on real data once the client clears the sample data |

All ten assists are voice-addressable via Tier 1 from the moment voice is implemented.

---

## Rollout Phases

### Phase 1 — Foundation

Phase 1 focuses on the reusable framework rather than breadth of feature count. The main deliverable is the AI helper component shell: trigger button, floating window, assist selector, shared request pipeline, result handling, clipboard support, and Groq Whisper microphone input wired to the text fields.

This phase establishes the canonical data flow for every assist: capture inputs (typed or spoken), compose prompt, call the chosen model, return structured text, show result, allow copy, and allow user editing and saving into the relevant workflow. Once this loop works well, the rest of the rollout becomes mostly repeatable prompt and field design work.

Voice Tier 1 is delivered in this phase, since it requires only wiring the microphone to the existing input fields.

### Phase 2 — First-value assists and WhatsUp task creation

The first live assist is the bio/profile writer, because it combines high user appeal with low implementation complexity. Service description generation and FAQ generation follow quickly, because they fit onboarding and setup workflows and reuse very similar prompt handling.

Voice Tier 2 (task creation) is delivered in this phase alongside the task-related AI assists. The mobile-optimised WhatsUp shortcut is published as a bookmarkable browser link.

The goal of this phase is to validate the interaction model, prompt quality, and save-back workflow while delivering clear end-user value early.

### Phase 3 — Revenue and workflow assists plus voice queries

Once the first-value assists are stable, the next wave covers proposal generation, blog post drafts, email response suggestions, invoice message generation, and the plain-language terms explainer. These features extend AI from onboarding into recurring daily workflow usage.

This phase turns AI from a novelty into an ongoing retention feature inside the product.

Voice Tier 3 (spoken queries with text-to-speech responses) is delivered in this phase, adding the data retrieval and spoken response layer on top of the established voice foundation.

### Phase 4 — Data-dependent intelligence and deeper voice

The sample data, client sentiment summaries, booking and availability insights, voice tier 4 (voice form filling) and voice tier 5 (business intelligence by voice) are delivered in this phase. The sample data is built first, because every data-dependent feature is built and shown on it.

The deeper voice features — proactive morning briefings, conversational follow-up and the natural-sounding voice — are delivered in this phase, on top of the established voice foundation.

### Phase 5 — Wave accounting integration

The Wave integration is delivered last, because it serves only the clients who use Wave.

---

## Data-Dependent Assists

Assists 9 and 10 and voice tier 5 sit in the same shared AI helper architecture. All three work on the sample data from the client's first day, and on the client's real data once the sample data is cleared. On real data that is too thin for a credible result, each says so and names what is needed.

### Client sentiment summary (AI assist 9)

Analyses stored communication history and client interaction patterns to produce a sentiment summary for a given client. Helps the business owner understand the health of a client relationship before a meeting or before sending a proposal.

**Credible on real data when:** the client has at least 3 months of activity in Mybizz CS and stored communication with the customer concerned.

### Booking and availability insights (AI assist 10)

Analyses booking history and patterns to surface insights about availability, peak demand, underused slots, and service popularity. Helps the business owner make decisions about scheduling and service offerings.

**Credible on real data when:** the client has at least 3 months of booking history.

---

## Sample Data

Every new client instance starts with a full set of sample data, so the dashboard, reports, AI assists and voice questions show real-looking results from the client's first day.

### What the sample data holds

Three months of activity for a business in the client's own trade, in the client's system currency and app language: contacts, companies, services, bookings, packages, orders, time entries, quotes, deals, invoices and payments, email campaigns with results, leads from several sources, tasks, blog posts and reviews. The summary tables are computed from it, so every dashboard figure is filled.

### How the sample data is kept apart

- Every sample row is recorded in one table, `sample_rows` (table name and row id), so the instance always knows which rows are sample data.
- Sample contacts use addresses at `example.com`, a domain reserved for examples that delivers no email. Mybizz CS sends no email, reminder or campaign to a sample contact, takes no payment for a sample invoice, and sends no sample event to Wave.
- While sample data exists, every screen shows a banner: "You are viewing sample data. Clear sample data."

### Clearing the sample data

- The Owner clears the sample data with one button: **Clear sample data**, on the dashboard banner and in Settings.
- A confirmation dialog states that the step cannot be undone.
- A background task deletes every row recorded in `sample_rows`, then empties `sample_rows` and recomputes the summary tables from the client's real data.
- Rows the client created stay untouched. A real record that links to a sample record (a real booking for a sample service) keeps its own data, and the link is emptied.
- The banner disappears when the task completes, and the activity log records who cleared the sample data and when.

---

---

## Wave Accounting Integration

### What it is

Wave is a free accounting platform widely used by small and solo businesses — freelancers, consultants, coaches, trades — exactly the Mybizz target market. Wave handles bookkeeping, profit and loss, tax preparation, and financial reporting in a way that Mybizz does not and does not try to replicate. The two platforms are naturally complementary rather than competitive.

Mybizz manages the operational side of a business: bookings, clients, invoices, payments, marketing. Wave manages the financial recording side: accounting, ledger entries, tax categories, and reports. The gap between them is the manual data entry a business owner otherwise has to do themselves — copying paid invoices from Mybizz into Wave, re-entering customer records, manually recording revenue. That is the gap the integration closes.

### The concept

The integration is intentionally lean — a one-way, event-driven push from Mybizz to Wave. Mybizz does not pull data from Wave. Mybizz does not try to replace Wave's accounting features. Mybizz tells Wave what happened, at the moment it happened, so the business owner does not have to.

**The three events pushed:**

**1. Invoice paid** — when a Mybizz invoice is marked paid (manually or via Stripe/Paystack callback), the payment is recorded in Wave automatically. The correct customer record is created or matched in Wave if it does not already exist.

**2. New customer** — when a new client is created in Mybizz, a matching customer record is created in Wave. The business owner's client database and their Wave accounting contacts stay in sync without manual duplication.

**3. Refund issued** — when a refund is processed in Mybizz, the corresponding adjustment is recorded in Wave. This is the one event that, if missed, creates the most accounting headaches.

That is the entire integration scope. Three triggers. Three push events. No sync UI, no mapping configuration screens, no two-way data flow. The user connects their Wave account once, and the three events flow automatically from that point forward.

### Why this fits Mybizz's design philosophy

The integration is additive, not transformational. It does not change how Mybizz works. It does not change how Wave works. It eliminates one repetitive manual task — copying financial events between two systems — without adding a new tool, a new subscription, or a new concept for the business owner to learn.

Wave's free plan is genuinely free for accounting. The integration therefore adds accounting capability to Mybizz's offering at zero additional cost to the client. The Mybizz value proposition — one platform, one price — extends naturally: "and if you use Wave, your accounts update themselves."

### Connection model

The client connects their Wave account once through the Mybizz Settings Hub — the same pattern as the Vault and payment gateway configuration. Credentials are stored in the Vault. The connection is visible, but the mechanics are invisible from that point on.

The business owner never needs to think about it again.

### Explicit scope boundaries

The following are deliberately out of scope:

- Mybizz does not pull financial data from Wave — no balance displays, no Wave reports inside Mybizz
- Mybizz does not attempt to replace any Wave accounting feature
- Mybizz does not sync backwards — Wave changes do not affect Mybizz records
- The integration is optional — clients who do not use Wave are unaffected

### Build order

Wave has a live API and the technical path is clear. The integration is built last in the AI and voice rollout (Phase 5), because it serves only the clients who already use Wave, while the AI and voice features serve every client.

---

## Data Flow and Integration Pattern

Each AI assist follows the same underlying execution pattern:

1. Collect user input from either the current form context or the floating helper input fields (typed, or spoken via Groq Whisper).
2. Compose a prompt using page context plus user-supplied data.
3. Send the request through one shared AI request function.
4. Display the generated response inside the floating helper result area.
5. Speak the response via browser SpeechSynthesis where appropriate (Tier 3 queries).
6. Allow the user to copy, edit, and save the result into the appropriate application field or workflow.

The build advantage of this pattern is that the code structure repeats while prompts and field mappings change by assist type. That is why the first assist is the hardest and the rest become progressively easier to add.

---

## Screen and UX Considerations

The AI helper feels like a contextual assistant rather than a separate subsystem. It is reachable anywhere it is relevant, but it does not dominate core workflows or interrupt form completion unnecessarily.

The floating-window approach is central to that experience, because it keeps the current page visible and lets the user remain grounded in the task they were already performing. The component supports quick open, quick close, assist switching, result copying, voice input, spoken output, and clean handoff back into the host form.

The result interaction always includes:

- Readable generated output.
- One-click copy to clipboard.
- The ability to edit before saving or using the result.
- Clear action choices such as replace, insert, append, or cancel where relevant.
- An optional text-to-speech playback button for spoken output.

### WhatsUp mobile UX

On mobile, voice interaction is the primary mode. The user opens Mybizz via a home screen bookmark, taps the microphone, speaks, and hears the response. The floating AI helper adapts to the mobile viewport. No app install is required.

---

## Host Contexts

The host areas for the rollout are the areas associated with the assist list: onboarding and profile setup, services configuration, FAQs, proposals, blog and social publishing, email-related workflows, invoicing, and clause-related legal explanation areas.

Each host form uses the same helper button and the same floating component, with contextual defaults varying by form. This is the architectural principle of one component, one integration point, many assist types.

---

## Technical Approach

### Shared service layer

One shared AI request layer (`server_ai/`) handles prompt submission, model invocation, result return, and error handling consistently across the app. Every AI and Groq request is routed through the Mybizz management app (App 5), which holds Mybizz's AI provider keys and records each client's AI usage; client instances hold no Mybizz AI keys. The Groq Whisper transcription call is handled server-side within this same layer: audio bytes are sent to the Anvil server module, passed through App 5 to Groq, and the resulting text is returned to the client before being passed into the AI helper prompt pipeline.

This keeps model choice and request logic centralised, making it easy to improve prompts, swap providers, or add common safeguards without touching every host form.

### Prompt modularity

Each assist is defined by:

- its assist identity,
- its required input fields,
- its context mapping,
- its prompt template,
- and its result-handling behaviour.

New assists are added by defining a new menu item and its associated prompt and configuration, not by rebuilding the component shell.

### Save-back logic

Generated content does not remain isolated in the helper. It transfers easily into the relevant workflow field, either through direct insertion or through user-controlled copy, edit and save actions. AI output feels embedded in normal business workflows rather than like a detached experiment.

### Cost model

| Service | Provider | Paid by | Estimated cost at 100 clients |
|---|---|---|---|
| AI text generation | LLM provider (configurable) | Mybizz | Depends on usage |
| Speech to text | Groq Whisper API | Mybizz | About $2 per month in total |
| Text to speech (default) | Browser SpeechSynthesis | No one | Zero |
| Text to speech (natural voice) | Groq TTS | Mybizz | Same negligible range as Groq Whisper |
| Morning briefings | LLM + Groq | Mybizz | One briefing per user per day |
| Conversational follow-up | LLM | Mybizz | Minimal — short context window |
| Assists 9 and 10 | LLM | Mybizz | Higher token use — larger data context |
| Voice tier 4 (form filling) | LLM | Mybizz | Slightly higher token use than a tier 3 query |
| Voice tier 5 (business intelligence) | LLM | Mybizz | Low — the instance computes the figures; the LLM only writes the answer |
| Wave integration | Wave API | No one | Wave's free plan |

No additional cost passes to the client for any AI, voice or Wave feature.

---

## Delivery Estimate

The reusable helper architecture plus all ten assists is estimated at roughly three to four days including prompt tuning, because the architecture avoids rebuilding the same pattern repeatedly. Adding the voice module (Groq Whisper wiring, text-to-speech and Tier 1–3 capability) adds approximately one more day, given the shared pipeline architecture. The deeper voice features, assists 9 and 10, voice tiers 4 and 5, the sample data and the Wave integration are estimated separately when their phases are planned.

The credibility of this estimate depends on keeping the design centralised and resisting the temptation to build bespoke page-specific AI tools. The first assist consumes the most effort, because it establishes the API connection, result-handling logic and UX conventions for the rest. After that, expansion is much more mechanical.

---

## Risks and Design Guardrails

- AI and voice remain enhancement layers, never dependencies for core app operation.
- Data-dependent features run on sample data until the client clears it; on thin real data they say so instead of guessing.
- Voice tier 4 never saves without the user's confirmation.
- Voice tier 5 sends the AI layer figures and placeholder labels only, never a person's name.
- The UI preserves user context rather than redirecting people into separate AI pages.
- The architecture stays centralised so future additions do not multiply maintenance overhead.
- Prompt quality and output usability matter more than feature count.
- Voice queries (Tier 3) fail gracefully: if the data cannot be retrieved, Mybizz responds with a clear spoken message rather than silence or an error code.

---

## Development Sequence

1. Reserve clean architectural space in relevant forms for AI helper usage.
2. Build the reusable AI helper component shell and shared request pipeline, including Groq Whisper microphone input.
3. Implement the bio/profile writer as the first production assist (voice-addressable immediately).
4. Add service description and FAQ generation.
5. Deliver WhatsUp task creation (Tier 2) and the mobile shortcut.
6. Expand into proposals, blog drafts, email suggestions, invoice messaging, and terms explanation.
7. Add Tier 3 voice queries with the spoken response layer.
8. Build the sample data, the `sample_rows` record and the Clear sample data button.
9. Add sentiment summary and booking insights, built and shown on the sample data.
10. Add voice tier 4 (voice form filling) for booking, contact, invoice, task, quote and deal.
11. Add voice tier 5 (business intelligence by voice) with its analysis functions.
12. Add the morning briefing, conversational follow-up and the natural-sounding voice.
13. Add the Wave accounting integration.

---

## Outcome Target

Implemented this way, Mybizz gains a coherent AI layer that feels integrated, premium and expandable rather than bolted on. The product value comes not only from the ten individual assists, the voice module and the Wave integration, but from the fact that they are delivered through one elegant reusable assistant model that is easy to place throughout the app.
