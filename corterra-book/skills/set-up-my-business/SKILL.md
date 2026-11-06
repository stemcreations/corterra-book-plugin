---
name: set-up-my-business
description: Sets up a new business on Corterra Book step by step, or shows what is already set up and what is still missing. Use when the owner wants their business, studio, spa or booking page set up or finished, asks what is set up or missing, or asks what this connection can do. For adding or changing one service on a business already running, use services-and-products; for memberships and packs after setup, use memberships-and-packs; for changing hours or closing days, use hours-and-closures.
---

The tools here are on the Corterra Book connector. If a call fails because nothing is connected, tell the owner to connect Corterra Book from this plugin's Connectors tab, sign in with their dashboard email and password, and choose Read & write.

## Always

- One topic at a time, in the order of the steps below. Before every save, show exactly what you are about to save and wait for the owner's yes.
- Save only what the owner told you or agreed to. Never invent a service, price, provider, hours or policy.
- A save refused with `read_only_key`: tell the owner to disconnect Corterra Book, connect it again and choose Read & write. Save nothing more until they have.
- A save refused with `pro_required`: say in one sentence that it needs the Pro plan, skip that topic, and carry on with the next.
- Never take or promise a payment, and never sell a membership or pack: selling happens at the Front Desk in the dashboard.

## Which tool answers what

| The owner wants to see or change | Tools |
|---|---|
| the business name, colours and font | `get_business_info()`, `update_business_info()` |
| the timezone, booking rules and reminders | `get_business_settings()`, `update_business_settings()` |
| weekly hours and holidays | `get_business_hours()`, `add_business_hours()`, `remove_business_hours()`, `set_observed_holidays()`, `add_custom_holiday()` |
| services | `list_products()`, `list_product_categories()`, `create_product_category()`, `list_tax_codes()`, `create_product()` |
| providers (Pro) | `list_providers()`, `create_provider()`, `set_provider_products()` |
| memberships and packs | `list_memberships()`, `create_membership()`, `create_membership_agreement()`, `list_bundles()`, `create_bundle()` |
| the Business Page | `get_business_page()`, `update_business_page()` |

## Steps

Copy this checklist and tick it off as you go.

- [ ] 1. **Read what is set up, once, before asking anything**: `get_business_info()`, `get_business_settings()`, `get_business_hours()`, `list_products()`, `list_providers()` and `get_business_page()`. Tell the owner in a few lines what is done and what is missing. Skip any step below that is already done, unless the owner wants to change it.
- [ ] 2. **Business details and booking settings.** Confirm the business name, then the timezone: without the right timezone, same-day times don't show to customers. Then ask about the booking rules that matter to them: time between appointments, how far ahead people can book, how late they can cancel, and whether bookings confirm on their own. Save with `update_business_info()` and `update_business_settings()`, sending only what changes.
  - Colours, if the owner wants their own: the background (`secondary_color`) sits behind every page their customers see and the kiosk; the accent (`primary_color`) colours buttons, links, prices and their emails. Both are kept readable, so an accent that wouldn't read on the background is adjusted on the pages: say so rather than promise the exact colour everywhere, and point them to the Branding page's preview to see it. No background is a light page (`clear=["secondary_color"]` removes one).
  - The font family reaches their pages and the kiosk; the weight changes the mobile app only.
- [ ] 3. **Weekly hours and holidays.** Ask for the open hours of each day. Save one block per open stretch with `add_business_hours()`: days run 0 = Monday to 6 = Sunday, times are 24-hour `HH:MM`, and a lunch break is two blocks. A day that already has hours: remove its block with `remove_business_hours()` before adding the new one. Ask which holidays they close for, offering the ones in `get_business_hours()`'s `holiday_catalog`, and use each holiday's `key` from there. `set_observed_holidays()` replaces the whole list, so send every holiday they observe, the ones already observed included. A date of their own that comes back every year goes to `add_custom_holiday()`.
- [ ] 4. **Services.** For each service ask the name, the length, the price and, for a group, what each extra person pays. Put it in a category: read `list_product_categories()`, and create one with `create_product_category()` only when none fits. Save each with `create_product()`. If it is refused with `tax_code_required`, read `list_tax_codes()`, ask which code fits, and save again with it.
- [ ] 5. **Providers (Pro).** Ask who performs the services and when they work. Create each with `create_provider()`, then give each the services they do with `set_provider_products()`.
- [ ] 6. **Memberships and packs, only if the owner sells them.** Turn them on with `update_business_settings()` (`enable_memberships_and_bundles`).
  - A membership: ask the name, the price, whether it bills monthly or yearly, the member discount, and any services it limits per month. Save with `create_membership()`.
  - An agreement members sign: write it in the owner's own words with `create_membership_agreement()` before the membership, show the whole text, and suggest whoever handles their legal documents checks it.
  - A pack: ask the services it covers, the number of visits, the price, and whether it expires. Save with `create_bundle()`.
  - A membership's name, price and billing period, and a pack's price and visit count, can't be changed from here afterwards, so read them back before saving.
- [ ] 7. **The Business Page.** Read `get_business_page()` and offer what is already on file. Write the tagline and the About text with the owner. Before saving, say that the page's email is also the reply-to address for their emails and its address is their sales-tax address. Save with `update_business_page()`, and publish only when they say so.
- [ ] 8. **Finish.** Read back what was set up, then list what they finish in the dashboard: connecting payments, the logo and photos, sales tax, staff accounts and the kiosk (Pro), and messaging. Then offer the automatic follow-up emails and review links as a next step (automatic-follow-ups).

A business that is already set up: give a short picture of it, then one line on each thing you can help with next: booking and moving appointments, filling quiet days, winning back clients, automatic follow-up emails and review links, adding or changing services and products, memberships and packs, changing hours and closures, and a weekly review.

## Gotchas

- `create_provider()` takes the provider's whole week of hours, and `set_provider_products()` the provider's whole list of services: each replaces what was there.
- `set_observed_holidays()` with an empty list clears every holiday.
- A members-only service can be created before its membership, but nobody can book it until the membership links it.
- `update_business_page()` refused with `tax_address_locked`: don't retry it. Save the rest without the address and tell the owner to change the address in the dashboard.
- `list_products()` returns 25 at a time: check `complete` before saying how many services there are.
- Each save returns what it saved, so there is no need to list again after it.

## Hand off

- One more service, class, add-on or product, once the business is running: services-and-products.
- Memberships, packs and combos after setup: memberships-and-packs.
- Changing hours, closing a day or part of one: hours-and-closures.
- Booking a customer: book-and-reschedule.
- How the business is doing: weekly-review.
- Automatic follow-up emails and review links: automatic-follow-ups.
