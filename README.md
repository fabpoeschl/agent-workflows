# Agent Workflows

Reusable workflow prompts and project context for AI coding agents.

## Repository Structure

```
AGENTS.md              ← top-level dispatch (symlinked to project root as AGENTS.md + CLAUDE.md)
workflows/
  specification.md     ← interview for requirements, write specs and tests
  implementation.md    ← implement code changes
  bugfix.md            ← diagnose and fix bugs
  review.md            ← review a diff
commands/
  spec.md              ← /spec command (Claude Code)
  review.md            ← /review command (Claude Code)
  fix.md               ← /fix command (Claude Code)
projects/              ← per-project context and specs (gitignored)
  <project-name>/
    context.md         ← project overview, tech stack, architecture, conventions, dev setup
    specs/             ← YAML spec files created during specification workflow
```

## Setup

```sh
# Clone to a shared location
git clone <repo-url> ~/.agents

# From any project directory
~/.agents/link
```

The `link` script:
1. Symlinks `.agents → ~/.agents` in the project root
2. Symlinks `AGENTS.md` (OpenCode) and `CLAUDE.md` (Claude Code) to the project root
3. Symlinks `.claude/commands → .agents/commands` for Claude Code slash commands
4. Scaffolds `projects/<name>/context.md` and `projects/<name>/specs/` if they don't exist
5. Symlinks `doc/context.md` and `doc/specs/` into the project
6. Installs a `post-checkout` hook so new git worktrees auto-link
7. Adds all generated files to `.gitignore`

## Worktrees

After running `link` once in the main worktree, any `git worktree add` will automatically run `link` in the new worktree via the `post-checkout` hook. All worktrees share the same `projects/<name>/` directory.

## Project Context

Fill in `doc/context.md` after first setup. Every workflow loads this file — it should cover project overview, tech stack, architecture, conventions, and development setup.
