# Rail 03 — Memory & Task Tracking

**Always-on. The single biggest improvement to long-horizon agent work.**

LLMs forget. Context windows fill up. Sessions end. Markdown TODO files rot. This rail solves the "where were we?" problem by storing structured state in the repo.

## Tools

### Beads (`bd`) — `gastownhall/beads`

A persistent, dependency-aware issue tracker stored as a Dolt database in `.beads/`. Versioned alongside the code. Designed specifically for AI-supervised workflows.

**Core CLI commands:**
| Command | Purpose |
|---|---|
| `bd init` | One-time per project. Creates `.beads/` and updates `AGENTS.md`. |
| `bd setup claude` | Install Claude Code hooks + settings |
| `bd setup codex` / `bd setup factory` / `bd setup mux` | Other agents |
| `bd prime` | Workflow context for session start |
| `bd doctor` | Health check; `--fix` repairs common issues |
| `bd ready` | List unblocked issues, ready to work on |
| `bd ready --include-deferred` | Include future-deferred issues |
| `bd ready --json` | Machine-readable |
| `bd show <id>` | View issue detail |
| `bd show <id> --json` | Machine-readable |
| `bd create "title" -t bug\|feature\|task -p 0-4 -d "desc"` | New issue |
| `bd create "task" --due=+6h` | With due date |
| `bd create "task" --defer=tomorrow` | Hidden from `bd ready` until then |
| `bd update <id> --claim` | Assign to current user, set in_progress |
| `bd update <id> --status in_progress` | Change status |
| `bd update <id> --due=+2d` | Set due date |
| `bd update <id> --defer=""` | Clear defer (show immediately) |
| `bd close <id> --reason "Done"` | Close with reason |
| `bd done <id> "Reason"` | Same, terser |
| `bd list --deferred` | Issues with defer set |
| `bd list --defer-before=tomorrow` | Time-windowed list |
| `bd dep add <child-id> <parent-id> --type discovered-from` | Link follow-up work |
| `bd dep tree <id>` | Show dependency tree |
| `bd remember "insight"` | Persistent project memory (DO NOT create `MEMORY.md`) |
| `bd export -o .beads/issues.jsonl` | Export for git commit |
| `bd import .beads/issues.jsonl` | Import after pull |
| `bd stats` | Project progress |
| `bd context` | Safe-first error guidance |
| `bd bootstrap` | Automated recovery |

**Plugin slash commands** (if `/plugin install beads` is run):
| Command | Purpose |
|---|---|
| `/beads:init` | Initialise in current project |
| `/beads:create "title" type priority` | Create issue |
| `/beads:ready` | Find ready work |
| `/beads:show <id>` | Show issue |
| `/beads:update <id> <status>` | Update status |
| `/beads:close <id> "reason"` | Close issue |
| `/beads:workflow` | Show workflow guide |
| `/beads:stats` | Project stats |
| `/beads:version` | Version info |
| `@task-agent` | Autonomous agent — finds ready work, claims, executes, creates discovery issues, closes, repeats |

**Issue types:** `bug`, `feature`, `task`, `chore`, `epic`
**Priorities:** `0` (P0, highest) through `4` (P4, lowest)
**Statuses:** `open` (○), `in_progress` (◐), `blocked` (●), `closed` (✓), `deferred` (❄)
**Dependency types:** `blocks`, `discovered-from`, `parent-of`, `relates-to`

**Critical agent rules:**
1. Use `bd` instead of markdown TODOs. Always.
2. Use `bd remember "insight"` instead of creating `MEMORY.md` files.
3. Only `blocks` dependencies affect the ready queue.
4. When you discover follow-up work, file it: `bd create` + `bd dep add ... --type discovered-from`.
5. Close issues with a real reason, not "done".
6. Run `bd export` before commits, `bd import` after pulls — or use the git hooks in `examples/git-hooks/`.

**Modes:**
- **Maintainer (default):** issues stored in `.beads/`, versioned with code.
- **Contributor (`--contributor`):** open-source contributors who want to track their own tasks without polluting the upstream repo.
- **Stealth (`bd prime --stealth`):** personal tracking on a shared project, no commits.

## Anti-patterns

- ❌ Creating `TODO.md` or `MEMORY.md` files. Use `bd` instead.
- ❌ Forgetting to `bd close`. Zombie in-progress issues block the ready queue.
- ❌ Committing without `bd export`. State is lost.
- ❌ Treating `bd remember` as a chat log. It's for genuine persistent insights only.
