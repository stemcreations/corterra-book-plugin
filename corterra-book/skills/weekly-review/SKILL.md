---
name: weekly-review
description: Reads how a business on Corterra Book did over the last week and how the next week looks, bookings, cancellations and no-shows, new clients, what sold and revenue by provider, next week's classes and quiet days, customers drifting away and prepaid packs still owed, then sums it up with at most three next steps. It reads only and changes nothing. Use when the owner asks how last week went, how business is doing, for a weekly summary or a catch-up, or what to focus on this week. For filling the quiet days it finds, use fill-quiet-days; for the customers to win back, use win-back-clients.
---

The tools here are on the Corterra Book connector. If a call fails because nothing is connected, tell the owner to connect Corterra Book from this plugin's Connectors tab, sign in with their dashboard email and password, and choose Read & write.

## Always

- Read only. Never save, book, move, cancel, email or make a code from here: each next step names the skill that does it, and waits for the owner.
- Last week is the seven days ending today, unless the owner names other dates. Give the four last-week reports the same dates (`date_from` and `date_to`, or `from_date` and `to_date` for revenue by provider). Each echoes its `from` and `to`, and those are the dates to give.
- Every report here returns a page. Quote its `summary`, `bands`, `liability`, `totals` and `total`, which cover everything; never count the rows of a page that is not `complete`.
- An estimate is said to be one: `net_cents`, the packs' `liability` and any `missed_visits`.
- Refused with `revenue_not_permitted`: say in one sentence that revenue reports are off for this connection, then carry on with the rest. Refused with `pro_required`: say in one sentence that it needs the Pro plan, then carry on.
- Never take a payment or sell anything.

## Which tool answers what

| The owner wants | Tools |
|---|---|
| last week's bookings, cancellations and no-shows | `analyze_no_shows()` with the dates, its `summary` |
| last week's new clients | `list_new_clients()` with the dates, its `summary` |
| what sold last week | `analyze_sales()` with the dates and `group_by` `kind` |
| revenue by provider | `analyze_staff_revenue()` with the dates |
| next week's quiet days | `find_schedule_gaps()` with `days` 7, each `summary.per_day` entry's `columns` |
| next week's classes | `list_upcoming_classes()` with `days` 7, its `summary` |
| how the customer list is doing | `find_customers_by_activity()` with `limit` 1, its `summary` |
| prepaid packs still owed | `analyze_prepaid_usage()` with `view` `packs` and `limit` 1, its `liability` and `bands` |
| what the automatic follow-up emails did | `get_automation_results()` with `days` 7 |

## Steps

Copy this list and tick it off.

- [ ] Read each report once, with a small `limit` (the summaries carry the answers): `analyze_no_shows()`, `list_new_clients()`, `analyze_sales()`, `analyze_staff_revenue()`, `find_schedule_gaps()`, `list_upcoming_classes()`, `find_customers_by_activity()`, `analyze_prepaid_usage()`, `get_automation_results()`.
- [ ] Write a short summary in this order, each figure with its dates:
  - **Last week:** bookings, cancellations and no-shows (with the no-show rate); new clients, and how many have not come back; what sold, `sales_collected_cents` by kind; service revenue by provider.
  - **Next week:** the quietest days, each with how booked its columns are (from `columns`, its `quietest_column` first), and for a column with a high `booked_same_day_pct` that it mostly fills on the day; any class with seats left.
  - **Customers:** how many fall in each band of days since their last visit, and how many are `overdue`; the packs still owed, by value, with its basis; and for each automatic follow-up that is on, the emails it sent last week and how many of those customers booked within 7 days.
- [ ] End with at most three next steps, each one thing to do and the skill that does it, e.g. fill next Tuesday (fill-quiet-days), win back the lapsed regulars (win-back-clients), welcome back the new clients who came once (win-back-clients). Ask which, if any, the owner wants, and do nothing until they say.

## Gotchas

- `sales_collected_cents` is everything sold and collected, gift cards left out; `analyze_staff_revenue()`'s `revenue_cents` is service revenue only. They answer different questions: never add one to the other or subtract one from the other.
- Both money reports date a sale by the day it was paid, not the appointment. Amounts are before tax and tips, and every figure is in cents.
- A visit covered by a pack or a membership costs nothing and is not a sale. A provider's zero-dollar work sits in five counts: `redeemed_count` (a pack, credit, membership or guest pass covered it), `covered_at_booking_count` (covered when it was booked), `member_service_count` (a zero-dollar members-only service: member visits, never free), `comped_count`, and `free_count` (a code or a staff discount took it to zero, or a zero-dollar service anyone can book). The work was done. They count sale lines, each seat and each add-on, so they can be more than the visits.
- The packs' `liability` is an estimate at today's list price. Quote its `basis`, and say the total leaves out any `unpriced_packs`.
- The `never` band of packs is people with no visit on record; it is not a count of people who never came.
- `repeat_offenders` is customers with two or more no-shows or late cancels; `customers_with_incidents` counts everyone with one.
- `late_cancels_recorded` false: late cancels are not recorded while the no-show fee is off, so never report "no late cancellations". Say what `late_cancel_note` says.
- `find_schedule_gaps()` and `list_upcoming_classes()` count forward from today, today included.
- A day's own `utilization_pct` blends every column, and a column with many seats holds it near 0%. Never call a day empty from it; give each column's share from `columns`.
- A column with a high `booked_same_day_pct` (walk-ins) always looks open ahead. Say it mostly fills on the day rather than calling it empty, and lead the quiet days with the columns people book ahead.
- `get_automation_results()` takes 7, 30 or 90 days ending today, never other dates: when the owner named other dates, give its figures as the last 7 days. A follow-up that is off sends nothing, so say it is off rather than reporting zeros. `booked` is a booking made within 7 days of an email, not proof the email brought it.

## Hand off

- Filling the quiet days: fill-quiet-days.
- Customers drifting away, idle packs, new clients who came once: win-back-clients.
- A closed day or new hours ahead: hours-and-closures.
- Booking, moving or cancelling someone: book-and-reschedule.
- Switching on or changing the automatic follow-up emails: automatic-follow-ups.
