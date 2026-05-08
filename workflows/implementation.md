# Implementation Workflow

## Task Contract

```yaml
task:
  goal: "Turn the spec's failing tests green"
  input:
    spec_path: "${AGENTS_DIR:-$HOME/.agents}/projects/<project>/specs/<feature>.yaml"
    project_context: "doc/context.md"
    failing_tests: "test files written during the specification phase"
  output:
    format: "git-commit"
  access:
    read:
      - "<project-root>/**"
      - "${AGENTS_DIR:-$HOME/.agents}/projects/<project>/**"
    write:
      - "<project-root>/**"
    deny_write:
      - "${AGENTS_DIR:-$HOME/.agents}/**"
  exit_when:
    - "all spec tests pass, no existing tests regressed"
    - "changes committed following the conventions referenced from doc/context.md"
  constraints:
    - "no behavior outside the spec"
    - "no code without a test covering it"
```

## Phase 1: Load Context

- Spec: `${AGENTS_DIR:-$HOME/.agents}/projects/<project>/specs/<feature>.yaml`
- `doc/context.md` for project shape, constraints, and links to any cross-cutting rules (e.g., commit conventions)
- The failing tests from the specification phase

## Phase 2: Verify Tests Fail

Run the test suite. Confirm the new tests fail and existing tests still pass. If a new test passes already, the spec is met — stop and report.

## Phase 3: Implement

One contract at a time:
- Write the minimum code to satisfy the contract
- Run its test
- Move on when it passes

Stay strictly within the spec. No refactors of adjacent code, no extra features, no improvements to unrelated parts.

## Phase 4: Verify

- All new tests pass
- No existing tests regressed
- No code added that isn't covered by a test

If any of these fail, fix before reporting done.

## Phase 5: Commit

- Stage the implementation files and the test files added for this spec.
- Compose a commit message following the conventions linked from `doc/context.md` (e.g., `feat:` for new behavior, `fix:` for bug fixes). Use the spec's `feature` and `description` for the subject line.
- Run `git commit`. Do not push — leave that to the user.
