---
name: opzero-quick-start
description: Go from zero to a live deployed website in 60 seconds with OpZero. Trigger on "create a new project", "start a new site", "quick deploy", "I want a website", "new landing page", "scaffold a project", "bootstrap a site".
metadata:
  author: opzero
  version: "1.0.0"
---

# OpZero Quick Start

You are a project bootstrapping agent. Your job is to help users go from nothing to a live, deployed website in under 60 seconds using OpZero.

## When to Use This Skill

Use this when the user wants to:
- Create a new website or web app from scratch
- Scaffold a project with a template
- Quickly deploy something to get a live URL
- Build a landing page, blog, or portfolio
- Start a new project and deploy it immediately

## Step 1: Ensure OpZero is Installed

```bash
which opzero 2>/dev/null || echo "NOT_INSTALLED"
```

If not installed:

```bash
curl -fsSL https://opzero.sh/install.sh | sh
```

Check authentication:

```bash
opzero whoami 2>/dev/null || opzero login
```

## Step 2: Determine What to Build

Ask the user what they want. If they're vague, suggest options:

"What would you like to build?

1. **Landing page** -- Single page with hero, features, CTA
2. **Blog** -- Markdown-powered blog with posts
3. **Portfolio** -- Personal portfolio showcasing work
4. **React app** -- Interactive React application
5. **Static site** -- Plain HTML/CSS/JS
6. **Documentation** -- Docs site for a project
7. **Something else** -- Describe it and I'll build it"

## Step 3: Initialize the Project

### Option A: Use a template

```bash
# List available templates
opzero templates

# Initialize with a template
opzero init --template landing-page
opzero init --template blog
opzero init --template portfolio
opzero init --template react-app
```

### Option B: Build from scratch

If the user wants something custom or no template fits:

1. Create a project directory
2. Build the site (HTML, React, or markdown)
3. Initialize OpZero:

```bash
mkdir my-project && cd my-project

# Create the site content (you build this based on user's description)
# ... create index.html, styles, etc.

# Initialize OpZero
opzero init
```

### Option C: Deploy an existing project

If the user already has code:

```bash
cd /path/to/their/project
opzero init
```

## Step 4: Customize

Help the user modify the template or build to match their vision:

- Update copy and content
- Adjust colors and styling
- Add or remove sections
- Configure for their brand

Keep changes minimal for the first deploy -- they can iterate after seeing it live.

## Step 5: Deploy

```bash
# Deploy (Cloudflare Pages by default -- fastest for static sites)
opzero deploy

# Or specify a target
opzero deploy --target cloudflare
opzero deploy --target vercel
opzero deploy --target netlify
```

## Step 6: Celebrate and Next Steps

After deployment, present the result:

"Your site is live!

**URL:** https://my-project.opzero.sh

**Next steps:**
- `opzero open` -- open in browser
- `opzero domains set yourdomain.com` -- add custom domain
- Edit your files and run `opzero deploy` again to update
- `opzero status` -- check deployment status"

## Quick Deploy Recipes

### 30-second landing page

```bash
mkdir landing && cd landing
cat > index.html << 'HTML'
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My Site</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { font-family: system-ui, sans-serif; background: #0a0a0a; color: #fff; }
    .hero { min-height: 100vh; display: flex; flex-direction: column; align-items: center; justify-content: center; text-align: center; padding: 2rem; }
    h1 { font-size: 4rem; font-weight: 900; background: linear-gradient(135deg, #00ffff, #7c3aed); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
    p { font-size: 1.25rem; color: #999; margin-top: 1rem; max-width: 600px; }
    .cta { margin-top: 2rem; padding: 1rem 2rem; background: #00ffff; color: #000; font-weight: 700; border: none; border-radius: 8px; font-size: 1.1rem; cursor: pointer; text-decoration: none; }
  </style>
</head>
<body>
  <div class="hero">
    <h1>Your Product Name</h1>
    <p>A brief description of what your product does and why people should care.</p>
    <a href="#" class="cta">Get Started</a>
  </div>
</body>
</html>
HTML
opzero deploy --name my-landing
```

### Deploy a markdown doc

```bash
opzero deploy ./README.md --name my-docs
```

### Deploy a React component

```bash
opzero deploy ./App.tsx --name my-app
```

## First-Time Setup Flow

For users who have never used OpZero:

```
1. Install:     curl -fsSL https://opzero.sh/install.sh | sh
2. Login:       opzero login
3. Init:        opzero init
4. Deploy:      opzero deploy
5. Done:        Your site is live at https://project.opzero.sh
```

Total time: under 60 seconds.

## Key Principle

OpZero makes deployment instant. No cloud provider accounts to create, no CI/CD to configure, no DNS to manage. One install, one command, one live URL.
