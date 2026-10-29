---
title: "ceo-review-2026-06-15"
doc-id: "mb-586b5956"
updated: "2026-10-29"
---
# CEO Review — Mybizz CS V1

**Date:** 2026-06-15
**Mode:** HOLD SCOPE — validation and alignment only
**Context:** 41 ADRs, 43 tables, 50 wireframes, 6-phase build plan. Phase 0 complete, Phase 1 in progress.
**Previous reviews:** CEO review (2026-05-26) approved. Sanity audit (2026-06-01) resolved 21 issues. CEO fix plan (2026-06-14) found 18 mechanical gaps.

---

## Section 1: Architecture Review

**Verdict: Sound. No architectural issues.**

The five-app dependency model (mb-3-cs → master_template → blank_client_template → client instances → Mybizz_management) is well-designed and verified by live testing (ADR-28, ADR-29, ADR-30, ADR-41).

**What works:**
- Data isolation is structural — each client is a separate Anvil app. Cross-client access is architecturally impossible.
- Code propagation is automatic — merge `develop` → `stable` releases to all clients.
- Master template is stateless — zero client data.
- `app_tables` resolution verified — server modules in master_template resolve to the calling client's tables.

**Failure modes identified:**

| Failure | Impact | Mitigation | Status |
|---|---|---|---|
| Schema change without migration plan | Breaks existing clients | ADR-30 mandates documented migration plan before merge to stable | Documented, enforced by process |
| Accidental code in client instance | Breaks dependency update model | ADR-28: client instances contain only tables + startup module | Documented, needs runtime check |
| Branch tracking drift | Client on wrong branch | ADR-29: version tags for staged rollout | Documented |
| Anvil account suspension | All clients down | Single account risk — no mitigation in docs | **Gap: no documented recovery plan** |

**One concern:** The Anvil Business plan hosts all 100 clients on one account. If that account is suspended or compromised, all clients go down simultaneously. The docs don't address this single-point-of-failure. This is an operational risk, not an architectural one — but it should be documented in [[[mb-3-cs-project-context:docs-standard-local/deployment-procedures|Deployment Procedures]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-standard-local/deployment-procedures.md).

**Action needed:** Add a brief section to [[[mb-3-cs-project-context:docs-standard-local/deployment-procedures|Deployment Procedures]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-standard-local/deployment-procedures.md) covering Anvil account-level failure scenarios and recovery.

---

## Section 2: Error & Rescue Map

**Verdict: Mostly mapped. Two gaps.**

The build plan's Phase 0 TODOs address Vault error handling, test coverage, observability, and deployment procedures. ADR-034 documents Anvil platform constraints. ADR-038 covers webhook idempotency.

**Mapped error paths:**
- Vault: encryption/decryption failures → user sees "Unable to retrieve credential" + admin alert
- Payments: webhook signature verification failure → reject with 400 + log to `webhook_log`
- Payments: duplicate webhook → idempotency check → skip processing + log
- Brevo: API rate limit (429) → queue-based retry with exponential backoff
- Auth: failed login → rate limiting + account lockout after N attempts
- Bookings: double-booking → availability check rejects + shows "Slot no longer available"

**Gaps:**

| Gap | What's missing | Fix |
|---|---|---|
| `app_tables` returns empty on corrupted client instance | No documented behavior for when a client instance's tables are missing or corrupted | Add to ADR-034: "If `app_tables` resolution fails, show user a generic error and alert Mybizz_management" |
| Background task failure (ADR-036) | Seven background tasks documented but no failure/retry policy for each | Add per-task failure behavior to ADR-036 or build plan |

**Action needed:** Document `app_tables` failure behavior and per-background-task retry policy.

---

## Section 3: Security & Threat Model

**Verdict: Strong. One gap.**

**What's covered:**
- Vault: encrypted at rest, TOTP step-up on every access (ADR-003)
- Payment: gateway-agnostic service layer, webhook signature verification (ADR-038)
- Auth: rate limiting, account lockout, audit trail
- RBAC: per-tab governance on Settings, per-form governance throughout
- Secrets: stored in Vault only, never in code or config tables
- Data isolation: structural (separate apps), not application-level

**Gap:**

| Threat | Current state | Risk |
|---|---|---|
| Brevo credential compromise | Client creates their own Brevo account; Mybizz stores SMTP key and API key in Vault | If a client's Brevo account is compromised, attacker can send emails as that client. Mybizz has no monitoring for unusual Brevo activity. |
| Vault TOTP bypass | TOTP required on every open — no grace period | Strong. But what if Owner loses TOTP device? No recovery path documented. |

**Action needed:**
1. Document TOTP recovery path (e.g., Mybizz_management can reset TOTP after identity verification).
2. Note in integration-architecture.md that Brevo credential monitoring is a Mybizz_management concern (out of V1 scope but documented as intention).

---

## Section 4: Data Flow & Interaction Edge Cases

**Verdict: Core flows mapped. Edge cases need attention.**

**Happy path flows traced:**
- Lead capture → simultaneous `leads` + `contacts` creation (ADR-004)
- Booking flow → service → provider → date/time → meeting type → intake form → payment → confirmation
- Email campaigns → Brevo API → webhook → tracking
- Onboarding → currency selection → legal acknowledgement → Settings navigation

**Edge cases needing documentation:**

| Flow | Edge case | Current state |
|---|---|---|
| Booking | User navigates away mid-payment | No documented behavior — does the booking hold? Does it expire? |
| Booking | Slow connection during payment processing | No timeout handling documented |
| Onboarding | User closes browser mid-onboarding | ADR-023 says resumable, but no timeout/expiry documented |
| Contact dedup | Same email, different names | Build plan says "manual merge if confidence < 90%" — no UI spec for merge dialog |
| Currency immutability | First transaction is refunded/voided | Currency is immutable after first transaction — but what if that transaction is reversed? |
| Landing page | Lead submitted with duplicate email | ADR-004 says simultaneous creation — but does it update existing contact or reject? |

**Action needed:** Document the six edge cases above in the relevant implementation plans or ADRs.

---

## Section 5: Code Quality Review

**Verdict: Cannot evaluate — no code exists yet.**

Phase 0 is marked complete, but the actual codebase (`mb-3-cs`) needs inspection to verify. The build plan describes what should exist; this review validates the plan, not the implementation.

**One structural concern:** The build plan's completion criteria are well-defined, but there's no code review checklist for the implementer. For a solo developer, this means self-review — which is where bugs hide.

**Action needed:** Add a "self-review checklist" to each build plan phase — a list of specific things to verify before marking a stage complete.

---

## Section 6: Test Review

**Verdict: Test framework defined. Coverage gaps identified.**

The build plan specifies test files for each stage (`test_navigation.py`, `test_settings.py`, etc.) and Phase 0 TODO 2 addresses test coverage. Phase 5 TODO 2 defines the testing framework.

**Test coverage map:**

| Area | Test type | Status |
|---|---|---|
| Navigation | Unit tests | Specified in build plan Stage 1.3 |
| Settings | Unit tests | Specified in build plan Stage 1.4 |
| Vault | Unit tests | Specified in build plan Stage 1.5 |
| Bookings | Integration tests | Specified in build plan Stage 2.2 |
| Payments | Integration + webhook tests | Specified in build plan Stage 2.3 |
| Contact dedup | Unit tests | Specified in build plan Stage 3.1 |
| Email campaigns | Integration tests | Specified in build plan Stage 3.3 |
| Public pages | Responsive + accessibility | Specified in build plan Stage 4.1 |
| Analytics | Unit tests | Specified in build plan Stage 5.1 |

**Gaps:**

| Gap | What's missing |
|---|---|
| E2E booking flow | No end-to-end test spec covering the full happy path from service selection to payment confirmation |
| Error path tests | Build plan specifies happy-path tests but not error-path tests (payment failure, webhook timeout, Brevo 429) |
| RBAC coverage matrix | No test matrix showing which roles can access which forms/tabs — build plan says "RBAC coverage verified" but doesn't specify how |

**Action needed:** Add E2E test spec for booking flow, error-path test cases, and RBAC coverage matrix to the build plan.

---

## Section 7: Observability & Monitoring

**Verdict: ADR-032 defines the architecture. Implementation details missing.**

ADR-032 (Observability Architecture) and Phase 0 TODO 3 address observability. The build plan mentions logging and metrics.

**What's defined:**
- Activity log table for audit trail
- Webhook log for payment events
- Health log for instance health (ADR-036)
- Background task logging

**What's missing:**

| Concern | Gap |
|---|---|
| Alert thresholds | No documented thresholds for "degraded" vs "unhealthy" health status |
| Dashboard metrics | No spec for what the operator sees in Mybizz_management — just "deferred" |
| Log retention | No policy for how long logs are kept |
| Error rate monitoring | No documented approach for tracking error rates across client instances |

**Action needed:** Document health thresholds, log retention policy, and error rate monitoring approach. These are small additions to ADR-032 or [[[mb-3-cs-project-context:docs-standard-local/deployment-procedures|Deployment Procedures]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-standard-local/deployment-procedures.md).

---

## Section 8: Database & State Management

**Verdict: Schema is comprehensive. Two mechanical gaps remain.**

The authoritative schema has 43 tables across 8 groups. The sanity audit (2026-06-01) resolved most schema gaps. The CEO fix plan (2026-06-14) found 18 mechanical gaps.

**Remaining mechanical gaps (from fix plan):**

| Gap | Severity | Status |
|---|---|---|
| `bookings` table missing 12 columns in anvil.yaml | Critical | Documented in fix plan — needs implementation |
| `tasks` table missing `instance_id` | Critical | Documented in fix plan — needs implementation |
| `blog_posts` missing 5 columns from schema | High | Documented in fix plan |
| `business_profile` missing 7 columns from schema | High | Documented in fix plan |
| `email_templates.is_active` type mismatch (string vs bool) | High | Documented in fix plan |
| `webhook_log` schema vs ADR-038 mismatch | High | Documented in fix plan — needs reconciliation |
| 4 summary tables from ADR-040 not in schema or app | Medium | Documented in fix plan |
| `time_entries` scope conflict (V2 deferred vs documented in schema) | Low | Keep table, mark as V2 in schema |

**No new schema issues found in this review.** The fix plan's 18 gaps are the authoritative list.

**Action needed:** Execute the fix plan before Phase 1 implementation begins. The critical and high items must be resolved first.

---

## Section 9: API Design & Contract

**Verdict: No external API. Internal patterns are sound.**

Mybizz CS has no public API. All data access is server-side via Anvil server modules. The only external interfaces are:
- Webhook endpoints for payment gateways (Stripe, Paystack)
- Webhook endpoints for Brevo email events
- Public-facing pages served via Anvil Routing

**Webhook contracts:**
- Stripe: `[base_url]/api/webhooks/stripe` — signature verification required
- Paystack: `[base_url]/api/webhooks/paystack` — signature verification required
- Brevo: webhook events for email delivery status

**One concern:** The webhook log schema mismatch between ADR-038 and authoritative-schema.md (documented in fix plan) means the actual webhook contract is ambiguous. This must be reconciled before webhook implementation.

**Action needed:** Reconcile `webhook_log` schema between ADR-038 and authoritative-schema.md.

---

## Section 10: Performance & Scalability

**Verdict: Adequate for V1 (100 clients). V2 needs planning.**

**At V1 scale (100 clients):**
- Each client is a separate Anvil app — no shared database bottleneck
- Anvil Business plan supports unlimited apps, 1M rows, 100GB storage
- Background tasks run per-client — no cross-client contention
- Brevo free tier: 300 emails/day per client — sufficient for V1

**At 10x (1,000 clients):**
- Anvil account limits become the constraint — row/storage limits may be per-account (unresolved question in platform-overview.md Section 18)
- Schema migrations become a major operational burden (manual per-client)
- Brevo free tier insufficient — clients would need paid plans

**At 100x (10,000 clients):**
- Not feasible on current architecture without significant changes
- Manual schema migrations impossible at this scale
- Anvil hosting costs would be substantial

**The 100-client V1 cap is the right call.** The architecture is designed for this scale. The unresolved Anvil billing question (per-account vs per-app limits) is the one thing that could force a change.

**Action needed:** Resolve the Anvil billing question before Phase 2 (when real client data starts flowing).

---

## Section 11: Design & UX

**Verdict: Design system is comprehensive. Two gaps.**

ADR-027 (Design Rules), ADR-015 (Material 3 Theme), ADR-037 (UI Customization), and the design consultation deliverables define a complete M3 design system. 50 wireframes cover all screens.

**What's strong:**
- M3 design tokens and theme system
- 11-tab Settings with RBAC governance per tab
- Four homepage templates using shared components
- Mobile responsive strategy documented
- Accessibility patterns in wireframes (focus states, ARIA labels)

**Gaps:**

| Gap | Impact | Fix |
|---|---|---|
| Empty state specs missing | No documented behavior for empty dashboards, empty contact lists, empty booking calendars | Add empty state specs to build plan stages |
| Loading state specs missing | No documented behavior for slow server calls (ADR-034 notes Anvil server call latency) | Add loading state specs to build plan stages |
| Error state specs missing | No documented behavior for form validation errors, network failures | Add error state specs to build plan stages |
| Onboarding wireframe missing | High-priority item from sanity audit — still outstanding | Create `wireframe-onboarding-OnboardingForm.html` |
| Policy editor wireframe missing | Medium-priority item from sanity audit — still outstanding | Create policy editor wireframe |

**Action needed:** Create the two missing wireframes and add empty/loading/error state specs to the build plan.
**Action needed:** Add a brief section to [[[mb-3-cs-project-context:docs-standard-local/deployment-procedures|Deployment Procedures]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-standard-local/deployment-procedures.md) covering Anvil account-level failure scenarios and recovery.

---

## CEO Review Summary

**Mode:** HOLD SCOPE — validation and alignment only

**Strongest challenges:**
1. **18 mechanical schema gaps** (from fix plan) — 2 critical, 8 high. Must be resolved before Phase 1 implementation.
2. **6 undocumented edge cases** in core flows (booking interruption, onboarding timeout, contact merge UI, currency reversal, lead dedup).
3. **Missing empty/loading/error state specs** across all screens.

**Recommended path:**
1. Execute the CEO fix plan's 18 mechanical gaps (critical + high first).
2. Document the 6 edge cases in relevant ADRs or implementation plans.
3. Create the 2 missing wireframes (onboarding, policy editor).
4. Add empty/loading/error state specs to build plan stages.
5. Resolve the Anvil billing question (per-account vs per-app limits).
6. Then proceed with Phase 1 implementation.

**Accepted scope (unchanged):**
- V1: Auth, RBAC, Vault, Settings, Services, Bookings, Payments, Invoicing, CRM, Email Campaigns, Tasks, Blog, Public Website, Landing Pages, Analytics, Security, Compliance
- 43 tables, 41 ADRs, 50 wireframes, 5 custom components
- Dependency-based architecture, 100-client cap, Anvil Business plan
- Three-tier email (Anvil built-in + Brevo SMTP + Brevo Campaigns)
- Two payment gateways (Stripe + Paystack)

**Deferred (unchanged):**
- Client portal, advanced analytics, complex task automation, PayPal, time tracking, custom segment builder, Mybizz_management

**NOT in scope:**
- E-commerce, hospitality, membership verticals
- Multi-tenant architecture (dependency-based only)
- Public API
- Self-service provisioning (Mybizz_management handles this)

---

*End of CEO Review — 2026-06-15*
