# Agent Workflows

Reusable workflow configurations for AI coding agents. These Markdown files serve as system prompts that guide agents through structured, phase-based workflows for common software engineering tasks.

## Workflows

| File | Purpose |
|------|---------|
| `planning.md` | Plan new features or complex refactorings with phased, reviewable designs |
| `implementation.md` | Implement code changes following a structured build-verify-commit cycle |
| `bugfix.md` | Diagnose and fix bugs through evidence-based root cause analysis |
| `review.md` | Perform code reviews across correctness, performance, security, and architecture |

## Language-Specific Extensions

These are included alongside a workflow when working in a specific stack. They add framework-specific commands, testing rules, and review checklists — core workflow rules still apply.

| File | Purpose |
|------|---------|
| `rails.md` | Rails conventions: test commands, migration practices, N+1 detection, CI debugging |
| `javascript.md` | JS/TS conventions: test runners, ESLint/Prettier, type checking, component testing |

## Conventions

All workflows share a common preamble:

1. Load project context from `doc/agents/project.md`
2. Review `doc/agents/lessons.md` for relevant prior findings

Task-specific artifacts (plans, reviews) are written to `doc/agents/tasks/<task-name>/`. This directory is deleted after the user approves the task as completed.

Each workflow ends by starting a new conversation — artifacts (task files, committed code) carry state forward between sessions, not conversation context.

## Setup

The `link` script symlinks the workflow files into a project's `doc/agents/` directory and scaffolds `project.md` and `lessons.md` if they don't exist:

```sh
# Clone to a shared location
git clone <repo-url> ~/.agents

# From any project directory
~/.agents/link
```

Workflows reference two project-local files that you fill in per-repo:

- `doc/agents/project.md` — project overview, tech stack, architecture, dev setup. Also the place for project-specific overrides (custom plan mode triggers, commit message formats, etc.)
- `doc/agents/lessons.md` — accumulated notes on patterns, gotchas, and fixes
