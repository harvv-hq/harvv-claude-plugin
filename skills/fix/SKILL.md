---
name: fix
description: Get AI-powered fix suggestions for a specific Harvv UX issue and apply them to your codebase. Use when you want to fix a dead click, rage click, or conversion issue detected by Harvv.
allowed-tools: Read Write Edit Bash Glob Grep
---

# Fix Harvv Issue

Fetch a specific issue from Harvv and generate a code fix for it.

## Instructions

1. Get the issue ID from $ARGUMENTS (e.g., `/harvv:fix 123` or `/harvv:fix site_id issue_id`)

2. Fetch the issue details:
```bash
curl -s -H "Authorization: Bearer ${user_config.harvv_api_key}" "https://harvv.com/v1/sites/SITE_ID/issues/ISSUE_ID" | python3 -m json.tool
```

3. Read the issue data:
   - `element` — the CSS selector/identifier of the problematic element
   - `description` — what's wrong
   - `fix_suggestion` — Harvv's AI-generated suggestion
   - `sessions_affected` — how many users hit this issue
   - `category` — type of issue (dead_click, rage_click, etc.)

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
   - "Track progress at: https://harvv.com/site.html#/app/site/SITE_ID/issue/ISSUE_ID"
   - Suggest marking the issue as "Fixing" in Harvv

## If no issue ID provided

Fetch all open issues and ask which one to fix:
```bash
curl -s -H "Authorization: Bearer ${user_config.harvv_api_key}" "https://harvv.com/v1/sites/SITE_ID/issues?status=open" | python3 -m json.tool
```
