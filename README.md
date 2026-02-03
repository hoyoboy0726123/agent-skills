# 🤖 Agent Skills

Skills for AI coding assistants. Compatible with Claude Code, Gemini CLI, Codex CLI, and other agents supporting the [Agent Skills](https://agentskills.io) standard.

## Skills

| Skill | Description |
|-------|-------------|
| [github-pages-deploy](./github-pages-deploy/) | Deploy static frontend projects to GitHub Pages |

## Installation

### Claude Code

```bash
# Personal (all projects)
cp -r github-pages-deploy ~/.claude/skills/

# Project-specific
cp -r github-pages-deploy .claude/skills/
```

### Other Agents

Copy `SKILL.md` content to your agent's instruction directory.

## Prerequisites

These skills require:

- **Git** - Version control
- **GitHub CLI (`gh`)** - GitHub operations

Install GitHub CLI:
```bash
# Windows
winget install GitHub.cli

# macOS
brew install gh

# Linux
sudo apt install gh
```

Then authenticate:
```bash
gh auth login
```

## License

MIT
