---
name: setup-mcp
description: >-
  Add, remove, or list MCP servers across AI coding tools (Claude Code, Cursor,
  Copilot, Codex, OpenCode). Knows the correct file path, format, and schema for
  each harness at both user/global and project level. Use when the user says "add
  MCP", "set up MCP", "configure MCP server", or wants to sync an MCP across tools.
---

# Setup MCPs

Add MCP servers to AI coding tools. Each tool has its own config format and path — this skill knows them all.

## Workflow

1. User says which MCP to add (name, URL or command, any auth)
2. User says which harness (or "all")
3. Add it to the correct file in the correct format
4. Ask: "Want me to add this to your other harnesses too?" (list which ones the user has configured)

## Discovering Existing MCPs

Before adding, check what's already configured to avoid duplicates and to reuse patterns (naming, auth style):

| Tool | Check |
|------|-------|
| Claude Code | `cat ~/.claude.json` → look at top-level `mcpServers` |
| Cursor | `cat ~/.cursor/mcp.json` |
| Copilot | `cat ~/Library/Application\ Support/Code/User/mcp.json` |
| Codex | `cat ~/.codex/config.toml` → `[mcp_servers.*]` sections |
| OpenCode | `cat ~/.config/opencode/opencode.jsonc` → `"mcp"` key |

Also check project-level configs in the current working directory.

## Harness Reference

### Claude Code

**Paths:**
- Global: `~/.claude.json` → top-level `"mcpServers"` object
- Project (shared): `.mcp.json` at project root
- Per-project-local: `~/.claude.json` → `"projects".<absolute-path>."mcpServers"`

**Format:**
```json
{
  "mcpServers": {
    "server-name": {
      "type": "http",
      "url": "https://example.com/mcp"
    }
  }
}
```

```json
{
  "mcpServers": {
    "server-name": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "some-mcp-server"],
      "env": { "API_KEY": "${API_KEY}" }
    }
  }
}
```

**Fields:** `type` (http|stdio|sse), `url`, `command`, `args`, `env`, `headers`, `headersHelper`, `oauth`, `alwaysLoad`
**Env interpolation:** `${VAR}`, `${VAR:-default}`
**CLI:** `claude mcp add --scope user|project|local --transport http <name> <url>`

---

### Cursor

**Paths:**
- Global: `~/.cursor/mcp.json`
- Project: `.cursor/mcp.json`

**Format:**
```json
{
  "mcpServers": {
    "server-name": {
      "url": "https://example.com/mcp"
    }
  }
}
```

```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "some-mcp-server"],
      "env": { "API_KEY": "value" }
    }
  }
}
```

**Fields (remote):** `url`, `headers`, `auth`
**Fields (stdio):** `command`, `args`, `env`, `envFile`
**No `type` field** — Cursor infers from `command` vs `url`.
**Variables:** `${env:NAME}`, `${userHome}`, `${workspaceFolder}`
**CLI:** `cursor --add-mcp '<json>'` (user scope), add `--mcp-workspace` for project scope

---

### GitHub Copilot (VS Code)

**Paths:**
- Global: `~/Library/Application Support/Code/User/mcp.json` (macOS)
- Project: `.vscode/mcp.json`

**Format (note: top-level key is `servers`, not `mcpServers`):**
```json
{
  "servers": {
    "server-name": {
      "type": "http",
      "url": "https://example.com/mcp"
    }
  }
}
```

```json
{
  "servers": {
    "server-name": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "some-mcp-server"],
      "env": { "API_KEY": "value" }
    }
  }
}
```

**Fields:** `type` (stdio|http|sse), `url`, `command`, `args`, `env`, `headers`
**Quirk:** Key is `"servers"` not `"mcpServers"`

---

### Codex (OpenAI)

**Paths:**
- Global: `~/.codex/config.toml`
- Project: `.codex/config.toml`

**Format (TOML):**
```toml
[mcp_servers.server-name]
url = "https://example.com/mcp"

[mcp_servers.local-server]
command = "npx"
args = ["-y", "some-mcp-server"]
env = { API_KEY = "value" }
```

**Fields (remote):** `url`, `bearer_token_env_var`, `http_headers`, `env_http_headers`, `scopes`
**Fields (stdio):** `command`, `args`, `env`, `env_vars`, `cwd`
**Shared:** `enabled`, `required`, `startup_timeout_sec`, `tool_timeout_sec`, `enabled_tools`, `disabled_tools`
**CLI:** `codex mcp add <name> --env VAR=VALUE -- <command>`

---

### OpenCode

**Paths:**
- Global: `~/.config/opencode/opencode.jsonc` (or `opencode.json`)
- Project: `.opencode/opencode.jsonc` (or `opencode.jsonc` / `opencode.json` at root)

**Format (JSONC, top-level key is `mcp`):**
```jsonc
{
  "mcp": {
    "server-name": {
      "type": "remote",
      "url": "https://example.com/mcp",
      "enabled": true
    }
  }
}
```

```jsonc
{
  "mcp": {
    "server-name": {
      "type": "local",
      "command": ["npx", "-y", "some-mcp-server"],
      "environment": { "API_KEY": "value" },
      "enabled": true
    }
  }
}
```

**Fields (remote):** `type: "remote"`, `url`, `headers`, `oauth`, `enabled`, `profile`, `timeout`
**Fields (local):** `type: "local"`, `command` (array!), `environment`, `enabled`, `profile`, `timeout`
**Profile:** `"always"` | `"lazy"` | `"manual"` | `"off"`
**Env interpolation:** `{env:VAR}`, `{file:path}`
**CLI:** `opencode mcp add` (interactive, if available)

---

## Decision Guide

- **"Add to all"** → add to every harness where the user has a global config file
- **Remote MCP (URL-based)** → most tools support it similarly, just different field names
- **Local/stdio MCP** → watch for `command` (string in Claude/Cursor/Codex) vs `command` (array in OpenCode)
- **OAuth MCPs** → each tool handles OAuth differently; most just need the URL and will prompt for auth on first use
- **Project vs global** → if the MCP is useful across all projects, prefer global; if repo-specific, use project-level config
