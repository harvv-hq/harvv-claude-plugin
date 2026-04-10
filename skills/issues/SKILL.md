---
name: issues
description: Fetch and display UX issues detected by Harvv on your site. Shows dead clicks, rage clicks, conversion blockers, and AI-powered fix suggestions. Use when reviewing analytics, debugging UX problems, or checking what's broken on a site.
allowed-tools: Bash
---

# Harvv Issues

Fetch detected UX issues from the Harvv API and display them.

## Instructions

1. Get the API key from user config or ask the user
2. Fetch sites list, then issues for the requested site

Run this command to fetch issues:

```bash
curl -s -H "Authorization: Bearer ${user_config.harvv_api_key}" "https://harvv.com/v1/sites" | python3 -m json.tool
```

If the user specified a site (via $ARGUMENTS), fetch issues for that site:

```bash
curl -s -H "Authorization: Bearer ${user_config.harvv_api_key}" "https://harvv.com/v1/sites/SITE_ID/issues" | python3 -m json.tool
```

3. Display the issues in a clear format:
   - Issue title (first sentence of description)
   - Status (Open, Quoted, Approved, Fixing, Verifying, Fixed)
   - Visitors affected and percentage
   - Fix suggestion if available
   - Link to full dashboard view

4. If no site specified, list all sites first and ask which one to check.

5. Always include:
   - "View full details at: https://harvv.com/site.html#/app/site/SITE_ID"
   - For each issue: "See issue: https://harvv.com/site.html#/app/site/SITE_ID/issue/ISSUE_ID"

## If no API key is configured

Tell the user:
1. Sign up at https://harvv.com (free, no credit card)
2. Install the pixel on your site
3. Go to Settings → API Keys → Create Key
4. Run: `/harvv:issues` again

## Output format

Present issues as a prioritized list, grouped by severity:

**HIGH IMPACT**
- #123: "Product title links are not clickable" — 847 visitors affected (12%)
  Fix: Make the `<a>` tag wrap the full product card...
  [View in Harvv →](https://harvv.com/site.html#/app/site/xxx/issue/123)

**MEDIUM IMPACT**
- ...
