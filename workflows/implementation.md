# Implementation Workflow

When following a plan, complete all steps (implement, verify, commit) for each phase before starting the next.

## Steps

### Phase 1: Understand the Task
- Start a new conversation.
- Load project context from `doc/context.md`.
- If a plan is provided, read only the `## Summary` and current `## Phase N` section — do not load the entire plan.
- If no plan is provided, outline your approach and ask for confirmation if the task affects 3+ files or multiple layers.

### Phase 2: Implement and Verify
- Read affected files before modifying them. Search for existing abstractions before creating new ones.
- Run tests incrementally as you go, not just at the end.
- If the plan doesn't match the actual code, flag the discrepancy and confirm with the user before adapting.
- If tests fail due to a conceptual error, re-evaluate the approach and confirm with the user before continuing.
- If a phase requires more than 3 attempts without meaningful progress, stop and present findings to the user.

### Phase 3: Review and Commit
- Do a code review (following `.agents/workflows/review.md`) of changed files and implement any corrections before committing.
- Commit changes. If pre-commit hook reports issues, fix and re-commit.
- Ask for approval before pushing.
- If a plan file exists (`doc/plans/<task-name>.md`), delete it after approval.
- Start a new conversation for the next task.

## Rules
- If no plan exists, use plan mode (`.agents/workflows/planning.md`) when the task requires: 3+ files, 2+ layers, schema changes, API changes, or auth changes.
- Do not mock internal application code; only mock external services (HTTP boundaries).
- Use conventional commit messages: `feat:`, `fix:`, `test:`, `chore:`
- When following a plan: `feat(<task-name>): <description> [phase N]`
