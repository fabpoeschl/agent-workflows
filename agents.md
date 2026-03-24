# Agent Workflows

Load the workflow prompt from `doc/agents/` that matches the current task. Only load one workflow at a time. Also load all stack extension files present in the project.

## Workflow Selection

| When the task is to... | Load |
|------------------------|------|
| Plan a new feature, design a refactoring, or scope a complex change | `doc/agents/planning.md` |
| Implement code changes, follow a plan, or build a feature | `doc/agents/implementation.md` |
| Diagnose and fix a bug, investigate a failure, or debug an issue | `doc/agents/bugfix.md` |
| Review code changes, a pull request, or a diff | `doc/agents/review.md` |

## Stack Extensions

Load **all** `doc/agents/stack-*.md` files present alongside the selected workflow. These are linked automatically by the setup script based on detected project stacks (e.g. a Rails app with a JavaScript frontend gets both `stack-rails.md` and `stack-javascript.md`).

## Project Context

All workflows expect these project-local files to exist:

- `doc/agents/project.md` — project overview, tech stack, architecture, dev setup
- `doc/agents/lessons.md` — accumulated notes on patterns, gotchas, and fixes
