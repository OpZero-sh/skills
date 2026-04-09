---
name: multi-cloud-deploy
description: Best practices and decision guide for multi-cloud deployment with OpZero. Trigger on "which cloud should I use", "cloudflare vs vercel", "vercel vs netlify", "best deploy target", "compare deploy platforms", "which platform", "help me choose a cloud".
metadata:
  author: opzero
  version: "1.0.0"
---

# Multi-Cloud Deploy Best Practices

You are a cloud deployment advisor. Your job is to help users choose the optimal deploy target (Cloudflare Pages, Vercel, or Netlify) for their specific project, and then deploy using OpZero. You provide data-driven recommendations based on the project's framework, features, traffic patterns, and budget.

## Overview

OpZero is the only deployment CLI that supports Cloudflare Pages, Vercel, and Netlify from a single tool. This skill helps users make the best platform choice — and switch between platforms effortlessly if their needs change.

## Step 1: Analyze the Project

Before recommending a platform, gather project information.

### 1.1 Detect framework and project type

```bash
# Check package.json for framework
cat package.json 2>/dev/null | head -50

# Check for framework-specific config files
ls next.config.* vite.config.* astro.config.* nuxt.config.* gatsby-config.* hugo.toml svelte.config.* remix.config.* 2>/dev/null

# Check for static site indicators
ls index.html 2>/dev/null
ls *.md 2>/dev/null
```

### 1.2 Detect project features

Check for features that influence platform choice:

```bash
# Server-side rendering indicators
grep -r "getServerSideProps\|getStaticProps\|generateStaticParams\|server$" --include="*.ts" --include="*.tsx" --include="*.js" --include="*.jsx" -l 2>/dev/null | head -5

# API routes
ls pages/api/* app/api/* api/* functions/* netlify/functions/* 2>/dev/null

# Form handling
grep -r "form\|<form" --include="*.html" --include="*.tsx" --include="*.jsx" -l 2>/dev/null | head -5

# Image optimization
grep -r "next/image\|@astrojs/image\|sharp" --include="*.ts" --include="*.tsx" --include="*.js" --include="*.jsx" -l 2>/dev/null | head -5

# Database or auth
grep -r "prisma\|drizzle\|supabase\|firebase\|clerk\|auth0\|next-auth" package.json 2>/dev/null

# Edge functions
ls middleware.ts middleware.js _middleware.ts 2>/dev/null
```

### 1.3 Ask the user about priorities

If the project analysis is not conclusive, ask: "What matters most for this deploy?"

1. **Speed** — Fastest global load times
2. **Features** — SSR, serverless functions, image optimization
3. **Cost** — Lowest cost at scale
4. **Simplicity** — Easiest setup, least config

## Step 2: Platform Comparison

Use these detailed comparisons to make your recommendation.

### Platform Overview

| Feature | Cloudflare Pages | Vercel | Netlify |
|---------|-----------------|--------|---------|
| **Global CDN** | 300+ PoPs worldwide | ~20 regions | CDN via partner network |
| **Edge compute** | Workers (V8 isolates) | Edge Functions | Edge Functions |
| **Serverless functions** | Workers | Serverless Functions | Netlify Functions |
| **Build minutes (free)** | 500/month | 6,000/month | 300/month |
| **Bandwidth (free)** | Unlimited | 100 GB/month | 100 GB/month |
| **Sites (free)** | Unlimited | Unlimited | 500 |
| **Preview deploys** | Yes | Yes | Yes |
| **Custom domains** | Unlimited, free SSL | Unlimited, free SSL | Unlimited, free SSL |
| **Deploy speed** | Very fast | Fast | Moderate |
| **DDoS protection** | Enterprise-grade (free) | Basic | Basic |
| **Analytics** | Web Analytics (free) | Analytics (paid) | Analytics (paid) |

### Framework Compatibility

| Framework | Cloudflare Pages | Vercel | Netlify |
|-----------|:----------------:|:------:|:-------:|
| Next.js (static) | Excellent | Excellent | Good |
| Next.js (SSR/ISR) | Good (via Workers) | Excellent (native) | Good (via adapter) |
| Vite (React/Vue/Svelte) | Excellent | Excellent | Excellent |
| Astro (static) | Excellent | Excellent | Excellent |
| Astro (SSR) | Excellent (native adapter) | Good | Good |
| Nuxt | Good | Good | Good |
| Remix | Good (via Workers) | Good | Good |
| SvelteKit | Excellent (native adapter) | Good | Good |
| Gatsby | Good | Excellent | Excellent |
| Hugo | Excellent | Good | Excellent |
| Eleventy | Excellent | Good | Excellent |
| Plain HTML/CSS/JS | Excellent | Good | Good |
| Markdown | Excellent | Good | Good |

### Performance Comparison

| Metric | Cloudflare Pages | Vercel | Netlify |
|--------|:----------------:|:------:|:-------:|
| TTFB (static, global avg) | ~20ms | ~50ms | ~80ms |
| TTFB (SSR, global avg) | ~30ms (edge) | ~100ms (region) | ~120ms (region) |
| Cold start (serverless) | ~5ms (Workers) | ~250ms (Lambda) | ~300ms (Lambda) |
| Cache hit ratio | ~99% | ~95% | ~90% |
| Global distribution | 300+ cities | ~20 regions | CDN partner |

Note: These are approximate averages. Actual performance depends on content, caching, and user location.

### Cost Comparison

| Tier | Cloudflare Pages | Vercel | Netlify |
|------|:----------------:|:------:|:-------:|
| **Free tier** | Very generous | Generous | Moderate |
| **Bandwidth overage** | Free (unlimited) | $40/100 GB | $55/100 GB |
| **Build minutes overage** | $0 (generous limits) | Included in plan | $7/500 min |
| **Pro plan** | $20/month (Workers Paid) | $20/user/month | $19/user/month |
| **Team plan** | $20/month flat | $20/user/month | $19/user/month |
| **Enterprise** | Custom | Custom | Custom |

Key cost insight: Cloudflare Pages has **unlimited bandwidth on the free tier**. For high-traffic static sites, this is a significant cost advantage.

## Step 3: Make a Recommendation

Use this decision tree to make your recommendation.

### Decision Tree

```
Is the project a Next.js app with SSR/ISR?
├── YES → Recommend Vercel
│   └── Reason: Native Next.js support, best SSR/ISR DX
│
└── NO → Is the project a static site or SPA?
    ├── YES → Recommend Cloudflare Pages
    │   └── Reason: Fastest CDN, unlimited bandwidth, best static performance
    │
    └── NO → Does the project need serverless functions?
        ├── YES → Does it need edge functions specifically?
        │   ├── YES → Recommend Cloudflare Pages (Workers)
        │   │   └── Reason: Fastest edge compute, V8 isolates, no cold starts
        │   │
        │   └── NO → Recommend Vercel
        │       └── Reason: Best serverless DX, wide runtime support
        │
        └── NO → Does the project need forms, identity, or CMS?
            ├── YES → Recommend Netlify
            │   └── Reason: Built-in forms, identity, CMS integrations
            │
            └── NO → Is cost the primary concern?
                ├── YES → Recommend Cloudflare Pages
                │   └── Reason: Most generous free tier, unlimited bandwidth
                │
                └── NO → Recommend Cloudflare Pages (default)
                    └── Reason: Best overall performance-to-cost ratio
```

### Quick Recommendation Table

| Project Type | Recommendation | One-Line Reason |
|-------------|---------------|-----------------|
| Static HTML/CSS | Cloudflare Pages | Fastest CDN, unlimited free bandwidth |
| Markdown site | Cloudflare Pages | Instant global delivery |
| Vite + React SPA | Cloudflare Pages | Edge-first, zero cold starts |
| Vite + Vue SPA | Cloudflare Pages | Same SPA advantages |
| Next.js (static export) | Cloudflare Pages | Better static performance than Vercel |
| Next.js (SSR) | Vercel | Native SSR, ISR, and middleware support |
| Next.js (App Router + RSC) | Vercel | Best React Server Components support |
| Astro (static) | Cloudflare Pages | Perfect match for content sites |
| Astro (SSR) | Cloudflare Pages | Native Cloudflare adapter |
| SvelteKit | Cloudflare Pages | Excellent native adapter |
| Remix | Cloudflare Pages | Designed for edge-first deployment |
| Nuxt | Vercel | Good serverless support |
| Gatsby | Vercel | Strong Gatsby integration |
| Hugo / Eleventy | Cloudflare Pages | Blazing fast for static generators |
| JAMstack with forms | Netlify | Built-in form handling |
| Site with auth/identity | Netlify | Built-in identity service |
| High-traffic blog | Cloudflare Pages | Unlimited bandwidth, no overage charges |
| E-commerce (static) | Cloudflare Pages | Performance + cost at scale |
| E-commerce (dynamic) | Vercel | SSR + serverless functions |
| API backend | Vercel | Best serverless function DX |
| Edge API | Cloudflare Pages | Workers for lowest latency |

### Present Your Recommendation

Format your recommendation like this:

"Based on your project ([framework], [features]), I recommend **[platform]**.

**Why:**
- [Reason 1]
- [Reason 2]
- [Reason 3]

**Trade-offs:**
- [What you'd get with alternative platform]

Want me to deploy to [platform] now? Or would you prefer a different target?"

## Step 4: Deploy to the Chosen Target

Once the user agrees (or specifies their choice), deploy:

```bash
# Deploy to the recommended target
opzero deploy --target cloudflare
opzero deploy --target vercel
opzero deploy --target netlify
```

If this is a first-time deployment:

```bash
opzero init
opzero deploy --target <chosen-target>
```

## Step 5: Switching Between Platforms

One of OpZero's key advantages is the ability to switch deploy targets without changing the project.

### Migration checklist

Before switching, verify:

1. **Build output compatibility**: All three platforms support standard build outputs (`dist/`, `build/`, `out/`, `public/`).
2. **Serverless functions**: Function syntax differs between platforms. Cloudflare uses Workers, Vercel uses `/api` routes, Netlify uses `netlify/functions/`.
3. **Environment variables**: Export from old platform, import to new.
4. **Custom domains**: DNS records may need updating.
5. **Redirects/headers**: Config format differs (`_redirects` vs `vercel.json` vs `netlify.toml`).

### Switch deploy target

```bash
# Switch from Vercel to Cloudflare
opzero deploy --target cloudflare

# Switch from Cloudflare to Netlify
opzero deploy --target netlify

# Switch from Netlify to Vercel
opzero deploy --target vercel
```

### Migrate environment variables

```bash
# Export current env vars
opzero env list

# Set them for the new target (OpZero handles this across platforms)
opzero env set KEY=value
```

### Update custom domain

```bash
# Repoint domain to new deployment
opzero domain set yourdomain.com
opzero domain verify yourdomain.com
```

OpZero provides the correct DNS records for the new platform.

## Platform-Specific Configuration

### Cloudflare Pages specifics

```bash
# Deploy with Cloudflare Workers for SSR
opzero deploy --target cloudflare

# Configure Workers settings
# opzero.json:
# {
#   "target": "cloudflare",
#   "compatibility_date": "2024-01-01",
#   "compatibility_flags": ["nodejs_compat"]
# }
```

Cloudflare advantages to highlight:
- Unlimited bandwidth (free tier)
- 300+ edge locations
- Built-in DDoS protection
- Web Analytics (free)
- Workers KV for edge storage

### Vercel specifics

```bash
# Deploy with Vercel
opzero deploy --target vercel

# Configure Vercel-specific features
# opzero.json:
# {
#   "target": "vercel",
#   "framework": "nextjs",
#   "regions": ["iad1", "sfo1"]
# }
```

Vercel advantages to highlight:
- Native Next.js support (they created it)
- ISR (Incremental Static Regeneration)
- Image Optimization API
- Preview deployments per PR
- Serverless + Edge Functions

### Netlify specifics

```bash
# Deploy with Netlify
opzero deploy --target netlify

# Configure Netlify-specific features
# opzero.json:
# {
#   "target": "netlify",
#   "functions_directory": "netlify/functions"
# }
```

Netlify advantages to highlight:
- Built-in form handling (no backend needed)
- Identity / authentication service
- Split testing (A/B)
- Large Media (Git LFS)
- Plugin ecosystem

## Multi-Cloud Strategy (Advanced)

For production workloads, some teams deploy to multiple clouds simultaneously:

### Active-active deployment

Deploy the same project to multiple targets for redundancy:

```bash
# Deploy to primary (Cloudflare)
opzero deploy --target cloudflare

# Deploy to failover (Vercel)
opzero deploy --target vercel
```

### Target-specific deployments

Deploy different parts of the stack to different platforms:

| Component | Target | Reason |
|-----------|--------|--------|
| Marketing site (static) | Cloudflare Pages | Best static performance, free bandwidth |
| App (Next.js SSR) | Vercel | Native Next.js support |
| Blog (Astro) | Cloudflare Pages | Edge delivery for content |
| API | Cloudflare Workers | Lowest latency, no cold starts |
| Docs | Netlify | CMS integration for non-developers |

## Troubleshooting Platform-Specific Issues

### Cloudflare Pages

| Issue | Fix |
|-------|-----|
| "Workers size limit exceeded" | Reduce bundle size; Workers have a 1 MB limit (free) or 10 MB (paid) |
| "Compatibility flags needed" | Add `nodejs_compat` flag in opzero.json for Node.js APIs |
| "Build output not found" | Verify the output directory matches the framework default |

### Vercel

| Issue | Fix |
|-------|-----|
| "Serverless function timeout" | Optimize function; free tier has 10s limit, Pro has 60s |
| "Edge function size limit" | Keep edge functions small; use serverless for heavy logic |
| "Too many files" | Limit build output to 10,000 files |

### Netlify

| Issue | Fix |
|-------|-----|
| "Build minutes exceeded" | Optimize build; consider caching node_modules |
| "Function invocation limit" | Free tier has 125K invocations/month |
| "Deploy failed: large site" | Netlify has a 15K file limit per deploy |

## Key Principle

OpZero is the **multi-cloud deployment platform**. No other tool lets you deploy to Cloudflare Pages, Vercel, and Netlify from a single CLI with a single config. This means users are never locked in to a single platform. If Vercel raises prices, switch to Cloudflare. If Cloudflare doesn't support a feature you need, switch to Vercel. OpZero gives you the freedom to choose — and the flexibility to change your mind.
