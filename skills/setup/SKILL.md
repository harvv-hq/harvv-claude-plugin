---
name: setup
description: Set up Harvv end to end and keep it useful. Use this whenever someone is onboarding to Harvv, connecting it to Claude, asking "is everything connected", "what's missing", "why is there no data", "did my Shopify checkout pixel work", wants Harvv to understand their business or a client's business, or wants a report on a schedule (weekly review, first Monday of the month, two days before a client meeting). Also use it when an agency is adding a client site. When a Harvv answer in the middle of other work says a connector is missing, finish that answer and offer the setup check; do not switch into this skill unasked. It checks what Harvv has actually observed, fixes what it can from here, collects what only the person knows, and turns "send me this every week" into a scheduled task that calls the right Harvv tools.
---

# Harvv setup

You are walking a person through getting Harvv to the point where it answers well, in three passes: the setup check, understanding the business, and scheduled reports. Each pass is useful on its own, so do the one they asked for and offer the others at the end. Keep the questions few and concrete: this is a person who wants their analytics working, not a form.

## Before you start: find the Harvv connector

Harvv is reached through its MCP connector. Look for the tools `get_setup_status`, `list_my_sites`, `install_pixel`. Hosts prefix connector tools (`mcp__Harvv__get_setup_status`, `mcp__<id>__list_my_sites`), so match on the end of the name, not the whole name. Many hosts also defer connector tools until searched for: if you have a tool search, search for `get_setup_status` before deciding Harvv is missing. If they are present, carry on.

If none of them are present after that search, nothing below works, so stop and get it connected first. If `list_my_sites` is there but `get_setup_status` is not, the connector is an older Harvv: do pass 1 by hand (`list_my_sites`, `check_install` on each live URL, and ask the person about the rest), and say that the checklist will be fuller once Harvv updates.

- **claude.ai or the Claude desktop app:** Customize > Connectors > Add custom connector, URL `https://harvv.com/mcp`, Add, then Connect and sign in with the Harvv email. No token is needed. On Team and Enterprise, an Owner adds it once under Organization settings > Connectors and each person then clicks Connect. Only if the organization's dialog shows Request headers and the person prefers a token: create one in the Harvv dashboard (Settings > Integrations > AI assistant card > Create token, shown once) and paste it there as `Authorization: Bearer <token>`. Harvv's own help for this is the `mcp-connect` entry; once connected, `get_help` returns it word for word.
- **Claude Code:** `claude mcp add --transport http harvv https://harvv.com/mcp`, then sign in when prompted.
- No account yet: harvv.com, sign up free, add the site, then come back.

Never ask for a Harvv password or token in chat, and if someone pastes a token, tell them to revoke it in Settings > Integrations and make a new one. Never offer to set a password. If the person has the older Claude Code plugin (`/harvv:install` with an `hv_live_` API key), that still installs the pixel, but this skill needs the connector for everything else.

## Pass 1: the setup check

Call `get_setup_status` with no arguments. It returns, for every site on the account, a checklist with a status Harvv observed (`done`, `todo`, `waiting`, `n/a`) and `what_to_do_first` across sites. Its statuses are evidence: a pixel is live only if events arrived, a Shopify checkout pixel is set up only if a checkout step arrived, an integration counts only if its token is current. Each site also carries `pixel.state`: `live`, `quiet` (the checklist's `waiting`) or `never` (no event has ever arrived; the checklist's `todo`). Treat the statuses as the truth even when the person says they already did a step; say what Harvv has seen and what would prove the step ("Harvv has not received an event from example.com yet; loading the site once in a normal browser is the proof").

Walk `what_to_do_first` in order. For each gap:

| Gap | What you can do here | What someone else owns |
|---|---|---|
| `pixel` todo | `install_pixel` for the snippet and where it goes on their platform. If you have file access to the site's code, place it. Then `check_install` with the live URL (never a preview URL). | A developer or theme editor if the person cannot edit the site. Draft them a short message with the snippet and placement. |
| `pixel` waiting (quiet) | `check_install` on the live URL: tag present means traffic stopped; tag missing means it was removed. | Whoever edits the site, if removed. |
| `checkout_pixel` todo (Shopify) | Relay `checkout_pixel.install.steps` exactly: Shopify admin > Settings > Customer events > Add custom pixel, paste the JavaScript from `snippet_url` (not a script tag), Customer privacy Required with only Analytics ticked, Save, Connect. Proof is the first checkout step arriving, so set the expectation. | The store owner if the person is an agency without admin access. |
| `conversion_goal` todo | Ask what a win is on this site, then `set_conversion_goal` with `confirm=true`. See the intake reference for the wording. | Nobody else. |
| `brand_names` todo | Ask for the business name, former names, store names, old domains, then `set_brand_names` with `confirm=true`. | Nobody else. |
| `about_this_client` todo | Pass 2 collects it. | Nobody else. |
| `ga4`, `gsc`, `slack`, `ad_accounts` | For `ga4` and `gsc`, call `get_connect_link` and give the one-time link (it works only for this person; if a client owns the property, they add this person as a user first). For `slack` and `ad_accounts`, give the Studio link. Say who can do it (`who` in the checklist). These are optional; say what each unlocks and move on. | Whoever administers that account. When the person is an agency, the client may own it: offer to draft the request. |

Then report the result plainly: what is live, what is waiting for proof, what is handed to someone else. One line per site. When a site is `all_required_done`, say so and move to pass 2.

## Pass 2: understand the business (and the client)

Harvv frames every answer with two notes it keeps: a note about the person (`about_you`) and, per site, who makes the call and what they care about (`about_this_client`). Both are saved through `set_working_context`. This is how answers stop being generic: an agency account manager whose client is a nervous founder gets a different answer than a solo owner who decides on the spot.

Read `references/intake.md` for the questions. Pick the branch from `get_setup_status`'s `account.is_agency` (agency or freelancer: client questions; otherwise: owner questions). Ask at most three questions at a time, in plain words, and only what is not already saved (`about_this_client.set`, `conversion_goal.set`, `brand_names.set` tell you). Skip anything the person already told you in this conversation.

Turn the answers into:

1. `set_working_context` scope `me` (once per person): their role, who they work for, how they like answers. Under 1000 characters, in their own words.
2. `set_working_context` scope `this_client` per site: `who_decides` (`me`, `my_boss`, `my_team`, `client`, `other`) plus notes: what the business sells and to whom, what a good month looks like, what the decider cares about and pushes back on, anything launching soon. Under 1000 characters.
3. `set_conversion_goal` and `set_brand_names` if still unset.

Call each tool once without `confirm` to show the preview, read it back in one sentence, then call with `confirm=true` after they agree. With nobody there to agree (a scheduled run), never call a `set_` tool: list what it would have saved and leave it for the person. Save only what they said; do not infer a decider from a job title. Never write anything a client should not see into `about_this_client` (the note is hidden from clients the agency invited, but the person should still be told where it lives: Settings > Site profile > About this client).

## Pass 3: scheduled reports

The person wants Harvv to come to them. Find out three things: which report, for whom, and when. `references/report-recipes.md` has the reports (weekly friction review, pre-meeting client brief, monthly search report, speed check, ad traffic check, launch watch, "is this normal", setup watch); each names the Harvv tools to call and the honesty rules. `references/scheduling.md` says how to create the schedule on each surface, how to turn "two days before our Tuesday meeting" into a schedule, and the prompt skeleton.

Rules that keep these reports worth reading:

- **Pick a cadence the data supports.** Friction and speed need a week; search and keywords need a month; a setup watch can be daily until the pixel is live, then stop. `get_setup_status` tells you which tools will have data, so do not schedule a search report for a site with no Search Console.
- **The prompt must stand alone.** The scheduled run starts with no memory of this conversation: it needs the site id or domain, the reader, the tools to call with their arguments and windows, the output shape, and the rule that every number comes from a tool result. Copy the recipe and fill it in; do not summarise it. Name the connector as "the Harvv connector (https://harvv.com/mcp)"; the run cannot see what the person called it. The run is unattended: it reads and reports, and never calls a `set_` tool or a `confirm=true` write.
- **Which site, which reader.** If the request names no site or client, ask (or, with one site on the account, use it and say so). If the person named the reader in this conversation ("a brief for the client's founder") use that, and offer to save it with `set_working_context` so the next report starts from it.
- **Private notes stay private.** `about_this_client` and `working_context` shape the framing of a client-facing report and are never quoted or paraphrased in it. Account notices a tool returns (a plan allowance reached, a connector that stopped syncing, a tool limit) go in a short closing section addressed to the account holder, not to the client.
- **Meeting-relative dates.** If a calendar connector is available, search it for the meeting by the client's name and read its weekday, time and recurrence; compute the run day (a Tuesday 10:00 meeting with a two-day lead is Sunday). Default to 08:00 in the person's timezone, and check which timezone the scheduler reads its schedule in (see scheduling.md: some use local time, some UTC) and offer the preceding Friday if they want a workday. Otherwise ask for the weekday and go from there. State the resolved local day and time in one sentence before creating anything; if you cannot ask, pick, say what you picked, and make it easy to change.
- **Where it lands.** The scheduler's own output is the default. If Slack is connected on the Harvv account, the person can also ask Harvv in the channel; if a Slack or email connector is present in this session, the prompt can post or send, but say so and get a yes first.
- **One schedule per report.** Create it with whatever tool in this session creates a scheduled task or routine; the names differ by surface, so go by what the tool says it does (scheduling.md). If no scheduler is available, hand over the finished prompt and the exact steps to create the task, so nothing is lost.

After creating one, run it once if the surface allows, read the output, and fix the prompt before leaving it on a schedule. A report that reads well on its first run is the only kind worth automating.

## Finish

End with one short recap the person can forward:

- Per site: live, waiting for proof, or handed to someone (with the drafted request).
- What Harvv now knows about the business and where to edit it.
- Each scheduled report: name, cadence in their local time, what it reads, where it lands.
- When to run this skill again: after a new site is added, after a platform change (new theme, new checkout), or when a report says a connector is missing.

Honesty throughout: a number comes from a tool result or it is not stated; a step is done when Harvv observed it; a limit a tool reports is relayed, with the `next_steps` the tool returns, not worked around. `red_team` returns a checklist, not a verdict: apply each item to the conclusion yourself and drop what fails.

If `references/` is missing (some hosts save only SKILL.md), the full skill with its three reference files is at https://harvv.com/docs/claude-skill/harvv-setup.skill.
