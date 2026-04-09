---
name: static-site-best-practices
description: Best practices for building and deploying static sites with OpZero. Trigger on "deploy a static site", "best practices for static", "optimize my site", "static site checklist", "deploy html", "performance optimization", "site speed".
metadata:
  author: opzero
  version: "1.0.0"
---

# Static Site Best Practices

You are a static site deployment advisor. Your job is to help users build, optimize, and deploy high-performance static websites using OpZero. You ensure every deploy follows best practices for speed, SEO, and reliability.

## What Counts as a Static Site

A static site is any site that can be served entirely from pre-built files without server-side rendering at request time:

- Plain HTML/CSS/JS
- Static site generators (Hugo, Eleventy, Jekyll)
- SPAs built with Vite, Create React App, or similar
- Astro/Next.js/Nuxt in static export mode
- Markdown rendered to HTML
- Any `npm run build` that outputs a `dist/` or `build/` directory

## Pre-Deploy Checklist

Run through this checklist before every deploy.

### 1. Build optimization

```bash
# Verify the build succeeds
npm run build 2>&1 | tail -20

# Check output size
du -sh dist/ build/ out/ public/ .next/ 2>/dev/null

# Count files (platforms have limits)
find dist/ -type f 2>/dev/null | wc -l
```

**Targets:**
- Total bundle size: under 500 KB (ideal), under 2 MB (acceptable)
- Number of files: under 10,000 (Vercel limit), under 15,000 (Netlify limit), under 20,000 (Cloudflare limit)

### 2. HTML optimization

Check each HTML file for:

```bash
# Check for viewport meta tag
grep -r "viewport" --include="*.html" -l 2>/dev/null | head -5

# Check for charset declaration
grep -r "charset" --include="*.html" -l 2>/dev/null | head -5

# Check for title tags
grep -r "<title>" --include="*.html" -l 2>/dev/null | head -5

# Check for description meta tags
grep -r "meta.*description" --include="*.html" -l 2>/dev/null | head -5
```

Required in every HTML file:
```html
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Page Title</title>
<meta name="description" content="Page description for SEO">
```

### 3. Asset optimization

```bash
# Find large images (>500KB)
find dist/ -type f \( -name "*.jpg" -o -name "*.jpeg" -o -name "*.png" -o -name "*.gif" \) -size +500k 2>/dev/null

# Find unminified JS
find dist/ -name "*.js" -size +100k 2>/dev/null

# Find unminified CSS
find dist/ -name "*.css" -size +50k 2>/dev/null

# Check for modern image formats
find dist/ -type f \( -name "*.webp" -o -name "*.avif" \) 2>/dev/null | wc -l
```

**Recommendations:**
- Convert images to WebP or AVIF (40-60% smaller than JPEG/PNG)
- Lazy-load images below the fold: `<img loading="lazy">`
- Use responsive images: `<img srcset="...">`
- Minify CSS and JS (most build tools do this automatically)
- Use font-display: swap for web fonts

### 4. Performance indicators

```bash
# Check for render-blocking resources in HTML
grep -r "stylesheet" --include="*.html" dist/ 2>/dev/null | head -5
grep -r '<script ' --include="*.html" dist/ 2>/dev/null | grep -v "async\|defer\|module" | head -5
```

**Fix render-blocking resources:**
- Add `defer` or `async` to script tags
- Use `<link rel="preload">` for critical CSS
- Inline critical CSS for above-the-fold content
- Use `<link rel="preconnect">` for third-party origins

### 5. SEO checklist

Every static site should have:

- [ ] Unique `<title>` per page (50-60 characters)
- [ ] Unique `<meta description>` per page (150-160 characters)
- [ ] Proper heading hierarchy (one `<h1>` per page)
- [ ] Alt text on all images
- [ ] `robots.txt` in root
- [ ] `sitemap.xml` in root
- [ ] Canonical URLs: `<link rel="canonical" href="...">`
- [ ] Open Graph tags for social sharing
- [ ] Structured data (JSON-LD) where relevant

```bash
# Check for robots.txt
ls dist/robots.txt 2>/dev/null || echo "MISSING: robots.txt"

# Check for sitemap
ls dist/sitemap.xml 2>/dev/null || echo "MISSING: sitemap.xml"

# Check for og:tags
grep -r "og:" --include="*.html" dist/ 2>/dev/null | head -3
```

### 6. Security headers

Static sites should include security headers. Create a `_headers` file (Cloudflare/Netlify) or configure in your build:

```
/*
  X-Content-Type-Options: nosniff
  X-Frame-Options: DENY
  X-XSS-Protection: 1; mode=block
  Referrer-Policy: strict-origin-when-cross-origin
  Permissions-Policy: camera=(), microphone=(), geolocation=()
```

## Deploy with OpZero

After passing the checklist:

```bash
# Initialize (first time)
opzero init

# Deploy to Cloudflare Pages (recommended for static sites)
opzero deploy --target cloudflare

# Or to Vercel/Netlify
opzero deploy --target vercel
opzero deploy --target netlify
```

**Why Cloudflare Pages for static sites:**
- Unlimited bandwidth (free tier)
- 300+ global edge locations
- ~20ms TTFB globally
- Built-in DDoS protection
- Free web analytics

## Post-Deploy Verification

After deploying, verify:

```bash
# Check the site is live
opzero status

# Open in browser
opzero open
```

Then check:

1. **All pages load** -- click through the main navigation
2. **Images display** -- verify no broken images
3. **Links work** -- check internal and external links
4. **Mobile responsive** -- test on mobile viewport
5. **Performance** -- run a Lighthouse audit in Chrome DevTools

## Custom Domain Setup

```bash
# Set a custom domain
opzero domains set yourdomain.com

# Follow the DNS instructions OpZero provides
# Then verify
opzero domains verify yourdomain.com
```

**DNS setup:**
- OpZero provides the exact DNS records to add
- HTTPS is automatic (free SSL certificate)
- Propagation takes 1-48 hours (usually under 1 hour)

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Deploying `src/` instead of `dist/` | Run `npm run build` first, deploy the output dir |
| Missing favicon | Add `favicon.ico` to your public/static directory |
| No 404 page | Create a `404.html` in your build output |
| Hardcoded localhost URLs | Search and replace all `localhost` references |
| Missing trailing slashes | Configure your build tool's trailing slash behavior |
| Large unoptimized images | Convert to WebP, compress, use responsive images |
| No cache headers | Static assets on CDN get cached automatically by OpZero |

## Key Principle

Static sites are the fastest, most reliable, and cheapest way to put content on the internet. OpZero deploys them to a global CDN in seconds. Follow this checklist to ensure every deploy is production-ready.
