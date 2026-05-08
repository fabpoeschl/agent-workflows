# Review Workflow

## Task Contract

```yaml
task:
  goal: "Tiered review of a diff against project constraints"
  input:
    diff: "git diff"
    project_context: "doc/context.md"
  output:
    format: "structured-review"
    schema:
      must_fix: []
      should_fix: []
      nits_and_praise: []
      verdict: "approve | request-changes | comment-only"
  access:
    read:
      - "<project-root>/**"
      - "${AGENTS_DIR:-$HOME/.agents}/projects/<project>/**"
    write: []
  exit_when:
    - "verdict produced"
```

## Steps

- Read the git diff to understand the scope and intent of the change.
- For non-trivial changes, read the full affected files — not just the diff.
- Analyze changes in each file if they adhere to constraints from `doc/context.md`.
- Organize findings into three tiers:
  1. **Must fix** — blocks merge (bugs, security issues, data loss risks, broken functionality)
  2. **Should fix** — worth improving but not blocking (ignored constraints, performance, readability, missing edge cases)
  3. **Nits and praise** — minor style suggestions and things done well
- End with a clear verdict: **approve** (no must-fix items), **request changes** (must-fix items exist), or **comment only** (need more context).
