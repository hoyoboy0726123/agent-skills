# 🤖 Agent Skills

Reusable skills for AI coding assistants. Compatible with [Claude Code](https://code.claude.com/), [GitHub Copilot](https://github.com/features/copilot), [Codex CLI](https://github.com/openai/codex), and other agents supporting the [Agent Skills](https://agentskills.io) open standard.

## Available Skills

| Skill | Description |
|-------|-------------|
| [github-pages-deploy](./github-pages-deploy/) | Deploy static frontend projects to GitHub Pages |

## Installation

### Claude Code

```bash
# Personal (all projects)
mkdir -p ~/.claude/skills
cp -r github-pages-deploy ~/.claude/skills/

# Project-specific
mkdir -p .claude/skills
cp -r github-pages-deploy .claude/skills/
```

### GitHub Copilot

```bash
mkdir -p .github/skills
cp -r github-pages-deploy .github/skills/
```

### Other Agents

Copy the skill folder to your agent's skills directory, or provide the `SKILL.md` content directly.

## Prerequisites

Skills in this repository require:

### Git

| 平台 | 安裝方式 |
|------|----------|
| Windows | [下載安裝檔](https://git-scm.com/download/win) 或 `winget install Git.Git` |
| macOS | [下載安裝檔](https://git-scm.com/download/mac) 或 `brew install git` |
| Linux | `sudo apt install git` (Debian/Ubuntu) |

### GitHub CLI

| 平台 | 安裝方式 |
|------|----------|
| Windows | [下載 .msi 安裝檔](https://cli.github.com/) 或 `winget install GitHub.cli` |
| macOS | [下載 .pkg 安裝檔](https://cli.github.com/) 或 `brew install gh` |
| Linux | [安裝說明](https://github.com/cli/cli/blob/trunk/docs/install_linux.md) |

安裝後執行認證：
```bash
gh auth login
```

## Creating New Skills

Skills follow the [Agent Skills specification](https://agentskills.io/specification):

```
skill-name/
└── SKILL.md          # Required: YAML frontmatter + Markdown instructions
```

Basic `SKILL.md` structure:

```markdown
---
name: skill-name
description: What this skill does and when to use it.
---

# Instructions

Step-by-step guidance for the AI agent...
```

## References

- [Agent Skills Specification](https://agentskills.io/specification)
- [Anthropic Skills Repository](https://github.com/anthropics/skills)
- [Awesome Claude Skills](https://github.com/travisvn/awesome-claude-skills)

## License

MIT
