---
name: automatic-follow-ups
description: Sets up the follow-up emails a business on Corterra Book sends on its own, the thank-you and review ask after a visit, the rebook reminder for each service and the win-back (and says where the New-customer welcome in a first booking's confirmation is changed), and on Pro its own custom follow-ups (after certain services, a first visit, a lapse, a cancel, a no-show or a pack running low), with their wording, waits, checks, offers, notes to the business and the review links they show, switches each on or off, and reads what they sent and who booked after. Use when the owner wants review requests, rebooking reminders, a welcome, win-back emails, or a follow-up after certain services, a no-show, a cancel or a pack running low to go out automatically, wants to change their wording or timing, add review links, or asks how the follow-ups are doing. For reaching lapsed customers once, now, use win-back-clients; for how the whole week went, use weekly-review.
---

The tools here are on the Corterra Book connector. If a call fails because nothing is connected, tell the owner to connect Corterra Book from this plugin's Connectors tab, sign in with their dashboard email and password, and choose Read & write.

## Always

- A follow-up switched on emails the business's customers on its own from then on. Switch one on only after the owner has seen its subject and wording and said yes to switching it on.
- Before every save, show exactly what you are about to save and wait for the owner's yes. You may draft the wording: short, plain, in the business's own voice.
- Never write a greeting, a link, a button or a sign-off into the wording. The email adds "Hi Maria," from the customer's first name, its own buttons, and the business's name, address and unsubscribe link.
- Never put an offer on the thank-you, and never word it to ask only happy customers for a review or to send unhappy ones somewhere else: Google forbids both.
- An offer is a retention code, and only when the owner asks for one.
- Never ask anyone for a Yelp review. A Yelp link is shown on the Business Page and never emailed.
- A custom follow-up is made switched off. Make it, change it or delete it only after the owner's yes to exactly what you showed them.
- A change refused with `read_only_key`: tell the owner to disconnect Corterra Book, connect it again and choose Read & write.
- Never take a payment or sell anything.

## Which tool answers what

| The owner wants | Tools |
|---|---|
| the follow-ups, their wording and settings, and each service's rebook weeks | `list_automations()` |
| new wording, an offer, the win-back's timing, or one switched on or off | `update_automation()` |
| when a visit counts: checked out, or its time has passed | `set_followups_start()` |
| which services get a rebook reminder and when, and which the emails name | `set_service_followups()` |
| the review links | `get_review_links()`, `update_review_links()` |
| a follow-up of their own (Pro): after certain services, a first visit, a lapse, a cancel, a no-show or a pack running low, with its own waits, checks, branches and emails | `list_custom_followups()`, `create_custom_followup()` |
| a custom follow-up changed, switched on or off | `update_custom_followup()` |
| a custom follow-up gone for good | `delete_custom_followup()` |
| an offer code for a follow-up | `list_promo_codes()`, `create_promo_code()` |
| how the follow-ups are doing | `get_automation_results()`, `list_automation_emails()` |
| what one customer got | `list_customers()`, `get_customer_automations()` |

## Steps

Copy the list for the owner's question and tick it off.

**Setting them up**
- [ ] Read `list_automations()` and `get_review_links()` once. Tell the owner in a few lines which follow-ups are on, when a visit counts (`start`), which services have a rebook reminder, and which review links are set.
- [ ] When a visit counts. Out of the box it is when the visit is checked out at the front desk: the thank-you goes that moment, and a visit nobody checks out starts nothing. The other choice is once the appointment time has passed, and then the follow-ups go the next day. Change it with `set_followups_start()` only if the owner wants the other.
- [ ] Review links, before the thank-you goes on. Ask for their Google review link, their Facebook page and any other review site; offer `facebook_suggestion` when it is there. Save with `update_review_links()`. A thank-you with no link to show is skipped.
- [ ] Rebook weeks. For each service, suggest how many weeks until a client usually comes back for it, ask, and save with `set_service_followups()`. Ask which services the emails may name; leave anything private unnamed.
- [ ] Each follow-up the owner wants. Show its subject and wording from `list_automations()`, or your draft. Save the wording with `update_automation()` `emails` after the yes, then switch it on with `update_automation()` `enabled` true after a clear yes to switching it on.
- [ ] The win-back. Confirm how long without a visit starts it (`lapse_days`, out of the box 60), whether to use each customer's own usual gap instead (`use_rhythm`), and the days before its second email (`wait_days`, out of the box 14).
- [ ] An offer the owner asked for, on the rebook reminder or the win-back. Read `list_promo_codes()` and reuse a retention code that fits, or ask the discount and how many days it is good for after the email, create it with `create_promo_code()` (`purpose` `retention`), read its `label` back, and attach it as that email's `promo_code_id`.

**A follow-up of their own (Pro)**
- [ ] Read `list_custom_followups()` and `list_automations()` once. If `pro` is false, tell the owner custom follow-ups are part of Pro and stop; any they made before are kept, switched off.
- [ ] Ask what starts it, one of `starts` (its `trigger`): a visit to certain services (find their ids in `list_automations()`' `services`), a first visit, a lapse (how many days without a visit, or each customer's own usual gap), the customer cancelling, a no-show, or a pack running low (how many visits left). A follow-up keeps what starts it. To start from the win-back or the rebook reminder, `copy_from` it; a rebook copy still needs its services.
- [ ] Draft the steps from what the owner describes: the waits in days (4 weeks is 28) and each email's subject and wording. Where the owner wants it, add a check ("stop here if they've booked since" or "if they have a membership"), a branch (if so these steps, otherwise those, then on past it), or an email to the business about the customer (`email_business`, to the Business Page email unless they give an address). End on an email. Up to 10 steps, those inside a branch included. Give it a short name, like "Massage rebook" or "After a no-show".
- [ ] Show the name, what starts it, any services, every step and what stops it (`starts`' `stops`). For a visit to certain services, say each of those services stops getting the ready-made rebook reminder while this one is on. After the yes, make it with `create_custom_followup()`; it is saved switched off.
- [ ] Switch it on with `update_custom_followup()` `enabled` true after a clear yes to switching it on.
- [ ] To change one, read its steps from `list_custom_followups()`, change what the owner asked, and send every step back in order, a branch with its `yes` and `no`, each one that stays with its `id`.

**How they are doing**
- [ ] Read `get_automation_results()`: the last 30 days, unless the owner names 7, 90 or all time. For each follow-up that is on, give what was sent, opened and clicked, how many booked within 7 days, the code used, and what was skipped and why. The custom follow-ups are its `custom` rows.
- [ ] The latest emails: `list_automation_emails()`. One customer: find them with `list_customers()`, then `get_customer_automations()`.

## Gotchas

- Nothing from before a follow-up was switched on starts it: no thank-you for last month's visits, and no win-back for everyone who had already stopped coming.
- The New-customer welcome is no email: it is a welcome inside the confirmation of a customer's first booking, on out of the box. No tool changes it. To change its wording or switch it off, the owner opens the dashboard: Automations, then Customer follow-ups.
- At most one automatic email goes to a customer a day. When two are due, the other waits a day.
- `emails` is in order, one object per email, each with only what changes. The win-back's second email alone is `[{}, {"subject": "…"}]`.
- `promo_code_id` `0` takes an offer off. An ordinary code, made without `purpose` `retention`, is refused there.
- A save refused with `invalid_followup` or `invalid_review_link` carries `errors`, one sentence per field. Read them to the owner: nothing was saved.
- A review link must be on that site's own address. Ask for the link from their review page; never build one.
- `booked` counts a booking made within 7 days of the email, not proof the email brought it. `clicked` counts only while click tracking is on, so a zero there does not mean nobody clicked.
- Skips with "No mailing address" mean the emails have no business address for their footer: the owner adds the address in the dashboard.
- The thank-you also shows "Follow us on Instagram" when the Business Page has an Instagram link.
- The ready-made follow-ups work on every plan; custom follow-ups and an email campaign to a group need Pro. A custom follow-up refused `pro_required` means the business is not on Pro.
- A custom follow-up stops on its own by what starts it: after certain services, a booking of one of them (anything else leaves it going); a lapse, any booking; a cancel or a no-show, a booking made after it; a pack running low, buying a pack. A first visit's runs to its end unless a check stops it. Two can share a service: one email a day keeps them apart.
- Only the customer's own cancel, from their email or the app, starts a cancellation follow-up; a cancel by staff starts nothing.
- An email to the business never counts as the customer's email for the day, and it goes even if the customer unsubscribed. A check, a branch or an email to the business happens right away, once the waits before it are up.
- `steps` replaces the whole list. A step sent without its `id` is a new one, and anyone waiting on a step you left out moves on to the next.
- To pause a custom follow-up, switch it off; `delete_custom_followup()` asks first (call it without `confirm`) and cannot be undone.

## Hand off

- Reaching lapsed customers once, now, with a call list, a campaign or an offer email: win-back-clients.
- How the whole week went: weekly-review.
- A slow day or week to fill: fill-quiet-days.
- The Business Page's address or Instagram link: set-up-my-business.
