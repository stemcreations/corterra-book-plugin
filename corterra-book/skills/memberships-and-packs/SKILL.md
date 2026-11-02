---
name: memberships-and-packs
description: Sets up and changes what customers of a business on Corterra Book prepay for, memberships with the services they include, their monthly limits, member discount, sharing and friend passes, the agreement members sign, packs of visits, combos, and how each is listed on the online shop, and takes one off sale or deletes it. It never sells anything. Use when the owner wants to create a membership, change what a membership includes or how many visits it allows, write or attach a membership agreement, create or change a pack or punch pass, put packs together in a combo, list one on the online shop, or stop selling one. For a single service, class or add-on, use services-and-products; for setting up a brand-new business, use set-up-my-business.
---

The tools here are on the Corterra Book connector. If a call fails because nothing is connected, tell the owner to connect Corterra Book from this plugin's Connectors tab, sign in with their dashboard email and password, and choose Read & write.

## Always

- Before every save, read back what will be saved and wait for the owner's yes.
- Save only what the owner told you or agreed to. Never invent a name, a price, a visit count or a limit. An agreement and a shop blurb are the owner's own words: write them with the owner or leave them out.
- Before changing something, read it in full with `list_memberships()`, `list_bundles()` or `list_combos()`, and send only what changes. A list you send replaces the whole list.
- A change to a pack's services reaches every customer who already holds it: say so and ask first.
- To stop selling something, turn it off. Deleting is for good: run the delete's preview first and show it.
- A change refused with `read_only_key`: tell the owner to disconnect Corterra Book, connect it again and choose Read & write. Refused with `pro_required`: say in one sentence that it needs the Pro plan, then carry on with the rest.
- Never sell anything or take a payment. Memberships, packs and combos are sold at the Front Desk and on the online shop.

## Which tool answers what

| The owner wants | Tools |
|---|---|
| memberships and packs switched on (one switch for both) | `get_business_settings()`, `update_business_settings()` |
| a membership | `list_memberships()`, `create_membership()`, `update_membership()` |
| the services a membership includes | `list_products()` with `search`, for the members-only versions |
| the agreement members sign | `list_membership_agreements()`, `create_membership_agreement()`, `update_membership_agreement()` |
| a pack | `list_bundles()`, `create_bundle()`, `update_bundle()` |
| a combo | `list_combos()`, `create_combo()`, `update_combo()` |
| the form a sale emails | `list_forms()` |
| a tax category | `list_tax_codes()` |
| the online shop | `get_online_shop()`, `update_online_shop_item()`, `update_online_shop()` |
| to stop selling one | `is_active` false on `update_membership()`, `update_bundle()` or `update_combo()`; `delete_membership()`, `delete_bundle()` |

## Steps

Copy the list for the job and tick it off.

**Something new**
- [ ] Read `get_business_settings()`. With `memberships_enabled` false, memberships and packs are off: ask, and on a yes switch both on with `update_business_settings()` and `enable_memberships_and_bundles`.

**A new membership**
- [ ] Read `list_memberships()` once. Ask for what is missing of the name, the price, and monthly or yearly. Offering both means two memberships.
- [ ] Find what it includes: `list_products()` with `search` for the members-only version of each service (`is_member`). With none, it is made first in services-and-products. Ask which included services are capped and how many times a month; every other one is unlimited.
- [ ] Offer, as one question, what the business's other memberships carry: the member discount, sharing and with how many people, bring-a-friend passes, the agreement, the form a sale emails, and hiding the public version of an included service from members (`hidden_products`). Add only what the owner says yes to.
- [ ] Read it all back: the name, the price per month or per year, each included service with its cap or "unlimited", every extra, and the tax category (the one the other memberships carry, or none unless the owner picks one from `list_tax_codes()`). Say that once saved, its name, price and tax category change only in the dashboard, and monthly or yearly never changes. Wait for the yes, then `create_membership()`.
- [ ] It starts off the online shop: offer to list it.

**A membership change**
- [ ] Read `list_memberships()` and find it. Its services are ids: name them from `list_products()`.
- [ ] Read back the old and the new, wait for the yes, and save with `update_membership()`, sending only what changes. To change one cap, send every cap; to add a service, send every included service plus the new one.
- [ ] Caps: when one number covers several services (a `monthly_limits` row listing more than one), send `monthly_limits`, every row with its `id`. Otherwise send `service_limits` with every cap. Never both in one call.
- [ ] The name, price or tax category: the owner changes those in the dashboard (Memberships). Monthly or yearly never changes: the other one is a new membership.
- [ ] A new member discount changes what members pay at a visit, not what the membership bills.

**An agreement**
- [ ] Read `list_membership_agreements()`, and show the whole text of the one being changed.
- [ ] Take the text in the owner's own words. Draft one only when asked, show all of it, and say whoever handles their legal documents should check it. Leave prices and dates out: the member's name, membership, price and the end of the minimum term are added when they sign.
- [ ] Ask the minimum term, if any. It shows on the member's account and is never enforced: never say a member cannot cancel. Members sign on the iPad kiosk; ask before letting staff email it too (`allow_email_signing`).
- [ ] On the yes, save with `create_membership_agreement()` or `update_membership_agreement()`, then attach it with `update_membership()` and `agreement_id` on each membership the owner names. It applies to sales from then on: current members are never asked to sign.

**A new pack**
- [ ] Read `list_bundles()`, and `list_products()` with `search` for the services it covers. Ask for what is missing of the services, the number of visits, the price and its name.
- [ ] Ask whether it expires. The default is never; otherwise a length in days, weeks or months, from the purchase or from the first visit.
- [ ] Read it back, wait for the yes, and save with `create_bundle()`. It starts off the online shop: offer to list it.

**A pack change**
- [ ] Read `list_bundles()`. A new name, on or off, the expiry or the form a sale emails: `update_bundle()` with only that.
- [ ] Its services: send every service that stays plus the change. Everyone who already holds the pack gets the new set at once, and loses any service taken out: say so and wait for the yes. To leave current holders as they are, make a new pack for future sales and turn the old one off instead; they keep using what they hold.
- [ ] The price or the number of visits: the owner changes those in the dashboard (the pack's Edit sheet).
- [ ] A new expiry never reaches packs already sold.

**A combo**
- [ ] Ask the name, the one price, and its parts, each "this many visits for any of these services". Services that each need their own count go in parts of their own.
- [ ] Read it back, wait for the yes, and save with `create_combo()`. It starts off the online shop.
- [ ] A change: read `list_combos()`, then `update_combo()`. A new price or new parts reach only future sales; what customers bought stays theirs.

**The form a sale emails**
- [ ] Read `list_forms()` and offer the forms whose `trigger` is `when_sold`. Ask which, never pick one, and save its id as `sale_form_id` on the membership, pack or combo; `0` removes it.

**The online shop**
- [ ] Read `get_online_shop()` and say what customers would see: the `name`, the price and `includes`.
- [ ] On a yes, list or unlist one item with `update_online_shop_item()`, `kind` and `item_id` from `get_online_shop()`: its name for customers, a blurb in the owner's words, who may buy it (`purchase_rule`) and its dates.
- [ ] The shop's own switch, its note and gift cards: `update_online_shop()`, on a yes.

**Stop selling it**
- [ ] For now: `is_active` false on `update_membership()`, `update_bundle()` or `update_combo()`. Customers keep what they bought.
- [ ] For good: `delete_membership()` or `delete_bundle()` without `confirm`, show the preview, and on the yes call again with `confirm`. No tool deletes a combo or an agreement: the owner does that in the dashboard.

## Gotchas

- A membership's `price` is per `billing_interval`: a yearly one's price is the whole year.
- Never add a membership's caps up: services that share one number each show the whole number. State totals from `monthly_limits` ("8 a month across X and Y").
- `linked_product_ids`, `service_limits`, `monthly_limits`, `hidden_products` and `friend_voucher_product_ids` each replace the whole set, and a capped service left out becomes unlimited. A pack's `session_option_ids` and a combo's `components` replace the whole set too.
- A membership includes only members-only services (`is_member`); a public one is refused `unknown_product`. A hidden public service comes back for a member once a capped included service's visits for the month are used.
- Refused `in_use`: a membership anyone is on, paused members included, can be neither turned off nor deleted until the owner moves or cancels them from each customer's profile. A pack with visits left is turned off, not deleted. A combo's parts cannot change while a sale of it is under way. Say so; never retry or work around it.
- Memberships and packs are one switch: read `memberships_enabled`, and no other field, before saying either is on or off.
- A combo's part is not a pack: `update_bundle()` refuses it `not_a_bundle`, so change it with `update_combo()`.
- Deleting a membership takes it off its members-only services. One left with no membership stays on sale, but nobody can book it: say so.
- A form asked at bookings is refused `not_sent_when_sold`. With the owner's yes, change its `trigger` to `when_sold` with `update_form()` first.

## Hand off

- A service, class or add-on, or a members-only version of one: services-and-products.
- A brand-new business: set-up-my-business.
- Booking someone with their pack or membership: book-and-reschedule.
- Pack holders and members who stopped coming: win-back-clients.
