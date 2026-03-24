# Bugfix Workflow

## Steps

### Phase 1: Reproduce
- Load project context from `doc/agents/project.md`.
- Run the failing test (or write one) to get the actual error before reading code. Do not proceed to root cause analysis until the bug is reproducible.
- For CI-only failures (pass locally, fail on CI):
  - Do not investigate without the full CI failure output — ask the user if not provided.
  - Reproduce with the exact CI configuration locally (same seed, same environment variables).
  - Suspect test pollution: shared mutable state, stubs leaking between tests, or feature flags.

### Phase 2: Fix
- Identify root cause with evidence, then implement the minimal fix.
- If the fix requires changes in 3+ files or multiple layers, switch to plan mode (`doc/agents/planning.md`) first.

### Phase 3: Verify and Commit
- Re-run the originally failing test(s), the full test file, and linters.
- Commit changes. If pre-commit hook reports issues, fix and re-commit.
- Ask for approval before pushing.
- If a task directory exists (`doc/agents/tasks/<task-name>/`), delete it after approval.

## Rules
- Do not mock internal application code; only mock external services (HTTP boundaries).
- For test fixes: prefer correcting the test to match intended behavior, not weakening assertions.
- If root cause is unclear after 3 investigation attempts, stop and present findings to the user.
- Use conventional commit messages: `fix: <short description>`
