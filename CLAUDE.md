# OpZero Skills Repo

Agent skills for the skills.sh ecosystem. Each skill is a SKILL.md file in `skills/<name>/SKILL.md`.

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

## Adding a New Skill

1. Create `skills/<name>/SKILL.md` with YAML frontmatter
2. Write the body as an agent playbook (not human docs)
3. Add to the table in README.md
4. Push to main -- skills.sh indexes automatically
