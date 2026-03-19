# setup-agents-config

Sets up AI agent configuration for repositories so all coding tools (Cursor, Codex, Claude Code, GitHub Copilot, OpenCode) can read your project instructions.

## What It Does

Creates the following structure in your project:

```
repo-root/
├── AGENTS.md              # Your project instructions (all tools)
├── CLAUDE.md              # References @AGENTS.md (Claude Code)
├── .agents/               # Configuration directory
└── .claude -> .agents/    # Symlink for Claude Code
```

## How to Invoke

Ask the AI assistant:

```
"Set up agents config for this project"
"Configure AGENTS.md for this repo"
"Initialize AI harnesses"
```

The skill will check existing setup and create any missing files/folders.

## Install Globally

To use this skill across all projects, install it in your tool's global config folder:

- Cursor: `~/.cursor/skills/`
- Claude Code: `~/.claude/skills/`
