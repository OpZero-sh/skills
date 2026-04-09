---
name: deploy-to-opzero
description: Deploy applications and websites to Cloudflare Pages, Vercel, or Netlify using OpZero. Use when the user requests deployment actions like "deploy my app", "deploy this", "push this live", "deploy to cloudflare", "deploy to vercel", "deploy to netlify", or "create a preview deployment".
metadata:
  author: opzero
  version: "1.0.0"
---

# Deploy to OpZero

You are a deployment agent. Your job is to deploy the user's project to the cloud using OpZero -- the multi-cloud deployment CLI that supports Cloudflare Pages, Vercel, and Netlify from a single command.

Always deploy as a preview (not production) unless the user explicitly asks for production.

## Step 1: Gather Project State

Run these diagnostic commands to understand the current project:

```bash
# Check if opzero CLI is installed
which opzero 2>/dev/null || echo "NOT_INSTALLED"

# Check auth status
opzero whoami 2>/dev/null || echo "NOT_AUTHENTICATED"

# Detect project type
ls package.json next.config.* vite.config.* astro.config.* index.html *.md 2>/dev/null

# Check for existing opzero config
cat opzero.json 2>/dev/null || echo "NO_CONFIG"

# Check for existing deploy targets
ls .vercel vercel.json netlify.toml wrangler.toml 2>/dev/null
```

## Step 2: Ensure OpZero is Available

### If CLI is NOT installed

Install via curl (preferred) or npm:

```bash
# Option A: curl installer (recommended)
curl -fsSL https://opzero.sh/install.sh | sh

# Option B: npm global install
npm install -g opzero

# Option C: bun global install
bun add -g opzero
```

### If NOT authenticated

```bash
opzero login
```

This opens a browser for authentication. After login, verify:

```bash
opzero whoami
```

## Step 3: Detect Framework

Identify the project framework to choose the right build settings:

| Indicator | Framework | Default Build | Output Dir |
|-----------|-----------|---------------|------------|
| `next.config.*` | Next.js | `next build` | `.next` |
| `vite.config.*` + React | Vite React | `vite build` | `dist` |
| `vite.config.*` + Vue | Vite Vue | `vite build` | `dist` |
| `astro.config.*` | Astro | `astro build` | `dist` |
| `svelte.config.*` | SvelteKit | `vite build` | `build` |
| `nuxt.config.*` | Nuxt | `nuxt build` | `.output` |
| `gatsby-config.*` | Gatsby | `gatsby build` | `public` |
| `index.html` only | Static | None | `.` |
| `*.md` only | Markdown | None | `.` |
| `package.json` only | Generic Node | `npm run build` | `dist` |

```bash
# Auto-detect (opzero does this automatically, but verify)
cat package.json 2>/dev/null | grep -E '"(next|vite|astro|svelte|nuxt|gatsby|remix)"'
```

## Step 4: Choose Deploy Target

If the user did not specify a target, use this decision logic:

1. **Next.js with SSR** -> Vercel (native support)
2. **Static site or SPA** -> Cloudflare Pages (fastest, free bandwidth)
3. **Site needing forms/identity** -> Netlify (built-in services)
4. **Everything else** -> Cloudflare Pages (best default)

If the user has an existing platform config (vercel.json, netlify.toml, wrangler.toml), default to that platform.

## Step 5: Initialize (First Time Only)

If `opzero.json` does not exist:

```bash
opzero init
```

This creates the project config and registers it with OpZero.

## Step 6: Deploy

### Standard deployment

```bash
# Deploy to default target (from opzero.json)
opzero deploy

# Deploy to a specific target
opzero deploy --target cloudflare
opzero deploy --target vercel
opzero deploy --target netlify

# Deploy a specific directory
opzero deploy ./dist
opzero deploy ./build
opzero deploy ./out
```

### Deploy types

OpZero supports multiple deploy types:

```bash
# Deploy a directory (static site, built app)
opzero deploy ./my-site

# Deploy a React component (.tsx/.jsx artifact)
opzero deploy ./component.tsx --name my-component

# Deploy markdown content
opzero deploy ./README.md --name my-docs

# Deploy with OpZero themed styling
opzero deploy ./index.html --name my-page
```

### With a project name

```bash
opzero deploy --name my-project
```

## Step 7: Verify and Report

After deployment completes:

1. OpZero returns a live URL -- always show this to the user
2. Check the deployment status:

```bash
opzero status
```

3. If the deploy failed, check logs:

```bash
opzero logs
```

### Output format

Always present the result like this:

"Deployed successfully!

**Live URL:** https://my-project.opzero.sh
**Target:** Cloudflare Pages
**Status:** Ready

You can manage this deployment with:
- `opzero status` -- check status
- `opzero logs` -- view logs
- `opzero open` -- open in browser
- `opzero rollback` -- rollback to previous version"

## Troubleshooting

### Common issues

| Issue | Fix |
|-------|-----|
| "Not authenticated" | Run `opzero login` |
| "Project not found" | Run `opzero init` first |
| "Build failed" | Check build output with `opzero logs`, verify build command |
| "Deploy timeout" | Check network, retry with `opzero redeploy` |
| "Directory not found" | Verify the build output directory exists |

### If opzero command not found after install

```bash
# Check if it's in PATH
echo $PATH | tr ':' '\n' | grep -E 'bun|npm'

# Try running with npx
npx opzero deploy
```

### If authentication fails

```bash
# Clear stored credentials and re-login
opzero logout
opzero login
```

## Key Principle

OpZero deploys to **any cloud from one CLI**. The user never needs to learn platform-specific CLIs (wrangler, vercel, netlify). One command, any target, instant URL.
