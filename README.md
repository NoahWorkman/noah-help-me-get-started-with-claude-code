# Noah, Help Me Get Started with Claude Code

A practical guide to Claude Code from someone who uses it to build everything: websites, MCP servers, robot dogs, CRMs, AI tools, and more. This is how I actually work, not the marketing version.

> **Not using Claude Code (the CLI)?** See [`PASTE-THIS-INTO-YOUR-AI.md`](PASTE-THIS-INTO-YOUR-AI.md) for a version of this guide you can drop into Claude Desktop, ChatGPT, Grok, or any chat-based AI.

---

## Table of Contents

- [What Is Claude Code](#what-is-claude-code)
- [Install It](#install-it)
- [First 10 Minutes](#first-10-minutes)
- [Working with GitHub Repos](#working-with-github-repos)
- [Models and Token Usage](#models-and-token-usage)
- [The Chrome Flag (Browser Automation)](#the-chrome-flag-browser-automation)
- [CLAUDE.md (Project Memory)](#claudemd-project-memory)
- [Custom Slash Commands (Skills)](#custom-slash-commands-skills)
- [MCP Servers (External Tool Connections)](#mcp-servers-external-tool-connections)
- [Auto Memory (Cross-Session Learning)](#auto-memory-cross-session-learning)
- [Subagents (Parallel Workers)](#subagents-parallel-workers)
- [My Actual Setup](#my-actual-setup)
- [Tips I Learned the Hard Way](#tips-i-learned-the-hard-way)
- [All the Official Links](#all-the-official-links)

---

## What Is Claude Code

Claude Code is an agentic coding tool that runs in your terminal. It's not an autocomplete or a chatbot bolted onto an IDE. It can:

- Read, write, and edit files across your entire codebase
- Run shell commands (git, npm, docker, whatever)
- Search and understand large codebases
- Create commits, branches, and pull requests
- Control Chrome for browser automation and testing
- Connect to external services via MCP servers
- Plan complex changes before executing them
- Remember context across sessions

It's Claude (the LLM) with full access to your local machine, scoped by permissions you control.

**Official docs:** [code.claude.com/docs](https://code.claude.com/docs/en/)
**GitHub:** [github.com/anthropics/claude-code](https://github.com/anthropics/claude-code)

---

## Install It

### Native Install (Recommended)

```bash
# macOS / Linux / WSL
curl -fsSL https://claude.ai/install.sh | bash

# Homebrew (macOS alternative)
brew install --cask claude-code
```

### Verify It Works

```bash
claude --version
```

### First Launch

```bash
claude
```

It will prompt you to authenticate. Log in with your Anthropic account or enter your API key.

**Setup docs:** [code.claude.com/docs/en/setup](https://code.claude.com/docs/en/setup)

---

## First 10 Minutes

Open a terminal, `cd` into any project, and run `claude`. That's it. You're in an interactive session.

### Try These First

```
# Ask about the codebase
> what does this project do?
> explain the architecture
> find all the API endpoints

# Make changes
> add a hello world route to the express server
> fix the TypeScript errors in src/utils.ts
> write tests for the auth module

# Git operations (it has full git access)
> what files have I changed?
> create a commit with a good message
> create a PR for this branch
```

### Key Commands Inside a Session

| Command | What It Does |
|---------|-------------|
| `/help` | See all available commands |
| `/cost` | Check how many tokens you've used this session |
| `/model` | Switch between models (opus, sonnet, haiku) |
| `/clear` | Clear context and start fresh (saves tokens) |
| `/memory` | View/edit your CLAUDE.md files |
| `/compact` | Compress conversation to save context window |
| `/agents` | View, create, and manage subagents |
| `Shift+Tab` | Toggle plan mode (think before coding) |
| `Ctrl+B` | Background a running subagent task |
| `Esc` | Cancel current generation |

### CLI Flags

```bash
claude                        # Start interactive session
claude "your task here"       # One-shot task
claude -p "query"             # Print mode (non-interactive, good for pipes)
claude -c                     # Continue your most recent session
claude -r                     # Resume a specific session (shows picker)
claude --model opus           # Start with a specific model
claude --chrome               # Enable browser automation
```

**Quickstart guide:** [code.claude.com/docs/en/quickstart](https://code.claude.com/docs/en/quickstart)

---

## Working with GitHub Repos

Claude Code has deep GitHub integration through the `gh` CLI. Here's how I use it.

### The Basics

```bash
# Clone a repo and start working
gh repo clone your-username/your-repo
cd your-repo
claude
```

Claude automatically detects it's a git repo and understands the full history, branches, and remote.

### My Git Workflow with Claude

```
# Start a feature
> create a branch called feature/new-auth

# Do the work
> implement JWT authentication with refresh tokens

# Review what changed
> show me a summary of all changes

# Commit and push
> commit these changes with a descriptive message
> push this branch and create a PR
```

### PR Workflows

```bash
# Review a PR
claude --from-pr 123

# Create a PR from current branch
> create a pull request with a good description

# Work on someone else's PR
gh pr checkout 456
claude
> what does this PR change?
```

### My Repo Organization

I keep repos organized by purpose. Here's what I have publicly that you can reference:

| Repo | What It Is |
|------|-----------|
| [claude-skills](https://github.com/NoahWorkman/claude-skills) | Reusable Claude Code slash command library |
| [petoi-bittle-project](https://github.com/NoahWorkman/petoi-bittle-project) | Robot dog: voice commands, Bluetooth, AI vision, custom MCP server |
| [clawdbot](https://github.com/NoahWorkman/clawdbot) | Cross-platform personal AI assistant |
| [resume-retool](https://github.com/NoahWorkman/resume-retool) | AI-powered resume customization tool |
| [comfyui-vrgamedevgirl](https://github.com/NoahWorkman/comfyui-vrgamedevgirl) | Custom ComfyUI nodes for film/video post |
| [videos_test](https://github.com/NoahWorkman/videos_test) | Public video test files |

My private repos include my portfolio site (React 19 + Vite + TanStack Router + Tailwind), a TypeScript CRM dashboard, pitch decks, and various project-specific work. Same Claude Code workflow for all of them.

---

## Models and Token Usage

This is important. Claude Code gives you access to three model tiers and you're paying per token. Understanding this will save you money and help you pick the right tool for the job.

### The Three Models

| Model | Best For | Speed | Input (per 1M tokens) | Output (per 1M tokens) |
|-------|---------|-------|----------------------|----------------------|
| **Opus 4.6** | Complex reasoning, architecture, multi-file refactors | Slowest | $5 | $25 |
| **Sonnet 4.5** | Most daily coding, solid balance of quality/speed | Medium | $3 | $15 |
| **Haiku 4.5** | Quick lookups, simple edits, fast iteration | Fastest | $1 | $5 |

### Switching Models

```bash
# At startup
claude --model sonnet
claude --model opus
claude --model haiku

# During a session
/model sonnet
/model opus

# The power move: opus for planning, sonnet for execution
claude --model opusplan
```

The `opusplan` alias uses Opus when you're in plan mode (Shift+Tab) for deep thinking, then automatically drops to Sonnet when executing. Best of both worlds.

### Checking Your Spend

```
/cost          # Current session cost and token count
```

### How Tokens Actually Work

Every message you send and every response Claude gives uses tokens. Reading files, running commands, searching code, all of it feeds into the context window and burns tokens. A few things to know:

- **Context window**: Claude keeps the full conversation in memory. Longer conversations = more tokens per message because it re-reads everything
- **`/clear`**: Resets the conversation. Use this between unrelated tasks to save tokens
- **`/compact`**: Compresses the conversation without losing context. Good for long sessions
- **File reads cost tokens**: Every file Claude reads goes into the context. Be specific about what you want it to look at when possible
- **Subagents**: Claude can spawn smaller agents for parallel tasks. These use their own token budgets

### Cost Optimization

- Use **Sonnet** as your daily driver. It handles 90% of tasks well
- Switch to **Opus** for architecture decisions, complex debugging, or multi-file refactors
- Use **Haiku** when you're doing rapid iteration on small changes
- Run `/clear` between unrelated tasks
- Be specific in your prompts. "Fix the auth bug in src/auth/login.ts" costs way less than "find and fix all bugs"

### Setting a Budget (Print Mode)

```bash
# Cap spending on a single task
claude -p "refactor the auth module" --max-budget-usd 5.00
```

### Typical Costs

Rough numbers from my usage:
- A focused coding session (1-2 hours): $2-8 depending on model
- Heavy day of building: $15-30
- Quick question or small fix: $0.10-0.50

**Cost docs:** [code.claude.com/docs/en/costs](https://code.claude.com/docs/en/costs)
**Model config:** [code.claude.com/docs/en/model-config](https://code.claude.com/docs/en/model-config)

---

## The Chrome Flag (Browser Automation)

This is one of the features that surprised me. Claude can control your Chrome browser.

### Setup

1. Install the **Claude in Chrome** extension (v1.0.36+) from the Chrome Web Store
2. Launch Claude Code with the flag:

```bash
claude --chrome
```

Or enable it during a session with `/chrome`.

### What It Can Do

- Navigate to URLs, click elements, fill forms
- Read page content and console logs
- Take screenshots and record GIFs of interactions
- Test your web app end-to-end
- Interact with authenticated pages (it uses your real browser session)
- Debug frontend issues by reading console output

### Example Uses

```
# Test your app
> go to localhost:3000, log in with test credentials, and verify the dashboard loads

# Debug a production issue
> open our staging site, check the network tab for failed API calls

# Record a demo
> navigate through the signup flow and record a GIF of the process

# Scrape and analyze
> open this URL and extract all the pricing information into a table
```

### Enable by Default

If you want `--chrome` on every session without typing it:

```
/chrome
# Select "Enabled by default"
```

### Important Notes

- Requires a direct Anthropic plan (Pro, Max, Team, or Enterprise)
- Uses your actual browser, so it has access to your logged-in sessions
- Avoid triggering JavaScript alert/confirm dialogs; they block the extension

**Chrome docs:** [code.claude.com/docs/en/chrome](https://code.claude.com/docs/en/chrome)

---

## CLAUDE.md (Project Memory)

This is the single most impactful thing you can set up. A `CLAUDE.md` file in your project root tells Claude how to work in that specific codebase. It's loaded into context at the start of every session.

### What Goes in It

- Build/test/lint commands
- Architecture overview
- Coding conventions and style preferences
- File organization patterns
- Things Claude should never do in this project
- Links to key files

### Example

See [`examples/CLAUDE.md.example`](examples/CLAUDE.md.example) in this repo for a starter template.

### The Hierarchy

CLAUDE.md files stack. More specific files override more general ones:

| File | Location | Scope |
|------|----------|-------|
| `~/.claude/CLAUDE.md` | Home directory | All your projects (personal preferences) |
| `./CLAUDE.md` | Project root | Shared with team via git |
| `./.claude/CLAUDE.md` | Project .claude dir | Alternative project location |
| `./CLAUDE.local.md` | Project root | Personal, auto-gitignored |

### Creating One

```bash
cd your-project
claude
> /init    # Auto-generates a CLAUDE.md based on your codebase
```

Or create it manually. I find the manual approach gives better results because you know your project better than an auto-scan.

**Memory docs:** [code.claude.com/docs/en/memory](https://code.claude.com/docs/en/memory)

---

## Custom Slash Commands (Skills)

This is where it gets powerful. You can create custom commands that Claude executes with specialized instructions.

### How It Works

Create a markdown file. The filename becomes the command. The contents become Claude's instructions when you invoke it.

### Where They Live

```
~/.claude/skills/<skill-name>/SKILL.md    # Personal, all projects
.claude/skills/<skill-name>/SKILL.md      # Project-specific, committed to git

# Legacy (still works):
~/.claude/commands/<name>.md              # Personal commands
.claude/commands/<name>.md                # Project commands
```

### Example: A Simple Commit Skill

Create `~/.claude/skills/commit/SKILL.md`:

```markdown
---
name: commit
description: Validate, commit, and push changes
---

1. Run the project's lint/format/test commands
2. Check git status and diff
3. Create a descriptive commit message
4. Commit and push
```

Now `/commit` runs your custom workflow every time.

### Example: A Code Review Skill

See [`examples/commands/review.md`](examples/commands/review.md) for a working example.

### My Setup

I have **34 custom slash commands**. Some highlights:

| Command | What It Does |
|---------|-------------|
| `/commit-push` | Validate, commit, and push |
| `/prd` | Generate a full product requirements doc |
| `/contract-review` | Analyze a contract for red flags |
| `/git-sanitize` | Scan for secrets before pushing |
| `/retro` | Run a conversation retrospective |
| `/record-gifs` | Record browser interaction GIFs |
| `/contrast-check` | Audit CSS color contrast for accessibility |

You can browse more examples in my [claude-skills](https://github.com/NoahWorkman/claude-skills) repo.

**Skills docs:** [code.claude.com/docs/en/skills](https://code.claude.com/docs/en/skills)

---

## MCP Servers (External Tool Connections)

MCP (Model Context Protocol) lets Claude connect to external services and tools. Think of it as giving Claude hands that reach outside your filesystem.

### What MCP Servers Do

Instead of copy-pasting data between Claude and other tools, MCP servers let Claude directly:
- Query databases
- Call APIs
- Control hardware (I have one for a robot dog)
- Read from project management tools
- Interact with cloud services

### Installing an MCP Server

```bash
# Remote HTTP server
claude mcp add --transport http --scope user <name> <url>

# Remote SSE server
claude mcp add --transport sse --scope user <name> <url>

# Local npm package
claude mcp add --transport stdio --scope user <name> -- npx -y <package>
```

Key: `--scope user` makes it available across all projects. Without it, it's project-local.

### Managing MCPs

```bash
claude mcp list              # See all connected MCPs and status
claude mcp remove <name>     # Remove one
/mcp                         # Check status during a session
```

### My Connected MCPs

| Server | Type | What It Does |
|--------|------|-------------|
| **Bittle** | Local (Python) | Controls my Petoi robot dog via Bluetooth |
| **cQuenced** | Remote (HTTP) | Admin access to my product's backend |
| **Lokka-Microsoft** | Local (npm) | Microsoft Graph (calendar, email, files) |
| **Linear** | Remote (SSE) | Project management and issue tracking |
| **Claude in Chrome** | Extension | Browser automation (see Chrome section) |

### Building Your Own MCP

If you have an API or service you want Claude to access, you can build a custom MCP server. The [petoi-bittle-project](https://github.com/NoahWorkman/petoi-bittle-project) repo has a working example of a custom MCP server in Python.

**MCP docs:** [code.claude.com/docs/en/mcp](https://code.claude.com/docs/en/mcp)

---

## Auto Memory (Cross-Session Learning)

Claude Code remembers things across sessions. Not just what you tell it, but patterns it discovers while working.

### How It Works

Claude maintains a memory directory per project:

```
~/.claude/projects/<project-hash>/memory/
  MEMORY.md          # Main index (first 200 lines loaded every session)
  debugging.md       # Topic-specific notes
  patterns.md        # Patterns discovered in your codebase
```

### What Gets Remembered

- Debugging insights ("this error means X, fix it by doing Y")
- Architecture patterns it discovered
- Your preferences it learned from corrections
- Facts about the project
- Lessons from mistakes

### Managing Memory

```
/memory              # View and edit memory files during a session
```

You can also directly edit the files in `~/.claude/projects/`.

### Why This Matters

Without memory, every session starts cold. With memory, Claude gets better at your specific project over time. It won't repeat mistakes, it'll follow your conventions, and it'll know the gotchas.

**Memory docs:** [code.claude.com/docs/en/memory](https://code.claude.com/docs/en/memory)

---

## Subagents (Parallel Workers)

Subagents are specialized AI agents that Claude spawns to handle specific tasks. Each runs in its own context window with its own system prompt, tool access, and permissions. This is how Claude handles complex work without bloating your main conversation.

### Built-in Subagents

Claude Code ships with these out of the box:

| Agent | Model | What It Does |
|-------|-------|-------------|
| **Explore** | Haiku (fast) | Read-only codebase search and analysis |
| **Plan** | Inherits | Research for plan mode (reads code, no writes) |
| **General-purpose** | Inherits | Complex multi-step tasks with full tool access |

You don't invoke these directly. Claude delegates to them automatically based on the task.

### Creating Custom Subagents

The `/agents` command gives you an interactive interface to create, manage, and delete subagents.

```
/agents
# Select "Create new agent" > "User-level"
# Describe what the subagent should do
# Choose tools and model
# Done
```

Or create them manually as markdown files:

```
~/.claude/agents/           # Personal, all projects
.claude/agents/             # Project-specific, committed to git
```

### Example: A Custom Subagent

Create `~/.claude/agents/code-reviewer.md`:

```markdown
---
name: code-reviewer
description: Reviews code for quality and best practices. Use proactively after code changes.
tools: Read, Glob, Grep, Bash
model: sonnet
---

You are a senior code reviewer. When invoked:
1. Run git diff to see recent changes
2. Review for bugs, security issues, style problems
3. Format feedback by priority: critical, warnings, suggestions
```

### Subagent Features

- **Tool restrictions**: Limit what tools a subagent can use (read-only, no file edits, etc.)
- **Model selection**: Run cheap subagents on Haiku, expensive ones on Opus
- **Persistent memory**: Give subagents their own memory that builds up across sessions
- **Hooks**: Run scripts before/after subagent tool calls (e.g., validate SQL is read-only)
- **Background execution**: Press `Ctrl+B` to background a running subagent and keep working
- **Resumable**: Resume a completed subagent to continue where it left off

### When to Use Subagents vs. Main Conversation

**Use the main conversation** for interactive work, quick changes, and tasks needing back-and-forth.

**Use subagents** when:
- The task produces verbose output (test suites, log analysis)
- You want tool restrictions (read-only reviewer)
- Work is self-contained and returns a summary
- You want to run things in parallel

### CLI Flag for One-Off Subagents

```bash
claude --agents '{
  "reviewer": {
    "description": "Reviews code for quality",
    "prompt": "You are a code reviewer. Focus on security and best practices.",
    "tools": ["Read", "Grep", "Glob"],
    "model": "sonnet"
  }
}'
```

**Subagent docs:** [code.claude.com/docs/en/sub-agents](https://code.claude.com/docs/en/sub-agents)

---

## My Actual Setup

For reference, here's what my daily workflow looks like:

### Morning

```bash
claude --chrome --model opusplan
# Review priorities, check on open PRs, plan the day
```

### Building (most of the day)

```bash
cd ~/Projects/my-project
claude
> /model sonnet          # Daily driver for coding
# ... build features, fix bugs, iterate ...
> /commit-push           # Validate and ship
```

### Stack I Build With

- React 19 + Vite + TanStack Router
- Tailwind CSS 4.1 + shadcn/ui
- TypeScript everywhere
- Python for MCP servers and AI tools
- Cloudflare Pages for deployment

Claude Code handles all of it. Same tool, different languages, different domains.

---

## Tips I Learned the Hard Way

1. **Be specific.** "Fix the bug" costs 10x more tokens than "fix the null pointer in src/auth/login.ts line 42"

2. **Use `/clear` between tasks.** Context builds up. If you're done with one task and starting another, clear it

3. **Plan mode for big changes.** Hit `Shift+Tab` before asking for a major refactor. Claude will think through the approach before writing code

4. **Don't fight the permissions.** Claude asks before doing risky things (deleting files, pushing code). This is good. Let it ask

5. **CLAUDE.md is worth the investment.** 30 minutes writing a good CLAUDE.md saves hours of re-explaining conventions

6. **Skills compound.** Every slash command you build saves time on every future session. Start with the things you do repeatedly

7. **Read the diff.** Claude shows you what it changed. Actually read it. It's right 95% of the time, but that 5% matters

8. **Use subagents.** For complex tasks, Claude spawns sub-agents that work in parallel with their own context. Run `/agents` to create custom ones for your workflow. Press `Ctrl+B` to background a running task

9. **Git is your safety net.** Claude works on real files. Commit before asking it to do something risky. You can always revert

10. **The `--print` flag is underrated.** Use it for scripting: `claude -p "generate a migration for adding a users table" > migration.sql`

---

## All the Official Links

### Getting Started
- [Installation & Setup](https://code.claude.com/docs/en/setup)
- [Quickstart Guide](https://code.claude.com/docs/en/quickstart)
- [Overview](https://code.claude.com/docs/en/overview)
- [How Claude Code Works](https://code.claude.com/docs/en/how-claude-code-works)
- [Common Workflows](https://code.claude.com/docs/en/common-workflows)
- [Best Practices](https://code.claude.com/docs/en/best-practices)

### Configuration
- [CLAUDE.md & Memory](https://code.claude.com/docs/en/memory)
- [Skills (Custom Commands)](https://code.claude.com/docs/en/skills)
- [Settings & Permissions](https://code.claude.com/docs/en/settings)
- [Model Configuration](https://code.claude.com/docs/en/model-config)
- [MCP Integration](https://code.claude.com/docs/en/mcp)
- [Hooks & Automation](https://code.claude.com/docs/en/hooks-guide)

### Advanced
- [Chrome Integration](https://code.claude.com/docs/en/chrome)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Headless Mode](https://code.claude.com/docs/en/headless)
- [CLI Reference](https://code.claude.com/docs/en/cli-reference)

### IDE Integrations
- [VS Code Extension](https://code.claude.com/docs/en/vs-code)
- [JetBrains IDEs](https://code.claude.com/docs/en/jetbrains)
- [GitHub Actions](https://code.claude.com/docs/en/github-actions)

### Cost & Monitoring
- [Cost Management](https://code.claude.com/docs/en/costs)
- [Model Pricing](https://platform.claude.com/docs/en/docs/about-claude/models)

### Community
- [Claude Code GitHub](https://github.com/anthropics/claude-code)
- [Noah's Claude Skills Library](https://github.com/NoahWorkman/claude-skills)
- [Awesome Claude Code (Community)](https://github.com/hesreallyhim/awesome-claude-code)

---

## Questions?

Text me. Or honestly, just ask Claude:

```bash
claude
> how do I set up an MCP server?
> help me write a custom slash command
> what's the best model for my use case?
```

It's surprisingly good at explaining itself.
