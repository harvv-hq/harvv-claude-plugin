---
name: install
description: Install the Harvv analytics pixel into any website or web app. Detects dead clicks, rage clicks, scroll patterns, and 15+ behavioral signals. Use when setting up analytics, adding tracking, or when a user wants to understand why their site isn't converting.
allowed-tools: Read Write Edit Bash Glob Grep
---

# Install Harvv Pixel

Install the Harvv behavioral analytics pixel into this project. The pixel is 5KB gzipped, captures 18 behavioral signals, and requires zero configuration.

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

## What the pixel captures
- Dead clicks (clicks on non-interactive elements)
- Rage clicks (frustrated repeated clicking)
- Scroll depth and patterns
- Hover intent on CTAs
- Form field interactions
- Page navigation patterns
- Network request performance
- Time to first interaction
- Viewport snapshots
- Zero PII — no cookies, no personal data
