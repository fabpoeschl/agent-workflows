Goal:
Diagnose and fix a bug by:
1. reproducing it,
2. identifying the root cause,
3. writing or validating a bug-pinning test,
4. implementing the minimal safe fix,
5. verifying the fix,
6. committing the result.

Do not push.

Inputs:
- a failing test, stack trace, bug report, or reproduction case
- `doc/context.md`
- the current repository
- `${AGENTS_DIR:-$HOME/.agents}/projects/<project>/**` (read-only except for the state file)

Persistent Bugfix State:
Maintain a compact machine-readable state file at:

`${AGENTS_DIR:-$HOME/.agents}/projects/<project-name>/state/fix-<bug-slug>.yaml`

If the final bug label is unclear, start with a temporary slug and rename later if necessary.

The state file exists to:
- preserve continuity across `/compact`
- avoid repeating reproduction and root-cause analysis
- track the active reproduction, hypothesis, test, and verification state
- constrain the fix to the actual root cause

The state file is operational state only.
It is not conversational memory or a work log.

Allowed contents only:
- current phase
- reproduction checkpoint
- active root-cause hypothesis
- confirmed root cause
- bug-pinning test status
- directly relevant code references
- unresolved assumptions/questions
- verification checkpoints
- commit readiness

State Constraints:
- keep the file compact and continuously compressed
- replace stale information instead of appending history
- remove resolved hypotheses and obsolete notes
- never store transcripts, reasoning dumps, or conversational summaries
- never duplicate large logs, stack traces, diffs, or full test output
- never expand into a broad implementation plan
- prefer structured fields and terse bullets
- preserve only information still operationally relevant
- keep file size roughly constant over time

Required State Schema:

```yaml
phase: <load-context|reproduce|analyze-root-cause|test-pinned|fixing|verifying|ready-to-commit|done>

bug_slug: <slug>

reproduction:
  status: <unknown|confirmed|not-reproduced>
  command_or_case: <minimal reproduction checkpoint>

root_cause:
  hypothesis: <current best hypothesis>
  confirmed: <confirmed root cause or empty>

test:
  path: <bug-pinning test path or empty>
  status: <missing|failing|passing>

code_refs:
  - path: <file>
    note: <why currently relevant>

assumptions:
  - <active assumption>

questions:
  - <open question>

verification:
  failing_checkpoint: <pre-fix failing command/test>
  passing_checkpoint: <post-fix verification command/test>
  regressions:
    - <relevant regression test/check>

status:
  reproduced: false
  root_cause_confirmed: false
  bug_test_present: false
  fix_applied: false
  verification_passed: false
  commit_created: false
```

Execution Process:

1. Resolve Paths
- Determine the project name with:

  `basename "$(git worktree list | head -1 | awk '{print $1}')"`

- Resolve the state directory:

  `${AGENTS_DIR:-$HOME/.agents}/projects/<project-name>/state`

- Create the directory if missing.

2. Load Context
- Read `doc/context.md` for:
  - architecture constraints
  - development workflow
  - testing conventions
  - commit conventions
- Load the failing test, bug report, stack trace, or reproduction case.
- Initialize the compact state file immediately.

3. Reproduce Before Changing Code
- Confirm the bug using the smallest reliable reproduction.
- Prefer an existing failing test when available.
- If no test exists:
  - derive the smallest reproducible path,
  - or create a minimal reproduction.
- Do not modify production code before reproduction succeeds.
- If reproduction cannot be confirmed, stop and report that explicitly.
- Store only the minimal reproduction checkpoint in state.

4. Analyze Root Cause
- Inspect only code relevant to the reproduced failure.
- Identify:
  - where the failure originates,
  - whether the visible symptom differs from the underlying cause,
  - which invariant or abstraction is violated,
  - the smallest safe correction.
- Do not patch symptoms when the cause is elsewhere.
- When multiple hypotheses exist:
  - test the cheapest discriminating checks first,
  - eliminate invalid hypotheses quickly,
  - keep only the active hypothesis in state.

5. Pin the Bug with a Test
- Ensure a test fails specifically because of the bug.
- If no suitable test exists:
  - write the smallest possible bug-pinning test before fixing.
- The test must:
  - fail before the fix,
  - pass after the fix,
  - exercise the real failure mode.
- Record only the test path and status in state.

6. Implement the Minimal Fix
- Fix the confirmed root cause only.
- Keep edits local, minimal, and reversible.
- Reuse existing abstractions, helpers, and conventions.
- Avoid unrelated cleanup, refactors, renames, formatting churn, or opportunistic improvements.
- If a safe fix requires broader architectural changes:
  - stop,
  - document the constraint clearly,
  - avoid speculative redesign.

7. After Every Meaningful Step
- Update the compact state file.
- Compress or replace stale information.
- Remove disproven hypotheses.
- Retain only:
  - current reproduction checkpoint,
  - active code references,
  - active assumptions/questions,
  - current verification checkpoints.
- Determine the next smallest useful action.

8. Verification
Verification order:
1. run the bug-pinning test,
2. run nearby/surrounding regression tests,
3. rerun the original reproduction path if distinct.

Confirm all of the following:
- the bug-pinning test passes,
- the original failure is resolved,
- no relevant regressions were introduced.

If verification fails:
- continue debugging before concluding work.

Store only final verification checkpoints in state.

9. Commit
- Stage only the fix and directly related tests.
- Create a `fix:` commit message following `doc/context.md` conventions.
- Run `git commit`.
- Do not push.
- Update state after commit.

10. `/compact` Continuity Rules
Before and after `/compact`:
- ensure the state file is current.

After compaction, reload only:
- the compact bugfix state file,
- directly relevant source files,
- the bug-pinning test,
- the minimal reproduction details,
- `doc/context.md` if needed.

Do not reread unrelated parts of the repository unless required.

Hard Rules:
- Fix the root cause, not the symptom.
- No scope creep.
- No unrelated refactors or cleanup.
- Do not modify `${AGENTS_DIR:-$HOME/.agents}/**`
  except the designated bugfix state file.
- Do not declare success until all are true:
  1. the bug-pinning test passes,
  2. relevant regression checks pass,
  3. the fix is committed.
