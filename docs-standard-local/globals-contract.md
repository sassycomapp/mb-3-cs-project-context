---
title: "globals-contract"
doc-id: "mb-0542088e"
updated: "2026-11-01"
---
# Mybizz CS — globals.py Contract

The client-side state held in `globals.py`, and how it is loaded.

**Standard:** [[[default:docs-standard-global/docstd-globals-contract]]](C:/dev/project-library-global/docs-standard-global/docstd-globals-contract.md)

**Authority:** [[[default:adr-global/adr-form-architecture-and-state|Form Architecture and State Management]]](C:/dev/project-library-global/adr-global/adr-form-architecture-and-state.md) ADR. Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct.
**Status:** Living document — every new shared variable is added here before it is used.

---

## Purpose

This document defines every shared state variable in `client_code/shared/globals.py`. Each entry gives the variable's type, which form sets it, which forms read it, and what happens when it is `None`.

**Rules:**
- Every form reads a variable with `getattr(globals, 'var_name', None)` and handles `None`. A variable is never assumed to be set.
- `globals.py` holds selections and cached display data only. It never holds credentials, tokens, prices used for payment, or anything the server must trust: the server re-reads every record and re-checks every rule from its own data (F-01, F-30).
- Every value is cleared on sign-out.
- Data passed between the steps of one form (onboarding, the Sidesheet flows) stays in that form's `self.item`; `globals.py` is used only when the state crosses forms.

---

## State Variables

### session_user

| Field | Value |
|-------|-------|
| **Type** | `dict` — `role` (`owner`, `manager`, `admin`, `staff`, `customer`), `name`, `email`, `preferences` (`tooltips`, `morning_briefing`), `is_demo_visitor` (`bool`) — or `None` |
| **Set by** | `LoginForm` after sign-in; `AccountForm` when preferences change |
| **Read by** | `AdminLayout`, `CustomerLayout` (which menu items to show), `HelpTooltipComponent`, `EditModeComponent` (Owner or Manager), `WhatsUpForm` (briefing) |
| **When None** | Open `auth.LoginForm` |

Display only: it decides what the interface shows. Every server function checks the role itself.

### feature_switches

| Field | Value |
|-------|-------|
| **Type** | `dict` — `marketing_enabled`, `blog_enabled`, `comments_enabled` (all `bool`) — or `None` |
| **Set by** | `LoginForm` after sign-in; `SettingsForm` (Feature Switches tab) on save |
| **Read by** | `AdminLayout` (menu items), `BlogPostForm` (comments) |
| **When None** | Treat every switch as off and reload from the server |

### app_language

| Field | Value |
|-------|-------|
| **Type** | `str` — `en`, `fr`, `pt`, `es`, `de`, `it` — or `None` |
| **Set by** | `LoginForm` and `PublicLayout` at startup, from `business_profile.app_language`; `OnboardingForm` step 2 and `BusinessProfileTab` on save |
| **Read by** | `shared/translations` (`t(key)`), `HelpTooltipComponent` (video language) |
| **When None** | Use `en` |

### sample_data_present

| Field | Value |
|-------|-------|
| **Type** | `bool` or `None` |
| **Set by** | `LoginForm` after sign-in; `DataTab` and the banner after Clear sample data |
| **Read by** | `AdminLayout` (the sample data banner) |
| **When None** | Ask the server |

### palette_key

| Field | Value |
|-------|-------|
| **Type** | `str` — `professional_blue`, `warm_coral`, `calm_teal`, `sophisticated_purple` — or `None` |
| **Set by** | `LoginForm` and `PublicLayout` at startup, from `business_profile.palette`; `PaletteSettingsForm` on save |
| **Read by** | `shared/palette_utils.py` (applies the palette and its dark block, `palette-tokens.md`) |
| **When None** | Apply `professional_blue` |

### current_contact

| Field | Value |
|-------|-------|
| **Type** | `app_tables.contacts` row or `None` |
| **Set by** | `ContactListForm` (on selection), `BookingEditorForm` (when a booking creates the contact) |
| **Read by** | `ContactViewerForm`, `ContactEditorForm`, `BookingEditorForm`, `InvoiceEditorForm` |
| **When None** | Open `crm.ContactListForm` |

### current_booking

| Field | Value |
|-------|-------|
| **Type** | `app_tables.bookings` row or `None` |
| **Set by** | `BookingListForm` and `BookingCalendarForm` (on selection), `BookingEditorForm` (on creation) |
| **Read by** | `BookingViewerForm`, `BookingEditorForm`, `InvoiceEditorForm` |
| **When None** | Open `bookings.BookingListForm` |

### current_order

| Field | Value |
|-------|-------|
| **Type** | `app_tables.orders` row or `None` |
| **Set by** | `OrderListForm` (on selection) |
| **Read by** | `OrderViewerForm`, `InvoiceEditorForm` |
| **When None** | Open `orders.OrderListForm` |

### current_invoice

| Field | Value |
|-------|-------|
| **Type** | `app_tables.invoice` row or `None` |
| **Set by** | `InvoiceListForm` (on selection), `BookingViewerForm` and `OrderViewerForm` (View Invoice), `TimeEntryListForm` (after Create Invoice) |
| **Read by** | `InvoiceViewerForm`, `InvoiceEditorForm` |
| **When None** | Open `invoices.InvoiceListForm` |

### current_company

| Field | Value |
|-------|-------|
| **Type** | `app_tables.companies` row or `None` |
| **Set by** | `CompanyListForm` (on selection) |
| **Read by** | `CompanyViewerForm`, `DealEditorForm`, `QuoteEditorForm` |
| **When None** | Open `crm.CompanyListForm` |

### current_deal

| Field | Value |
|-------|-------|
| **Type** | `app_tables.deals` row or `None` |
| **Set by** | `DealBoardForm` (on selection) |
| **Read by** | `DealEditorForm`, `QuoteEditorForm` (Create Quote on a deal) |
| **When None** | Open `sales.DealBoardForm` |

### current_quote

| Field | Value |
|-------|-------|
| **Type** | `app_tables.quotes` row or `None` |
| **Set by** | `QuoteListForm` (on selection), `DealEditorForm` (Create Quote) |
| **Read by** | `QuoteEditorForm` |
| **When None** | Open `sales.QuoteListForm` |

### inbox_selection

| Field | Value |
|-------|-------|
| **Type** | `dict` — `address_id` (`str` row id or `None` for Unassigned), `folder` (`inbox`, `drafts`, `sent`, `archive`, `spam`, `trash`), `sort`, `filters` (`dict`) — or `None` |
| **Set by** | `TeamInboxForm` on every change of address, folder, sort or filter |
| **Read by** | `TeamInboxForm` (restores the view on return), `ComposeForm` (the From address) |
| **When None** | The user's own personal address, folder `inbox`, newest first, no filter |

### booking_flow_state

| Field | Value |
|-------|-------|
| **Type** | `dict` with the keys below |
| **Set by** | `BookingFlowForm` (the customer booking flow, steps 1–7, `DESIGN.md` Booking Design) |
| **Read by** | `BookingFlowForm` (each step reads the earlier selections) |
| **When None** | Initialise to `{}` at the start of the flow |

**Keys:**

| Key | Type | Set at step | Read at step |
|-----|------|-------------|--------------|
| `service` | `app_tables.services` row | 1 — service | 2–7 |
| `provider` | `app_tables.users` row, or `None` for "any available" | 2 — provider | 3–7 |
| `group_session` | `app_tables.group_sessions` row or `None` | 3 — session, for a group service | 4–7 |
| `places` | `int` (default `1`) | 3 — places, for a group service | 6–7 |
| `date` | `str` (ISO date, business time zone) | 3 — date | 4–7 |
| `time_slot` | `str` (`HH:MM`, business time zone) | 3 — time | 4–7 |
| `meeting_type` | `str` — `at_business`, `at_customer`, `video`, `phone` | 4 — meeting type | 5–7 |
| `customer_address` | `dict` or `None` (only for `at_customer`) | 4 — meeting type | 6–7 |
| `intake_data` | `dict` or `None` | 5 — intake form | 6–7 |
| `special_requests` | `str` or `None` | 5 — intake form | 6–7 |
| `payment_option` | `str` — `full`, `deposit`, `on_the_day`, `invoice`, `package` | 6 — payment | 6–7 |
| `customer_package` | `app_tables.customer_packages` row or `None` (only for `package`) | 6 — payment | 6–7 |
| `cached_context` | `dict` or `None` — the response of `get_booking_context` | 1 | 2–5 (no re-fetch on back navigation) |

- The resource a service needs is not a key: the server finds a free resource when it offers slots and reserves it at `confirm_booking`.
- `confirm_booking` re-checks the slot, the provider, the resource, the places and the price on the server; nothing in this dict is trusted.
- **Reset:** cleared to `{}` on confirmation, on cancellation, and when the 15-minute payment hold expires (UF-38).

### order_flow_state

| Field | Value |
|-------|-------|
| **Type** | `dict` — `service` (row), `brief` (`str`), `attachments` (list of media), `payment_option` (`full`, `deposit`, `invoice`) |
| **Set by** | `OrderFlowForm` (UF-09) |
| **Read by** | `OrderFlowForm` |
| **When None** | Initialise to `{}` at the start of the flow |

**Reset:** cleared to `{}` on order confirmation or cancellation.

---

## Defensive Loading Pattern

`globals.py` is imported with a relative import. In a client instance the forms run from `master_template`'s package, so an import by App 1's package name fails there. From a form in a package (`client_code/crm/ContactViewerForm/`):

```python
from ...shared import globals as app_state

class ContactViewerForm(ContactViewerFormTemplate):
    def __init__(self, **properties):
        self.contact = getattr(app_state, 'current_contact', None)
        if self.contact is None:
            open_form('crm.ContactListForm')
            return
        self.init_components(**properties)
```

- `open_form()` in `__init__` is always followed by `return`.
- **Do not** use `app_state.current_contact` directly: it raises `AttributeError` if the variable was never set.

---

*End of globals.py Contract*
