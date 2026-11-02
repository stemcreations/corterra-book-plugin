---
name: hours-and-closures
description: Sets a business's weekly opening hours on Corterra Book and its providers' working hours, closes the business or some providers for a day, part of a day or a holiday, deals with the bookings each closure leaves inside it, and reopens what was closed. Use when the owner wants to change opening hours, close on a date or for a holiday, close early or block off part of a day, give a provider a day off or new working hours, or open a closed day again. For moving or cancelling one booking on its own, use book-and-reschedule; for setting up a brand-new business, use set-up-my-business.
---

The tools here are on the Corterra Book connector. If a call fails because nothing is connected, tell the owner to connect Corterra Book from this plugin's Connectors tab, sign in with their dashboard email and password, and choose Read & write.

## Always

- Before every save, read back what will be saved (the days, the date, the hours and who it covers) and wait for the owner's yes. When the owner's request already says to go ahead, that is the yes: save it.
- A closure stops new bookings and never cancels the ones already made, so the bookings inside it never hold up saving it. After every closure, find those bookings and ask the owner about each one: move it or cancel it. Never move or cancel one without their yes, and say the customer is emailed either way.
- The weekly hours, a provider's hours and the observed holidays are each saved as a whole: read them first and keep everything that stays.
- A booking-page announcement is words only and closes nothing. Close first; announce only when the owner wants customers told.
- A change refused with `read_only_key`: tell the owner to disconnect Corterra Book, connect it again and choose Read & write. Refused with `pro_required`: say in one sentence that it needs the Pro plan, then carry on with the rest.
- Never take a payment or charge a fee.

## Which tool answers what

| The owner wants | Tools |
|---|---|
| the hours, closed days and holidays now | `get_business_hours()` |
| the providers and their hours (Pro) | `list_providers()` |
| new weekly hours | `remove_business_hours()`, then `add_business_hours()` |
| a provider's own hours (Pro) | `update_provider()` with `hours` |
| a whole day closed, or a provider's day off | `add_blackout_date()` |
| part of a day closed, or closing early | `add_time_block()` |
| a holiday every year | `set_observed_holidays()`, `add_custom_holiday()` |
| the bookings inside a closure | `list_bookings()` with the date, or a time block's `conflicts` |
| to move or cancel one of them | `check_availability()`, `reschedule_booking()`, `cancel_booking()` |
| to tell customers | `create_announcement()` |
| to reopen | `remove_blackout_date()`, `list_time_blocks()` and `remove_time_block()`, `remove_custom_holiday()`, `set_observed_holidays()` |

## Steps

Copy the list for the job and tick it off.

**First, every job**
- [ ] Read `get_business_hours()` once. On Pro, read `list_providers()` too: the business has weekly hours of its own and each provider has their own. When the owner does not say which they mean, ask.

**New weekly hours**
- [ ] Ask the days and times. A lunch break is two blocks on one day; a closed day has none.
- [ ] Read back the whole week as it will be, and say that bookings already made outside the new hours stay where they are.
- [ ] On the yes, `remove_business_hours()` each row being replaced, by its `id`, then `add_business_hours()` each new block: days run 0 = Monday to 6 = Sunday, times are 24-hour `HH:MM`.
- [ ] A provider's hours (Pro): `update_provider()` with `hours`, that provider's whole week, every day that stays plus the change.

**A whole day closed**
- [ ] Ask whether the whole business is closed or only some providers (a provider's day off).
- [ ] On the yes, or when the owner already said to go ahead, `add_blackout_date()`: the whole business by default, or `covers_all` false with those providers' `schedule_ids`.
- [ ] Then the bookings inside it, below.

**Part of a day, or closing early**
- [ ] Ask the date, the closed hours and who it covers. Closing early is a block from the new closing time to the latest closing time of anything it covers that day. The whole business is every provider's `schedule_ids` plus `covers_global`, in one block; a business with no providers uses `covers_global` alone.
- [ ] Read it back; on the yes, or when the owner already said to go ahead, `add_time_block()`. Its `conflicts` are the bookings inside it.

**A holiday**
- [ ] A common holiday: take its `key` from `holiday_catalog`, never a guessed one, and save with `set_observed_holidays()`: every key already observed plus the new one. A date of the owner's own that comes back every year: `add_custom_holiday()`. A date that does not come back: a whole day closed, above.
- [ ] Then the bookings on its next date (its `next_date` in `holiday_catalog`).

**The bookings inside a closure**
- [ ] Read them: a time block's `conflicts`, or `list_bookings()` with the closed date as `date_from` and `date_to` (and a provider's `schedule_id` for their day off). Leave out cancelled ones.
- [ ] Show them to the owner, who, what and when, and ask about each: move it or cancel it. Say the customer is emailed either way.
- [ ] To move one: `check_availability()` for an open time, then `reschedule_booking()` on the yes. To cancel one: `cancel_booking()` on the yes.
- [ ] Classes: a session nobody had booked is called off by the closure itself. A booked session keeps its bookings, and they are among the bookings above.
- [ ] If the owner wants customers told on the booking page: `create_announcement()` dated to the closure, in their words.

**Reopen**
- [ ] A closed day: find its `id` in `get_business_hours()`'s `blackout_dates`, then `remove_blackout_date()`. A time block: `list_time_blocks()` for that date, then `remove_time_block()`. A holiday: `set_observed_holidays()` without its key, or `remove_custom_holiday()`.

## Gotchas

- A date holds one closed day. A second `add_blackout_date()` on a date already closed is refused: to change who it covers (another provider off the same day), remove that date's closed day and add one covering everyone who is off.
- `get_business_hours()` lists past closed days too: look only at today and later.
- Every `day_of_week` runs 0 = Monday to 6 = Sunday, in the weekly hours and in each provider's `hours`.
- `set_observed_holidays()` replaces the whole list, and an empty list removes every holiday. A key that is not in `holiday_catalog` is dropped without a word.
- `update_provider()`'s `hours` replaces the provider's whole week.
- Changing the weekly hours moves no booking already made.
- A time block must cover a provider or `covers_global` (`no_coverage`), and its end must be after its start (`invalid_range`).
- `list_bookings()` returns 50 at a time: check `complete`.

## Hand off

- Moving or cancelling one booking on its own, or booking someone: book-and-reschedule.
- Filling the days around a closure: fill-quiet-days.
- A class's own times: services-and-products.
- A brand-new business: set-up-my-business.
