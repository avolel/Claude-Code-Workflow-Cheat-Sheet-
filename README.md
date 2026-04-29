# Claude Code Workflow Cheatsheet

A complete, verified reference for [Claude Code](https://code.claude.com/docs/en/overview) — Anthropic's agentic coding tool that lives in your terminal, understands your codebase, and helps you ship faster.

## What's Inside

A single markdown cheatsheet covering everything you need to go from zero to productive with Claude Code:

- **Installation** — every official install method (curl, Homebrew, WinGet, npm)
- **CLAUDE.md** — how to write effective project memory using the WHY/WHAT/HOW framework
- **Memory hierarchy** — global, project, and subfolder memory files
- **Project structure** — the `.claude/` directory anatomy
- **Skills** — auto-invoked knowledge packs that give Claude project-specific expertise
- **Hooks** — deterministic shell callbacks for safety gates and automation
- **Permissions** — `allow` / `ask` / `deny` rules with real examples
- **Daily workflow** — the Explore → Plan → Implement → Commit pattern
- **Quick reference** — slash commands, keyboard shortcuts, and CLI flags

## Files

| File | Description |
|------|-------------|
| [`Cheat-sheet.md`](./Cheat-sheet.md) | The full cheatsheet |

## How to Use

Open the cheatsheet in any markdown viewer (GitHub, VS Code, Obsidian, Typora) and use it as a reference while you set up Claude Code.

For best results:

1. Skim sections 1–4 once to understand the model
2. Copy the example `CLAUDE.md` from section 2 into your own project
3. Bookmark section 12 (Quick Reference) — you'll come back to it often

## Quick Start

```bash
# Install (macOS/Linux/WSL)
curl -fsSL https://claude.ai/install.sh | bash

# Launch in your project
cd your_project
claude

# Generate a starter CLAUDE.md
/init
```

That's the whole onboarding. The cheatsheet covers everything that comes after.

## Requirements

- A Claude **Pro**, **Max**, **Team**, **Enterprise**, or **API/Console** account (free Claude.ai does not include Claude Code)
- macOS 13+, Windows 10+, or a modern Linux distro
- 4 GB+ RAM

## References

- [Claude Code Documentation](https://code.claude.com/docs/en/overview)
- [Setup Guide](https://code.claude.com/docs/en/setup)
- [GitHub Repository](https://github.com/anthropics/claude-code)
- [npm Package](https://www.npmjs.com/package/@anthropic-ai/claude-code)

## License

This cheatsheet is provided as-is for personal and team use. Claude Code itself is © Anthropic.
