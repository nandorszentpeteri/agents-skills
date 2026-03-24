---
name: setup-agents-config
description: >-
  Set up AI agent configuration for repositories or globally. Only invoke when
  the user explicitly asks to set up agents config, configure AGENTS.md,
  initialize AI harnesses, or install skills/agents globally. Do NOT use
  proactively.
---

# Setting Up AI Agent Configuration

This skill sets up AI agent configuration at **project level** or **globally**. It creates the necessary files, folders, and symlinks so all AI coding tools can read your project instructions or share skills/agents across projects.

Ask the user which scope they need (project or global), or infer it from their request.

**Note:** This guide is for Linux and macOS. Windows paths may differ.

## Core Concept

**All tools read from `AGENTS.md` + `.agents/`** — this is the universal standard. The only exception is Claude Code, which requires `CLAUDE.md` + `.claude/`.

You don't need tool-specific folders like `.github/copilot-instructions.md`, `.cursor/`, or `.codex/` in your project.

## Tool Compatibility

| Tool | Project Discovery | Global Location |
|------|-------------------|-----------------|
| Codex (OpenAI) | `AGENTS.md` + `.agents/` | `~/.codex/` |
| Cursor | `AGENTS.md` + `.agents/` | `~/.cursor/` |
| GitHub Copilot | `AGENTS.md` + `.agents/` | None |
| OpenCode | `AGENTS.md` + `.agents/` | `~/.config/opencode/` |
| **Claude Code** | `CLAUDE.md` + `.claude/` | `~/.claude/` |

## Project Setup

### File Structure

```
repo-root/
├── AGENTS.md              # Single source of truth (all tools)
├── CLAUDE.md              # References @AGENTS.md (Claude Code only)
├── .agents/               # Configuration directory
└── .claude -> .agents/    # Symlink for Claude Code compatibility
```

**Why the symlink?** Claude Code looks for `.claude/`. Symlinking `.agents` to `.claude` lets you maintain one directory that works for all tools.

### Check Existing Setup

Before creating files, check what already exists:

```bash
# Check for existing config files
ls -la AGENTS.md CLAUDE.md .agents .claude 2>/dev/null

# Verify .claude symlink points to .agents
readlink .claude

# Check CLAUDE.md content
cat CLAUDE.md 2>/dev/null
```

Report missing or misconfigured items to the user before proceeding.

### Setup Commands

Run these commands in the repository root:

```bash
# 1. Create AGENTS.md (user adds project-specific instructions)
touch AGENTS.md

# 2. Create CLAUDE.md referencing AGENTS.md
echo "@AGENTS.md" > CLAUDE.md

# 3. Create .agents directory
mkdir -p .agents

# 4. Create symlink for Claude Code
ln -sf .agents .claude
```

### Verification

After setup, verify the structure:

```bash
ls -la AGENTS.md CLAUDE.md .agents/ .claude
cat CLAUDE.md  # Should show: @AGENTS.md
readlink .claude  # Should show: .agents
```

## Global Setup

Global setup lets you share skills and agents across all projects by keeping them in a central `~/.agents/` directory and symlinking into each tool's global config.

### Global Directory Structure

```
~/.agents/
├── skills/
│   └── <skill-name>/
│       └── SKILL.md
└── agents/
    └── <agent-name>/
        └── ...
```

### Check Existing Setup

```bash
# Check if central directory exists
ls -la ~/.agents/skills ~/.agents/agents 2>/dev/null

# Check tool-specific global dirs
ls -la ~/.claude/skills ~/.cursor/skills ~/.codex/skills ~/.config/opencode/skills 2>/dev/null
```

Report what already exists to the user before proceeding.

### Setup Commands

```bash
# 1. Create central directories
mkdir -p ~/.agents/skills ~/.agents/agents

# 2. Copy or clone skills/agents into ~/.agents/
#    (method left to user — git clone, manual copy, etc.)

# 3. Create tool-specific parent dirs
mkdir -p ~/.claude/skills ~/.claude/agents
mkdir -p ~/.cursor/skills ~/.cursor/agents
mkdir -p ~/.codex/skills ~/.codex/agents
mkdir -p ~/.config/opencode/skills ~/.config/opencode/agents

# 4. Symlink individual items into each tool
ln -sf ~/.agents/skills/<skill-name> ~/.claude/skills/<skill-name>
ln -sf ~/.agents/skills/<skill-name> ~/.cursor/skills/<skill-name>
ln -sf ~/.agents/skills/<skill-name> ~/.codex/skills/<skill-name>
ln -sf ~/.agents/skills/<skill-name> ~/.config/opencode/skills/<skill-name>
# Same pattern for agents
ln -sf ~/.agents/agents/<agent-name> ~/.claude/agents/<agent-name>
ln -sf ~/.agents/agents/<agent-name> ~/.cursor/agents/<agent-name>
ln -sf ~/.agents/agents/<agent-name> ~/.codex/agents/<agent-name>
ln -sf ~/.agents/agents/<agent-name> ~/.config/opencode/agents/<agent-name>
```

**Key points:**
- GitHub Copilot is skipped (no global config directory)
- If the user doesn't specify which skills/agents to install, list available ones under `~/.agents/` and ask
- Existing tool configs are never overwritten — we only add into `skills/` and `agents/` subdirs

### Global Verification

```bash
# Verify symlinks resolve correctly
ls -la ~/.claude/skills/ ~/.cursor/skills/ ~/.codex/skills/ ~/.config/opencode/skills/

# Check a specific symlink target
readlink ~/.claude/skills/<skill-name>
# Should show: ~/.agents/skills/<skill-name> (or the expanded absolute path)
```

### Global Troubleshooting

**Symlink broken (skill not found by tool):**
- Check the target exists: `ls -la ~/.agents/skills/<skill-name>`
- Recreate the symlink: `ln -sf ~/.agents/skills/<skill-name> ~/.claude/skills/<skill-name>`

**Tool not picking up global skills:**
- Verify the tool supports the global location (see Tool Compatibility table above)
- Check the symlink is in the correct directory for that tool

## Troubleshooting

**Claude Code not finding instructions:**
- Verify `.claude` symlink exists: `ls -la .claude`
- Check `CLAUDE.md` contains `@AGENTS.md`
- Verify symlink target: `readlink .claude` should show `.agents`

**Codex not loading instructions:**
- Check `AGENTS.md` exists at repo root
- Run `codex --ask-for-approval never "Summarize current instructions"`

**Other tools not reading config:**
- Verify `AGENTS.md` exists at repo root
- Check `.agents/` directory exists
