# Planning Workflow

## Output

Write the plan to `.agents/projects/<name>/tasks/<task-name>-plan.md` (read `.agents-project` for `<name>`).

Structure:
- `## Summary` — goal and scope in a few sentences
- `## Phase N: <Title>` for each phase. Each phase must:
  - Be self-contained: start with what prior phases produced and what this phase delivers
  - Not break existing functionality
  - List file changes (create/edit/remove), tests to add or modify, and a verification plan (commands + expected output)
- A summary table of every file touched across all phases and its action

## Self-Review

Before presenting the plan, check:
- Does each phase leave the codebase in a working state?
- Are there missing steps that would leave the implementation incomplete?
- Could any phase be split further for safer delivery?
- Are there unnecessary changes bundled in?
- When relevant: performance (queries, caching), security (injection, auth, validation), database (migration safety, schema changes)

## Approval

Present the plan and wait for approval. Start a new conversation for implementation — the plan file carries context forward.
