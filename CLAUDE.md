# CLAUDE.md — OpZero Skills Library

## What this repo is

Agent skills for the OpZero platform. Each skill is a SKILL.md file in `skills/<name>/SKILL.md` — declarative playbooks that Claude Code reads and executes as step-by-step workflows. No TypeScript/JS code, pure markdown with YAML frontmatter.

## Structure

```
skills/
  deploy-to-opzero/SKILL.md      -- Flagship deploy skill
  opzero-quick-start/SKILL.md    -- Zero-to-live bootstrapping
  opzero-mcp-setup/SKILL.md      -- MCP server configuration
  multi-cloud-deploy/SKILL.md    -- Platform comparison + decision guide
  static-site-best-practices/SKILL.md -- Static site optimization
```

## SKILL.md Format

```yaml
---
name: skill-name
description: One-line description with trigger phrases
metadata:
  author: opzero
  version: "1.0.0"
---

# Skill Title

Body is a playbook for AI agents. Use imperative voice, shell commands, decision trees, and tables.
```

## Adding a new skill

1. Create `skills/<name>/SKILL.md` with YAML frontmatter
2. Write the body as an agent playbook (not human docs)
3. Add to the table in README.md
4. Push to main -- skills.sh indexes automatically

## Active skills

- **deploy-to-opzero** — Multi-step deploy: detect project type, check config, select target (Cloudflare/Vercel/Netlify), build, deploy
- **opzero-quick-start** — Scaffold from template, customize, deploy in 60 seconds

## Testing a skill

Skills are tested by invoking them in Claude Code:

```bash
# In a Claude Code session, trigger the skill:
claude "deploy my app"           # triggers deploy-to-opzero
claude "create a new website"    # triggers opzero-quick-start
```

Verify the skill:
1. Triggers activate on the right phrases
2. Steps execute in order
3. Error handling works (missing deps, failed builds, auth issues)
4. Output matches expected deploy URL pattern

## Environment variables

Skills themselves don't need env vars — they orchestrate tools that do. The target repo must have its env vars configured (pulled from Vercel).

## Related repos

- **OpZero.sh** (`~/opzero-sh/OpZero.sh`) — Deploy target and API backend
- **cli** (`~/opzero-sh/cli`) — MCP tools that skills invoke
- **Infra** (`~/opzero-sh/Infra`) — Skill loader references these files
