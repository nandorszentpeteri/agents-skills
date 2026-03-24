# setup-agents-config

Sets up AI agent configuration for repositories or globally, so all coding tools (Cursor, Codex, Claude Code, GitHub Copilot, OpenCode) can read your project instructions or share skills/agents across projects.

## What It Does

### Project Setup

Creates the following structure in your project:

```
repo-root/
├── AGENTS.md              # Your project instructions (all tools)
├── CLAUDE.md              # References @AGENTS.md (Claude Code)
├── .agents/               # Configuration directory
└── .claude -> .agents/    # Symlink for Claude Code
```

### Global Setup

Symlinks skills and agents from a central `~/.agents/` directory into each tool's global config:

```
~/.agents/                          # Central store
├── skills/<skill-name>/SKILL.md
└── agents/<agent-name>/...

~/.claude/skills/<skill-name> -> ~/.agents/skills/<skill-name>
~/.cursor/skills/<skill-name> -> ~/.agents/skills/<skill-name>
~/.codex/skills/<skill-name>  -> ~/.agents/skills/<skill-name>
~/.config/opencode/skills/<skill-name> -> ~/.agents/skills/<skill-name>
```

GitHub Copilot is skipped (no global config directory). Existing tool configs are never overwritten.

## How to Invoke

Ask the AI assistant:

```
"Set up agents config for this project"
"Configure AGENTS.md for this repo"
"Install my skills globally"
"Set up global agents config"
```

The skill will check existing setup and create any missing files, folders, or symlinks.

## Install Globally

To use this skill across all projects, install it in your tool's global config folder:

- Cursor: `~/.cursor/skills/`
- Claude Code: `~/.claude/skills/`
