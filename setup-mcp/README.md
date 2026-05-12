# setup-mcp

Add MCP servers to any AI coding tool — knows the correct path, format, and schema for each.

## Supported Tools

- **Claude Code** — `.mcp.json` / `~/.claude.json`
- **Cursor** — `.cursor/mcp.json` / `~/.cursor/mcp.json`
- **GitHub Copilot** — `.vscode/mcp.json` / `~/Library/.../Code/User/mcp.json`
- **Codex** — `.codex/config.toml` / `~/.codex/config.toml`
- **OpenCode** — `.opencode/opencode.jsonc` / `~/.config/opencode/opencode.jsonc`

## How to Invoke

```
"Add the Slack MCP to Claude Code"
"Set up Atlassian MCP for all my tools"
"Add playwright MCP to this project"
"What MCPs do I have configured?"
```

The skill discovers your existing MCPs first to match naming conventions and avoid duplicates, then adds the server in the correct format. It will ask if you want it added to other harnesses too.
