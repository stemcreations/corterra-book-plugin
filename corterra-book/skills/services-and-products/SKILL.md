---
name: services-and-products
description: Adds and changes what a business on Corterra Book books or sells one at a time, services, classes and their schedules, add-ons and retail items, with their prices, lengths, categories, the providers who do them and their own reminders, and takes one off sale or deletes it. Use when the owner wants to add a new service, class, add-on or product, change one's price, length, schedule or details, choose who performs it, or stop offering it. For memberships, packs and combos, use memberships-and-packs; for setting up a brand-new business, use set-up-my-business.
---

The tools here are on the Corterra Book connector. If a call fails because nothing is connected, tell the owner to connect Corterra Book from this plugin's Connectors tab, sign in with their dashboard email and password, and choose Read & write.

## Always

- Before every save, read back what will be saved (the name, the length, the price and anything else that changes) and wait for the owner's yes.
- Save only what the owner told you or agreed to. Never invent a price, a length, a schedule or a description. A description is booking-page copy: write it with the owner or leave it out.
- Before changing something, read it in full with `list_products()` and its `product_id`, and send only what changes.
- To stop offering something for now, turn it off. Deleting is for good: run the delete's preview first and show it.
- A change refused with `read_only_key`: tell the owner to disconnect Corterra Book, connect it again and choose Read & write. Refused with `pro_required`: say in one sentence that it needs the Pro plan, then carry on with the rest.
- Never take a payment or sell anything.

## Which tool answers what

| The owner wants | Tools |
|---|---|
| to find a service, class, add-on or product | `list_products()` with `search`, then with `product_id` for the whole record |
| a near-copy of one that exists | `duplicate_product()`, then `update_product()` |
| something new | `create_product()` |
| to change one | `update_product()` |
| a category | `list_product_categories()`, `create_product_category()` |
| a tax category | `list_tax_codes()` |
| which providers do it (Pro) | `list_providers()`, `set_provider_products()` |
| its own reminders (Pro) | `get_product_reminders()`, `set_product_reminders()` |
| to tell customers | `create_announcement()` |
| to stop offering it | `update_product()` with `is_active` false, or `delete_product()` |

## Steps

Copy the list for the job and tick it off.

**A new service**
- [ ] Look for a close match with `list_products()` and `search`. For a near-twin (another length, a group version), `duplicate_product()`, then `update_product()` on the copy with what differs. A copy starts off: turn it on with `is_active` once the owner is happy.
- [ ] Otherwise ask for whatever is missing of the length and the price, and for a group, how many people at most and what each extra person pays.
- [ ] Put it in a category: read `list_product_categories()` and use the one that fits. Create one with `create_product_category()` only when none does.
- [ ] Read it back, wait for the yes, and save with `create_product()`. Refused `tax_code_required`: read `list_tax_codes()`, ask which fits, and save again with `tax_code`.
- [ ] On Pro, ask who performs it: read `list_providers()`, then `set_provider_products()` for each chosen provider with their current `product_ids` plus the new one.

**A class**
- [ ] Ask the length, the price, the seats in each session, and the times: which weekdays and start times from which date, or a list of dates. On Pro, ask whether it runs on a provider or a room.
- [ ] Read the schedule back in words, wait for the yes, and save with `create_product()`: `is_class`, `class_capacity`, and `class_blocks` (a repeat with `weekdays`, 0 = Monday, `start_times` and `start_date`; or picked `sessions`).
- [ ] A members-only version follows the class. Ask which class, `duplicate_product()` it, then `update_product()` on the copy with `is_member`, `follows_class_id` and the membership the owner names in `membership_tier_ids`. Send none of the class's own name, length, seats, category or instructor.
- [ ] New times for a class: read it with `product_id` and send `class_blocks` with every block that stays plus the change. A repeat being edited keeps its `series_id`.

**An add-on**
- [ ] Ask the price, the time it adds (0 is fine) and which services offer it. Save with `create_product()`: `is_addon`, `duration_minutes`, `price` and `main_option_ids`. On Pro, ask whether everyone who does those services performs it; if not, send `schedule_ids`.
- [ ] One more service for an add-on that exists: read the add-on with `product_id`, then `update_product()` on it with its `main_option_ids` plus the new service.

**A retail item**
- [ ] Ask the price, a SKU if they use one, and the stock if they count it. Save with `create_product()`: `is_retail`, `price`, `sku` and `inventory_qty`.

**A change**
- [ ] Read the product with `list_products()` and `product_id`. Read back the old and the new values, wait for the yes, and save with `update_product()`, sending only what changes.
- [ ] Its own reminders (Pro): `get_product_reminders()`, then `set_product_reminders()` with every reminder that stays, in the owner's own words.
- [ ] To tell customers: `create_announcement()`. It is words only and changes no price.

**Stop offering it**
- [ ] For now: `update_product()` with `is_active` false.
- [ ] For good: `delete_product()` without `confirm`. Show what blocks it, the upcoming bookings that stay, and a class's booked sessions. On the owner's yes, call again with `confirm`, and with `cancel_bookings` only when they chose to cancel those bookings.

## Gotchas

- `schedule_ids`, `membership_tier_ids`, `addon_option_ids` and `main_option_ids` each replace the whole list. Start from the product's own `addon_option_ids` or `main_option_ids`, never from `addons`, which leaves out switched-off add-ons.
- `list_products()` leaves out empty fields and every `description`: no `addons` means none. Read one with `product_id` before changing it.
- A class's booked session cannot be removed or moved (`has_bookings`): name each one, and the owner cancels it on the calendar first. Fewer seats than are booked (`capacity_below_booked`): show the sessions, ask whether to keep everyone or cancel the newest, and call again with `over_capacity`.
- A group needs `additional_seat_price`. Send 0 only when the extra people are free.
- A members-only service can be saved before its membership exists, but nobody can book it until a membership includes it: say so.
- Refused `no_performer`: the add-on's providers would leave one of its services with nobody to do it, and the message names the service.
- `set_provider_products()` replaces the provider's whole list.
- `list_products()` returns 25 at a time: check `complete` before counting.

## Hand off

- Memberships, packs and combos, and what a membership includes: memberships-and-packs.
- Opening hours and closed days: hours-and-closures.
- A brand-new business: set-up-my-business.
- Booking someone into it: book-and-reschedule.
