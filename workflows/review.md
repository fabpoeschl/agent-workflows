# Review Workflow

## Steps

### Phase 1: Understand the Change
- Load project context from `doc/context.md`.
- Read the git diff to understand the scope and intent of the change.
- For non-trivial changes, read the full affected files — not just the diff.

### Phase 2: Write the Review
- Organize findings into three tiers:
  1. **Must fix** — blocks merge (bugs, security issues, data loss risks, broken functionality)
  2. **Should fix** — worth improving but not blocking (performance, readability, missing edge cases)
  3. **Nits and praise** — minor style suggestions and things done well
- End with a clear verdict: **approve** (no must-fix items), **request changes** (must-fix items exist), or **comment only** (need more context).
- If a plan file exists for this task, write the review to `doc/reviews/<task-name>.md`. Otherwise, present directly in conversation.

## Rules
- Do not flag issues or suggest refactors that are out of scope for the current change.
