# Claude Code Workflow Cheatsheet

**Getting Started • Project Setup • File Structure • Skills • Hooks • Memory • Workflows • 2026 Edition**

> A complete, verified reference for Claude Code — Anthropic's agentic coding tool that lives in your terminal, understands your codebase, and helps you ship faster.

---

## 1. Getting Started

### Install Claude Code

The recommended install methods (no Node.js required):

```bash
# macOS, Linux, WSL
curl -fsSL https://claude.ai/install.sh | bash

# Windows PowerShell
irm https://claude.ai/install.ps1 | iex

# macOS via Homebrew
brew install --cask claude-code

# Windows via WinGet
winget install Anthropic.ClaudeCode
```

### Alternative: install via npm (requires Node.js 18+)

```bash
npm install -g @anthropic-ai/claude-code
```

> ⚠️ Do **NOT** use `sudo npm install -g` — it causes permission and security issues.

### First Launch

```bash
cd your_project
claude          # Launches Claude Code, prompts you to log in on first use
/init           # Scans your codebase and generates a starter CLAUDE.md
```

### System Requirements

- **OS:** macOS 13.0+, Windows 10 1809+, Ubuntu 20.04+, Debian 10+, Alpine 3.19+
- **Hardware:** 4 GB+ RAM, x64 or ARM64
- **Account:** Pro, Max, Team, Enterprise, or API/Console access (free Claude.ai does **not** include Claude Code)

### Verify Your Install

```bash
claude --version    # Confirm install
claude doctor       # Detailed health check of install + config
claude update       # Manually update to latest
```

---

## 2. Understanding CLAUDE.md

**CLAUDE.md** = persistent project memory. Loaded automatically at the start of every Claude Code session — you never have to reference it manually.

### The WHY / WHAT / HOW Framework

| Section | Purpose |
|---------|---------|
| **WHY** | Purpose of the project, why it exists, what problem it solves |
| **WHAT** | Tech stack, directory map, architectural decisions |
| **HOW** | Build/test/lint commands, workflows, conventions, gotchas |

> 💡 Keep CLAUDE.md under ~60–80 lines. A focused, lean file outperforms a sprawling one.

### Example CLAUDE.md

```markdown
# Project: MyApp
A FastAPI REST API + React SPA backed by Postgres.
Used internally to manage customer onboarding.

## Commands
- `npm run dev`    — start dev server
- `npm run test`   — run Jest test suite
- `npm run lint`   — eslint + prettier check

## Architecture
- /app    → Next.js App Router pages
- /lib    → shared utilities
- /prisma → DB schema & migrations

## Conventions
- Use TypeScript strict mode
- All API routes return { data, error }
- See @docs/architecture.md for deeper context

## Gotchas
- Postgres must run on port 5433 (not 5432) — port conflict with system Postgres
- `npm run test` requires DATABASE_URL_TEST to be set
```

### Importing Other Files

Use `@filename` to pull in additional context:

```markdown
See @README.md for project overview
See @package.json for available commands
@~/.claude/personal-rules.md
```

Recursive imports are supported up to 5 levels deep.

---

## 3. Memory File Hierarchy

Claude Code merges memory files from multiple locations. Lower entries override or extend higher ones.

```
~/.claude/CLAUDE.md       → User-global — applies to ALL projects
~/CLAUDE.md               → Parent — useful for monorepo roots
./CLAUDE.md               → Project — committed to git, shared with team
./frontend/CLAUDE.md      → Subfolder — scoped to that directory
```

### Best Practices

- Keep each file **under 200 lines** — context is precious
- Subfolder files **append** to (don't replace) parent context
- Commit project-level CLAUDE.md to git so the whole team benefits
- Put personal preferences in `~/.claude/CLAUDE.md` (never committed)

### Editing Memory

```bash
/memory   # Opens memory files in your default editor
/init     # Regenerate project CLAUDE.md from scratch
```

---

## 4. CLAUDE.md Best Practices

- Run `/init` first, then refine the output by hand
- Be **specific** in instructions — vague rules produce vague code
- Document **gotchas** Claude couldn't infer (port conflicts, weird env requirements, broken tests)
- Reference docs inline with `@filename`
- Keep code-style rules **out** of CLAUDE.md — let your linter/formatter handle them
- Keep memory **concise** — every line costs context budget
- **Commit to git** so your team shares the same baseline
- Treat it as a **living document** — update after architectural changes

---

## 5. Project File Structure

```
your_project/
├── CLAUDE.md                    ← Project memory (THE most important file)
├── .claude/
│   ├── settings.json            ← Shared team settings (committed)
│   ├── settings.local.json      ← Personal overrides (gitignored)
│   ├── skills/                  ← Auto-invoked knowledge packs
│   │   ├── code-review/
│   │   │   └── SKILL.md
│   │   └── testing/
│   │       └── SKILL.md
│   ├── commands/                ← Custom slash commands (/deploy, etc.)
│   │   └── deploy.md
│   ├── agents/                  ← Specialized subagents
│   │   └── security-reviewer.md
│   └── .mcp.json                ← MCP server configuration
├── src/
└── .gitignore                   ← Add .claude/settings.local.json here
```

### User-level (applies across all projects)

```
~/.claude/
├── CLAUDE.md                    ← Personal global preferences
├── settings.json                ← Personal global settings
├── skills/                      ← Skills available everywhere
└── commands/                    ← Personal slash commands
```

---

## 6. Adding Skills (The Superpower)

**Skills** are markdown guides that Claude **auto-invokes** based on your natural-language requests. Unlike slash commands (which you trigger), skills activate automatically when context matches.

| Type | Location |
|------|----------|
| Project skill | `.claude/skills/<name>/SKILL.md` |
| Personal skill | `~/.claude/skills/<name>/SKILL.md` |

> 🎯 The `description` field is **critical** — Claude uses it to decide when to invoke the skill. Be specific about triggers.

### Example SKILL.md

```markdown
---
name: testing-patterns
description: Use when writing or reviewing Jest tests. Covers AAA pattern, factory mocks, and test naming conventions.
allowed-tools: Read, Grep, Glob, Bash(npm test:*)
---

# Testing Patterns

## Structure
- Use `describe` + `it` blocks
- Follow Arrange / Act / Assert (AAA)
- One assertion per test when possible

## Mocking
- Use factory functions over inline mocks
- Place factories in `__tests__/factories/`
```

### Skills vs. Slash Commands vs. Subagents

| | When to use |
|---|---|
| **Slash command** | You want explicit control. You type `/command` and it runs. |
| **Skill** | Expertise should apply automatically based on context. |
| **Subagent** | The task needs an isolated context window (heavy research, parallel work). |

---

## 7. Skill Ideas Worth Building

- **code-review** — apply your team's review checklist
- **testing-patterns** — enforce test conventions
- **commit-messages** — generate conventional commits
- **docker-deploy** — standardize container builds
- **codebase-visualizer** — generate architecture diagrams
- **api-design** — REST/GraphQL conventions
- **security-review** — OWASP checks on changed code
- **db-migration** — safe migration patterns

---

## 8. Setting Up Hooks

**Hooks** = deterministic shell commands Claude Code runs automatically at lifecycle events. Useful for safety gates, formatting, and audit logging.

### Hook Events

| Event | Fires when |
|-------|------------|
| `PreToolUse` | Before Claude uses a tool (e.g., before running Bash) |
| `PostToolUse` | After a tool completes |
| `Notification` | Claude needs your attention |
| `Stop` | Claude finishes responding |
| `UserPromptSubmit` | You submit a prompt |

### Example: Block dangerous bash commands

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "scripts/security-check.sh",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

### Exit Codes

| Code | Meaning |
|------|---------|
| `0` | ✅ Allow the action |
| `2` | ❌ Block the action (Claude sees stderr as feedback) |
| Other | ⚠️ Non-blocking error |

> ⚠️ Hooks run with **your** credentials and shell access. Audit any hook before adding it.

---

## 9. Permissions & Safety

Configure what Claude can do without asking. Three buckets: `allow` (auto-approved), `ask` (prompts you), `deny` (always blocked).

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(git status)",
      "Bash(git diff)",
      "Read(./src/**)",
      "Edit(./src/**)"
    ],
    "ask": [
      "Bash(git push:*)",
      "Bash(npm install *)"
    ],
    "deny": [
      "Read(./.env*)",
      "Read(./secrets/**)",
      "Read(./**/*.key)",
      "Bash(rm -rf:*)",
      "Bash(sudo:*)",
      "Bash(curl:*)"
    ]
  }
}
```

### Permission Modes (`Shift+Tab` to cycle)

| Mode | Behavior |
|------|----------|
| **Default** | Asks before destructive actions |
| **Auto-accept** | Approves edits automatically ("yolo mode") |
| **Plan mode** | Read-only — Claude proposes a plan but doesn't act |

---

## 10. The 4-Layer Architecture

| Layer | What it does |
|-------|--------------|
| **L1 — CLAUDE.md** | Persistent context, conventions, and rules |
| **L2 — Skills** | Auto-invoked knowledge packs Claude pulls in by context |
| **L3 — Hooks** | Deterministic safety gates and automation |
| **L4 — Subagents** | Specialized agents with their own isolated context windows |

These compose: CLAUDE.md sets the baseline, skills add expertise, hooks enforce rules, and subagents tackle isolated subtasks.

---

## 11. Daily Workflow Pattern

A repeatable loop that gets the most out of Claude Code:

1. **Start in your project** — `cd project && claude`
2. **Enter Plan Mode** — `Shift+Tab+Tab` (read-only exploration)
3. **Describe intent clearly** — what you want, why, and any constraints
4. **Review the plan** — push back if anything's off
5. **Switch to Auto-Accept** — `Shift+Tab` once Claude understands the task
6. **Let Claude work** — review diffs as they appear
7. **Compact when context grows** — `/compact` summarizes and frees space
8. **Rewind if needed** — `Esc Esc` opens the rewind menu to undo
9. **Commit frequently** — small, reviewable chunks
10. **Start a fresh session per feature** — keeps context focused

### The "Explore → Plan → Implement → Commit" Pattern

Don't jump to "build me a feature." Let Claude **explore** the codebase first, then **propose a plan**, then **implement**, then **commit**. This is Anthropic's recommended workflow.

---

## 12. Quick Reference

### Essential Slash Commands

| Command | Action |
|---------|--------|
| `/init` | Generate CLAUDE.md from your codebase |
| `/memory` | Edit memory files in your editor |
| `/compact` | Compress conversation context to free space |
| `/clear` | Start a fresh conversation |
| `/help` | List all available commands (built-in + custom + MCP) |
| `/doctor` | Diagnose installation issues |
| `/status` | Check current session status |
| `/config` | Open settings |
| `/bug` | Report a bug to Anthropic |
| `/cost` | Show token usage and cost for current session |
| `/model` | Switch model (Sonnet, Opus, Haiku) |
| `/review` | Code review for current changes |

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Shift+Tab` | Cycle permission modes (toggle auto-accept) |
| `Shift+Tab+Tab` | Enter Plan Mode |
| `Esc` | Interrupt Claude |
| `Esc Esc` | Open rewind menu (undo changes) |
| `Ctrl+R` | Show full output / context |
| `Ctrl+V` | Paste image (e.g., a screenshot of an error) |
| `!` (prefix) | Run a shell command directly |
| `@` (prefix) | Mention a file or folder |
| `\` + Enter | Insert a line break in prompts |

### CLI Flags

| Flag | Purpose |
|------|---------|
| `claude` | Interactive mode |
| `claude "fix the bug"` | Start with an initial prompt |
| `claude -p "analyze this"` | One-shot query, no session |
| `claude -c` | Continue last session |
| `claude --resume` | Pick a session to resume |
| `claude update` | Manually update to latest version |

---

## Bonus: Pro Tips

- **Use `@filename` liberally** in prompts to give Claude exact context instead of letting it grep around.
- **Screenshot errors and paste with `Ctrl+V`** — Claude reads images well and saves you the typing.
- **Pair Plan Mode with hard problems** — let Claude think before it touches anything.
- **One feature per session** — start fresh between unrelated tasks. Context bleed makes Claude slower and dumber.
- **Treat Claude's output like a PR from a junior dev** — review it, don't merge blindly.
- **MCP servers extend Claude's reach** — connect to GitHub, databases, browsers, your design tool. Configure in `.mcp.json`.

---

*References: [Claude Code Docs](https://code.claude.com/docs/en/overview) • [Setup Guide](https://code.claude.com/docs/en/setup) • [GitHub](https://github.com/anthropics/claude-code)*
