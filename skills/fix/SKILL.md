---
name: fix
description: Get AI-powered fix suggestions for a specific Harvv UX issue and apply them to your codebase. Use when you want to fix a dead click, rage click, or conversion issue detected by Harvv.
allowed-tools: Read Write Edit Bash Glob Grep
---

# Fix Harvv Issue

Fetch a specific issue from Harvv and generate a code fix for it.

## Resolving the API key

Resolve the key in this order (never print the full key back to the user):
1. `$HARVV_API_KEY` environment variable, if set
2. Otherwise ask the user: "What's your Harvv API key? Make one in Harvv under Settings, Integrations, API keys (pick Read only). It starts with hk_live_." Suggest they export it as HARVV_API_KEY so future commands skip this question.

In the bash examples below, `$HARVV_API_KEY` stands for whichever value you resolved.

Every call is `POST https://harvv.com/v1/tools/<tool>` with the tool's arguments as a JSON body. The answers are the same ones the Harvv MCP connector gives, and each data call uses the same Credits (setup and help calls are free). Docs: https://harvv.com/docs/api

## Instructions

1. Get the issue ID from $ARGUMENTS (e.g., `/harvv:fix 123` or `/harvv:fix site_id issue_id`)

2. Fetch the site's findings in full and pick the one with that id:
```bash
curl -s -X POST "https://harvv.com/v1/tools/get_issues" \
  -H "Authorization: Bearer $HARVV_API_KEY" -H "Content-Type: application/json" \
  -d '{"site_id": "SITE_ID", "detail": "full", "limit": 50}' | python3 -m json.tool
```
   To check it is still happening before you change code:
```bash
curl -s -X POST "https://harvv.com/v1/tools/verify_finding" \
  -H "Authorization: Bearer $HARVV_API_KEY" -H "Content-Type: application/json" \
  -d '{"site_id": "SITE_ID", "issue_id": "ISSUE_ID"}' | python3 -m json.tool
```

3. Read the finding: the element (its CSS selector or label), what is wrong, the recommended fix, how many sessions it affected, and its kind (dead click, rage click, and so on). Use the field names the answer gives; do not guess ones it does not have.

4. Search the codebase for the element:
   - Use Grep to find the element in the code (search for class names, IDs, text content)
   - Read the surrounding code to understand the context
   - Look at CSS, event handlers, and HTML structure

5. Generate and apply a fix:
   - For dead clicks: make the element interactive (add click handler, wrap in `<a>` or `<button>`)
   - For rage clicks: fix the unresponsive element (broken handler, missing href, disabled state)
   - For scroll issues: improve content visibility, add CTAs above the fold
   - For form issues: fix validation, improve labels, reduce friction

6. After applying the fix, tell the user:
   - What was changed and why
   - "The fix has been applied. Deploy your changes and Harvv will verify the fix automatically."
   - "Track progress at: https://harvv.com/newdashboardui#/site/SITE_ID/issue/ISSUE_ID"
   - Suggest marking the issue as "Fixing" in Harvv

## If no issue ID provided

Fetch all open issues and ask which one to fix:
```bash
curl -s -X POST "https://harvv.com/v1/tools/get_issues" \
  -H "Authorization: Bearer $HARVV_API_KEY" -H "Content-Type: application/json" \
  -d '{"site_id": "SITE_ID", "status": "open"}' | python3 -m json.tool
```
