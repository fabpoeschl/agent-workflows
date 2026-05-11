Goal: produce an unambiguous feature spec via interview, then write failing tests before any implementation.

Inputs:
- the user's free-form request
- `doc/context.md`
- the current project codebase
- `${AGENTS_DIR:-$HOME/.agents}/projects/<project>/**` when needed

Persistent state:
Maintain a compact feature state file at:
`${AGENTS_DIR:-$HOME/.agents}/projects/<project-name>/state/<feature-slug>.yaml`

Use a temporary slug initially if the final feature name is not yet known. Rename the state file once the feature name is finalized.

Purpose:
- preserve operational continuity across `/compact`
- avoid re-asking resolved questions
- avoid rereading unnecessary code

The state file is not conversational memory.
It must contain only:
- current interview phase
- resolved decisions
- unresolved questions
- active assumptions
- directly relevant code references
- important constraints
- spec/test completion status

State rules:
- keep the file minimal and continuously compressed
- remove resolved or superseded items
- never store transcripts or conversational summaries
- never duplicate finalized spec content
- do not restate full contracts, full acceptance criteria, or full test cases once they exist in the spec
- the state file is an operational scratchpad, not a draft spec
- prefer structured fields and bullets over prose
- avoid historical reasoning unless still relevant
- keep the file approximately constant in size over time
- prefer replacement and compression over accumulation

State file structure:
```yaml
phase: <requirements|analysis|interview-core|interview-edge|interview-failures|interview-constraints|spec-draft|awaiting-approval|tests>
feature_slug: <slug>
resolved:
  - <decision>
unresolved:
  - <question>
assumptions:
  - <active assumption>
code_refs:
  - path: <file>
    note: <why relevant>
constraints:
  - <constraint>
status:
  spec_written: false
  spec_approved: false
  tests_written: false
```

Process:
1. Initial requirement analysis
   - Read and understand the provided requirements first.
   - Identify:
     - what is already clear
     - what is ambiguous
     - what is completely missing
     - what assumptions are currently being made
   - Initialize the compact state file and record only durable unresolved state.

2. Path resolution
   - Resolve paths like this:
     - project name: `basename "$(git worktree list | head -1 | awk '{print $1}')"`
     - state dir: `${AGENTS_DIR:-$HOME/.agents}/projects/<project-name>/state`
     - specs dir: `${AGENTS_DIR:-$HOME/.agents}/projects/<project-name>/specs`
   - Create missing directories before writing.

3. Codebase analysis before interview
   - Analyze the current project codebase before interviewing.
   - Determine:
     - where the feature likely belongs
     - which modules are affected
     - existing architectural boundaries
     - existing conventions and patterns
     - existing tests and testing conventions
     - reusable utilities and abstractions
     - constraints imposed by current implementation
   - Record only directly relevant references and constraints into the state file.
   - Do not reread the full codebase after `/compact`.
   - After compaction, reload only:
     - finalized spec
     - compact state file
     - directly relevant files when necessary

4. Structured interview process
   - Interview in small phases only after codebase analysis.
   - Never ask all questions at once.
   - Ask only the minimum next questions required to eliminate ambiguity.

Interview phases:
1. Core behavior
   - happy path
   - inputs
   - outputs
   - side effects

2. Edge cases
   - boundaries
   - concurrency
   - ordering
   - idempotency
   - empty/null states

3. Failure modes
   - validation failures
   - infrastructure failures
   - retry behavior
   - partial success handling

4. Constraints
   - performance
   - security
   - compatibility
   - migration requirements
   - architectural constraints

After each phase:
- update the compact state file
- compress resolved state
- remove superseded assumptions
- determine whether additional clarification is required
- ask only the next necessary questions

5. Ambiguity rules
   - Distinguish explicitly between:
     - confirmed behavior
     - inferred behavior
     - unresolved behavior
   - Never silently invent requirements.
   - If a reasonable default is inferred from existing conventions:
     - state the inference explicitly
     - ask for confirmation only if deviation would materially affect implementation or tests

6. Anti-redundancy rules
   - Never ask a question already answered by:
     - the user
     - the compact state file
     - the spec draft
     - existing code conventions
   - Before asking a question, check whether the answer already exists implicitly in the codebase or prior responses.

7. Spec completion criteria
   Stop interviewing only when:
   - every observable behavior is specified
   - all important edge cases are resolved
   - failure behavior is defined
   - testable acceptance criteria exist
   - no unresolved ambiguity would materially change implementation or tests

8. Spec authoring
   - Write the spec to:
     - `<specs-dir>/<feature-name>.yaml`
   - Use this YAML structure:
```yaml
feature: <name>
description: <one-sentence description>
contracts:
  - id: <slug>
    description: <what this contract specifies>
    preconditions:
      - <required state before the action>
    inputs:
      <key>: <value or type description>
    outputs:
      <key>: <value or type description>
    side_effects:
      - <observable state change beyond the return value>
    errors:
      - condition: <when this error occurs>
        type: <error class or HTTP status>
        message: <expected message or description>
constraints:
  - <architectural, security, or business constraint>
assumptions:
  - <explicit assumptions>
test_notes:
  - <important testing expectations>
```
   - Update state after spec drafting.

9. Approval gate
   - Present the spec and wait for explicit user approval before writing tests.
   - Update the state file when approval is granted.

10. Test authoring
   - After approval, run `/compact`.
   - Reload only:
     - approved spec
     - compact state file
     - directly relevant files
   - Write failing tests before any implementation:
     - one test per contract
     - tests must fail before implementation exists
     - follow testing conventions from `doc/context.md`
     - write only to configured test directories
   - Update state after test writing.

Important rules:
- Perform codebase analysis before interviewing for detailed specs.
- Use the analysis to ask better, implementation-aware clarification questions.
- Do not implement the feature.
- End only when:
  1. the spec is written and approved
  2. a failing test exists for every contract
