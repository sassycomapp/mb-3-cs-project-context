---
title: "eng-review-2026-06-16"
doc-id: "mb-3e8f2a76"
updated: "2026-10-29"
---
# Engineering Review — Mybizz CS V1

**Date:** 2026-06-16
**Mode:** HOLD SCOPE — execution readiness validation
**Reviewer:** Build Agent (plan-eng-review)
**Inputs:** CEO review 2026-06-15, CEO remediation report, ADRs 28-41, build-plan.md, integration-architecture.md, authoritative-schema.md

---

## Confirmed Architecture (Do Not Re-Litigate)

These are sealed decisions verified by live testing. The engineering review operates within these constraints.

| Decision | ADR | Status |
|----------|-----|--------|
| Dependency-based, not multi-tenant | ADR-41 | Confirmed — Sealed |
| All forms and server modules in master_template only | ADR-28 | Confirmed |
| Client instances: Data Tables + startup module only | ADR-28 | Confirmed |
| `app_tables` resolves to client instance tables at runtime | ADR-28 | Confirmed — live tested |
| Branch-based update model (develop → stable) | ADR-29 | Confirmed |
| blank_client_template as provisioning clone source | ADR-30 | Confirmed |
| Content panel pattern for admin navigation | ADR-35 | Approved |
| globals.py for inter-form state | ADR-35 | Approved |
| reportlab for PDF invoice generation | ADR-039 | Approved |
| No client_id/tenant_id columns ever | ADR-41 | Confirmed — Sealed |

---

## Dilemma 1: Background Task Limits Per Client Instance

### The Problem

ADR-036 documents 7 scheduled/event-driven tasks per client instance. At 100 clients, that's ~600 scheduled tasks on one Anvil Business account. The questions from `docs/questions-to-raise-with-anvil.md` remain unanswered:

1. How many scheduled tasks per app on Business plan?
2. How many concurrent background tasks per app?
3. Do limits apply per app or per account?
4. Is there a daily/monthly execution cap?

### Current State

ADR-036 states: "Anvil Business plan verification required: Confirm that 600+ scheduled tasks across 100 apps on a single account is within limits. This is a blocking question before launch."

The remediation report added per-task failure/retry policies, but the fundamental capacity question is unresolved.

### Analysis

**Per-app vs per-account is the critical unknown.** If limits are per-app, each client instance is independently bounded and 100 clients is fine. If limits are per-account, 600 tasks may hit the ceiling.

**What we know from Anvil docs:**
- Business plan: unlimited background task timeout
- Business plan: scheduled tasks available
- Free/Personal: 30-second timeout, no scheduled tasks

**What we don't know:**
- Whether "unlimited" means truly unlimited or has a soft cap
- Whether concurrent task limits exist per account
- Whether task scheduling has per-account overhead

### Recommendation

**Action 1 (blocking):** Email Anvil support with the specific questions from `docs/questions-to-raise-with-anvil.md` Section 3. This must happen before Phase 2 implementation begins.

**Action 2 (mitigation):** Design the task schedule with graceful degradation. If per-account limits exist, the platform should degrade gracefully:

```
Priority 1 (must run):  Health heartbeat, webhook processing
Priority 2 (should run): Appointment reminders, campaign enrollment
Priority 3 (nice to have): Lifecycle updates, automated task generation
```

If task capacity is constrained, Priority 3 tasks can be consolidated into a single daily task that runs all three sequentially. Priority 2 tasks can be staggered across clients (not all clients at the same minute).

**Action 3 (architecture):** The health heartbeat (every 15 minutes, 100 clients = 9,600 executions/day) is the highest-volume task. If per-account limits exist, consider:
- Reducing heartbeat frequency to every 30 minutes (4,800/day)
- Using Mybizz_management polling (ADR-033 Pattern 3) instead of per-client heartbeats
- Batching health checks into a single Mybizz_management task that polls all clients

### Verdict

**Not blocking for Phase 1.** Phase 1 has no background tasks (auth, settings, Vault are synchronous). **Blocking for Phase 2** when booking reminders and webhook processing begin.

**Status:** TODO ITEM 8.13 (Anvil Billing Model Clarification) covers this. The Anvil support email is the critical path.

---

## Dilemma 2: Webhook Handling — Immediate 200 + Background Task Pattern

### The Problem

ADR-038 defines the webhook pattern: receive → validate signature → log to `webhook_log` → dispatch background task → return 200. This is architecturally sound, but has three unresolved concerns:

1. **`webhook_log` schema mismatch** — ADR-038 defines columns (`id`, `source`, `payload`, `status`, `received_at`, `processed_at`, `error_message`, `idempotency_key`) but the authoritative schema may differ. The CEO fix plan flagged this as a high-severity gap.

2. **Background task dispatch reliability** — If `anvil.server.launch_background_task()` fails (task queue full, server error), the webhook is logged but never processed. The HTTP handler already returned 200, so the gateway won't retry.

3. **Idempotency enforcement location** — ADR-038 shows idempotency checks inside `handle_payment_success()`, but the check should happen earlier — in the background task dispatcher or even in the HTTP handler — to prevent duplicate task dispatch.

### Analysis

**Schema mismatch:** This is a documentation reconciliation task, not an architecture problem. The authoritative schema is the source of truth. ADR-038's column definitions should be validated against it. TODO ITEM 8.12 tracks this.

**Dispatch reliability:** The current pattern has a gap:

```
HTTP handler receives webhook
  → validates signature (good)
  → adds row to webhook_log (good)
  → launch_background_task() (WHAT IF THIS FAILS?)
  → returns 200 (gateway thinks it's handled)
```

If `launch_background_task()` raises an exception, the row is in `webhook_log` with status `"pending"` but no background task will ever process it. The gateway got 200, so it won't retry.

**Fix:** Wrap the dispatch in a try/except. If the background task fails to launch, update the `webhook_log` row status to `"dispatch_failed"`. A periodic reconciliation task (or Mybizz_management polling) can re-dispatch these.

**Idempotency placement:** The current ADR-038 pattern checks idempotency inside `handle_payment_success()` — after the background task has already started. This is correct for preventing duplicate business logic, but it doesn't prevent duplicate task dispatch. The HTTP handler should check `webhook_log` for an existing row with the same `idempotency_key` before adding a new row.

### Recommendation

**Fix 1:** Add dispatch failure handling to the webhook HTTP handler pattern:

```python
try:
    anvil.server.launch_background_task('process_stripe_webhook', event_row.get_id())
except Exception:
    event_row.status = 'dispatch_failed'
    # Reconciliation task will retry
```

**Fix 2:** Move idempotency check into the HTTP handler, before the `add_row`:

```python
idempotency_key = extract_idempotency_key(event)  # event.id for Stripe
existing = app_tables.webhook_log.search(idempotency_key=idempotency_key)
if existing:
    return anvil.server.HttpResponse(status=200)  # Already logged
```

**Fix 3:** Add a reconciliation background task (low priority, runs hourly) that finds `webhook_log` rows with status `"pending"` or `"dispatch_failed"` older than 5 minutes and re-dispatches them.

### Verdict

**The pattern is sound. The implementation needs three hardening additions.** None require architectural changes — they're implementation-level fixes to ADR-038's code examples.

**Status:** Should be added to ADR-038 as implementation notes. Tracks as part of TODO ITEM 8.12.

---

## Dilemma 3: Server Call Latency in the Multi-Step Booking Flow

### The Problem

ADR-035 states: "Target: No more than 3 server round-trips in the core booking path (service selection → confirmation)." ADR-034 Constraint 4: "Every `anvil.server.call()` is an HTTP round-trip. Each call adds 100-500ms depending on server warm-up and network conditions."

The booking flow has 7 steps:
1. Select service
2. Select provider
3. Select date/time
4. Select meeting type
5. Fill intake form
6. Process payment
7. Confirmation

Each step potentially needs server data. At 100-500ms per call, 7 calls = 700ms-3.5s of pure round-trip overhead.

### Analysis

ADR-035's 5 mitigation strategies are documented:

1. **Pre-load data** — Load provider availability + service details + intake form templates in fewer calls
2. **Batch operations** — Combine "get available times" + "get provider details" into one call
3. **Client-side caching** — Cache in `globals.py` for the flow duration
4. **Defer non-critical** — Confirmation email after flow completes
5. **Background task** — Calendar events, reminders, campaign enrollment async

**The 3-round-trip target is achievable but requires careful call design:**

```
Round-trip 1: get_booking_context(service_id)
  → Returns: service details, available providers, available date ranges, intake form template

Round-trip 2: get_available_slots(provider_id, date_range)
  → Returns: available time slots for the selected provider and date range

Round-trip 3: confirm_booking(booking_data)
  → Returns: booking confirmation, payment processing result
```

Steps 1-3 (service, provider, date/time) are served by round-trip 1 (pre-loaded) and round-trip 2 (slot selection). Steps 4-5 (meeting type, intake form) are client-side selections from pre-loaded data. Step 6 (payment) is part of round-trip 3. Step 7 (confirmation) is client-side display.

**The risk:** If the booking flow is redesigned after implementation, the round-trip budget may be silently exceeded. There's no automated check for this.

### Recommendation

**Fix 1:** Document the 3-round-trip budget explicitly in the build plan Stage 2.3, with the specific server functions that constitute each round-trip.

**Fix 2:** Add a performance gate to Stage 2.3 completion criteria: "Booking flow completes in under 3 seconds on a standard connection (measured from service selection to confirmation display)."

**Fix 3:** Use `globals.py` to cache the `get_booking_context()` response. If the user navigates back to change their service selection, the cache can be invalidated and the call re-made. But forward navigation (selecting a time slot after selecting a provider) should not trigger a new server call if the data is already cached.

### Verdict

**Well-handled in ADR-035.** The 3-round-trip target is the right constraint. The implementation must be monitored to stay within budget. No architectural changes needed.

**Status:** Implementation discipline. Add performance gate to build plan Stage 2.3.

---

## Dilemma 4: State Management Between Forms

### The Problem

ADR-035 defines `globals.py` as the inter-form state mechanism. This works for V1, but has known weaknesses:

1. **No scoping** — All state is global to the session. No cleanup mechanism.
2. **No type safety** — `globals.current_contact` could be anything.
3. **Defensive loading** — Content panel forms must work without guaranteed state (user might bookmark/direct-link to a sub-view).
4. **Back-button behavior** — Browser back doesn't map cleanly to content panel state.

### Analysis

**globals.py is the right choice for V1.** The alternatives were rejected in ADR-035:

| Alternative | Why rejected |
|-------------|-------------|
| `open_form()` arguments | Not available with content panel pattern |
| URL parameters | Exposes state in URL; not suitable for sensitive data |
| Anvil Storage | Persistent across sessions; not suitable for transient flow state |
| Server-side session state | Requires server call to read; adds latency |

**The real risk is not globals.py itself — it's the lack of defensive loading.** If a form assumes `globals.current_contact` is set and it's not (user navigated directly, session expired, browser refresh), the form crashes.

### Recommendation

**Fix 1:** Every form that reads from `globals.py` must have a defensive guard:

```python
def __init__(self, **event_args):
    import globals
    self.contact = getattr(globals, 'current_contact', None)
    if self.contact is None:
        # Redirect to contact list or show error
        open_form('content_panel', 'contact_list')
        return
```

**Fix 2:** Define a `globals.py` contract document — a simple table listing every shared state variable, its type, which form sets it, which forms read it, and what happens when it's None.

**Fix 3:** For the booking flow specifically, use a `booking_flow_state` dict in `globals.py` with explicit keys:

```python
# globals.py
booking_flow_state = {
    'service': None,
    'provider': None,
    'date': None,
    'time_slot': None,
    'meeting_type': None,
    'intake_data': None,
}
```

This makes the booking flow state explicit, inspectable, and resettable.

### Verdict

**globals.py is adequate for V1.** The defensive loading pattern is the critical implementation detail. No architectural change needed — but the contract document and defensive guards are mandatory.

**Status:** Implementation discipline. Add to build plan Stage 1.3 (Dashboard & Navigation) as a completion criterion.

---

## Dilemma 5: PDF Invoice Generation Library Choice

### The Problem

ADR-039 recommends `reportlab` for PDF invoice generation. The alternatives rejected were `weasyprint` (needs Cairo/Pango), `pdfkit` (needs wkhtmltopdf binary), `fpdf2` (less mature), and HTML-to-PDF services (external dependency).

### Analysis

**reportlab is the right choice.** It's the most widely used Python PDF library, runs on CPython, has no external dependencies, and provides fine-grained control over layout.

**The one concern:** ADR-039 notes "reportlab must be verified as installable on Anvil's server environment." Anvil supports server-side Python packages via the Server Package configuration. `reportlab` is a pure Python package (with optional C extensions for speed). The base pure-Python version should install without issues.

**The BlobMedia concern:** ADR-039 notes that `BlobMedia` has row size limits. For V1 (simple invoices with 1-20 line items), this is not a problem. A single-page A4 invoice PDF is typically 50-150KB, well within BlobMedia limits. V2 can address external storage if invoice complexity grows.

### Recommendation

**Action 1:** During Phase 1 infrastructure setup (Stage 1.1), install `reportlab` as a server dependency and generate a test PDF to confirm it works in Anvil's server environment. This is a 10-minute verification.

**Action 2:** Use `reportlab.lib.pagesizes.A4` and `reportlab.pdfgen.canvas` for invoice generation. The template should include:
- Business header (name, logo, address from `business_profile`)
- Invoice details (number, date, due date)
- Line items table (service, quantity, unit price, total)
- Payment terms
- Footer (contact info)

**Action 3:** Store generated PDFs in the `invoices` table as a `BlobMedia` column. Monitor file sizes during development — if any invoice exceeds 1MB, investigate and optimize.

### Verdict

**ADR-039 is sound.** `reportlab` is the correct library. The only action is a quick install verification during Stage 1.1.

**Status:** No change needed. Verification is part of Phase 1 infrastructure.

---

## Cross-Cutting Concern: webhook_log Schema Reconciliation

The CEO review flagged a mismatch between ADR-038's `webhook_log` column definitions and the authoritative schema. This is a blocking concern for Phase 2 (payment integration).

**ADR-038 defines:**
| Column | Type |
|--------|------|
| `id` | UUID |
| `source` | Text |
| `payload` | Text |
| `status` | Text |
| `received_at` | DateTime |
| `processed_at` | DateTime |
| `error_message` | Text |
| `idempotency_key` | Text |

**Action:** Before Phase 2 begins, reconcile these definitions against [[[mb-3-cs-project-context:docs-local/authoritative-schema|Authoritative Database Schema]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/authoritative-schema.md). If the authoritative schema is missing columns, add them. If ADR-038 is wrong, update it. This is a 15-minute documentation task.

**Status:** TODO ITEM 8.12. Must be resolved before Stage 2.4 (Payment Integration).

---

## Cross-Cutting Concern: Phase 0 Completion Verification

The CEO review notes: "Phase 0 is marked complete, but the actual codebase (mb-3-cs) needs inspection to verify."

**Phase 0 deliverables:**
- Stage 0.1: Vault error handling — `server_shared/vault_service.py` with rescue block
- Stage 0.2: Test coverage framework configured
- Stage 0.3: Observability specification (thresholds now defined after remediation)
- Stage 0.4: Deployment procedures (now includes Anvil account failure scenarios)

**Action:** Before Phase 1 implementation begins, verify that Phase 0 deliverables actually exist in the `mb-3-cs` codebase. This is a code inspection, not a planning task.

**Status:** Pre-Phase 1 gate. Not a planning change — a verification task.

---

## Summary: Readiness Verdict

| Concern | Status | Blocking? |
|---------|--------|-----------|
| Background task limits | Unresolved — Anvil support email needed | Blocks Phase 2, not Phase 1 |
| Webhook pattern | Sound — needs 3 hardening fixes | Implementation detail, not blocking |
| Booking flow latency | Well-handled in ADR-035 | Performance gate needed in build plan |
| State management | globals.py adequate for V1 | Defensive guards mandatory |
| PDF generation | reportlab confirmed | Quick install verification in Stage 1.1 |
| webhook_log schema | Reconciliation needed | Blocks Phase 2 |
| Phase 0 verification | Code inspection needed | Blocks Phase 1 start |

**The project-library is ready for Phase 1 implementation.** The 5 dilemmas are either resolved, have clear mitigation paths, or are blocking only for Phase 2. No architectural changes are needed.

**Recommended execution order:**
1. Verify Phase 0 deliverables in `mb-3-cs` codebase
2. Install `reportlab` and verify PDF generation in Anvil
3. Begin Phase 1 (Auth, RBAC, Vault, Navigation, Settings)
4. Before Phase 2: reconcile webhook_log schema, send Anvil support email on background task limits
5. Phase 2 implementation with the 3-round-trip booking flow budget

---

*End of Engineering Review — 2026-06-16*
