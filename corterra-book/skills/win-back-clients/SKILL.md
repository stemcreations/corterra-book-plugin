---
name: win-back-clients
description: Finds a business's customers who used to come in regularly and stopped on Corterra Book, biggest loss first, along with pack holders who stopped using their visits, newer customers who drifted off and members who have not been in, then helps reach them with a call list, an email campaign or an offer email. Use when the owner asks who stopped coming, who to win back or reach out to, who has not been in for a while, who still has visits left on a pack, or which members are not using their membership. For filling one slow day or week, use fill-quiet-days; for booking someone who replies, use book-and-reschedule; for a win-back email that goes out on its own from now on, use automatic-follow-ups.
---

The tools here are on the Corterra Book connector. If a call fails because nothing is connected, tell the owner to connect Corterra Book from this plugin's Connectors tab, sign in with their dashboard email and password, and choose Read & write.

## Always

- Nothing is emailed until the owner has seen the exact subject, the message and who it goes to, and said yes.
- Never suggest a discount unless the owner asks for one. Lead with what the person already owns (visits left on a pack, a membership they pay for), or a personal check-in.
- Leave out anyone with a booking coming up: they are not lost.
- `missed_visits` is an estimate from the person's own average gap between visits. Say so whenever you quote it.
- Check `complete` before saying anything about a whole list. When it is false, say you are showing part of it.
- A campaign tool refused with `pro_required`: say in one sentence that email campaigns need the Pro plan, then offer the call list instead.
- A change refused with `read_only_key`: tell the owner to disconnect Corterra Book, connect it again and choose Read & write.
- Never take a payment or sell anything. A pack or membership is sold at the Front Desk in the dashboard.

## Which tool answers what

| The owner wants | Tools |
|---|---|
| regulars who stopped coming, biggest loss first | `find_win_back_customers()` |
| pack holders who stopped using their visits, and newer customers who came regularly then stopped | `find_customers_to_reengage()` |
| members who have not been in | `analyze_prepaid_usage()` with `view` `members` |
| one person's packs, membership and bookings | `get_customer_overview()` |
| a real open time to offer | `check_availability()` |
| an offer, only when the owner asks | `list_promo_codes()`, `create_promo_code()` |
| an email to the group (Pro) | `preview_campaign_audience()`, `draft_campaign()`, `review_campaign()`, `send_campaign_test()`, `send_draft_campaign()`, `list_campaigns()` |
| an offer email to one person | `send_offer_email()` |
| whether the automatic win-back email is on | `list_automations()` |

## Steps

Copy the list for the owner's question and tick it off, then reach the people the owner picks.

**Regulars who stopped coming**
- [ ] Read `find_win_back_customers()` once. When the owner names a service, pass its id from `list_products()` as `service_ids`; a category, its id from `list_product_categories()` as `category_id`. If the owner already said how many visits makes a regular, pass it as `min_visits` and go straight to the list.
- [ ] Otherwise leave `min_visits` out, show `summary.thresholds` (how many of the lapsed customers came at least 3, 5, 10, 15 and 20 times), and ask how many visits makes someone a regular at their business. Then read it again with their number as `min_visits`.
- [ ] Show the list biggest loss first: the name, the visits, how often they came, the last visit, the estimated missed visits, any visits left on a pack (`sessions_remaining`), and their usual day, time and service (`habits`). Leave out anyone with `has_upcoming` true.

**Pack holders and newer customers**
- [ ] Read `find_customers_to_reengage()` once. For `idle_packs`, show each person with what they still own (each pack's `reason_text`, said as it reads) and their usual day and time from `habits`.
- [ ] Newer customers who came regularly and stopped: the first read returns `new_regulars` with `needs_definition`. Show its `suggestions` and ask the owner three things: at least how many visits, no more than how many weeks apart, and a first visit within how many months. Read it again with all three. Ask every time; never reuse an earlier answer. Mention newer customers only when the owner asked about them.

**Members who have not been in**
- [ ] Read `analyze_prepaid_usage()` with `view` `members`. Show `bands`, the number of members by how long since their last visit, and pull the band the owner picks with `band`.
- [ ] For each member: their plan (`tier_name`), their last visit, and the visits they have left this month (`allowance_limits`). Leave out anyone with `upcoming_redemptions` above 0.

**Reach them**, the owner's choice of a call or text list or an email to the group. The offer below is only for an owner who asked for a discount: never list it as a choice.
- [ ] A call or text list, on any plan: the names, the number to dial (`phone_e164`) and email, and their usual day and time (`habits`, on both lists; a null one has no clear leader, so leave it blank). To offer a real time, read `check_availability()` for that day.
- [ ] An email to the group (Pro): `preview_campaign_audience()` with the chosen `customer_ids`, then `draft_campaign()` with those `customer_ids`, a subject and a short message, a `promo_code_id` only if the owner asked for an offer, and an item they want to sell as `shop_item_kind` + `shop_item_id`. Then `review_campaign()` and show the owner the subject, the message and the people. Ask whether they want a test copy in their own inbox first; on a yes, send it with `send_campaign_test()` and say where it went. Send with `send_draft_campaign()` only on their yes to sending.
- [ ] An offer the owner asked for: read `list_promo_codes()` and reuse a live code that fits, or ask the discount, the services and the end date, create it with `create_promo_code()`, and read its `label` back. For a few people, `send_offer_email()` to each, after the owner's yes for each.
- [ ] Afterwards, `list_campaigns()` shows each campaign's opens, the bookings that used its code, the revenue, and what its Buy box sold.
- [ ] To keep it going, read `list_automations()`. If the Win-back (`win_back`) is off, say that switched on, it emails each regular who stops coming on its own from then on, never the ones already lapsed, and offer to set it up (automatic-follow-ups). Switch nothing on from here.

## Gotchas

- A customer whose visits all fell within a few weeks (a small `active_span_days`) shows a huge `missed_visits`: they were visiting the area or did a short course, not a lost regular. Say so, or set them aside.
- `find_win_back_customers()` refused `invalid_window`: `window_days` must be larger than `lapsed_days`. Widen `window_days`.
- `find_customers_to_reengage()` leaves members off both lists (`members_left_off` counts them). Members come from `analyze_prepaid_usage()`.
- On `idle_packs` the people with no visit on record come last, and they are often the biggest group (the `never` band). A `used_undated` pack was used and only the dates were lost in an import: never say that person has never been in.
- A member with `payment_past_due` has a card problem the owner sorts out in the dashboard; a win-back note is not the answer. A plan for staff is not a lost customer either: show `tier_name` so the owner can skip it.
- Someone with `email_marketing_opted_out` unsubscribed from marketing email: they go on the call list, never in a campaign.
- A campaign sends one message to everyone, so it cannot name each person's own pack balance. For that, the owner calls or texts.
- `send_offer_email()` always carries a promo code, so it is never the way to send a check-in with no offer. Answering `failed` or `deferred`, it did not send: tell the owner.
- `min_visits` under 3 for newer customers is refused `min_visits_too_low`: two visits are one gap, not a habit.
- `{{first_name}}` in a campaign's subject or message becomes each customer's own first name, "there" when it is blank. It is the only placeholder; any other is refused.
- To sell a membership, package or combo, attach it: its `kind` and `id` from `get_online_shop()` as `draft_campaign()`'s `shop_item_kind` and `shop_item_id`. Every email then carries a Buy box to the item's own page, and `list_campaigns()` counts what it sold. An email carries a code or an item, not both. The item's `link` typed into the message opens the same page, but what it sells is not counted. Never a link copied from a checkout page (`/shop/order/…`): it is one customer's order and closes after a day, and the draft is refused.

## Hand off

- Filling one slow day or week: fill-quiet-days.
- Booking someone who replies: book-and-reschedule.
- How the business is doing week to week: weekly-review.
- Win-back, rebook and review emails that go out on their own: automatic-follow-ups.
