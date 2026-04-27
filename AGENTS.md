# Agent Workflows

## Context to load

1. Load `doc/context.md` for project overview, tech stack, architecture, and dev setup.
2. Load the relevant stack file from `.agents/stack/` based on the project's tech stack (e.g. `.agents/stack/rails.md`).

## Workflow selection

| When the task is to... | Load |
|------------------------|------|
| Plan a new feature, design a refactoring, or scope a complex change | `.agents/workflows/planning.md` |
| Implement code changes, follow a plan, or build a feature | `.agents/workflows/implementation.md` |
| Diagnose and fix a bug, investigate a failure, or debug an issue | `.agents/workflows/bugfix.md` |
| Review code changes, a pull request, or a diff | `.agents/workflows/review.md` |
