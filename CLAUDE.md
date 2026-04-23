# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a reference/learning repository documenting Claude Code setup, usage, and conventions. It contains no application source code — the content is documentation and notes about working with Claude Code.

## Claude Code Setup (from README)

Install Claude Code (requires WSL on Windows, not native Windows):
```bash
npm install -g @anthropic-ai/claude-code
```

Launch from terminal:
```bash
claude
```

Login options:
- `/login` → Option 1 for Claude Pro/Max/Team/Enterprise subscription
- `/login` → Option 2 for Anthropic Console API key billing

Enable shift+enter for newlines in the CLI:
```bash
/terminal-setup
```

## Key Claude Code Commands

- `/init` — generate or update CLAUDE.md with codebase documentation
- `/login` — authenticate with Claude
- `/terminal-setup` — configure terminal keybindings

## CLAUDE.md Purpose

CLAUDE.md acts as a shared AI context contract — documentation, conventions, and behavioral rules that guide Claude Code toward consistent outputs. Treat it as living documentation and keep it updated.
