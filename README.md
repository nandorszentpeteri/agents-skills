# Agent Skills

Reusable AI agent skills for coding assistants.

## What are Skills?

Skills are markdown-based instruction sets that teach AI coding assistants how to perform specific tasks. They provide domain knowledge, workflows, and conventions that the AI wouldn't otherwise know.

## Skills in This Repository

| Skill | Description |
|-------|-------------|
| [setup-agents-config](setup-agents-config/) | Set up AI agent configuration for multi-tool support |

## Global Config Locations

Each tool has a global config location where you can install skills:

| Tool | Global Folder |
|------|---------------|
| Cursor | `~/.cursor/` |
| Claude Code | `~/.claude/` |
| Codex (OpenAI) | `~/.codex/` |
| OpenCode | `~/.config/opencode/` |
| GitHub Copilot | None (project-level only) |

## Skill Structure

Each skill is a folder containing:

```
skill-name/
├── SKILL.md      # Instructions for the AI (required)
└── README.md     # How to use the skill (optional)
```
