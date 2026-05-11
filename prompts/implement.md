Goal:
Turn an approved feature spec's failing tests green with the minimum implementation required, then commit the result locally. Do not push.

Inputs:
- the approved spec at `${AGENTS_DIR:-$HOME/.agents}/projects/<project-name>/specs/<feature-name>.yaml`
- `doc/context.md`
- the current project codebase
- the failing tests written during `/spec`
- `${AGENTS_DIR:-$HOME/.agents}/projects/<project>/**` when needed for read-only reference

Persistent state:
Maintain a compact implementation state file at:

`${AGENTS_DIR:-$HOME/.agents}/projects/<project-name>/state/<feature-slug>-implement.yaml`

Use the spec's feature name as the slug when available.

Purpose:
- preserve operational continuity across `/compact`
- track contract-by-contract implementation progress
- avoid repeating analysis and test runs unnecessarily
- keep the implementation constrained to the approved spec
- maintain a minimal active working set
- support deterministic reload and continuation

The state file is not conversational memory.

It must contain only:
- current implementation phase
- target spec path
- active implementation scope
- contract completion status
- active assumptions
- directly relevant code references
- failing and passing test checkpoints
- blockers or unresolved implementation questions
- commit readiness status

The state file must never contain:
- conversational summaries
- chain-of-thought reasoning
- broad architectural reviews
- duplicated spec content
- full test output
- speculative future work
- unnecessary implementation planning prose

Non-negotiable invariants:
- The approved spec is the authority.
- The spec defines the maximum allowed behavior.
- Passing tests do not justify behavior not explicitly required by the spec.
- Tests do not authorize scope expansion beyond the approved spec.
- Existing repository conventions override agent preference unless they conflict with the spec.
- Passing tests are necessary but not sufficient:
  implementation must remain minimal and spec-constrained.
- Prefer adapting existing code paths over creating parallel paths.
- Never broaden public APIs unless explicitly required by the spec.
- Never introduce new abstractions unless existing structure makes them necessary.
- Avoid touching unrelated files even if cleanup opportunities are obvious.
- Maintain the smallest viable active working set throughout implementation.

Forbidden behaviors:
- speculative generalization
- opportunistic cleanup
- dependency upgrades unless explicitly required
- style-only rewrites
- formatting-only churn
- renaming for preference only
- introducing frameworks/helpers not required by the spec
- rewriting working code without spec justification
- fixing unrelated failing tests
- adding TODOs for speculative future work
- broad repository scans without targeted need
- repeated identical test runs without code changes

Contract definition:
A contract is the smallest independently verifiable behavior explicitly required by the approved spec and covered by one or more failing tests.

A contract must:
- map to specific tests
- produce observable behavior
- be implementable independently
- exclude unrelated refactors or speculative extensibility

Minimum implementation heuristic:
Choose the implementation with:
1. the fewest modified files
2. the smallest public surface area change
3. the least new abstraction
4. the shortest dependency chain
5. the highest consistency with existing repository patterns

provided the approved spec is fully satisfied.

State file rules:
- keep the file minimal and continuously compressed
- prefer replacement and compression over accumulation
- remove resolved or superseded items immediately
- retain only currently relevant assumptions and references
- avoid historical reasoning unless still operationally relevant
- keep the file approximately constant in size over time
- retain durable execution state, not implementation narrative

State update checkpoints:
Update the state file after:
- initial spec load
- failing-test verification
- each contract transition
- regression discovery
- verification completion
- commit creation

State reload rules:
On reload:
- trust the approved spec over the state file if inconsistent
- validate referenced files still exist
- remove stale assumptions immediately
- rerun tests if prior results may no longer be valid
- reload only:
  - approved spec
  - compact implementation state file
  - directly relevant files
  - relevant test commands/results if needed

Do not reread unrelated parts of the repository unless required.

Context management rules:
Maintain the smallest viable working set.

Continuously remove:
- resolved reasoning
- irrelevant architectural detail
- completed contract context
- stale hypotheses
- duplicate implementation notes

Do not keep broad repository knowledge in active context unless currently required.

Eviction priority:
1. resolved reasoning
2. historical test outputs
3. superseded assumptions
4. completed contract notes
5. broad architectural observations

Retain longest:
- unresolved blockers
- active contracts
- active edit scope
- failing checkpoints

State file structure:

```yaml
phase: <load-context|verify-failing|implementing|verifying|ready-to-commit|done>

feature_slug: <slug>

spec_path: <path>

active_scope:
  allowed_edit_paths:
    - <path>

  forbidden_without_escalation:
    - <path>

contracts:
  - id: <slug>

    status: <pending|in_progress|passing>

    tests:
      - <test reference>

    files_touched:
      - <path>

    notes:
      - <minimal implementation note if still relevant>

assumptions:
  - <active assumption>

code_refs:
  - path: <file>
    note: <why currently relevant>

test_status:
  failing_expected:
    - <test or command>

  now_passing:
    - <test or command>

  regressions:
    - <test or failure>

blockers:
  - <open issue>

status:
  spec_loaded: false
  tests_verified_failing: false
  implementation_complete: false
  regression_check_passed: false
  commit_created: false
```

Process:

1. Path resolution

Resolve paths as follows:

- repo root:
  `git rev-parse --show-toplevel`

- project name:
  use the spec path project name when available

- specs dir:
  `${AGENTS_DIR:-$HOME/.agents}/projects/<project-name>/specs`

- state dir:
  `${AGENTS_DIR:-$HOME/.agents}/projects/<project-name>/state`

Create missing state directory before writing.

Do not assume repository basename equals logical project/app name in a monorepo.

2. Load context

Load:
- the approved spec
- `doc/context.md`

Extract only:
- architecture constraints
- testing conventions
- implementation conventions
- commit conventions
- relevant monorepo or framework constraints

Do not retain unrelated documentation context.

Identify:
- failing tests created for the spec
- smallest likely implementation boundary
- initial allowed edit scope

Initialize the compact state file.

Record only:
- durable assumptions
- active contracts
- directly relevant references
- implementation boundaries
- targeted test checkpoints

3. Verify tests fail first

Run the narrowest relevant failing tests first.

Confirm:
- the new spec tests fail before implementation
- existing unaffected tests are not already failing due to unrelated causes

If a new spec test already passes without implementation changes:
- stop
- report that the spec may already be satisfied

Record only minimal failing checkpoints needed for continuation.

4. Analyze before editing

Inspect only code directly relevant to:
- the approved spec
- failing tests
- immediate implementation dependencies

Before editing:
- identify the smallest viable edit surface
- identify files that must not change
- inspect adjacent implementations
- identify existing naming conventions
- identify existing testing patterns
- identify existing error-handling conventions
- identify existing dependency boundaries

Avoid:
- broad repository scans
- loading unrelated large files
- architectural exploration beyond the active scope

Determine:
- where each contract should be implemented
- which existing abstractions should be reused
- which boundaries must not be crossed
- the minimum safe change set

Stay strictly within the approved spec.

5. Implement one contract at a time

Select one pending contract.

Implement only the minimum code required for that contract.

Prefer:
- existing patterns
- existing helpers/utilities
- existing architectural boundaries

Avoid:
- speculative extensibility
- parallel abstractions
- broad refactors

Run the narrowest relevant test(s) first.

When the contract passes:
- update state
- compress resolved notes
- remove stale assumptions
- move to the next smallest pending contract

If implementation requires ambiguity resolution:
- stop
- report the exact gap or contradiction
- do not invent behavior

6. Test execution policy

Start with:
- the narrowest failing test
- then the test file
- then the directly related module/package/engine tests

Expand test scope only when:
- shared infrastructure changed
- integration boundaries were crossed
- database behavior changed
- shared APIs changed
- failing output demonstrates broader coupling

Avoid repeated identical test runs without code changes.

7. Scope control

Maintain an explicit active implementation scope.

Do not expand the active scope unless:
- a contract cannot be satisfied
- a direct dependency requires inspection
- failing tests demonstrate additional coupling

Avoid touching:
- unrelated packages/apps/engines
- global configuration
- dependency manifests
- generated artifacts

unless explicitly required by the approved spec.

8. After each implementation step

Update the compact state file.

Compress:
- resolved notes
- obsolete assumptions
- stale references

Retain only:
- active blockers
- active contracts
- current edit scope
- currently relevant test checkpoints

Determine the next smallest valid implementation step.

9. Verification

After all contracts are implemented:

Run:
1. targeted contract tests
2. directly related module/package tests
3. affected integration tests
4. relevant regression checks

Confirm:
- all spec-related tests pass
- no relevant regressions exist
- implementation behavior matches the approved spec
- no unnecessary behavior was introduced
- no untested behavior was added beyond the spec

If verification fails:
- fix the issue before continuing

Record only final verification checkpoints in state.

10. Commit preparation

Before commit:
- review the staged diff for unrelated changes
- exclude debugging edits and temporary instrumentation
- exclude formatting-only changes unless required
- ensure no generated artifacts were unintentionally added
- ensure the state file reflects final status

Diff policy:
- avoid reformatting untouched code
- avoid import/order churn unless required
- preserve blame locality where possible
- optimize for reviewer readability

11. Commit

Stage:
- implementation files
- relevant tests
- compact implementation state file if appropriate

Compose a commit message following conventions from `doc/context.md`.

Use the spec's feature and description when appropriate.

Run:
- `git commit`

Do not push.

Update the state file after commit.

12. Use of `/compact`

Before or after `/compact`:
- ensure the state file is current
- compress stale operational detail aggressively

After compaction:
reload only:
- approved spec
- compact implementation state file
- directly relevant files
- relevant test commands/results if needed

Do not reload unrelated repository context unless required.

Stop immediately if:
- the spec contradicts the tests
- tests require unspecified behavior
- implementation requires architectural decisions absent from the spec
- multiple valid interpretations materially affect behavior
- required changes exceed the apparent scope of the approved spec

Implementation is complete only when:
1. all tests tied to the approved spec pass
2. relevant regression checks pass
3. implementation behavior matches the approved spec
4. no unresolved ambiguity remains
5. no unnecessary behavior was introduced
6. changes are committed locally
