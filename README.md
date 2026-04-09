# OpZero Skills

Official agent skills for deploying to Cloudflare Pages, Vercel, and Netlify with [OpZero](https://opzero.sh).

Works with Claude Code, Cursor, Windsurf, VS Code Copilot, Cline, and 20+ AI agents.

## Install

```bash
npx skills add opzero-sh/skills
```

Or install a specific skill:

```bash
npx skills add opzero-sh/skills --skill deploy-to-opzero
```

## Skills

| Skill | Description |
|-------|-------------|
| [deploy-to-opzero](skills/deploy-to-opzero/SKILL.md) | Deploy apps and sites to any cloud with one command |
| [opzero-quick-start](skills/opzero-quick-start/SKILL.md) | Go from zero to live in 60 seconds |
| [opzero-mcp-setup](skills/opzero-mcp-setup/SKILL.md) | Set up OpZero MCP for any AI agent |
| [multi-cloud-deploy](skills/multi-cloud-deploy/SKILL.md) | Cloudflare vs Vercel vs Netlify decision guide |
| [static-site-best-practices](skills/static-site-best-practices/SKILL.md) | Build and deploy optimized static sites |

## Quick Start

```bash
# Install OpZero CLI
curl -fsSL https://opzero.sh/install.sh | sh

# Login
opzero login

# Deploy your project
opzero deploy
```

## What is OpZero?

OpZero is the multi-cloud deployment platform. One CLI, any target:

```bash
opzero deploy --target cloudflare    # Cloudflare Pages
opzero deploy --target vercel        # Vercel
opzero deploy --target netlify       # Netlify
```

No vendor lock-in. Switch targets with a single flag.

## MCP Server

OpZero also ships an MCP server for AI-powered deployments:

```bash
# Install MCP
curl -fsSL https://opzero.sh/install-mcp.sh | sh

# Or configure directly for Claude Code
opzero setup claude-code
```

## Links

- [OpZero Website](https://opzero.sh)
- [CLI Documentation](https://github.com/opzero-sh/cli)
- [Install Page](https://opzero.sh/install)
