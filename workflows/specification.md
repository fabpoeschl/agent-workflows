# Specification Workflow

## Phase 1: Read Requirements

Read the provided requirements. Identify what is clear, what is ambiguous, and what is missing before asking anything.

## Phase 2: Interview

Ask clarifying questions in groups — do not ask everything at once:
1. Core behavior: happy path inputs, outputs, and side effects
2. Edge cases and boundary conditions
3. Error cases and failure modes
4. Constraints (performance, security, architectural)

After each group, incorporate the answers and ask follow-up questions if needed. Stop when the behavior is fully unambiguous.

## Phase 3: Write Specification

Resolve the specs directory: use `$SPECS_DIR` if set, otherwise default to `doc/specs`. Create it if missing (`mkdir -p`), then write the spec to `<specs-dir>/<feature-name>.yaml`.

Structure:
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
```

Present the spec and wait for approval.

## Phase 4: Write Tests

Compact context, then load `<specs-dir>/<feature-name>.yaml` (resolving `<specs-dir>` the same way as Phase 3).

Write tests before any implementation:
- One test per contract
- Tests must fail before the implementation exists
- Follow testing conventions from `doc/context.md`
