# Agent Workflows

Load the workflow prompt from `doc/agents/` that matches the current task, along with all stack extension files present in the project.

## Workflow Selection

| When the task is to... | Load |
|------------------------|------|
| Plan a new feature, design a refactoring, or scope a complex change | `doc/agents/planning.md` |
| Implement code changes, follow a plan, or build a feature | `doc/agents/implementation.md` |
| Diagnose and fix a bug, investigate a failure, or debug an issue | `doc/agents/bugfix.md` |
| Review code changes, a pull request, or a diff | `doc/agents/review.md` |

## Stack Extensions

Load **all** `doc/agents/stack-*.md` files present alongside the selected workflow. These are symlinked by the setup script (e.g. `stack-rails.md`, `stack-javascript.md`).

## Project Context

All workflows expect `doc/agents/project.md` to exist — project overview, tech stack, architecture, dev setup.
