---
name: create-skill
description: Instructions for creating new OpenCode agent skills
---

# Creating OpenCode Agent Skills

Agent skills allow OpenCode to discover reusable instructions. When you need to create a new skill for the agent to use, follow these guidelines.

## Directory Structure
Create a directory for the skill and place a `SKILL.md` file inside it. OpenCode will search the following locations:
- Project config: `.opencode/skills/<name>/SKILL.md`
- Project Claude-compatible: `.claude/skills/<name>/SKILL.md`
- Project agent-compatible: `.agents/skills/<name>/SKILL.md`
- Global config: `~/.config/opencode/skills/<name>/SKILL.md`
- Global Claude-compatible: `~/.claude/skills/<name>/SKILL.md`
- Global agent-compatible: `~/.agents/skills/<name>/SKILL.md`

## Frontmatter
Each `SKILL.md` file must start with YAML frontmatter. Allowed fields:
- `name` (required): 1-64 chars, lowercase alphanumeric with single hyphens. Must match the directory name.
- `description` (required): 1-1024 chars. Specific enough for the agent to choose correctly.
- `license` (optional)
- `compatibility` (optional)
- `metadata` (optional): string-to-string map.

Unknown frontmatter fields are ignored.

## Writing the Content
After the frontmatter, write the instructions in Markdown format. Tell the agent what it should do, when to use it, and any specific steps or context it needs to follow when the skill is invoked.
