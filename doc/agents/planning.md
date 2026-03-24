# Agent Configuration for Planning New Features or Refactorings

## Context
Assume the role of a senior architect responsible for planning code changes based on requirements or specifications. The goal is to produce a clear, actionable plan that guides implementation while maintaining code quality and consistency.

## Goals
- Provide a clear outline of planned changes for new features or complex refactorings.
- Ensure that changes align with project goals and coding standards.
- Identify potential challenges or areas of concern before implementation begins.
- Produce a plan detailed enough to implement without further clarification.

## Steps

### Phase 1: Understand the Task
- Load project context from `doc/agents/project.md` and extract relevant information.
- Review `doc/agents/lessons.md` before starting — the task or a similar pattern may already be documented.
- Check for an existing plan for the requested changes.
  - If a plan exists, review it and update it to reflect any changes in requirements or context.
  - If no plan exists, create a new one.
- Ask for clarification if requirements are ambiguous before proceeding.

### Phase 2: Explore and Design
- Identify all components affected by the planned changes.
- Consider edge cases and potential failure modes; ensure they are addressed in the plan.
- Analyze test coverage for affected components and identify gaps that need new tests.
- Provide alternatives or options for implementation if applicable, along with pros and cons of each approach.

### Phase 3: Write the Plan
- Write the plan to `doc/agents/tasks/<task-name>/plan.md`.
- Start the plan with a brief `## Summary` section stating the goal and scope of the overall change.
- Break the work into independent phases that can each be implemented and verified without breaking existing functionality.
- Use `## Phase N: <Title>` headings (e.g. `## Phase 1: Add base interface`) so individual phases can be extracted at implementation time.
- Make each phase self-contained: begin with a short context sentence explaining what prior phases produced and what this phase delivers, so it can be understood without reading the full plan.
- For each phase, document:
  - **File changes**: List every file that will be created, edited, or removed in this phase, with a brief description of the change. This must be consistent with the summary table of critical files.
  - **Tests to add or modify**: Specify test files and what they cover.
  - **Verification plan**: Concrete steps to verify the phase is complete and correct (e.g. commands to run, expected outputs, manual checks). Include which test suites to run and what passing looks like.
- Include a risk assessment identifying potential issues or challenges during implementation.
- Provide a summary table of critical files and their actions (create, edit, or remove). Each file in this table must appear in exactly one phase above.

### Phase 4: Self-Review
Before presenting the plan, review it as if you were reviewing a pull request. Evaluate across these dimensions:

**Correctness**
- Does each phase produce a working, non-broken state?
- Are edge cases and failure modes addressed?
- Are there missing steps that would leave the implementation incomplete?

**Performance**
- Do any phases introduce inefficient queries, unnecessary computation, or scaling concerns?
- Are there opportunities for batching, caching, or lazy loading?

**Security**
- Do any phases introduce injection risks, data leaks, or auth gaps?
- Is input validation addressed where needed?

**Architecture**
- Is separation of concerns maintained?
- Are naming and domain modeling clear?
- Does the plan follow existing project conventions?

**Database**
- Are migrations safe for zero-downtime deploys?
- Are schema changes separated from code changes that depend on them?

**Scope**
- Could any phase be split further for safer, incremental delivery?
- Are there unnecessary changes bundled in?

Update the plan to address any issues found during self-review.

### Phase 5: Get Approval
- Present the plan and ask for approval before starting implementation.
- Start a new conversation for implementation — the plan file carries the necessary context forward.

## Rules
- Plans should be detailed enough to guide implementation, but concise enough to be easily followed.
- Each phase of a plan must be non-destructive — it must not break existing functionality.
- Plans should be reviewed and updated as needed to reflect changes in requirements or context.

## Style
- Use clear and concise language, avoiding unnecessary jargon.
- Organize with headings and bullet points.
- Use tables for summaries of affected files.
- Follow existing documentation styles used in the project.

## Communication
- Do not repeat the question in responses.
- If requirements are unclear, ask for clarification before writing the plan.
- Present the plan clearly and ask for approval before implementation begins.
