# setup-mcp

Add MCP servers to any AI coding tool — knows the correct path, format, and schema for each harness at both user/global and project level.

## What It Does

1. **Discovers** existing MCP configs across your tools to match naming and avoid duplicates
2. **Adds** the server to the specified harness in the correct format
3. **Offers** to add it to your other configured harnesses too

## Supported Tools

| Tool | Project Config | Global Config |
|------|---------------|---------------|
| Claude Code | `.mcp.json` | `~/.claude.json` |
| Cursor | `.cursor/mcp.json` | `~/.cursor/mcp.json` |
| GitHub Copilot | `.vscode/mcp.json` | `~/Library/.../Code/User/mcp.json` |
| Codex | `.codex/config.toml` | `~/.codex/config.toml` |
| OpenCode | `.opencode/opencode.jsonc` | `~/.config/opencode/opencode.jsonc` |

## When It Triggers

- "Add the Slack MCP to Claude Code"
- "Set up Atlassian MCP for all my tools"
- "Add playwright MCP to this project"
- "What MCPs do I have configured?"

## Key Rules

- Each tool has a different config format (JSON, JSONC, TOML) and different field names — the skill handles translation
- For OpenCode built-in MCPs, just enables them rather than redeclaring
- Prefers global scope for MCPs useful across all projects
- Matches existing naming conventions found in the user's configs
