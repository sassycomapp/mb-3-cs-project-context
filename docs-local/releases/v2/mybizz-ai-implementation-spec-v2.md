---
title: "mybizz-ai-implementation-spec-v2"
doc-id: "mb-d9fee77c"
updated: "2026-11-01"
---
# Mybizz AI and Voice — Voice Tiers 4 and 5

**Prerequisite:** the AI and voice layer in `mybizz-ai-implementation-spec.md` built, live and stable in production.

---

## Context

This document specifies the two voice capabilities added to Mybizz CS after it is live: voice tier 4 and voice tier 5. Everything else in the AI and voice layer is built as part of Mybizz CS and is specified in `mybizz-ai-implementation-spec.md`. Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct.

Tiers 4 and 5 are added later for two reasons:

1. They build on the voice tiers 1 to 3 foundation, which must be stable and proven first.
2. Tier 5 depends on meaningful production data volume before its answers are credible and useful.

---

## Voice Tiers 4 and 5

Mybizz CS is built with voice tiers 1, 2 and 3. These tiers are added later.

### Tier 4 — Voice-driven form pre-fill

The user speaks a complete record. Mybizz parses the spoken input into structured data and pre-fills the relevant form for the user to review and confirm. The user does not type anything — they speak, review, and confirm.

**Examples:**
- "New booking — Sarah Johnson, Swedish massage, Friday 2pm" → booking form pre-filled, user confirms
- "New contact — Thabo Nkosi, thabo at gmail dot com, referred by Naledi" → contact form pre-filled
- "Create invoice for 3 hours consulting at R800 per hour for Makena Ltd" → invoice pre-filled

**What makes this different from Tier 2:**
Tier 2 (task creation) is simple — one field, one action. Tier 4 requires entity extraction from natural speech and mapping extracted values to specific form fields. Each form type (booking, contact, invoice) needs its own entity extraction prompt design.

**Build requirements:**
- Per-form entity extraction prompt for each supported form type
- Pre-fill logic mapping extracted entities to Anvil form fields
- Confirmation step before saving — the user always reviews before committing
- Graceful fallback if extraction is incomplete — present a partially filled form, not an error

### Tier 5 — Aggregated data queries and intelligence

Complex queries requiring cross-referencing, trend analysis, or multi-step reasoning across the user's data. Tier 5 is added later because it requires meaningful data volume to produce credible answers, and because the AI query layer needs to be mature before adding this complexity.

**Tier 5 is created and tested with dummy data**, so that it is proven before real production data volume exists.

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

---

## Prerequisites Checklist

Before tiers 4 and 5 are built, all of the following are confirmed:

- [ ] The AI helper component is live and stable in production
- [ ] Groq Whisper integration is confirmed stable in production
- [ ] Voice tiers 1, 2 and 3 are confirmed working on mobile via the WhatsUp shortcut
- [ ] Meaningful data volume exists across active client accounts (a minimum of 3 months of production use before building tier 5 against real data)

---

## Cost Model

| Feature | Provider | Paid by | Notes |
|---|---|---|---|
| Tier 4 entity extraction | LLM | Mybizz | Slightly higher token usage per interaction than tier 3 queries |
| Tier 5 aggregated queries | LLM | Mybizz | Higher token usage — data context is larger |

No additional cost passes to the client.

---

## Expansion Before Build

Before tiers 4 and 5 are built, this specification is expanded to include:

- Full component specifications for each tier
- Wireframe and screen requirements
- Data flow diagrams for tier 4 entity extraction
- Detailed prompt designs for tier 5 intelligence queries
- ADR entries for any new architectural decisions
- Build plan integration

---

*Owner: Mybizz founder*
*Related: `mybizz-ai-implementation-spec.md`*
