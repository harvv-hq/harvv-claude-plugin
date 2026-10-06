# Intake: what to ask, and where it goes

Ask only what is not already saved. `get_setup_status` tells you: `account.is_agency`, `about_you.set`, and per site `about_this_client.set`, `conversion_goal.set`, `brand_names.set`, `business_context`. Three questions at a time at most. Plain words. Accept short answers.

## About the person (scope `me`, once)

1. What is your role, and who do you do this for? (owner, marketer, agency account manager, developer, freelancer)
2. How do you want answers: short with the one thing to do, or the full reasoning?
3. Anything about how you work that Harvv should keep in mind? (reports go to a boss, you present to clients, you only have an hour a week)

Save as one paragraph in their words, under 1000 characters:

> "Account manager at a 6-person agency; runs 4 Shopify clients. Wants the one fix first, numbers second. Presents to clients on Tuesdays."

## About the site, owner branch (scope `this_client`, per site)

1. What does this site sell or do, and who is the customer? (one line)
2. What counts as a win here: an order, a form, a call or booking, a sign-up? That answer sets `set_conversion_goal` (`add_to_cart` for a store, `form` for a form, call or booking, `cv` if the site sends its own event).
3. Who makes the final call on changes to the site: you, a boss, a team, someone else? That sets `who_decides`.
4. What is a good month, in your terms? (orders, leads, revenue, bookings)
5. Anything coming up Harvv should watch: a launch, a sale, a redesign, a new ad push?
6. Names the business is searched by besides the domain: former names, a store name, an old domain. That sets `set_brand_names`.

## About the site, agency or freelancer branch

Same six, asked about the client, plus:

7. Who at the client signs off, what do they care about most, and what do they push back on? (`who_decides` is usually `client`; the rest goes in notes)
8. What did the client hire you for: conversion, SEO, ads, a redesign, everything? This decides which scheduled reports make sense.
9. Does the client own the Google Analytics, Search Console, Shopify and ad accounts, or do you? This decides who connects what in pass 1; if the client owns them, offer to draft the request now.
10. How often do you meet the client, and on what day? This feeds pass 3 (the pre-meeting brief).

## Writing the `this_client` note

Under 1000 characters, in their words, facts only, nothing a client should not read about themselves even though the note is hidden from invited clients:

> "DTC coffee store, subscriptions are the goal (add_to_cart). Client is the founder, cares about subscription conversion and ad ROAS, pushes back on anything touching the theme. Good month = 400 subs. Black Friday sale starts Nov 20. Client owns GA4 and Shopify; agency owns Meta. Weekly call Tuesdays 10:00 CT."

Preview first (`confirm` omitted), read the preview back in one sentence, then save with `confirm=true`. Tell them where to edit it later: Settings > Site profile > About this client; the note about themselves is under Settings > About you.

## Questions not to ask

- Anything Harvv can observe: traffic levels, platform, whether the pixel fired. Read `get_setup_status` and `get_site_stack` instead.
- Passwords, API keys, account logins. Connections happen in the Harvv dashboard through the person's own sign-in.
- Industry classification. Harvv already keeps `business_context`; only ask if it is empty and the answer to question 1 does not make it obvious.
