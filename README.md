# Claude Code Guide

A complete reference for installing, configuring, and using Claude Code effectively.

---

## Table of Contents

1. [Setup](#setup)
2. [IDE Integration](#ide-integration)
3. [Slash Commands](#slash-commands)
4. [Memory (CLAUDE.md)](#memory-claudemd)
5. [Auto Memory](#auto-memory)
6. [Context Management](#context-management)
7. [Permissions](#permissions)
8. [Hooks](#hooks)
9. [MCP Servers](#mcp-servers)
10. [Working Effectively](#working-effectively)
11. [Reference](#reference)

---

## Setup

### 1. Install Claude Code

Install Claude Code using Node.js.

```bash
npm install -g @anthropic-ai/claude-code
```

### 2. Launch

```bash
claude
```

### 3. Login

```bash
/login
```

| Option | Use when |
|--------|----------|
| Option 1: Claude account (subscription) | You have a Pro, Max, Team, or Enterprise subscription |
| Option 2: Anthropic Console (API key) | You use API usage billing via the Anthropic Console |

### 4. Terminal Setup

Enable `Shift+Enter` for multi-line input in the CLI:

```bash
/terminal-setup
```

---

## IDE Integration

### VS Code

1. Open VS Code.
2. Go to **Extensions** (`Ctrl+Shift+X`) and search for **Claude Code**.
3. Install the **Claude Code** extension.
4. Open an integrated terminal (bash), then run `claude` — or click the Claude Code icon in the sidebar.

### JetBrains IDEs

1. Open **Settings → Plugins → Marketplace**.
2. Search for **Claude Code** and install it.
3. Restart the IDE. Claude Code appears as a tool window.

---

## Slash Commands

These are typed directly in the Claude Code CLI prompt.

| Command | Description |
|---------|-------------|
| `/help` | List all available slash commands |
| `/login` | Authenticate with your Claude account or API key |
| `/logout` | Sign out of the current session |
| `/init` | Generate or update `CLAUDE.md` with codebase documentation |
| `/clear` | Clear the conversation context (keeps memory intact) |
| `/context` | Show a breakdown of current token usage |
| `/compact` | Manually compact the conversation to free context space |
| `/terminal-setup` | Configure terminal keybindings (e.g. Shift+Enter) |
| `/config` | Open interactive configuration (theme, model, etc.) |
| `/memory` | Open the project memory file (`CLAUDE.md`) in your editor |
| `/status` | Show current model, auth status, and billing plan |
| `/cost` | Show token usage and estimated cost for this session |
| `/review` | Run a code review on the current branch changes |
| `/mcp` | List connected MCP servers and their tools |
| `/fast` | Toggle Fast mode (Opus 4 with faster output) |
| `/bug` | Report a Claude Code bug to Anthropic |
| `!<command>` | Run a shell command directly and include output in context |

### Running Shell Commands

Prefix any shell command with `!` to run it in the session and have its output available to Claude:

```
! git log --oneline -10
! npm test
! cat package.json
```

---

## Memory (CLAUDE.md)

`CLAUDE.md` is a shared AI context contract — project documentation, conventions, and behavioral rules that guide Claude Code toward consistent outputs across every session.

### Initialize

```bash
/init
```

This scans the codebase and generates a `CLAUDE.md` with detected conventions, file structure, and key patterns.

### What to put in CLAUDE.md

- Project overview and architecture
- Coding conventions (naming, formatting, patterns to follow or avoid)
- Commands for running, testing, and building the project
- Anything Claude should always know about this repo

### Best practices

- Treat it as living documentation — update it when conventions change.
- Manual edits are fine; keep them accurate since Claude trusts this file.
- Multiple `CLAUDE.md` files are supported: one at the repo root, and optionally one per subdirectory for more focused context.
- The file is version-controlled — changes are visible in git history.

### Example CLAUDE.md

```markdown
# My Project

## Tech Stack
- Backend: Node.js + Express
- Database: PostgreSQL (never use ORM, write raw SQL)
- Tests: Jest — always run `npm test` before committing

## Conventions
- snake_case for database columns, camelCase for JS variables
- Never add comments unless the WHY is non-obvious
- Prefer editing existing files over creating new ones
```

---

## Auto Memory

Claude Code has a file-based memory system separate from `CLAUDE.md`. It stores facts about you, your preferences, and project state across conversations.

Memory lives in `~/.claude/projects/<project>/memory/` and is indexed in `MEMORY.md`.

### Memory types

| Type | What it stores |
|------|----------------|
| `user` | Your role, goals, expertise level |
| `feedback` | How you want Claude to behave — corrections and confirmations |
| `project` | Ongoing work, decisions, deadlines |
| `reference` | Where to find things (Linear boards, Grafana dashboards, Slack channels) |

### Asking Claude to remember something

Just tell Claude in plain language:

```
Remember that we always use integration tests against a real database, never mocks.
Remember my name is Laksiri and I'm a backend engineer.
```

Claude will save the relevant fact automatically.

### Asking Claude to forget something

```
Forget that I prefer dark mode.
```

---

## Context Management

Claude Code maintains a token window (up to 200k tokens) that holds system prompts, tool definitions, memory files, skills, and the full conversation history.

### Check usage

```bash
/context
```

Output shows usage by category:

```
System prompt:   6.5k tokens  (3.3%)
System tools:   11.1k tokens  (5.6%)
Memory files:      289 tokens  (0.1%)
Messages:            8 tokens  (0.0%)
Total:          18.8k / 200k  (9%)
```

### Clear context

```bash
/clear
```

- Wipes conversation history.
- Memory files (`CLAUDE.md`, auto memory) are preserved.
- Use this when switching to a new task or when responses feel slower.

### Manual compact

```bash
/compact
```

Summarizes and compresses prior conversation turns without fully clearing them. Useful mid-task when context is growing but you don't want to lose recent history.

### Auto-compact

When the context approaches its limit, Claude Code automatically compresses older messages. This is seamless — the session continues — but running `/clear` at natural task boundaries keeps things faster and more focused.

---

## Permissions

Claude Code asks for permission before running tools that could be risky (writing files, running shell commands, etc.). You can configure allowed and denied patterns to reduce prompts.

### Permission modes

| Mode | Behavior |
|------|----------|
| Default | Prompts for potentially risky actions |
| `--dangerously-skip-permissions` | Skip all prompts (use only in trusted, isolated environments) |

### Configuring permissions in settings

Permissions live in `.claude/settings.json` (project-level) or `~/.claude/settings.json` (global).

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(git diff*)",
      "Read(**)"
    ],
    "deny": [
      "Bash(rm -rf *)"
    ]
  }
}
```

- `allow` — patterns that are always permitted without prompting.
- `deny` — patterns that are always blocked.
- Patterns support glob syntax and tool-specific prefixes (`Bash(...)`, `Read(...)`, `Edit(...)`).

### Reducing prompts

Run the `/fewer-permission-prompts` skill to scan recent transcripts and automatically add common safe commands to your allowlist:

```
/fewer-permission-prompts
```

---

## Hooks

Hooks are shell commands that execute automatically in response to Claude Code events. They let you enforce policies, run formatters, log activity, or notify yourself — without Claude having to remember to do it.

Hooks are configured in `.claude/settings.json` under the `hooks` key.

### Hook events

| Event | Fires when |
|-------|-----------|
| `PreToolUse` | Before Claude runs any tool |
| `PostToolUse` | After a tool completes |
| `Stop` | When Claude finishes a response |
| `Notification` | When Claude sends a notification |

### Example: run linter after every file edit

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "npm run lint --fix"
          }
        ]
      }
    ]
  }
}
```

### Example: notify when Claude stops

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "notify-send 'Claude Code' 'Task complete'"
          }
        ]
      }
    ]
  }
}
```

Use the `/update-config` skill to configure hooks interactively:

```
/update-config
```

---

## MCP Servers

MCP (Model Context Protocol) extends Claude Code with external tools — databases, APIs, file systems, CI systems, and more.

### List connected servers

```bash
/mcp
```

### Add an MCP server

Edit `.claude/settings.json`:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/directory"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "your-token-here"
      }
    }
  }
}
```

### Common MCP servers

| Server | What it provides |
|--------|-----------------|
| `@modelcontextprotocol/server-filesystem` | Read/write access to a local directory |
| `@modelcontextprotocol/server-github` | GitHub issues, PRs, repos |
| `@modelcontextprotocol/server-postgres` | Query a PostgreSQL database |
| `@modelcontextprotocol/server-slack` | Read Slack messages, post to channels |
| `@modelcontextprotocol/server-brave-search` | Web search via Brave |

---

## Working Effectively

### Give Claude clear, specific tasks

Instead of: _"Fix the bug"_
Try: _"The login endpoint at `src/auth/login.js:42` returns 500 when the email contains a `+` character. Fix it."_

### Use `!` for shell output in context

```
! git diff HEAD~1
! npm test 2>&1 | tail -30
```

Claude can reason about command output when it's in the conversation.

### Reference files by path and line number

```
Look at src/api/users.js:88 — why does the query return duplicates?
```

### Start sessions with a plan

For large tasks, ask Claude to outline its approach before it starts writing code. Review and redirect before it commits to a direction.

### Use /clear between unrelated tasks

Context from a previous task can confuse Claude on a new one. Clear it at natural boundaries.

### Keep CLAUDE.md up to date

When a convention changes or Claude makes a repeated mistake, update `CLAUDE.md` so the correction persists across all future sessions.

### Models available

| Model | ID | Best for |
|-------|-----|----------|
| Claude Sonnet 4.6 | `claude-sonnet-4-6` | Default — balanced speed and capability |
| Claude Opus 4.7 | `claude-opus-4-7` | Most capable, complex reasoning tasks |
| Claude Haiku 4.5 | `claude-haiku-4-5-20251001` | Fastest, simple tasks |

Switch models with `/config` or pass `--model` on launch:

```bash
claude --model claude-opus-4-7
```

---

## Reference

1. [Claude Code Docs — Setup](https://docs.anthropic.com/en/docs/claude-code/setup)
2. [Claude Code Docs — Overview](https://docs.anthropic.com/en/docs/claude-code/overview)
3. [Claude Code — YouTube Tutorial (Playlist)](https://www.youtube.com/watch?v=SUysp3sJHbA&list=PL4cUxeGkcC9g4YJeBqChhFJwKQ9TRiivY)
4. [Mosh — Claude Code Tutorial](https://www.youtube.com/watch?v=IuyVVtr1uhY)
5. [Tech with Tim — Claude Code Tutorial](https://www.youtube.com/watch?v=qYqIhX9hTQk)
6. [MCP Servers — Official List](https://github.com/modelcontextprotocol/servers)

## Additional References
1. [Install/setup claude code](https://docs.anthropic.com/en/docs/claude-code/setup)
2. [Claude code - youtube tutorial](https://www.youtube.com/watch?v=SUysp3sJHbA&list=PL4cUxeGkcC9g4YJeBqChhFJwKQ9TRiivY)
3. [Mosh - Claude Code Tutorial](https://www.youtube.com/watch?v=IuyVVtr1uhY)
4. [Tech with Tim - Claude Code Tutorial](https://www.youtube.com/watch?v=qYqIhX9hTQk)