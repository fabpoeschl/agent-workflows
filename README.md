# Agent Workflows

Reusable workflow prompts and project context for AI coding agents.

## Repository Structure

```
AGENTS.md              ← top-level dispatch (symlinked to project root as AGENTS.md + CLAUDE.md)
workflows/
  planning.md          ← plan features and refactorings
  implementation.md    ← implement code changes
  bugfix.md            ← diagnose and fix bugs
  review.md            ← review code changes
stack/
  rails.md             ← Rails-specific conventions and patterns
  javascript.md        ← JavaScript-specific conventions and patterns
  python.md            ← Python-specific conventions and patterns
projects/              ← per-project context and tasks (gitignored)
  <project-name>/
    context.md         ← project overview, tech stack, architecture, dev setup
    tasks/
      <task>-plan.md   ← created by agent in plan mode
      <task>-review.md ← created by agent during review
```

## Setup

```sh
# Clone to a shared location
git clone <repo-url> ~/.agents

# From any project directory
~/.agents/link
```

The `link` script:
1. Creates `.agents → ~/.agents` symlink in the project root
2. Creates `.agents-project` with the project name (derived from the git root)
3. Symlinks `AGENTS.md` (OpenCode) and `CLAUDE.md` (Claude Code) to the project root
4. Migrates `doc/agents/project.md` to `projects/<name>/context.md` if found
5. Scaffolds `projects/<name>/context.md` if it doesn't exist
6. Installs a `post-checkout` hook so new git worktrees auto-link

## Worktrees

After running `link` once in the main worktree, any `git worktree add` will automatically run `link` in the new worktree via the `post-checkout` hook. All worktrees of the same project share the same `projects/<name>/` directory, so plans and reviews are visible everywhere without copying files.

## Project Context

Fill in `.agents/projects/<name>/context.md` after first setup. This file is loaded by every workflow and should cover: project overview, tech stack, architecture, and development setup.

## Stack Files

Edit the relevant file in `stack/` to capture conventions, testing patterns, and common pitfalls for your tech stack. Agents load the matching stack file based on what's declared in the project context.
