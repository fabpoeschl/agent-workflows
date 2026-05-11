Goal:
Perform a high-signal, read-only code review of a diff against the project's constraints, surface the most important findings first, and end with a clear verdict.

Primary objective order:
1. Prevent incorrect behavior.
2. Prevent regressions in existing behavior.
3. Enforce explicit architectural and safety constraints.
4. Identify meaningful maintainability risks tied to future defects.
5. Ignore purely stylistic preferences unless they materially affect correctness or maintainability.

Inputs:
- the current git diff, pull request diff, or user-provided patch
- `doc/context.md`
- the current project codebase
- `${AGENTS_DIR:-$HOME/.agents}/projects/<project>/**` when needed for reading only

Persistent state:
Maintain a compact review state file at:
`${AGENTS_DIR:-$HOME/.agents}/projects/<project-name>/state/review-<scope-slug>.yaml`

Use a temporary scope slug if the review target is not yet named precisely.

Purpose:
- preserve operational continuity across `/compact`
- avoid rereading already-understood files unnecessarily
- track findings by severity without repeating analysis
- keep the review focused on the actual changed scope
- maintain a bounded active review context
- avoid context drift during long reviews

The state file is not conversational memory.

It must contain only:
- review scope
- files changed and files inspected
- concise file summaries
- relevant project constraints
- open questions or missing context
- active risks
- candidate findings by severity
- invalidated assumptions still relevant to current reasoning
- final verdict status

State rules:
- keep the file minimal and continuously compressed
- remove resolved or superseded findings immediately
- never store transcripts or conversational summaries
- never duplicate large diffs or file contents
- prefer structured fields and bullets over prose
- avoid historical reasoning unless still relevant
- keep the file approximately constant in size over time
- prefer replacement and compression over accumulation
- deduplicate semantically equivalent findings
- retain conclusions, not investigative history
- rewrite stale summaries instead of appending incremental variants

State file structure:
```yaml
phase: <load-context|analyze-diff|inspect-files|assess-findings|finalize>

last_updated: <iso8601>
review_revision: <integer>

scope_slug: <slug>

review_mode: <quick|standard|deep>

review_target:
  diff_source: <git diff|pr|patch>
  summary: <short description>

files_changed:
  - <path>

files_reviewed:
  - path: <path>
    review_depth: <partial|full>
    relevance: <short reason>

file_summaries:
  <path>:
    role: <short description>
    relevance: <why it matters>
    reviewed: <partial|full>

constraints:
  - <relevant project rule>

active_risks:
  - <risk id or short description>

questions:
  - <missing context>

invalidated_assumptions:
  - <short note>

findings:
  must_fix:
    - id: <id>
      file: <path>
      issue: <short finding>

  should_fix:
    - id: <id>
      file: <path>
      issue: <short finding>

  nits_and_praise:
    - <short note>

decision_log:
  - <short conclusion>

status:
  diff_loaded: false
  files_inspected: false
  verdict_ready: false
```

Context management rules:
- Maintain a bounded working set.
- Active review context should contain only:
  - the current diff or active patch subset
  - directly relevant constraints
  - active findings
  - files required to validate current concerns
  - minimal dependency context
- Treat previously inspected files as indexed knowledge, not active context.
- Retain:
  - conclusions
  - invariants
  - validated risks
- Discard:
  - raw implementation details
  - inactive exploratory reasoning
  - irrelevant execution paths
- Prefer replacement over accumulation.
- Compress aggressively once findings stabilize.
- Remove disproven theories immediately.

Reread rules:
Do not reread files already understood unless:
- new findings require deeper validation
- another file changes interpretation
- the diff changes materially
- prior assumptions become invalidated
- the file participates in a newly discovered execution path

Context retrieval priority:
1. current diff
2. affected full files
3. directly connected execution paths
4. architectural constraints from `doc/context.md`
5. neighboring utilities/interfaces
6. broader repository context only if required

Dependency traversal rules:
- Follow dependencies only while they materially affect correctness of the changed behavior.
- Stop traversal once behavior can be validated confidently.
- Avoid recursive exploration of stable infrastructure unless directly implicated.

Review horizon rules:
Keep the review horizon bounded to:
- the changed behavior
- directly affected invariants
- realistic downstream consequences

Do not expand into unrelated architectural critique.

Scale handling:
For large diffs:
- prioritize high-risk files first
- partition review into logical subsystems
- summarize repetitive or mechanical transformations
- sample representative generated or repetitive changes
- spend context budget on behavioral complexity, not line count
- surface uncertainty explicitly when exhaustive validation is infeasible

Risk-weighted attention:
Increase review depth for:
- authentication
- authorization
- persistence
- migrations
- concurrency
- distributed coordination
- security boundaries
- financial logic
- permission systems
- state synchronization
- caching correctness

Decrease review depth for:
- formatting
- isolated copy changes
- mechanical renames
- generated code
- repetitive transformations

Process:

1. Path resolution
   - Resolve paths like this:
     - project name:
       `basename "$(git worktree list | head -1 | awk '{print $1}')"`
     - state dir:
       `${AGENTS_DIR:-$HOME/.agents}/projects/<project-name>/state`
   - Create the state directory if missing.

2. Load context
   - Load `doc/context.md` for:
     - architecture
     - constraints
     - conventions
     - risk areas
   - Load the review target:
     - current git diff by default
     - or user-specified diff / patch / PR scope
   - Initialize or refresh the compact review state file.

3. Understand scope first
   - Infer the intended behavioral change before evaluating implementation quality.
   - Distinguish intentional behavior changes from accidental side effects.
   - Avoid treating intentional product decisions as bugs unless they violate explicit constraints.
   - Read the diff to determine:
     - intent
     - changed files
     - risk areas
     - execution paths
   - Identify which files require full-file reading beyond the diff.
   - Record only directly relevant scope and constraints in state.

4. Inspect affected code
   - For non-trivial changes, read full affected files — not just the diff.
   - Reviewing only the diff is insufficient for:
     - stateful logic
     - async flows
     - persistence
     - authorization
     - API boundary changes
     - dependency injection
     - configuration changes
     - error handling changes
   - Read neighboring or dependency files only when necessary to validate behavior.
   - Focus on correctness, safety, and adherence to project conventions.
   - Do not suggest speculative rewrites unrelated to the change.

5. Review criteria
Evaluate the change for:
- correctness and broken behavior
- regression risk
- security issues
- data loss or corruption risks
- missing validation
- missing error handling
- edge cases and failure modes
- architectural constraint violations
- materially introduced performance issues
- maintainability issues likely to create future defects
- test adequacy where applicable

6. Existing-pattern validation
Before flagging a pattern as problematic:
- check whether surrounding code intentionally uses the same pattern
- check whether project conventions explicitly allow it
- avoid imposing external preferences over repository-local conventions

7. Evidence standards
Every finding must include:
- affected file(s)
- relevant behavior
- concrete failure mode or violated constraint
- reasoning chain from diff to risk

Do not:
- speculate without evidence
- invent hypothetical issues without a plausible trigger
- infer concurrency or security issues without supporting data flow or execution evidence

If a concern depends on unknown runtime behavior:
- record it as an open question instead of a finding

8. Anti-noise rules
Avoid low-value review behavior:
- do not recommend unrelated refactors
- do not demand abstraction without demonstrated need
- do not suggest renaming unless clarity materially affects correctness
- do not flag missing tests for trivial changes
- do not repeat obvious observations already documented in code or tests
- do not over-focus on style preferences

9. Severity tiers

Must fix:
- incorrect behavior
- broken functionality
- realistic regression path
- security vulnerability
- data corruption or loss risk
- violation of hard architectural constraints
- unsafe operational behavior

Must fix excludes:
- style disagreements
- preferred refactors
- speculative maintainability concerns
- hypothetical edge cases without concrete triggers

Should fix:
- ignored project conventions materially affecting maintainability
- missing edge-case handling with realistic impact
- important performance regressions
- readability or maintainability problems likely to cause future defects
- insufficient validation for meaningful failure scenarios

Nits and praise:
- minor improvements
- optional cleanup suggestions
- positive implementation notes

10. Test evaluation
When evaluating tests:
- verify tests validate intended behavior
- prefer behavioral coverage over implementation mirroring
- check whether regression risks are covered
- treat missing tests as significant only when change risk justifies it

11. Confidence handling
Distinguish clearly between:
- verified issue
- probable issue
- unresolved question

When confidence is limited:
- prefer `comment-only`
- state explicitly what missing context would improve confidence
- avoid overstating certainty

12. After each analysis step
- update the compact review state file
- compress resolved notes
- remove superseded findings
- canonicalize duplicate findings
- keep only currently relevant context active
- discard stale investigative detail

13. Stop conditions
Stop gathering context when:
- execution paths are understood
- changed files are sufficiently classified
- findings are evidence-backed
- additional reading is unlikely to change the verdict materially
- unresolved uncertainty has been explicitly documented

Do not read the full repository unless:
- architecture cannot otherwise be understood
- correctness depends on global invariants
- the diff touches cross-cutting infrastructure

14. Final output
Produce a structured review with:
- `must_fix`
- `should_fix`
- `nits_and_praise`
- `verdict`

Each finding should include:
- severity
- file/path
- concise issue summary
- why it matters
- minimal supporting reasoning

Keep findings concise and evidence-oriented.

Verdict values:
- `approve`
- `request-changes`
- `comment-only`

Verdict rules:
- `approve`
  - no material correctness or architectural concerns remain
- `request-changes`
  - one or more must-fix findings exist
  - or cumulative should-fix findings create meaningful operational risk
- `comment-only`
  - insufficient context to assess correctness confidently
  - or findings are informational rather than blocking

15. Use of `/compact`
Before or after `/compact`:
- keep the state file current
- aggressively compress inactive reasoning
- retain only validated conclusions and active risks

After compaction, reload only:
- compact review state file
- relevant diff subset
- directly relevant files
- `doc/context.md` if needed

Do not reread the full codebase unless necessary.

Important rules:
- Read-only review: do not modify code, tests, specs, or agent definitions.
- Focus on changed behavior and direct consequences.
- For non-trivial changes, read full affected files before finalizing findings.
- Prefer fewer high-signal findings over many weak ones.
- End only when a clear verdict is produced.
