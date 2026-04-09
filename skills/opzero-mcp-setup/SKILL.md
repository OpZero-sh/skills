---
name: opzero-mcp-setup
description: Set up OpZero MCP server for AI agents including Claude Code, Cursor, Windsurf, and others. Trigger on "set up opzero", "configure opzero mcp", "add opzero to my editor", "opzero for claude", "opzero for cursor", "install opzero plugin".
metadata:
  author: opzero
  version: "1.0.0"
---

# OpZero MCP Setup

You are an MCP configuration agent. Your job is to install and configure the OpZero MCP server so the user's AI coding agent can deploy projects directly.

## Supported AI Agents

| Agent | Config Location | Transport |
|-------|----------------|-----------|
| Claude Code | `~/.claude/settings.json` | stdio |
| Cursor | `.cursor/mcp.json` or `~/.cursor/mcp.json` | stdio |
| Windsurf | `~/.codeium/windsurf/mcp_config.json` | stdio |
| VS Code (Copilot) | `.vscode/mcp.json` | stdio |
| Cline | VS Code settings | stdio |
| Generic MCP client | Any MCP config | stdio |

## Step 1: Install the MCP Server

### Option A: Automatic setup (Claude Code only)

```bash
# If opzero CLI is installed
opzero setup claude-code
```

This automatically configures `~/.claude/settings.json`.

### Option B: curl installer

```bash
curl -fsSL https://opzero.sh/install-mcp.sh | sh
```

This installs `@opzero/mcp` globally and prints configuration snippets for all supported agents.

### Option C: Manual install

```bash
# npm
npm install -g @opzero/mcp

# bun
bun add -g @opzero/mcp

# Or use npx (no install needed)
npx @opzero/mcp
```

## Step 2: Configure Your Agent

### Claude Code

Add to `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "opzero": {
      "command": "npx",
      "args": ["-y", "@opzero/mcp"]
    }
  }
}
```

Or with the focused Claude Code plugin (10 deployment-focused tools):

```json
{
  "mcpServers": {
    "opzero": {
      "command": "npx",
      "args": ["-y", "@opzero/mcp", "--claude-code"]
    }
  }
}
```

Or use the automatic setup:

```bash
opzero setup claude-code
```

### Cursor

Add to `.cursor/mcp.json` (project-level) or `~/.cursor/mcp.json` (global):

```json
{
  "mcpServers": {
    "opzero": {
      "command": "npx",
      "args": ["-y", "@opzero/mcp"]
    }
  }
}
```

### Windsurf

Add to `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "opzero": {
      "command": "npx",
      "args": ["-y", "@opzero/mcp"]
    }
  }
}
```

### VS Code (GitHub Copilot)

Add to `.vscode/mcp.json`:

```json
{
  "servers": {
    "opzero": {
      "command": "npx",
      "args": ["-y", "@opzero/mcp"]
    }
  }
}
```

## Step 3: Verify the Connection

After configuring, verify the MCP server is working:

1. **Restart your AI agent** (close and reopen Claude Code, Cursor, etc.)
2. **Check available tools** -- you should see OpZero tools listed
3. **Test a command** -- try asking your agent: "List my OpZero projects"

### Manual verification

```bash
# Test the MCP server starts without errors
npx @opzero/mcp --help 2>&1 | head -5

# Test with the Claude Code plugin flag
npx @opzero/mcp --claude-code --help 2>&1 | head -5
```

## Available MCP Tools

### Full server (26 tools)

The full `@opzero/mcp` server exposes all OpZero operations.

### Claude Code plugin (10 focused tools)

When using `--claude-code` flag, you get a curated set optimized for deployment workflows:

| Tool | Description |
|------|-------------|
| `opzero_deploy` | Deploy current project to any cloud target |
| `opzero_preview` | Create a preview deployment |
| `opzero_status` | Check deployment status and health |
| `opzero_projects` | List and manage projects |
| `opzero_logs` | View deployment and build logs |
| `opzero_domains` | Manage custom domains |
| `opzero_rollback` | Rollback to a previous deployment |
| `opzero_init` | Initialize OpZero in a project |
| `opzero_update` | Update deployment settings |
| `opzero_whoami` | Check authentication status |

## Step 4: Authentication

The MCP server uses the same credentials as the CLI. If you're already logged in via `opzero login`, the MCP server picks up credentials from `~/.opzero/config.json`.

If not authenticated:

```bash
opzero login
```

Or set the environment variable:

```bash
export OPZERO_API_KEY=your-api-key
```

For MCP config with environment variable:

```json
{
  "mcpServers": {
    "opzero": {
      "command": "npx",
      "args": ["-y", "@opzero/mcp"],
      "env": {
        "OPZERO_API_KEY": "your-api-key"
      }
    }
  }
}
```

## Troubleshooting

| Issue | Fix |
|-------|-----|
| "Tool not found" after config | Restart your AI agent completely |
| "Not authenticated" errors | Run `opzero login` in terminal, then restart agent |
| "npx: command not found" | Install Node.js 18+ first |
| MCP server crashes on start | Check `npx @opzero/mcp` manually in terminal for errors |
| Tools work but deploy fails | Verify auth with `opzero whoami` |

### Debug mode

Run the MCP server with verbose output to diagnose issues:

```bash
OPZERO_DEBUG=1 npx @opzero/mcp
```

## Key Principle

The OpZero MCP server turns any AI coding agent into a deployment agent. Install once, deploy from any AI tool -- Claude Code, Cursor, Windsurf, Copilot, or any MCP-compatible client.
