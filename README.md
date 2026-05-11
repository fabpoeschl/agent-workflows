# Agent Prompts

Reusable prompts and project context for AI coding agents.

## Repository Structure

```
AGENTS.md              ← top-level dispatch (symlinked to project root as AGENTS.md + CLAUDE.md)
.pi/prompts/           ← generated local symlinks so pi sees `/spec`, `/implement`, `/fix`, `/review`
conventions.md         ← cross-cutting rules (currently: commit conventions)
prompts/               ← shared user-facing slash-command prompts for Claude and similar agents
  spec.md              ← /spec — analyze codebase, interview, write spec + failing tests
  implement.md         ← /implement — turn spec tests green, commit
  fix.md               ← /fix — diagnose, fix, commit
  review.md            ← /review — tiered diff review (read-only)
projects/              ← per-project state (gitignored)
  <project-name>/
    context.md         ← prose project shape, link to conventions
    config.yaml        ← machine-readable project config (test_dirs, etc.)
    specs/             ← YAML specs; agent resolves the path as $AGENTS_DIR/projects/<name>/specs
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
3. Builds `.claude/commands/` for Claude Code from `prompts/*.md`
4. Builds `.pi/prompts/` for pi prompt-template discovery from `prompts/*.md`
5. Scaffolds `projects/<name>/context.md`, `projects/<name>/config.yaml`, and `projects/<name>/specs/` if they don't exist
6. Symlinks `doc/context.md`, `doc/config.yaml`, and `doc/specs/` into the project (for human browsing)
7. Installs a `post-checkout` hook so new git worktrees auto-link
8. Adds all generated files to `.gitignore`

## Worktrees

After running `link` once in the main worktree, any `git worktree add` will automatically run `link` in the new worktree via the `post-checkout` hook. All worktrees share the same `projects/<name>/` directory.

## Prompt behavior

Specs are resolved as `${AGENTS_DIR:-$HOME/.agents}/projects/<project>/specs/<feature>.yaml` — the env-var path, not the `doc/specs` symlink, so the agent sees the same specs across all worktrees and sessions. The symlink stays for human browsing.

`/implement` and `/fix` end with a commit step; neither pushes — that's left to the user. The implementation prompt doesn't pin a model — pick one at invocation. `/review` is read-only.

## Project Context

Two files per project:

- `doc/context.md` — prose: overview, tech stack, architecture, development. Links to `.agents/conventions.md` for cross-cutting rules. Every prompt loads it.
- `doc/config.yaml` — machine-readable values the runtime parses (e.g., `test_dirs` for the `/spec` prompt's write allowlist). Add only fields prompts or pi-mono actually consume.
