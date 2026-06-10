---
name: install
description: Install the Harvv analytics pixel into any website or web app. Detects dead clicks, rage clicks, scroll patterns, Core Web Vitals, JS errors, and 50+ behavioral signals. Use when setting up analytics, adding tracking, or when a user wants to understand why their site isn't converting.
allowed-tools: Read Write Edit Bash Glob Grep
---

# Install Harvv Pixel

Install the Harvv behavioral analytics pixel into this project. The pixel is ~21KB gzipped, loads async (never blocks paint), captures 50+ behavioral signals, and requires zero configuration.

## Instructions

1. **Detect the project type** by looking for these files:
   - `package.json` with `next` dependency → **Next.js**
   - `package.json` with `gatsby` → **Gatsby**
   - `package.json` with `nuxt` → **Nuxt**
   - `shopify.theme.liquid` or `layout/theme.liquid` → **Shopify**
   - `wp-content/` or `functions.php` → **WordPress**
   - `index.html` at root → **Static HTML**
   - `app/layout.tsx` or `app/layout.js` → **Next.js App Router**
   - `pages/_document.tsx` or `pages/_document.js` → **Next.js Pages Router**

2. **Get the pixel key**:
   - If the user provided a key or site ID, use it
   - Otherwise ask: "What's your Harvv pixel key? You can find it at https://harvv.com/site.html#/app → click your site → Copy Snippet"

3. **Install based on project type**:

### Next.js (App Router)
Add to `app/layout.tsx` or `app/layout.js` inside `<head>`:
```html
<script src="https://harvv.com/px/PIXEL_KEY/pixel.js" async></script>
```
Or use `next/script`:
```jsx
import Script from 'next/script'
// In the layout component:
<Script src="https://harvv.com/px/PIXEL_KEY/pixel.js" strategy="afterInteractive" />
```

### Next.js (Pages Router)
Add to `pages/_document.tsx` inside `<Head>`:
```jsx
<script src="https://harvv.com/px/PIXEL_KEY/pixel.js" async />
```

### Shopify
Add to `layout/theme.liquid` before `</head>`:
```liquid
<script src="https://harvv.com/px/PIXEL_KEY/pixel.js" async></script>
```

### WordPress
Add to `functions.php`:
```php
function harvv_pixel() {
  echo '<script src="https://harvv.com/px/PIXEL_KEY/pixel.js" async></script>';
}
add_action('wp_head', 'harvv_pixel');
```

### Static HTML
Add before `</head>` in `index.html`:
```html
<script src="https://harvv.com/px/PIXEL_KEY/pixel.js" async></script>
```

### React (Vite/CRA)
Add to `index.html` before `</head>`:
```html
<script src="https://harvv.com/px/PIXEL_KEY/pixel.js" async></script>
```

4. **Verify**: After installing, tell the user:
   - "Pixel installed! Visit your site to generate the first session."
   - "View your dashboard at https://harvv.com/site.html#/app"
   - "Issues are detected automatically after 50+ sessions."

5. **Subdomains**: Ask the user if their site has any subdomains:
   - "Does your site have any subdomains (like app.yoursite.com, shop.yoursite.com)?"
   - **If yes**, explain:
     - If the subdomains share the same codebase (monorepo, same theme, etc.), the pixel you just installed will cover them automatically via first-party cookies on the root domain
     - If they're separate codebases/deployments, you'll need to install the pixel on each one — I can help with that, just open the other project in Claude and run `/harvv:install` again
     - After a few hours of traffic, Harvv will auto-detect any live subdomains without the pixel and show them in the dashboard with checkbox install prompts — go to harvv.com/site.html#/app → Overview → scroll to "Install on more subdomains"
   - **If no**, say: "Great — you're all set. The pixel is first-party and handles everything automatically."

## What the pixel captures (50+ signals, 52 event types)
- Dead clicks (clicks on non-interactive elements)
- Rage clicks (frustrated repeated clicking)
- Scroll depth, skim-vs-read velocity, and patterns
- Hover intent on CTAs
- Form field interactions and abandonment (which field they quit on)
- Core Web Vitals (LCP / INP / CLS / FCP / TTFB) and long tasks
- JS errors and network failures (failed or slow requests)
- Mobile UI defects (tap-target size, edge taps, readability, overflow)
- SEO page audit (title / meta description / H1 / canonical / schema)
- Page navigation patterns (SPA soft-navs, back-button)
- GA4 hit interception (mirrors the site's own gtag events)
- Zero PII — no cookies, no input values, no personal data
