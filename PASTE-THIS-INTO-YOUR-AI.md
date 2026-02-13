# Claude Code Knowledge Base

## Paste me into Claude Desktop, ChatGPT, Grok, Gemini, or any chat-based AI.

Copy this entire document and paste it into your conversation. The AI will use it as a reference to answer your questions about Claude Code accurately.

**You can also use this as:**
- A **custom instruction** or **system prompt** in Claude Desktop or ChatGPT
- A **project knowledge file** in Claude Desktop Projects or GPTs
- A starting message in any AI chat to give it full Claude Code knowledge

Everything below is the reference material.

---

## Instructions for the AI

You are helping a user learn and work with **Claude Code**, Anthropic's agentic CLI coding tool. This document contains accurate, verified information about Claude Code's features, commands, configuration, and best practices as of February 2026. Use it to answer questions. If the user asks about something not covered here, say so and point them to the official docs at https://code.claude.com/docs/en/.

**Important:** Claude Code is a terminal/CLI tool. The features described here (slash commands, MCP servers, subagents, file editing, Chrome automation) require the Claude Code CLI installed on the user's machine. They do not work inside Claude Desktop, ChatGPT, or other chat interfaces. When answering questions, make this distinction clear if the user seems confused.

---

## What Is Claude Code

Claude Code is an agentic coding tool that runs in the terminal. It gives Claude (the LLM) controlled access to the local filesystem, shell commands, git, and external services. It can read/write/edit files, run commands, search codebases, create commits and PRs, control Chrome, and connect to external tools via MCP servers.

Official docs: https://code.claude.com/docs/en/
GitHub: https://github.com/anthropics/claude-code

## Installation

Recommended (native binary):
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

macOS alternative:
```bash
brew install --cask claude-code
```

Note: The old `npm install @anthropic-ai/claude-code` method is deprecated.

After install, run `claude` to authenticate with an Anthropic account or API key.

Official setup guide: https://code.claude.com/docs/en/setup

## Models and Pricing

Three model tiers are available. Users select models at startup or during a session.

| Model | Best For | Input (per 1M tokens) | Output (per 1M tokens) |
|-------|---------|----------------------|----------------------|
| Opus 4.6 | Complex reasoning, architecture, multi-file refactors | $5 | $25 |
| Sonnet 4.5 | Most daily coding, balanced quality/speed | $3 | $15 |
| Haiku 4.5 | Quick lookups, simple edits, fast iteration | $1 | $5 |

**Model selection:**
- At startup: `claude --model sonnet` (or `opus`, `haiku`)
- During session: `/model sonnet`
- Hybrid mode: `claude --model opusplan` (uses Opus for planning, Sonnet for execution)

**Cost tracking:** Use `/cost` during a session to see token usage and spend.

**Cost optimization tips:**
- Use Sonnet as the daily driver (handles 90% of tasks)
- Switch to Opus for complex architecture or debugging
- Use Haiku for rapid iteration on small changes
- Run `/clear` between unrelated tasks to reset context
- Run `/compact` to compress long conversations
- Be specific in prompts ("fix the null pointer in src/auth/login.ts line 42" costs far less than "find and fix all bugs")

Official pricing: https://platform.claude.com/docs/en/docs/about-claude/models
Cost management: https://code.claude.com/docs/en/costs

## Key CLI Flags

```bash
claude                        # Interactive session
claude "task description"     # One-shot task
claude -p "query"             # Print mode (non-interactive, good for pipes/scripts)
claude -c                     # Continue most recent session
claude -r                     # Resume a specific session (shows picker)
claude --model opus           # Start with specific model
claude --chrome               # Enable browser automation
claude --from-pr 123          # Resume session linked to a PR
claude --max-budget-usd 5.00  # Cap spending (print mode only)
```

Full CLI reference: https://code.claude.com/docs/en/cli-reference

## In-Session Commands

| Command | What It Does |
|---------|-------------|
| `/help` | See all available commands |
| `/cost` | Check token usage and spend |
| `/model` | Switch between models |
| `/clear` | Clear context and start fresh |
| `/memory` | View/edit CLAUDE.md files |
| `/compact` | Compress conversation to save context |
| `/agents` | View, create, and manage subagents |
| `/init` | Auto-generate a CLAUDE.md for current project |
| `/chrome` | Enable/configure browser automation |
| `Shift+Tab` | Toggle plan mode (think before coding) |
| `Ctrl+B` | Background a running task |
| `Esc` | Cancel current generation |

## CLAUDE.md (Project Configuration)

A `CLAUDE.md` file in a project root tells Claude how to work in that codebase. It's loaded into context at session start. Contains build commands, architecture notes, coding conventions, and project-specific rules.

**File hierarchy (all stack, more specific overrides general):**

| File | Location | Scope |
|------|----------|-------|
| `~/.claude/CLAUDE.md` | Home directory | All projects (personal) |
| `./CLAUDE.md` | Project root | Shared with team via git |
| `./.claude/CLAUDE.md` | Project .claude dir | Alternative project location |
| `./CLAUDE.local.md` | Project root | Personal, auto-gitignored |

**Creating one:** Run `/init` in a session, or create manually.

Docs: https://code.claude.com/docs/en/memory

## Custom Slash Commands (Skills)

Custom commands created as markdown files with optional YAML frontmatter.

**File locations:**
```
~/.claude/skills/<skill-name>/SKILL.md    # Personal, all projects
.claude/skills/<skill-name>/SKILL.md      # Project-specific

# Legacy (still works):
~/.claude/commands/<name>.md
.claude/commands/<name>.md
```

**Example skill** (`~/.claude/skills/review/SKILL.md`):
```markdown
---
name: review
description: Review current changes for quality and bugs
---

Review all staged and unstaged changes. Check for bugs, security issues,
style problems, and missing tests. Format by priority: critical, warnings,
suggestions. End with a verdict: ship it, needs fixes, or needs rework.
```

Invoked with `/review` in a session.

Docs: https://code.claude.com/docs/en/skills

## MCP Servers (External Tools)

MCP (Model Context Protocol) connects Claude to external services: databases, APIs, hardware, cloud platforms.

**Install commands:**
```bash
# Remote HTTP server
claude mcp add --transport http --scope user <name> <url>

# Remote SSE server
claude mcp add --transport sse --scope user <name> <url>

# Local npm package
claude mcp add --transport stdio --scope user <name> -- npx -y <package>
```

**Note:** `--scope user` makes it available in all projects. All flags must come before the server name.

**Management:**
```bash
claude mcp list              # See status
claude mcp remove <name>     # Remove
/mcp                         # Check status in session
```

Docs: https://code.claude.com/docs/en/mcp

## Chrome Browser Automation

Claude can control Chrome for testing, debugging, form filling, screenshots, and GIF recording.

**Requirements:**
- Claude in Chrome extension (v1.0.36+)
- Claude Code v2.0.73+
- Direct Anthropic plan (Pro, Max, Team, or Enterprise)

**Enable:** `claude --chrome` at startup, or `/chrome` during a session.

**Capabilities:** Navigate pages, click elements, fill forms, read console logs, take screenshots, record GIFs, interact with authenticated pages.

Docs: https://code.claude.com/docs/en/chrome

## Subagents

Specialized AI agents Claude spawns for specific tasks. Each has its own context window, system prompt, and tool access.

**Built-in subagents:**
- **Explore** (Haiku): Read-only codebase search
- **Plan** (inherits model): Research for plan mode
- **General-purpose** (inherits model): Complex multi-step tasks

**Custom subagents:** Create via `/agents` command or as markdown files in `~/.claude/agents/` (personal) or `.claude/agents/` (project).

**Features:** Tool restrictions, model selection per agent, persistent memory, lifecycle hooks, background execution (Ctrl+B), resumable.

**CLI flag for one-off agents:**
```bash
claude --agents '{"reviewer": {"description": "Reviews code", "prompt": "You are a code reviewer.", "tools": ["Read", "Grep"], "model": "sonnet"}}'
```

Docs: https://code.claude.com/docs/en/sub-agents

## Auto Memory

Claude maintains a memory directory per project that persists across sessions:

```
~/.claude/projects/<project-hash>/memory/
  MEMORY.md          # Main index (first 200 lines loaded every session)
  debugging.md       # Topic-specific notes
```

Stores debugging insights, architecture patterns, user preferences, and lessons learned. Managed with `/memory` or by editing files directly.

Docs: https://code.claude.com/docs/en/memory

## Git and GitHub Integration

Claude Code uses the `gh` CLI for GitHub operations. It can clone repos, create branches, commit, push, create PRs, review PRs, and comment on issues.

**Key workflows:**
- `claude --from-pr 123`: Resume a session linked to a PR
- Natural language: "create a commit", "push and open a PR", "what changed in this branch"

Docs: https://code.claude.com/docs/en/common-workflows

## IDE Integrations

- VS Code: https://code.claude.com/docs/en/vs-code
- JetBrains: https://code.claude.com/docs/en/jetbrains
- GitHub Actions: https://code.claude.com/docs/en/github-actions

## Best Practices

1. **Be specific in prompts.** Targeted requests cost fewer tokens and produce better results
2. **Use `/clear` between tasks.** Prevents context buildup and saves money
3. **Use plan mode (Shift+Tab) for big changes.** Claude thinks through the approach before writing code
4. **Invest in CLAUDE.md.** 30 minutes of project config saves hours of re-explaining
5. **Build skills for repeated workflows.** They compound over time
6. **Read the diffs.** Claude shows changes before applying. Review them
7. **Commit before risky operations.** Git is your safety net
8. **Use subagents for verbose tasks.** Test suites, log analysis, and research stay out of your main context
9. **Use `--print` for scripting.** Pipe Claude into files or other commands
10. **Pick the right model.** Sonnet for daily work, Opus for complex problems, Haiku for quick tasks

## All Official Documentation Links

- Setup: https://code.claude.com/docs/en/setup
- Quickstart: https://code.claude.com/docs/en/quickstart
- Overview: https://code.claude.com/docs/en/overview
- How It Works: https://code.claude.com/docs/en/how-claude-code-works
- Common Workflows: https://code.claude.com/docs/en/common-workflows
- Best Practices: https://code.claude.com/docs/en/best-practices
- Memory & CLAUDE.md: https://code.claude.com/docs/en/memory
- Skills: https://code.claude.com/docs/en/skills
- Settings: https://code.claude.com/docs/en/settings
- Model Config: https://code.claude.com/docs/en/model-config
- MCP: https://code.claude.com/docs/en/mcp
- Hooks: https://code.claude.com/docs/en/hooks-guide
- Chrome: https://code.claude.com/docs/en/chrome
- Subagents: https://code.claude.com/docs/en/sub-agents
- Headless: https://code.claude.com/docs/en/headless
- CLI Reference: https://code.claude.com/docs/en/cli-reference
- Costs: https://code.claude.com/docs/en/costs
- Pricing: https://platform.claude.com/docs/en/docs/about-claude/models
- VS Code: https://code.claude.com/docs/en/vs-code
- JetBrains: https://code.claude.com/docs/en/jetbrains
- GitHub Actions: https://code.claude.com/docs/en/github-actions
