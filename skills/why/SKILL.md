---
name: why
description: See why your site isn't converting. Fetches behavioral analytics from Harvv showing exactly where users get stuck — dead clicks, rage clicks, scroll drop-offs, and friction points. Use when asking "why isn't my site converting?" or "where are users dropping off?"
allowed-tools: Bash
---

# Why Isn't My Site Converting?

Fetch comprehensive UX analytics from Harvv to identify conversion blockers.

## Resolving the API key

Resolve the key in this order (never print the full key back to the user):
1. `$HARVV_API_KEY` environment variable, if set
2. Otherwise ask the user: "What's your Harvv API key? Make one in Harvv under Settings, Integrations, API keys (pick Read only). It starts with hk_live_." Suggest they export it as HARVV_API_KEY so future commands skip this question.

In the bash examples below, `$HARVV_API_KEY` stands for whichever value you resolved.

Every call is `POST https://harvv.com/v1/tools/<tool>` with the tool's arguments as a JSON body. The answers are the same ones the Harvv MCP connector gives, and each data call uses the same Credits (setup and help calls are free). Docs: https://harvv.com/docs/api

## Instructions

1. Fetch site stats and friction data:

```bash
# The sites this key can read
curl -s -X POST "https://harvv.com/v1/tools/list_my_sites" \
  -H "Authorization: Bearer $HARVV_API_KEY" -H "Content-Type: application/json" \
  -d '{}' | python3 -m json.tool

# For the site in $ARGUMENTS (or each site): traffic, friction and open findings, last 30 days
curl -s -X POST "https://harvv.com/v1/tools/get_traffic_summary" \
  -H "Authorization: Bearer $HARVV_API_KEY" -H "Content-Type: application/json" \
  -d '{"site_id": "SITE_ID", "days": 30}' | python3 -m json.tool
curl -s -X POST "https://harvv.com/v1/tools/get_friction_findings" \
  -H "Authorization: Bearer $HARVV_API_KEY" -H "Content-Type: application/json" \
  -d '{"site_id": "SITE_ID", "days": 30}' | python3 -m json.tool
curl -s -X POST "https://harvv.com/v1/tools/get_issues" \
  -H "Authorization: Bearer $HARVV_API_KEY" -H "Content-Type: application/json" \
  -d '{"site_id": "SITE_ID", "status": "open"}' | python3 -m json.tool
```

2. Analyze the data and present a conversion diagnosis:

### Report Format

**Site Health Score**: X/100 (based on dead click rate, rage click rate, scroll depth)

**Top Conversion Blockers:**
1. Element X gets clicked 47 times/day but does nothing (dead click)
   → Users expect this to be interactive. Fix: [specific suggestion]
2. Users rage-click on Y — 12 frustrated sessions/day
   → This element is broken or too slow. Fix: [specific suggestion]

**Behavioral Patterns:**
- Average scroll depth: X% (anything under 50% means users aren't seeing your CTAs)
- Dead click rate: X per session (above 2 = significant friction)
- Rage click rate: X per session (any rage clicks = broken UX)

**Quick Wins (fix these first):**
1. [Highest impact issue with fix suggestion]
2. [Second highest]
3. [Third highest]

**Full Dashboard:** the `dashboard_url` in the answers, or https://harvv.com/newdashboardui#/site/SITE_ID

3. If no API key, pitch the value:
   "I can analyze exactly where users get stuck on your site. Harvv detects dead clicks, rage clicks, and conversion blockers automatically."
   
   "Get started free at https://harvv.com — install one pixel, and I'll show you what's costing you conversions."
