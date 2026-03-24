# Agent Workflows

Reusable workflow prompts for AI coding agents. These Markdown files guide agents through structured, phase-based workflows for common software engineering tasks.

## Repository Structure

```
agents.md              ← top-level dispatch (symlinked to project root)
doc/agents/
  planning.md          ← plan features and refactorings
  implementation.md    ← implement code changes
  bugfix.md            ← diagnose and fix bugs
  review.md            ← review code changes
  stack-rails.md       ← Rails-specific extension
  stack-javascript.md  ← JS/TS-specific extension
```

## Setup

```sh
# Clone to a shared location
git clone <repo-url> ~/.agents

# From any project directory
~/.agents/link
```

The `link` script:
1. Symlinks `agents.md` to the project root
2. Symlinks workflow and stack extension files into `doc/agents/`
3. Scaffolds `doc/agents/project.md` if it doesn't exist

## Project-Local Files

Created per-project (not symlinked) and should be committed to your repo:

- `doc/agents/project.md` — project overview, tech stack, architecture, dev setup

## How It Works

`agents.md` acts as a router. When an agent reads it, it picks the workflow that matches the current task:

| Task | Workflow |
|------|----------|
| Plan a feature or refactoring | `planning.md` |
| Implement code changes | `implementation.md` |
| Diagnose and fix a bug | `bugfix.md` |
| Review code or a PR | `review.md` |

Stack extensions (`stack-rails.md`, `stack-javascript.md`) are loaded alongside the workflow when present in the project.
