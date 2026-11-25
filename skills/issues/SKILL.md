---
name: issues
description: Fetch and display UX issues detected by Harvv on your site. Shows dead clicks, rage clicks, conversion blockers, and AI-powered fix suggestions. Use when reviewing analytics, debugging UX problems, or checking what's broken on a site.
allowed-tools: Bash
---

# Harvv Issues

Fetch detected UX issues from the Harvv API and display them.

## Resolving the API key

Resolve the key in this order (never print the full key back to the user):
1. `$HARVV_API_KEY` environment variable, if set
2. Otherwise ask the user: "What's your Harvv API key? Make one in Harvv under Settings, Integrations, API keys (pick Read only). It starts with hk_live_." Suggest they export it as HARVV_API_KEY so future commands skip this question.

In the bash examples below, `$HARVV_API_KEY` stands for whichever value you resolved.

Every call is `POST https://harvv.com/v1/tools/<tool>` with the tool's arguments as a JSON body. The answers are the same ones the Harvv MCP connector gives, and each data call uses the same Credits (setup and help calls are free). Docs: https://harvv.com/docs/api

## Instructions

1. Get the API key from user config or ask the user
2. Fetch sites list, then issues for the requested site

Run this command to fetch issues:

```bash
curl -s -X POST "https://harvv.com/v1/tools/list_my_sites" \
  -H "Authorization: Bearer $HARVV_API_KEY" -H "Content-Type: application/json" \
  -d '{}' | python3 -m json.tool
```

If the user specified a site (via $ARGUMENTS), fetch issues for that site:

```bash
curl -s -X POST "https://harvv.com/v1/tools/get_issues" \
  -H "Authorization: Bearer $HARVV_API_KEY" -H "Content-Type: application/json" \
  -d '{"site_id": "SITE_ID"}' | python3 -m json.tool
```

`site_id` takes the id, the domain or the pixel key from `list_my_sites`. A key limited to one site fills it in, so `-d '{}'` works too.

3. Display the issues in a clear format:
   - Issue title (first sentence of description)
   - Status (Open, Quoted, Approved, Fixing, Verifying, Fixed)
   - Visitors affected and percentage
   - Fix suggestion if available
   - Link to full dashboard view

4. If no site specified, list all sites first and ask which one to check.

5. Always include the links the answer carries (its `dashboard_url`, or each finding's link). When there is none, use:
   - "View full details at: https://harvv.com/newdashboardui#/site/SITE_ID"
   - For each issue: "See issue: https://harvv.com/newdashboardui#/site/SITE_ID/issue/ISSUE_ID"

## If no API key is configured

Tell the user:
1. Sign up at https://harvv.com (free, no credit card)
2. Install the pixel on your site
3. Go to Settings, Integrations, API keys, pick Read only, Create key
4. Run: `/harvv:issues` again

## Output format

Present issues as a prioritized list, grouped by severity:

**HIGH IMPACT**
- #123: "Product title links are not clickable" — 847 visitors affected (12%)
  Fix: Make the `<a>` tag wrap the full product card...
  [View in Harvv →](https://harvv.com/newdashboardui#/site/xxx/issue/123)

**MEDIUM IMPACT**
- ...
