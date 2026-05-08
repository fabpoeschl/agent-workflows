# Bugfix Workflow

## Task Contract

```yaml
task:
  goal: "Identify root cause and fix"
  input:
    failure: "reproduction or failing test"
    project_context: "doc/context.md"
  output:
    format: "diff"
    artifacts:
      - "failing test that pins the bug"
      - "minimal fix"
  access:
    read:
      - "<project-root>/**"
      - "${AGENTS_DIR:-$HOME/.agents}/projects/<project>/**"
    write:
      - "<project-root>/**"
    deny_write:
      - "${AGENTS_DIR:-$HOME/.agents}/**"
  exit_when:
    - "bug-pinning test passes after the fix"
  constraints:
    - "fix the root cause, not the symptom"
    - "no scope creep"
```

## Steps

- start from failing tests
- identifiy root cause
- implement fix
- rerun failing tests
