# Agent Workflows

Reusable workflow prompts and project context for AI coding agents.

## Repository Structure

```
AGENTS.md              ← top-level dispatch (symlinked to project root as AGENTS.md + CLAUDE.md)
conventions.md         ← cross-cutting rules (currently: commit conventions)
workflows/             ← each file declares a Task Contract (inputs, outputs, access, exit conditions)
  specification.md     ← interview, write spec + failing tests
  implementation.md    ← turn the spec's failing tests green, commit
  bugfix.md            ← diagnose, fix, commit
  review.md            ← tiered review of a diff (read-only)
commands/
  spec.md              ← /spec command (Claude Code)
  review.md            ← /review command (Claude Code)
  fix.md               ← /fix command (Claude Code)
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
3. Symlinks `.claude/commands → .agents/commands` for Claude Code slash commands
4. Scaffolds `projects/<name>/context.md`, `projects/<name>/config.yaml`, and `projects/<name>/specs/` if they don't exist
5. Symlinks `doc/context.md`, `doc/config.yaml`, and `doc/specs/` into the project (for human browsing)
6. Installs a `post-checkout` hook so new git worktrees auto-link
7. Adds all generated files to `.gitignore`

## Worktrees

After running `link` once in the main worktree, any `git worktree add` will automatically run `link` in the new worktree via the `post-checkout` hook. All worktrees share the same `projects/<name>/` directory.

## Workflows and contracts

Each workflow file opens with a `## Task Contract` YAML block declaring:

- `input` — files the agent reads
- `output` — what the run produces (e.g., `git-commit`, `structured-review`)
- `access` — read / write / deny_write rules per phase
- `exit_when` — conditions that signal the run is done

Specs are resolved as `${AGENTS_DIR:-$HOME/.agents}/projects/<project>/specs/<feature>.yaml` — the env-var path, not the `doc/specs` symlink, so the agent sees the same specs across all worktrees and sessions. The symlink stays for human browsing.

`implementation.md` and `bugfix.md` end with a commit step; neither pushes — that's left to the user. The implementation workflow doesn't pin a model — pick one at invocation.

## Project Context

Two files per project:

- `doc/context.md` — prose: overview, tech stack, architecture, development. Links to `.agents/conventions.md` for cross-cutting rules. Every workflow loads it.
- `doc/config.yaml` — machine-readable values the runtime parses (e.g., `test_dirs` for the spec workflow's write allowlist). Add only fields workflows or pi-mono actually consume.
