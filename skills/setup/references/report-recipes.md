# Report recipes

Each recipe names the Harvv tools, the cadence the data supports, and the honesty rules. Fill the skeleton in `scheduling.md` with one of these. Every number in the output comes from a tool result; when a tool reports a limit, relay it and its `next_steps` rather than working around it.

Common to every recipe:

- Resolve the site with `list_my_sites` by domain when the account has more than one site; pass `site_id` to every call, with an explicit `days` where the recipe gives one.
- Start with `get_setup_status` for that site when the last run said a connector was missing; skip it once everything required is done.
- Findings from `get_issues` carry an `age_label` and sometimes `count_suspect`: prefer findings measured inside the window, print the age of any you cite, and never quote a suspect count bare. `verify_finding` re-checks one when it matters.
- A tool whose history is shorter than the window says so in a coverage note: quote the covered days.
- Finish by passing the main conclusion to `red_team` as `draft_conclusion`. It returns a checklist, not a verdict: apply each item yourself, drop what fails, say what was checked.
- Read the `working_context` and `next_steps` a tool returns: frame the answer for the reader it names (never quote the note in a client-facing report), and end with the step it offers.
- Some hosts list Harvv's MCP prompts (`weekly_friction_review`, `client_search_report`, `speed_check`, `is_this_normal`); they are the same analyses as recipes 1, 3, 4 and 7, so use whichever is reachable.

## 1. Weekly friction review

Cadence: weekly, Monday morning. Needs: pixel live for 7+ days.
Tools: `get_traffic_summary` (days 7), `get_issues` (status open, limit 15), `get_fleet_benchmark`, then `get_page_friction` (days 7) for the path the finding you will headline names in its `pages` field, or with no path when none does (the tool then picks the worst page itself).
Output: the one thing to fix first and why, sessions affected (from the tool, with the window), dead-click and rage-click rates against the fleet median with the fleet sample size, what changed since last week only where a tool states a change, what could not be measured.

## 2. Pre-meeting client brief

Cadence: N days before a recurring meeting (see scheduling.md). Needs: pixel live; works better with GA4, Search Console or the Shopify checkout pixel.
Tools: `get_traffic_summary` (days 7, then days 30); for a store BOTH `get_conversions` (days 30, revenue and daily trend live here) and `get_commerce_funnel` (days 7, mid-funnel and stock signals live here); for a lead site `get_conversions` (days 30) and `get_forms` (days 7); `get_issues` (status open, limit 3); `get_search_console` if connected; `get_ad_spend` (days 7) and `get_ad_traffic_quality` (days 30, it needs more than a week to separate a campaign from noise) if ads run.
Output for the decider named in `about_this_client` (or the reader the person named): three wins with numbers, three things to decide with a recommendation each, one chart-worthy trend in words, and the asks (connections or access the agency is still waiting on). Under 400 words, no selectors or technical terms. Mark the sample size next to every behavioural claim. End with a short section headed "For you, not the client" for anything a tool told the account holder (plan allowance, a sync that stopped, a limit).

## 3. Monthly search report

Cadence: first Monday (or first weekday) of the month. Needs: Search Console connected for 30+ days.
Tools: `get_search_console` (days 28), `get_keyword_opportunities`, `get_seo_insights`, `get_keyword_volume` for the top 5 opportunities with the business's location.
Output: brand vs non-brand clicks separately, striking-distance pages, CTR leaks with the title rewrite suggestion, three content topics with volume, on-page fixes by page.

## 4. Speed check

Cadence: weekly or after a deploy (one-time run). Needs: pixel live.
Tools: `get_performance` (days 7), `get_js_errors`, `get_layout_changes`.
Output: LCP, INP, CLS at p75 by device with the pages that fail, new JS errors, layout shifts that appeared, one fix first.

## 5. Ad traffic check

Cadence: weekly during active campaigns. Needs: ads running (click ids in traffic); spend needs a connected ad account.
Tools: `get_ad_traffic_quality` (days 7), `get_ad_spend` if `spend_data.available`, `get_traffic_sources`.
Output: which campaigns send traffic that behaves worse than the site's own non-paid traffic, read `vs_unpaid` verdicts before calling anything bad, spend only if the tool returned it, the `ad_learnings` rules that apply.

## 6. Launch watch

Cadence: daily for the 7 days after a launch, sale or redesign, then stop (one-time runs or a short-lived cron the person turns off).
Tools: `get_traffic_summary` (days 1), `get_page_friction` for the launch page, `get_js_errors`, `get_performance` (days 1), `get_forms` if the launch is a form.
Output: is anything broken today (errors, rage clicks, dead clicks on the new page), is traffic arriving from the expected source, one line of "normal / look at this".
Before the launch, `annotate_change` with the date and what changed so later comparisons have a marker.

## 7. Is this normal

Cadence: weekly. Needs: 4+ weeks of data.
Tools: `get_traffic_summary` (days 7 and days 28), `get_fleet_benchmark`, `get_audience_state`.
Output: which of this week's numbers are inside the site's own range and the fleet's, which are not, and whether a change coincides with a marked change (`annotate_change` history).

## 8. Setup watch

Cadence: daily until `all_required_done`, then disable. Needs: nothing.
Tools: `get_setup_status`, `check_install` on the live URL when the pixel is still `never`.
Output: what became live since yesterday, what is still waiting, the one action and who owns it. When everything required is done, say so and tell the person to turn the task off (or do it, if the scheduler tool allows).

## Matching a request to a recipe

| They say | Recipe |
|---|---|
| "every Monday what should we fix" | 1 |
| "before my client call", "two days before our meeting" | 2 |
| "monthly SEO report", "rankings for the client" | 3 |
| "is the site fast", "after we deploy" | 4 |
| "are the ads working" | 5 |
| "we launch next week", "Black Friday" | 6 |
| "tell me if something looks off" | 7 |
| "tell me when the data starts coming in" | 8 |

Two requests in one ("weekly fixes and a monthly SEO report") are two schedules.
