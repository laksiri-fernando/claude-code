# Claude Code Guide

## Setup Instructions
1. Setup
```bash
# windows does not support. However wsl supports
npm install -g @anthropic-ai/claude-code
```

2. Add claude code to VSCode
- Launch code
- Go to extensions and install the extension "Claude Code to VS Code"


3. Launch claude code
- Launch code
- Open terminal in code (bash)
```bash
claude # follow instructions
```
- Alternatively, click "Claude Code" extension icon

4. Login
- In claude cli
```bash
/login
# If you have a subscription, select Option 1: Claude account with subscription · Pro, Max, Team, or Enterprise
# If you use the free account with KPI key, use Option 2: Anthropic Console account · API usage billing
```

5. Terminal Setup
```bash
/terminal-setup
# enable shift + enter for new line in claude cli sessions
```

## Memory (CLAUDE.md)

The CLAUDE.md file is essentially a shared “AI context contract” for your project—combining documentation, conventions, and behavioral rules to guide Claude Code toward consistent, high-quality outputs.

```bash
# claude cli

/init
```

- Initialize a new CLAUDE.md with codebase documentation
- Treat CLAUDE.md as living documentation
- Keep it updated to ensure better, more aligned code generation

## Reference
1. [Install/setup claude code](https://docs.anthropic.com/en/docs/claude-code/setup)
2. [Claude code - youtube tutorial](https://www.youtube.com/watch?v=SUysp3sJHbA&list=PL4cUxeGkcC9g4YJeBqChhFJwKQ9TRiivY)
3. [Mosh - Claude Code Tutorial](https://www.youtube.com/watch?v=IuyVVtr1uhY)
4. [Tech with Tim - Claude Code Tutorial](https://www.youtube.com/watch?v=qYqIhX9hTQk)