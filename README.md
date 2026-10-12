# Harvv — Claude Code Plugin

Detect dead clicks, rage clicks, and conversion killers on your website. One pixel, AI-powered fix suggestions, delivered right in your editor.

[Harvv](https://harvv.com) is a behavioral analytics platform that captures 50+ behavioral signals from your site with one async pixel (live size at [harvv.com/public/facts](https://harvv.com/public/facts)), detects UX issues automatically, and generates fix suggestions. This plugin brings that data directly into Claude Code so you can install the pixel, view detected issues, and apply AI-generated fixes without leaving your editor.

## Install

Two commands in Claude Code (the first registers the marketplace, once per machine):

```
/plugin marketplace add harvv-hq/harvv-claude-plugin
/plugin install harvv@harvv-claude-plugin
```

> **Where this works:** the `claude` terminal CLI, the VS Code / JetBrains extensions, the desktop app's **Code** tab, and claude.ai/code. If you see "/plugin isn't available in this environment", you're in a chat surface (claude.ai chat or the desktop chat tab) — switch to one of the above.

**No plugin system? Use plain skills instead.** Copy the four folders from this repo's `skills/` directory into your project's `.claude/skills/` — the commands load on every Claude Code surface with no marketplace needed:

```bash
git clone https://github.com/harvv-hq/harvv-claude-plugin /tmp/harvv-plugin
mkdir -p .claude/skills && cp -R /tmp/harvv-plugin/skills/* .claude/skills/
```

(With plain skills the commands are `/install`, `/issues`, `/fix`, `/why` — no `harvv:` prefix.)

## Setup

The plugin works in two stages:

### Stage 1: Install the pixel (no account required)

```
/harvv:install
```

Claude will:
1. Detect your project type (Next.js, Shopify, WordPress, React, etc.)
2. Ask for your email
3. Create a free Harvv account + site + pixel key automatically
4. Inject the pixel into the right file for your project
5. Send you a welcome email with a magic link to set your password

### Stage 2: API key (usually automatic)

If `/harvv:install` created your account, it already saved an API key for you and gave you a one-click activation link to your dashboard — nothing else to do.

If you already had a Harvv account, create a key once:

1. Go to [harvv.com/site.html#/app](https://harvv.com/site.html#/app) → Settings → API Keys
2. Click "Create Key" and copy the `hv_live_xxx` key
3. Set it as `HARVV_API_KEY` in your environment (or tell Claude when prompted)

## Commands

| Command | What it does | API key required |
|---------|-------------|:----:|
| `/harvv:install` | Install the pixel into your project. Auto-detects Next.js, Shopify, WordPress, React, Vue, static HTML, and more. | No |
| `/harvv:issues` | List all detected UX issues on your site(s). Shows status, impact, and fix suggestions. | Yes |
| `/harvv:fix 123` | Fetch issue #123, find the relevant element in your codebase, and apply an AI-generated fix. | Yes |
| `/harvv:why` | Full conversion diagnosis. Shows exactly where users get stuck with prioritized quick wins. | Yes |
| `/harvv:setup` | Onboarding: checks what Harvv has observed (pixel, Shopify checkout pixel, GA4, Search Console, Slack), collects what only you know, and turns "send me this every Monday" into a scheduled task. Needs the Harvv MCP connector, not the API key. Also runs in claude.ai as `harvv-setup.skill` from harvv.com/docs/claude-skill. | No (MCP) |

## What Harvv detects

- **Dead clicks** — users clicking elements that do nothing
- **Rage clicks** — frustrated repeated clicking on the same element
- **Scroll drop-offs** — where users stop scrolling
- **Hover hesitation** — users hovering on CTAs but not clicking
- **Form friction** — abandoned form fields
- **Slow network requests** — API calls killing your page speed
- **Layout issues** — nav wrapping, content offset, horizontal overflow
- **Conversion blockers** — pricing cards that look clickable but aren't

## Configuration

This plugin stores one sensitive setting via Claude Code's `userConfig`:

| Setting | Description | Required |
|---------|-------------|----------|
| `harvv_api_key` | Your Harvv API key (format: `hv_live_xxx`). Created at [harvv.com](https://harvv.com/site.html#/app) → Settings → API Keys. | Only for `/harvv:issues`, `/harvv:fix`, `/harvv:why` |

The API key is used to authenticate requests to `https://harvv.com/v1/*` endpoints. See the [API docs](https://harvv.com/docs/api) for details.

## Privacy & Security

**The Harvv pixel captures zero PII:**
- No cookies
- No session recordings
- No personal data (names, emails, form values, URLs with query strings)
- Only anonymous behavioral signals (click positions, scroll depth, hover durations, element identifiers)
- First-party cookies only (no cross-site tracking)

**The API key:**
- Stored via Claude Code's secure `userConfig` system (never logged)
- Scoped to read-only access for your sites only
- Revocable at any time from [harvv.com](https://harvv.com/site.html#/app) → Settings → API Keys

**What this plugin sends to Harvv's API:**
- Your email (during `/harvv:install` only, to create the account)
- Your project's domain (during `/harvv:install` only)
- Your API key (in the `Authorization` header, for `/harvv:issues`, `/harvv:fix`, `/harvv:why`)

**What this plugin does NOT send:**
- Your source code
- Files from your repository
- Any data beyond what's explicitly requested by the commands

## How it works

1. **Install** — `/harvv:install` injects a single `<script>` tag into your project's HTML. The pixel loads asynchronously (it never blocks rendering); its current size is published at https://harvv.com/public/facts.
2. **Capture** — Once deployed, the pixel captures 50+ behavioral signals from real visitors (anonymous, no PII).
3. **Detect** — Harvv's AI detection engine analyzes the data every hour and flags UX issues with statistical confidence thresholds.
4. **Query** — `/harvv:issues` pulls the detected issues via API and shows them in your terminal.
5. **Fix** — `/harvv:fix 123` reads a specific issue, searches your codebase for the affected element, and applies a code fix with Claude.
6. **Verify** — Harvv automatically measures before/after metrics to confirm the fix worked.

## Example usage

```
# Install the pixel in your Next.js project
> /harvv:install
✔ Detected Next.js project
✔ Enter your email: you@example.com
✔ Account created. Pixel key: abc123def456
✔ Injected into app/layout.tsx
📊 Dashboard: https://harvv.com/site.html#/app

# After some traffic, check what's broken
> /harvv:issues
Site: example.com — 3 issues detected

#142 [open] Product title links are not clickable
  847 visitors affected (12% of total)
  Fix: Wrap the <h3> in an <a> tag...
  View: https://harvv.com/site.html#/app/site/xxx/issue/142

#143 [open] Pricing card body triggers no action
  412 visitors affected
  ...

# Apply the fix
> /harvv:fix 142
✔ Found element in components/ProductCard.tsx:24
✔ Applied fix: wrapped h3 in <Link> component
✔ Marked issue as "fixing" in Harvv
Deploy your changes and Harvv will verify the fix.
```

## Links

- **Dashboard:** https://harvv.com/site.html#/app
- **API Docs:** https://harvv.com/docs/api
- **Privacy:** https://harvv.com/privacy
- **Security:** https://harvv.com/trust
- **GitHub:** https://github.com/harvv-hq/harvv-claude-plugin
- **Support:** hello@harvv.com

## License

MIT — see [LICENSE](./LICENSE)
