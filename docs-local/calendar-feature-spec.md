---
title: "calendar-feature-spec"
doc-id: "mb-2e6a1c57"
updated: "2026-11-01"
---
# Mybizz CS — Calendar Feature Specification

**Feature:** F-11 Bookings and scheduling — the booking calendar
**Package:** `bookings/` (client), `server_bookings/` (server)
**Authority:** Build specification for the booking calendar. Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct. Flows: `user-flows.md` UF-05 to UF-08 and UF-11.

---

## 1. Core Decisions

- The calendar is a feature module inside the `bookings/` package, not a reusable custom component.
- The calendar **displays** bookings and lets the user act on them. It does not decide availability or create bookings itself: availability is calculated by `server_bookings/`, and creating or changing a booking opens the booking forms (UF-05, UF-11).
- Pure Anvil and Material 3 only: no Anvil Extras, no custom JavaScript, no external libraries, minimal custom CSS.
- Every view is rendered dynamically from data; no hardcoded day or time components.
- Only components listed in [[[default:specifications-global/spec-m3_component_mapping|M3 Component Mapping]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md) are used; component names follow [[[default:specifications-global/spec-nomenclature|Nomenclature]]](C:/dev/project-library-global/specifications-global/spec-nomenclature.md).
- No hardcoded colours: every colour comes from theme tokens (F-31).

---

## 2. Who Uses the Calendar

| Role | What the calendar shows | Provider selector |
|---|---|---|
| Owner, Manager | All providers' bookings, one provider at a time or all together | Shown |
| Admin | All providers' bookings | Shown |
| Staff | The Staff user's own bookings only | Hidden |
| Customer | No calendar; the customer portal lists the customer's bookings (UF-29) | — |

The restriction is enforced on the server (section 7): a Staff user's browser never receives another provider's bookings.

---

## 3. Form Structure

All forms live in `client_code/bookings/BookingCalendarForm/`.

| Form | Role |
|---|---|
| `BookingCalendarForm` | Controller: toolbar, view switching, date navigation, filters, data loading, refresh |
| `BookingCalendarMonthForm` | Month view |
| `BookingCalendarWeekForm` | Week view |
| `BookingCalendarDayForm` | Day view |
| `tmpl_month_day_cell` | One day cell in the month view |
| `tmpl_week_day_column` | One day column in the week view |
| `tmpl_week_event` | One booking in the week view |
| `tmpl_week_timeslot` | One time row label in the week view's time column |
| `tmpl_day_event` | One booking in the day view |

Only the active view form is loaded. Switching view clears the content panel and adds the new view form; views are never kept loaded and hidden. This keeps the page light and each view's state simple.

### 3.1 BookingCalendarForm component tree

```
BookingCalendarForm
└── col_calendar (ColumnPanel)
    ├── flow_toolbar (FlowPanel)
    │   ├── hdg_calendar_title (Heading)            "Calendar"
    │   ├── btn_today (Button, outlined)             "Today"
    │   ├── btn_prev (IconButton, mi:chevron_left)
    │   ├── btn_next (IconButton, mi:chevron_right)
    │   ├── hdg_period_title (Heading)               e.g. "March 2026", "3–9 March 2026", "Tuesday 4 March 2026"
    │   └── dp_go_to_date (DatePicker)
    ├── flow_view_selector (FlowPanel)               segmented button: grouped Buttons with shared state
    │   ├── btn_view_month (Button)                  "Month"
    │   ├── btn_view_week (Button)                   "Week"
    │   └── btn_view_day (Button)                    "Day"
    ├── flow_filters (FlowPanel)
    │   ├── dd_provider (DropdownMenu)               "All providers" + each provider; hidden for Staff
    │   └── dd_resource (DropdownMenu)               "All resources" + each room/equipment; hidden when the client has no resources
    ├── flow_legend (FlowPanel)                      status legend: pending, confirmed, completed, no-show, cancelled, blocked
    └── col_content (ColumnPanel)                    the active view form is added here
```

- The view buttons are one segmented button: the selected view's button has the filled role, the others the outlined role, and the roles are set in code whenever the view changes.
- The default view is **week** on screens 600 px wide or more and **day** below 600 px. The last view used is remembered for the user's session.

---

## 4. The Views

### 4.1 Month view — BookingCalendarMonthForm

```
BookingCalendarMonthForm
└── col_month (ColumnPanel)
    └── dg_month (DataGrid)
        columns: mon, tue, wed, thu, fri, sat, sun   (titles Mon to Sun, equal widths)
        rows_per_page: 6; show_page_controls: False
        └── one drp_week (DataRowPanel) per week (up to six), each holding seven tmpl_month_day_cell, one per column
```

`tmpl_month_day_cell`:
- `hdg_day_number` (Heading) — the day number; today and days outside the month use the tokens in section 7.2.
- `flow_events` (FlowPanel) — up to three `card_event` items (status colour, start time, customer or session name); further bookings shown as "+n more".
- A blocked date or closure shows the blocked token across the cell.

Responsive: the grid keeps seven equal columns at every width. Below 600 px each cell shows its day number and a booking count instead of booking cards.

Clicking a day opens the day view for that date.

### 4.2 Week view — BookingCalendarWeekForm

The week runs Monday to Sunday: **the seven days run across, the times run down.**

```
BookingCalendarWeekForm
└── flow_week (FlowPanel)
    ├── col_time_labels (ColumnPanel)
    │   └── rp_time_labels (RepeatingPanel → tmpl_week_timeslot)     one row per time slot, e.g. 07:00, 07:30 …
    └── rp_week_days (RepeatingPanel → tmpl_week_day_column)         seven columns, Monday to Sunday
```

`tmpl_week_timeslot`:
- `hdg_time` (Heading, small) — the time label for one row.

`tmpl_week_day_column`:
- `hdg_day_name` (Heading) — day name and date; today marked with the primary token.
- `col_day_slots` (ColumnPanel) — one cell per time row; a blocked period shows the blocked token; a cell with no booking is empty.
- `rp_day_events` (RepeatingPanel → `tmpl_week_event`) — the day's bookings, each placed in the row of its start time.

`tmpl_week_event`:
- `card_event` (InteractiveCard, outlined) — the status colour as its accent.
- `hdg_event_title` (Heading, small) — customer name, or session name with places taken ("Yoga — 8/12").
- `text_event_time` (Text, body small) — start–end time.
- `text_event_detail` (Text, body small) — one of: the address for a visit at the customer's location, the resource name, "Video" or "Phone"; a recurring booking shows the repeat icon.

The time rows run from the earliest business-hours start to the latest business-hours end in the week, in steps of the shortest service duration rounded to 15, 30 or 60 minutes.

Responsive: below 600 px the week view is not offered; the day view is used instead.

### 4.3 Day view — BookingCalendarDayForm

```
BookingCalendarDayForm
└── col_day (ColumnPanel)
    ├── text_day_summary (Text)                     e.g. "6 bookings · 1 group session · closed 13:00–14:00"
    └── rp_day_events (RepeatingPanel → tmpl_day_event)            bookings and blocked periods in time order
```

`tmpl_day_event`:
- `card_event` (InteractiveCard, outlined) — the status colour as its accent.
- `hdg_event_title` (Heading) — customer name, or session name with places taken.
- `text_event_time` (Text) — start–end time; for a visit, the travel time before and after is shown as well.
- `text_event_detail` (Text) — service, provider, meeting type, address or resource, payment status (paid, deposit paid, pay on the day, invoiced).
- `text_event_notes` (Text) — the customer's notes or special requests, shortened to two lines.

A blocked period appears in the list as a `card_event` with the blocked token and the reason.

---

## 5. What the Calendar Shows

| Item | Shown as |
|---|---|
| Booking status | Accent colour and status word: pending, confirmed, completed, no-show, cancelled (cancelled shown faded and only when "show cancelled" is on) |
| Payment status | Day view detail line |
| Visit at the customer's location | Address in the detail line; travel time before and after shown as blocked time for the provider |
| Group session | Session name and places taken against the place limit |
| Room or equipment | Resource name in the detail line; filter by resource |
| Recurring booking | Repeat icon |
| Blocked dates, closures, date exceptions | Blocked colour, with the reason in the day view |
| Today | Marked in every view |

All times are stored in UTC and shown in the signed-in user's time zone.

---

## 6. Actions

| Action | Who | Result |
|---|---|---|
| Click a booking | Owner, Manager, Admin; Staff for their own | Opens `BookingViewerForm` for that booking (UF-11) |
| Click an empty slot (week or day view) | Owner, Manager, Admin | Opens `BookingEditorForm` with the provider, date and time filled in |
| Click a day (month view) | All calendar users | Opens the day view for that date |
| Today, previous, next, go to date | All calendar users | Moves the period and reloads |
| Change provider or resource filter | Owner, Manager, Admin | Reloads with the filter |

Changes made in `BookingViewerForm` or `BookingEditorForm` refresh the calendar on return.

---

## 7. Data

### 7.1 Server function

`server_bookings/calendar_service.py`:

```python
@anvil.server.callable
# role check with the project's custom role decorator: owner, manager, admin, staff
def get_calendar_items(start_utc, end_utc, provider_id=None, resource_id=None, show_cancelled=False):
    """Return the bookings and blocked periods that overlap [start_utc, end_utc)."""
```

Rules:
- Returns only items overlapping the requested period — the visible month, week or day — never the whole table.
- For a Staff user, `provider_id` is ignored and the signed-in user's own provider record is used.
- Uses `.search()` with date-range queries; never iterates a whole table.
- Returns plain dictionaries, one call per period change, so the browser makes one server call per navigation.

### 7.2 Item structure

```python
{
    "item_id": "bk_8f2c…",
    "kind": "booking",            # "booking" | "session" | "blocked"
    "status": "confirmed",        # "pending" | "confirmed" | "completed" | "no_show" | "cancelled" | "blocked"
    "start": datetime,            # UTC
    "end": datetime,              # UTC
    "travel_before_min": 0,       # visit at the customer's location only
    "travel_after_min": 0,
    "provider_id": "u_…",
    "provider_name": "Thandi",
    "title": "Sarah Johnson",     # customer name, session name, or block reason
    "service_name": "Deep tissue massage",
    "meeting_type": "at_business", # "at_business" | "at_customer" | "video" | "phone"
    "address": None,
    "resource_name": None,
    "places_taken": None,         # group session only
    "place_limit": None,
    "payment_status": "paid",     # bookings.payment_status (authoritative-schema.md)
    "is_recurring": False,
    "notes": ""
}
```

The item carries a **status, never a colour**. Each booking card shows its status as an accent bar on its left edge and the status word in the card, following the status rules in `DESIGN.md` ("Status Badges"):

| Status | Accent | Card |
|---|---|---|
| `confirmed` | `status-success` | normal |
| `pending` | `status-pending` | normal |
| `completed` | `%color:Primary%` | normal |
| `no_show` | `status-error` | normal |
| `cancelled` | `%color:On Surface Variant%` | 50% opacity |
| `blocked` | none | `%color:Surface Variant%` fill with `%color:Outline%` border |
| today marker | `%color:Primary Container%` fill on the day number | — |
| days outside the month | — | text `%color:On Surface Variant%` |

The palette colours follow the selected palette and dark mode, and the status colours are fixed platform colours, so the calendar is correct in every palette with no extra work.

### 7.3 Loading and refresh

1. `BookingCalendarForm` works out the period for the active view and date.
2. It calls `get_calendar_items` once and keeps the result.
3. It passes the items to the active view form, which renders them through its templates.
4. A timer refreshes the current period every 60 seconds while the calendar is open, because Anvil cannot push changes to the browser ([[[default:adr-global/adr-real-time-and-background-tasks|Real-Time Updates and Background Task Architecture]]](C:/dev/project-library-global/adr-global/adr-real-time-and-background-tasks.md) ADR). The timer stops when the user leaves the calendar.

---

## 8. Controller Logic

```python
class BookingCalendarForm(BookingCalendarFormTemplate):
    VIEWS = {
        "month": BookingCalendarMonthForm,
        "week": BookingCalendarWeekForm,
        "day": BookingCalendarDayForm,
    }

    def load_view(self, view_name):
        self.current_view = view_name
        self.set_view_button_roles(view_name)
        start, end = self.period_for(view_name, self.anchor_date)
        self.items = anvil.server.call(
            "get_calendar_items", start, end,
            provider_id=self.dd_provider.selected_value,
            resource_id=self.dd_resource.selected_value,
            show_cancelled=self.show_cancelled,
        )
        self.hdg_period_title.text = self.period_title(view_name, self.anchor_date)
        self.col_content.clear()
        view = self.VIEWS[view_name](items=self.items, anchor_date=self.anchor_date)
        self.col_content.add_component(view)
```

Event handlers for the view buttons, Today, previous, next, the date picker and both filters each update `current_view`, `anchor_date` or the filter, then call `load_view`.

---

## 9. Material 3 Rules

Use only:
- `Heading` (`hdg_`) for titles and labels that act as titles
- `Text` (`text_`) for body text
- `InteractiveCard` (`card_`) for every clickable booking
- `ColumnPanel` (`col_`) for vertical layout, `FlowPanel` (`flow_`) for horizontal layout
- `RepeatingPanel` (`rp_`) for dynamic lists
- `DataGrid` (`dg_`) with `DataRowPanel` (`drp_`) for the month grid
- `Button` (`btn_`) and `IconButton` (`btn_`) for actions; grouped Buttons for the segmented view selector
- `DropdownMenu` (`dd_`) for filters, `DatePicker` (`dp_`) for go-to-date

Do not use:
- `Label` (banned)
- `LinearPanel`
- Chips (no native chip; the legend uses small outlined cards)
- Anvil Extras, custom JavaScript or external widgets
- Hardcoded colours

---

## 10. Responsive Behaviour

| Width | Behaviour |
|---|---|
| 600 px and wider | Month, week and day views; week is the default |
| Below 600 px | Month and day views only; day is the default; month cells show counts instead of cards |

---

## 11. Acceptance Checks

- A Staff user's browser never receives another provider's bookings (checked on the server response).
- Each navigation makes exactly one server call.
- No colour value appears in any calendar form or item; every colour is a theme token.
- The week view shows seven day columns with bookings at their start times.
- Clicking a booking opens it; clicking an empty slot opens a new booking with provider, date and time filled in.
- Times display in the user's time zone.
- The calendar refreshes within 60 seconds of a booking change made elsewhere.
