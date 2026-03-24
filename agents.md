# Agent Workflows

Load the workflow prompt from `doc/agents/` that matches the current task. Only load one workflow at a time. If the task also involves a specific language or framework, load the matching extension file alongside the workflow.

## Workflow Selection

| When the task is to... | Load |
|------------------------|------|
| Plan a new feature, design a refactoring, or scope a complex change | `doc/agents/planning.md` |
| Implement code changes, follow a plan, or build a feature | `doc/agents/implementation.md` |
| Diagnose and fix a bug, investigate a failure, or debug an issue | `doc/agents/bugfix.md` |
| Review code changes, a pull request, or a diff | `doc/agents/review.md` |

## Language Extensions

Load one of these alongside the selected workflow when the project uses that stack:

| Stack | Load |
|-------|------|
| Ruby on Rails | `doc/agents/rails.md` |
| JavaScript / TypeScript | `doc/agents/javascript.md` |

## Project Context

All workflows expect these project-local files to exist:

- `doc/agents/project.md` — project overview, tech stack, architecture, dev setup
- `doc/agents/lessons.md` — accumulated notes on patterns, gotchas, and fixes
