# Agent Workflows

Reusable workflow prompts for AI coding agents. These Markdown files serve as system prompts that guide agents through structured, phase-based workflows for common software engineering tasks.

## Repository Structure

```
agents.md              ← top-level dispatch (symlinked to project root)
doc/agents/
  planning.md          ← plan features and refactorings
  implementation.md    ← implement code changes
  bugfix.md            ← diagnose and fix bugs
  review.md            ← review code changes
  rails.md             ← Rails-specific extension
  javascript.md        ← JS/TS-specific extension
```

This mirrors the target structure in your project. `agents.md` tells the agent which workflow to load based on the task.

## Setup

```sh
# Clone to a shared location
git clone <repo-url> ~/.agents

# From any project directory
~/.agents/link
```

The `link` script:
1. Symlinks `agents.md` to the project root
2. Symlinks workflow files into `doc/agents/`
3. Scaffolds `project.md` and `lessons.md` if they don't exist

## Project-Local Files

These are created per-project (not symlinked) and should be committed to your repo:

- `doc/agents/project.md` — project overview, tech stack, architecture, dev setup
- `doc/agents/lessons.md` — accumulated notes on patterns, gotchas, and fixes

## How It Works

`agents.md` acts as a router. When an agent reads it, it picks the single workflow that matches the current task:

| Task | Workflow |
|------|----------|
| Plan a feature or refactoring | `planning.md` |
| Implement code changes | `implementation.md` |
| Diagnose and fix a bug | `bugfix.md` |
| Review code or a PR | `review.md` |

Language extensions (`rails.md`, `javascript.md`) are loaded alongside the workflow when the project uses that stack.
