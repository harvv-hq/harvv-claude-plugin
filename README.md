# Harvv — Claude Code Plugin

Detect dead clicks, rage clicks, and conversion killers on your website. One pixel, AI-powered fix suggestions, delivered right in your editor.

## Install

```bash
/plugin install github:harvv/claude-plugin
```

## Setup

1. Sign up at [harvv.com](https://harvv.com) (free, no credit card)
2. Create a site and install the pixel
3. Go to Settings > API Keys > Create Key
4. The plugin will ask for your API key on first use

## Commands

| Command | What it does |
|---------|-------------|
| `/harvv:install` | Install the Harvv pixel into your project (auto-detects Next.js, Shopify, WordPress, etc.) |
| `/harvv:issues` | See all detected UX issues on your site |
| `/harvv:fix 123` | Get AI fix suggestion for issue #123 and apply it to your code |
| `/harvv:why` | Full conversion diagnosis — see exactly where users get stuck |

## What Harvv detects

- **Dead clicks** — users clicking elements that do nothing
- **Rage clicks** — frustrated repeated clicking
- **Scroll drop-offs** — where users stop scrolling
- **Hover hesitation** — users hovering on CTAs but not clicking
- **Form friction** — abandoned form fields
- **Slow network requests** — API calls killing your page speed
- **Layout issues** — nav wrapping, content offset, horizontal overflow

## How it works

1. Install the pixel (5KB, zero config)
2. Harvv captures 18 behavioral signals from real user sessions
3. AI detects patterns and generates fix suggestions
4. Use `/harvv:fix` to apply fixes directly in your code
5. Harvv verifies the fix worked with before/after metrics

## Privacy

- Zero PII collected
- No cookies
- No session recordings
- Only behavioral signals (clicks, scrolls, hovers)

## Links

- [Dashboard](https://harvv.com/site.html#/app)
- [Documentation](https://harvv.com/trust)
- [API Reference](https://harvv.com/docs/api)
