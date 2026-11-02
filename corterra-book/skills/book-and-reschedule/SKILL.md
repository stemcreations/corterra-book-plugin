---
name: book-and-reschedule
description: Books, moves, cancels, confirms and checks in a business's bookings on Corterra Book, marks or undoes a no-show, and keeps notes on a customer or a visit. Use when the owner asks to book someone, move or cancel a booking, confirm one, check someone in, mark someone as a no-show, or note something about a customer or a visit. For closing a whole day or part of one, use hours-and-closures; for no-show numbers over a period, use weekly-review.
---

The tools here are on the Corterra Book connector. If a call fails because nothing is connected, tell the owner to connect Corterra Book from this plugin's Connectors tab, sign in with their dashboard email and password, and choose Read & write.

## Always

- Before booking, moving or cancelling, read back the customer, the service, the provider when there is one, the date and the time, say that the customer will be emailed, and wait for the owner's yes.
- Offer only times `check_availability()` returned. Never promise a time you have not checked.
- Never add up a price yourself: the server prices every booking, seat and add-on, and returns the total.
- Ask before using a customer's pack or membership for a booking, and never choose what covers a seat in a group: ask.
- Nothing here takes a payment, charges a fee or gives a refund. Those happen at the Front Desk in the dashboard.
- A change refused with `read_only_key`: tell the owner to disconnect Corterra Book, connect it again and choose Read & write.

## Which tool answers what

| The owner wants | Tools |
|---|---|
| to find the customer | `list_customers()` |
| what the customer holds and their recent bookings | `get_customer_overview()` |
| the service and its add-ons | `list_products()` |
| a particular provider (Pro) or location | `list_providers()`, `list_locations()` |
| open times | `check_availability()` |
| what covers each seat of a group | `get_seat_cover_options()` |
| to book | `create_booking()` |
| to find a booking | `get_customer_overview()`, `list_bookings()`, `list_upcoming_classes()` |
| to move, cancel, confirm or check in | `reschedule_booking()`, `cancel_booking()`, `confirm_booking()`, `check_in_booking()` |
| a no-show, or to undo one | `mark_no_show()`, `undo_no_show()` |
| a note | `add_customer_note()`, `set_booking_notes()` |

## Steps

Copy the list for the job and tick it off.

**Book**
- [ ] Find the customer with `list_customers()`, searching by name, email or phone. If more than one matches, ask which. Someone new: ask for their first and last name and an email or phone.
- [ ] For a customer already on file, read `get_customer_overview()` once: it shows the packs and membership that could cover the booking.
- [ ] Find the service with `list_products()`, using `search` with its name. If the owner names a provider, find them with `list_providers()`.
- [ ] Read the open times with `check_availability()` for that date. For a group, give `party_size`, everyone including the booker; with add-ons, give the same `addon_option_ids` the booking will carry. If the time asked for is not open, offer the nearest open ones.
- [ ] A group where packs, passes or a plan could cover seats: read `get_seat_cover_options()` and ask which seat uses what.
- [ ] Read it back, wait for the yes, and book with `create_booking()`. Give the customer's first and last name and the email or phone already on file, so the booking goes to the existing customer.
- [ ] Refused `conflict` or `class_full`: read `check_availability()` again and offer other times. Refused `location_required`: read `list_locations()`, ask which, and book again with it.

**Move**
- [ ] Find the booking in `get_customer_overview()`'s `bookings`, or with `list_bookings()` for that date (it cannot search by customer).
- [ ] Read `check_availability()` for the new date, with the booking's add-ons as `addon_option_ids`.
- [ ] Read back the old and the new time, wait for the yes, and move it with `reschedule_booking()`, sending only what changes.

**Cancel**
- [ ] Find the booking, read it back, wait for the yes, and cancel it with `cancel_booking()`. A pack session or a seat cover goes back on its own, and no fee is charged here.

**Confirm and check in**
- [ ] A pending booking: `confirm_booking()`, which emails the customer.
- [ ] Someone has arrived: `check_in_booking()`, which checks in the whole party.

**No-show**
- [ ] Once the start time has passed: `mark_no_show()`. No money is charged and nobody is emailed; a pack session goes back.
- [ ] They turn up after all: `undo_no_show()`, then `check_in_booking()`.

**Notes**
- [ ] Something true of the customer at every visit: `add_customer_note()`, which adds a note.
- [ ] Something about this visit only: read the booking's current `notes` from `list_bookings()`, then `set_booking_notes()` with the old and new text together, since it replaces them.

## Gotchas

- `create_booking()` takes no customer id. It matches the customer by the details given, so send the email or phone on file, not a new spelling.
- Add-ons make a booking longer than the service alone. Without the same `addon_option_ids`, `check_availability()` offers times for the service alone, and the booking may then be refused `conflict`.
- A member books a members-only class through its "Member:" product, everyone else through the class itself.
- `reschedule_booking()` with `addon_option_ids` replaces the add-ons: leave it out to keep them.
- `cancel_booking()` refused `checked_in`: the check-in is undone in the dashboard first. Refused `checked_out`: it was paid for, so it is refunded at the Front Desk.
- `undo_no_show()` refused `fee_charged`: the no-show fee was already charged; tell the owner.

## Hand off

- Closing a day, or part of one, with the bookings inside it: hours-and-closures.
- How many no-shows or new clients over a period: weekly-review.
- Filling empty times with invitations: fill-quiet-days.
