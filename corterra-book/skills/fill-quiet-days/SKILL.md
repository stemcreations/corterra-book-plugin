---
name: fill-quiet-days
description: Finds a business's quiet days on Corterra Book and the regulars who usually book on those weekdays, then helps invite them with an email campaign, an offer email or a booking-page notice. Use when the owner says a day or a week is slow, empty or dead, asks where the gaps are and who to invite, or wants an offer aimed at the slow days. For regulars who stopped coming altogether, use win-back-clients; for booking one person, use book-and-reschedule.
---

The tools here are on the Corterra Book connector. If a call fails because nothing is connected, tell the owner to connect Corterra Book from this plugin's Connectors tab, sign in with their dashboard email and password, and choose Read & write.

## Always

- Nothing is emailed until the owner has seen the exact subject, the message and who it goes to, and said yes.
- The owner decides whether to make an offer at all. Suggest a small offer aimed at the quiet days rather than a discount for everyone, then use the owner's own numbers and wording.
- A promo code always has an end date: ask for it.
- A booking-page notice is words only. It never changes a price and never closes a time; a discount goes through a promo code.
- A campaign tool refused with `pro_required`: say in one sentence that email campaigns need the Pro plan, then offer a few one-at-a-time offer emails or a booking-page notice instead.
- Never take a payment or sell anything.

## Which tool answers what

| The owner wants | Tools |
|---|---|
| each column's quiet weekdays and who usually books them | `suggest_fill_campaign()` |
| open time per provider, day by day | `find_schedule_gaps()` |
| an offer | `list_promo_codes()`, `create_promo_code()` |
| an email to a group (Pro) | `preview_campaign_audience()`, `draft_campaign()`, `review_campaign()`, `send_campaign_test()`, `send_draft_campaign()`, `list_campaigns()` |
| an offer to one person | `send_offer_email()` |
| a notice on the booking page | `create_announcement()` |

## Steps

Copy this checklist and tick it off as you go.

- [ ] 1. **Who and when.** Read `suggest_fill_campaign()` once. It gives each column (a provider, a room, or General) its own quietest weekdays and, for each, the lapsed or overdue customers who have booked that column on that weekday. Give the owner one idea per column: the quiet day and how open it is, a few of the people with why they are on the list (how many times they came to that column on that day, when they last came), and the service they book there (`usual_service`, `top_services`). For open time per provider day by day, add `find_schedule_gaps()`.
- [ ] 2. **An offer, if they want one.** Ask. If yes, read `list_promo_codes()` and reuse a live code that fits. Otherwise ask the discount, which services, and the end date, create it with `create_promo_code()`, and read its `label` back.
- [ ] 3. **How to reach them**, the owner's choice:
  - An email campaign (Pro): `preview_campaign_audience()` with the quiet day's `customer_ids`, which says how many it reaches and whether today's sending limit spreads it over two days. Then `draft_campaign()` with those `customer_ids`, a subject, a short message, and the code's id as `promo_code_id` (or an item to sell as `shop_item_kind` + `shop_item_id`). Then `review_campaign()` and show the owner the subject, the message and the people. Ask whether they want a test copy in their own inbox first; on a yes, send it with `send_campaign_test()` and say where it went. Send with `send_draft_campaign()` only on their yes to sending.
  - A few people, one at a time: `send_offer_email()` for each customer, each after the owner's yes.
  - A notice for those days: `create_announcement()`, dated with `starts_on` and `ends_on`, `days_of_week` for the quiet days, and the code as `promo_code_id`.
- [ ] 4. **Afterwards.** `list_campaigns()` shows each campaign's opens, the bookings that used its code, the revenue, and what its Buy box sold.

## Gotchas

- `suggest_fill_campaign()` matches people by column and weekday, not by time of day.
- A column with a high `booked_same_day_pct` fills on the day (walk-ins), so its week ahead always looks open. Say so rather than calling it a gap, and lead with the columns people book ahead.
- Its `examples` lead with whoever booked that column on that weekday the biggest share of their visits there, so someone who came once, on that day, comes first (`visits_on_weekday` 1, `weekday_share_pct` 100). Call someone a regular only when they came several times; otherwise say they came once.
- People who had a marketing email within the business's `cooldown_days` are already left off its lists.
- `find_schedule_gaps()` names no customers: who to invite comes from `suggest_fill_campaign()`.
- Over the daily sending limit a campaign ends `partially_sent`. Call `send_draft_campaign()` again the next day: it reaches only the people not yet emailed, never anyone twice.
- `send_offer_email()` answering `failed` or `deferred` did not send: tell the owner, never say it went. Refused `opted_out`: that customer unsubscribed from marketing email.
- A code never adds to a membership discount, and members-only services never take a code.
- A "bring a friend" deal is a `buy_get` code, never `seats`.
- `draft_campaign()` given `customer_ids` ignores any `audience_type`.
- `{{first_name}}` in a campaign's subject or message becomes each customer's own first name, "there" when it is blank. It is the only placeholder; any other is refused.
- To sell a membership, package or combo, attach it: its `kind` and `id` from `get_online_shop()` as `draft_campaign()`'s `shop_item_kind` and `shop_item_id`. Every email then carries a Buy box to the item's own page, and `list_campaigns()` counts what it sold. An email carries a code or an item, not both. The item's `link` typed into the message opens the same page, but what it sells is not counted. Never a link copied from a checkout page (`/shop/order/…`): it is one customer's order and closes after a day, and the draft is refused.

## Hand off

- Regulars who stopped coming altogether: win-back-clients.
- Booking someone who replies: book-and-reschedule.
- How the quiet days went afterwards: weekly-review.
