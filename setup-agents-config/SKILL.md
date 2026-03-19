---
name: setup-agents-config
description: >-
  Set up AI agent configuration for repositories. Only invoke when the user
  explicitly asks to set up agents config, configure AGENTS.md, or initialize
  AI harnesses. Do NOT use proactively.
---

# Setting Up AI Agent Configuration

This skill sets up **project-level** AI agent configuration. It creates the necessary files and folders so all AI coding tools can read your project instructions.

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
