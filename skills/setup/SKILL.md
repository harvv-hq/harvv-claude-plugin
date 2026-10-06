---
name: setup
description: Set up Harvv end to end and keep it useful. Use this whenever someone is onboarding to Harvv, connecting it to Claude, asking "is everything connected", "what's missing", "why is there no data", "did my Shopify checkout pixel work", wants Harvv to understand their business or a client's business, or wants a report on a schedule (weekly review, first Monday of the month, two days before a client meeting). Also use it when an agency is adding a client site, or when any Harvv answer says a connector is missing. It checks what Harvv has actually observed, fixes what it can from here, collects what only the person knows, and turns "send me this every week" into a scheduled task that calls the right Harvv tools.
---

# Harvv setup

You are walking a person through getting Harvv to the point where it answers well, in three passes: the setup check, understanding the business, and scheduled reports. Each pass is useful on its own, so do the one they asked for and offer the others at the end. Keep the questions few and concrete: this is a person who wants their analytics working, not a form.

## Before you start: find the Harvv connector

Harvv is reached through its MCP connector. Look for tools named `get_setup_status`, `list_my_sites`, `install_pixel`. If they are present, carry on.

If they are not present, nothing below works, so stop and get it connected first:

- **claude.ai or the Claude desktop app:** Settings > Connectors > Add custom connector, URL `https://harvv.com/mcp`. The token comes from the Harvv dashboard: Settings > Integrations > AI assistant card > Create token (shown once). Harvv's own help for this is the `mcp-connect` entry; once connected, `get_help` returns it word for word.
- **Claude Code:** `claude mcp add --transport http harvv https://harvv.com/mcp`, then sign in when prompted.
- No account yet: harvv.com, sign up free, add the site, then come back.

Never ask for a Harvv password in chat and never offer to set one. If the person has the older Claude Code plugin (`/harvv:install` with an `hv_live_` API key), that still installs the pixel, but this skill needs the connector for everything else.

## Pass 1: the setup check

Call `get_setup_status` with no arguments. It returns, for every site on the account, a checklist with a status Harvv observed (`done`, `todo`, `waiting`, `n/a`) and `what_to_do_first` across sites. Its statuses are evidence: a pixel is live only if events arrived, a Shopify checkout pixel is set up only if a checkout step arrived, an integration counts only if its token is current. Treat that as the truth even when the person says they already did a step; say what Harvv has seen and what would prove the step ("Harvv has not received an event from example.com yet; loading the site once in a normal browser is the proof").

Walk `what_to_do_first` in order. For each gap:

| Gap | What you can do here | What someone else owns |
|---|---|---|
| `pixel` todo | `install_pixel` for the snippet and where it goes on their platform. If you have file access to the site's code, place it. Then `check_install` with the live URL (never a preview URL). | A developer or theme editor if the person cannot edit the site. Draft them a short message with the snippet and placement. |
| `pixel` waiting (quiet) | `check_install` on the live URL: tag present means traffic stopped; tag missing means it was removed. | Whoever edits the site, if removed. |
| `checkout_pixel` todo (Shopify) | Relay `checkout_pixel.install.steps` exactly: Shopify admin > Settings > Customer events > Add custom pixel, paste the JavaScript from `snippet_url` (not a script tag), Customer privacy Required with only Analytics ticked, Save, Connect. Proof is the first checkout step arriving, so set the expectation. | The store owner if the person is an agency without admin access. |
| `conversion_goal` todo | Ask what a win is on this site, then `set_conversion_goal` with `confirm=true`. See the intake reference for the wording. | Nobody else. |
| `brand_names` todo | Ask for the business name, former names, store names, old domains, then `set_brand_names` with `confirm=true`. | Nobody else. |
| `about_this_client` todo | Pass 2 collects it. | Nobody else. |
| `ga4`, `gsc`, `slack`, `ad_accounts` | Give the Studio link and say who can do it (`who` in the checklist). These are optional; say what each unlocks and move on. | Whoever administers that account. When the person is an agency, the client may own it: offer to draft the request. |

Then report the result plainly: what is live, what is waiting for proof, what is handed to someone else. One line per site. When a site is `all_required_done`, say so and move to pass 2.

## Pass 2: understand the business (and the client)

Harvv frames every answer with two notes it keeps: a note about the person (`about_you`) and, per site, who makes the call and what they care about (`about_this_client`). Both are saved through `set_working_context`. This is how answers stop being generic: an agency account manager whose client is a nervous founder gets a different answer than a solo owner who decides on the spot.

Read `references/intake.md` for the questions. Pick the branch from `get_setup_status`'s `account.is_agency` (agency or freelancer: client questions; otherwise: owner questions). Ask at most three questions at a time, in plain words, and only what is not already saved (`about_this_client.set`, `conversion_goal.set`, `brand_names.set` tell you). Skip anything the person already told you in this conversation.

Turn the answers into:

1. `set_working_context` scope `me` (once per person): their role, who they work for, how they like answers. Under 1000 characters, in their own words.
2. `set_working_context` scope `this_client` per site: `who_decides` (`me`, `my_boss`, `my_team`, `client`, `other`) plus notes: what the business sells and to whom, what a good month looks like, what the decider cares about and pushes back on, anything launching soon. Under 1000 characters.
3. `set_conversion_goal` and `set_brand_names` if still unset.

Call each tool once without `confirm` to show the preview, read it back in one sentence, then call with `confirm=true` after they agree. Save only what they said; do not infer a decider from a job title. Never write anything a client should not see into `about_this_client` (the note is hidden from clients the agency invited, but the person should still be told where it lives: Settings > Site profile > About this client).

## Pass 3: scheduled reports

The person wants Harvv to come to them. Find out three things: which report, for whom, and when. `references/report-recipes.md` has the reports (weekly friction review, pre-meeting client brief, monthly search report, speed check, ad traffic check, launch watch, "is this normal", setup watch); each names the Harvv tools to call and the honesty rules. `references/scheduling.md` says how to create the schedule on each surface, how to turn "two days before our Tuesday meeting" into a schedule, and the prompt skeleton.

Rules that keep these reports worth reading:

- **Pick a cadence the data supports.** Friction and speed need a week; search and keywords need a month; a setup watch can be daily until the pixel is live, then stop. `get_setup_status` tells you which tools will have data, so do not schedule a search report for a site with no Search Console.
- **The prompt must stand alone.** The scheduled run starts with no memory of this conversation: it needs the site id or domain, the reader, the tools to call with their arguments, the output shape, and the rule that every number comes from a tool result. Copy the recipe and fill it in; do not summarise it.
- **Meeting-relative dates.** If a calendar connector is available, find the recurring meeting and compute the day (a Tuesday 10:00 meeting with a two-day lead is Sunday; move to the preceding Friday morning if they prefer a workday). Otherwise ask for the weekday and go from there. Confirm the resolved local day and time before creating anything.
- **Where it lands.** The scheduler's own output is the default. If Slack is connected on the Harvv account, the person can also ask Harvv in the channel; if a Slack or email connector is present in this session, the prompt can post or send, but say so and get a yes first.
- **One schedule per report.** Create it with the scheduler available in this session (claude.ai or desktop scheduled tasks, or a Claude Code routine with the Harvv connector attached). If no scheduler is available, hand over the finished prompt and the exact steps to create the task, so nothing is lost.

After creating one, run it once if the surface allows, read the output, and fix the prompt before leaving it on a schedule. A report that reads well on its first run is the only kind worth automating.

## Finish

End with one short recap the person can forward:

- Per site: live, waiting for proof, or handed to someone (with the drafted request).
- What Harvv now knows about the business and where to edit it.
- Each scheduled report: name, cadence in their local time, what it reads, where it lands.
- When to run this skill again: after a new site is added, after a platform change (new theme, new checkout), or when a report says a connector is missing.

Honesty throughout: a number comes from a tool result or it is not stated; a step is done when Harvv observed it; a limit a tool reports is relayed, with the `next_steps` the tool returns, not worked around.
