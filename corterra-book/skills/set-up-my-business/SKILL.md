---
name: set-up-my-business
description: Sets up a new business on Corterra Book step by step, starting from how the business runs, explaining each booking rule in plain words and recommending what fits, or shows what is already set up and what is still missing. Use when the owner wants their business, studio, spa or booking page set up or finished, asks what is set up or missing, or asks what this connection can do. For adding or changing one service on a business already running, use services-and-products; for memberships and packs after setup, use memberships-and-packs; for changing hours or closing days, use hours-and-closures.
---

The tools here are on the Corterra Book connector. If a call fails because nothing is connected, tell the owner to connect Corterra Book from this plugin's Connectors tab, sign in with their dashboard email and password, and choose Read & write.

## Always

- Learn how the business runs (step 2) before going over any setting. Every recommendation comes from what the owner told you, with the reason in a few words.
- One topic at a time, in the order of the steps below. Before every save, show exactly what you are about to save and wait for the owner's yes.
- Save only what the owner told you or agreed to. Never invent a service, price, provider, hours or policy. A recommendation stays a suggestion until they say yes.
- A fact the owner hasn't given is a question, asked before you show the save, never a guess written into it: the days each provider works, the time an add-on adds, what each extra person in a group pays, and any claim in a description you draft, like "open to all levels". Never leave a question open in the message that shows a save: the owner says yes to the save and the question is lost.
- A value that matches the starting values below is what every new business begins with, not a choice the owner made. Never report one as "already set": call it the starting value and go over it in its step.
- Explain each setting in plain words: what it does and what their customers see because of it. Never a setting's name or a bare number on its own.
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
| rooms and providers (Pro) | `list_providers()`, `create_provider()`, `set_provider_products()` |
| memberships and packs | `list_memberships()`, `create_membership()`, `create_membership_agreement()`, `list_bundles()`, `create_bundle()` |
| the Business Page | `get_business_page()`, `update_business_page()` |

## Starting values

Every new business begins with these. Each is gone over in its step.

| Setting | Starts as |
|---|---|
| Timezone | UTC, which is wrong for almost every business |
| Accent colour | the house green, `#4a7c59` |
| Background colour | none, a light page |
| Font | the default |
| Time between appointments | 30 minutes |
| Start times | every 30 minutes |
| Clients at a time | 1 |
| Minimum notice | 4 hours |
| How far ahead customers can book | 0: no limit for appointments, a year ahead for classes |
| Cancellation cutoff | 0 hours: customers can cancel online right up to the start |
| Confirming bookings | off: every online booking waits for the owner's approval |
| Reminder email | the day before, at 8 in the morning |
| Reminder text | 24 hours before, only with the messaging add-on |
| Customer's address at booking | not asked |
| Memberships and packs | off |

## Steps

Copy this checklist and tick it off as you go.

- [ ] 1. **Read what is set up, once, before asking anything**: `get_business_info()`, `get_business_settings()`, `get_business_hours()`, `list_products()`, `list_providers()` and `get_business_page()`. Don't recite what you read. No hours and no services means a new business: say so in a line or two (for example, that nothing is bookable yet and the timezone is still UTC) and go to step 2. A business already running: see the paragraph after the steps.
- [ ] 2. **Get to know the business. Save nothing in this step.** Ask the owner to describe their business in a few sentences, in their own words. Then ask whatever their answer left out, at most four questions to a message:
  - what they offer, and how long a usual appointment or session runs;
  - a business that calls itself medical, like a med spa or a clinic: which of its treatments are medical, so the About text, the descriptions and any waiver say the same thing;
  - who does the work, and how many people work there;
  - rooms, stations or equipment booked on their own, like a sauna, a float tank or a treatment room: how many, and how many people each holds at once;
  - classes or group sessions at set times, like yoga at 9 and at 6, how many people one takes, and the room it runs in;
  - services for two or more people together, each with their own practitioner at the same time, like a couples massage;
  - whether they go to their customers;
  - how much time they need between customers: cleaning, turning a room over, travel, notes;
  - how far ahead their customers book, how much notice they need, their cancellation policy, and whether they want to approve each booking before it is confirmed;
  - what happens when a customer doesn't show up: whether they want a card on file when customers book online, and a fee for a no-show or a late cancellation;
  - the city or state they are in.

  Services and prices come later (step 6). Here you need only enough to shape the hours and the booking rules. Then read back in a few lines how you understood the business and what it means for the setup, for example: "Two private sauna rooms, one person each, 45-minute sessions and 15 minutes to clean: each room gets its own schedule, a session starts every hour, and 15 minutes are kept free after each one." Ask them to correct anything.
  - Separate rooms or more than one person doing the work, on a plan that is not Pro (`get_business_info()`'s `is_pro`): a schedule of their own for each room or person needs Pro. Say so in one sentence, and plan for one shared schedule, where every service shares one "clients at a time" number.
- [ ] 3. **Business details.** Confirm the business name, then set the timezone from where they are, as a name like `America/Denver`. Until it is right, the booking page's idea of "now" is off by hours: same-day times disappear early or times already past are offered, and reminder emails go out at the wrong hour. Save with `update_business_info()` and `update_business_settings()`, sending only what changes.
  - Colours, if the owner wants their own: the accent is still the house green and there is no background until they choose. Ask whether they have brand colours. The background (`secondary_color`) sits behind every page their customers see and the kiosk; the accent (`primary_color`) colours buttons, links, prices and their emails. Both are kept readable, so an accent that wouldn't read on the background is adjusted on the pages: say so rather than promise the exact colour everywhere, and point them to the Branding page's preview to see it. No background is a light page (`clear=["secondary_color"]` removes one).
  - The font family reaches their pages and the kiosk; the weight changes the mobile app only.
- [ ] 4. **Booking rules, one at a time.** For each rule in the table: say what it does and what customers see, give the starting value, recommend a value from step 2 with the reason, and ask. When every rule has an answer, read the whole list back and save it once with `update_business_settings()`, sending only what changes.
  - With rooms or providers (Pro), each one gets its own time between appointments, start times, clients at a time and minimum notice, and those decide its bookings. Skip those four here and go over them for each room and provider in step 7. Ask them here only for services that will be given to no room or provider.
  - A card on file at booking and the no-show fee can't be set from here. If the owner wants them (step 2), tell them where they are: Settings in the dashboard, then Card on File & No-Show Fees, which shows once payments are connected. List them again in step 10.

  | Rule | What it does, and what customers see | How to recommend |
  |---|---|---|
  | Time between appointments (`buffer_minutes`) | Time kept free after every appointment before the next one can start on the same schedule. The last appointment of the day must also leave that time before closing, so it moves the day's last start earlier. Customers simply see fewer start times. | The cleaning, turnover, travel or note time from step 2. Back to back is 0. |
  | Start times (`slot_interval_minutes`) | How often a start time is offered, counted from opening: open at 9 with every 30 minutes offers 9:00, 9:30, 10:00 and on. A time shows only when the service plus the time between appointments fits before closing. Classes ignore it and run at their own times. | Sessions of one length, one at a time in a room or chair: the session plus the time between, so sessions sit end to end and no gap is left too short to sell (45 minutes plus 15 is every 60). Several service lengths: every 15 or 30, for more choice. Keep it to 15, 30 or 60, the only spacings a room or provider takes and the ones the dashboard offers. |
  | Clients at a time (`max_concurrent_clients`) | How many people can be booked at overlapping times on the same schedule. Each person in a group counts. Classes don't count: a class has its own seats. Without rooms or providers, every service shares this one number. | One practitioner or one private room: 1. One space that holds several people at once, like a sauna seating six: that many. Separate rooms or several practitioners on Pro: a room or provider each in step 7, with 1 here. Not on Pro: the number of rooms or practitioners, and say that any service can take any of those places, so a massage takes one of the two sauna places. |
  | Minimum notice (`min_advance_hours`) | How close to a start time customers can still book online. 0 means right up to the start; a time already past never shows. | The warning they need to staff, heat or prepare. A business that takes same-day bookings: 0 to 2. |
  | How far ahead (`max_advance_days`) | How many days ahead customers can book an appointment. | How far ahead they plan their own schedule, often 30, 60 or 90 days. |
  | Cancellation cutoff (`cancellation_window_hours`) | How late customers can cancel by themselves, with the cancel link in their emails. Inside the cutoff the link says it is too late to cancel online and to contact the business; nothing is charged. 0 means right up to the start. | Their cancellation policy, often 24 or 48 hours. |
  | Confirming bookings (`auto_confirm`) | On: an online booking is confirmed at once; the customer's page says "You're booked!" and their confirmation email has a cancel link. Off: it waits for the owner's approval. The customer's page says "Your request is in!" and that the business will confirm soon, and their email says the same with no cancel link, so to change or cancel they contact the business. It gets no reminder until it is confirmed, and its time stays held. Nothing confirms or cancels it on its own: the owner confirms each one on the dashboard or with `confirm_booking()`. | On for most businesses. Off only for an owner who wants to approve every booking: tell them what their customers see, and that they must confirm each one. |
  | Customer's address (`require_address_at_booking`) | Customers type their street, city, state and ZIP when they book, and it is saved on their customer record. | On only for a business that goes to its customers. |
  | Reminders (`email_reminder_hour`, `sms_reminder_lead_hours`) | One reminder email the day before, at that hour of the business's day, to confirmed bookings. A reminder text that many hours before, only with the messaging add-on and the customer's consent. | 8 in the morning and 24 hours, unless they want other times. |
- [ ] 5. **Weekly hours and holidays.** Business hours are when the services given to no room or provider can be booked, and the hours the Business Page shows; a room or provider is booked only inside its own hours (step 7). Recommend hours from step 2 and the booking rules, and say where the day's last start falls: open 9 to 6 with 45-minute sessions, 15 minutes between and a start every 60 minutes, the last session starts at 5 and ends at 5:45. Ask for the open hours of each day. Save each open day with `add_business_hours()`: days run 0 = Monday to 6 = Sunday, times are 24-hour `HH:MM`, and a day holds one block of hours, so a weekly lunch break can't be set: say so (a gap on one date is a time block, hours-and-closures). A day that already has hours is refused with `day_has_hours`: remove its block with `remove_business_hours()` first. Ask which holidays they close for, offering the ones in `get_business_hours()`'s `holiday_catalog`, and use each holiday's `key` from there. `set_observed_holidays()` replaces the whole list, so send every holiday they observe, the ones already observed included. A date of their own that comes back every year goes to `add_custom_holiday()`.
- [ ] 6. **Services.** For each service ask the name, the length, the price and, for a group, what each extra person pays. An add-on: its price, the time it adds and which services offer it. A class or group session at set times from step 2 is a class: its seats and its times. Put each in a category: read `list_product_categories()`, and create one with `create_product_category()` only when none fits. Categories also decide the rebook reminder: it is skipped once the customer books anything in the same category, so a service customers come back for on its own timing gets a category of its own. Say so when you suggest categories. Save each with `create_product()`. If it is refused with `tax_code_required`, read `list_tax_codes()`, ask which code fits, and save again with it.
- [ ] 7. **Rooms and providers (Pro).** From step 2, each practitioner and each room or station booked on its own is a provider; a room is a provider with no staff, named the way customers know it ("Sauna 1"). A room that runs classes: ask whether it should have a provider of its own, which gives it its own calendar column, so the owner sees how much the room is used. If so, move its classes onto it with `update_product()` and `class_blocks`: every block, each repeat with its `series_id`. For each provider, ask which days it works and the hours of each day. Then go over its time between appointments, start times, clients at a time and minimum notice as in step 4's table, recommending from step 2. Create each with `create_provider()`: its whole week of hours, and its `buffer_minutes`, `slot_interval_minutes` (15, 30 or 60), `max_concurrent_clients` and `min_advance_hours`. Always send all four: one left out starts at no time between appointments, no notice, one client and every 30 minutes. Read each one back before saving. Then give each the services it does with `set_provider_products()`, and set a group service's group size now (Gotchas). A service given to two or more lets customers pick one, or take the next one free. A service for two or more people each with their own practitioner (step 2), once its providers exist: `update_product()` with `fixed_party_size`, `additional_seat_price`, `seats_on_separate_schedules` and those providers in `schedule_ids` (at least one per person). Customers then pick a provider for each person, or none when there are just enough.
- [ ] 8. **Memberships and packs, only if the owner sells them.** Turn them on with `update_business_settings()` (`enable_memberships_and_bundles`).
  - A membership: ask the name, the price, whether it bills monthly or yearly, the member discount, and any services it limits per month. Save with `create_membership()`.
  - An agreement members sign: write it in the owner's own words with `create_membership_agreement()` before the membership, show the whole text, and suggest whoever handles their legal documents checks it.
  - A pack: ask the services it covers, the number of visits, the price, and whether it expires. Save with `create_bundle()`.
  - A membership's name, price and billing period, and a pack's price and visit count, can't be changed from here afterwards, so read them back before saving.
- [ ] 9. **The Business Page.** Read `get_business_page()` and offer what is already on file. Write the tagline and the About text with the owner. Before saving, say that the page's email is also the reply-to address for their emails and its address is their sales-tax address. Save with `update_business_page()`, and publish only when they say so.
- [ ] 10. **Finish.** Read back what was set up, then list what they finish in the dashboard: connecting payments; then, if they want them from step 2, a card on file at booking and the no-show fee (Settings, then Card on File & No-Show Fees, which shows once payments are connected); the logo and photos, sales tax, staff accounts and the kiosk (Pro), and messaging. If bookings wait for their approval, remind them to confirm each one. Then offer the automatic follow-up emails and review links as a next step (automatic-follow-ups).

A business that is already set up: give a short picture of it, then one line on each thing you can help with next: booking and moving appointments, filling quiet days, winning back clients, automatic follow-up emails and review links, adding or changing services and products, memberships and packs, changing hours and closures, and a weekly review.

## Gotchas

- `create_provider()` takes the provider's whole week of hours, and `set_provider_products()` the provider's whole list of services: each replaces what was there.
- A provider's start times take only 15, 30 or 60 minutes; anything else is refused with `invalid_slot_interval_minutes`.
- A service saved in step 6 sits on business hours until step 7 gives it a provider, and business hours seat the business-wide clients at a time, 1 to start. A group larger than that is refused `party_exceeds_capacity`. On Pro, save a group service for one person, then send its `max_party_size` and `additional_seat_price` with `update_product()` once its room holds the group.
- A service with each person on their own provider needs Pro and is not yet offered with several locations (`separate_schedules_needs_pro`, `separate_schedules_not_on_growth`).
- How far ahead takes 1 day or more from here: once a number is saved, "no limit" can't be put back from this connection.
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
