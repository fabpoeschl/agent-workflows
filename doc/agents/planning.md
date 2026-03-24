# Planning Workflow

## Steps

### Phase 1: Understand the Task
- Load project context from `doc/agents/project.md`.
- Check for an existing plan for the requested changes. If one exists, update it rather than starting from scratch.

### Phase 2: Explore and Design
- Identify all components affected by the planned changes.
- Read the source files for affected components — do not plan based on file names or assumptions alone.
- Search for existing abstractions (helpers, utilities, base classes) before planning new ones.
- Analyze test coverage for affected components and identify gaps that need new tests.

### Phase 3: Write the Plan
- Write the plan to `doc/agents/tasks/<task-name>/plan.md`.
- Start with a brief `## Summary` section stating the goal and scope.
- Break the work into phases using `## Phase N: <Title>` headings. Each phase must:
  - Be self-contained: begin with a context sentence explaining what prior phases produced and what this phase delivers.
  - Be non-destructive: not break existing functionality.
  - List **file changes** (create, edit, or remove), **tests to add or modify**, and a **verification plan** (commands to run, expected outputs).
- Include a summary table of critical files and their actions. Every file in a phase must appear in this table, and vice versa.

### Phase 4: Self-Review
Before presenting the plan, review it. Always check:

- Does each phase produce a working, non-broken state?
- Are there missing steps that would leave the implementation incomplete?
- Could any phase be split further for safer, incremental delivery?
- Are there unnecessary changes bundled in?

Also check when relevant: performance (queries, computation, caching), security (injection, auth, validation), database (migration safety, schema separation).

Update the plan to address any issues found.

### Phase 5: Get Approval
- Present the plan and ask for approval before starting implementation.
- Start a new conversation for implementation — the plan file carries the necessary context forward.
