# Rail 03 — Memory & Task Tracking

**Always-on. Solves the "where were we?" problem across sessions.**

LLMs forget. Context windows fill. Sessions end. Markdown TODO files rot. Track work in a real issue tracker that the whole team — and every future agent session — can read, instead of scratch files.

## Tools

Pick the tracker the project already uses. **GitHub Issues** is the portable default; **Linear** suits teams already on it.

### GitHub Issues (portable default)

Native to any GitHub repo — no extra install. Use the `gh` CLI or the GitHub MCP.

**CLI:**
| Command | Purpose |
|---|---|
| `gh issue list` | Open issues, ready to work |
| `gh issue view <n>` | Issue detail |
| `gh issue create --title "..." --body "..."` | New issue |
| `gh issue comment <n> --body "..."` | Add context |
| `gh issue close <n> --comment "..."` | Close with a reason |
| `gh issue develop <n> --checkout` | Branch straight from an issue |

**MCP (`github`):** `list_issues`, `issue_read`, `issue_write`, `search_issues`, `sub_issue_write` — for richer queries, sub-issues, and cross-repo work.

**Conventions:**
- Reference the issue in commits/PRs (`Fixes #42`) so it auto-closes on merge.
- Use labels (`bug`, `feature`, `chore`) and milestones instead of priority files.
- Discover work mid-task → file an issue and link it to the parent.

### Linear (team alternative)

For teams already on Linear. Two ways in:
- **Linear MCP** — read/write issues, cycles, and projects directly.
- **Printing Press `linear` CLI** — compound SQL queries the Linear API can't express, against a local SQLite mirror (~50ms). See Rail 05.

Use Linear issue IDs (`ENG-123`) in branch names and PR titles so its automation links them.

## Agent rules

1. Track work in the project's issue tracker, not `TODO.md` / `MEMORY.md` files.
2. When you discover follow-up work, file it as an issue and link it to the parent.
3. Close issues with a real reason (and the resolving PR), not "done".
4. Reference issues in commits/PRs so they link and auto-close.

## Anti-patterns

- ❌ Creating `TODO.md` / `MEMORY.md`. Use the tracker.
- ❌ Leaving finished work open. Stale issues clog the ready queue.
- ❌ Vague closes ("done") with no reason or link to the resolving PR.

## Cross-reference

- For Linear via a local-mirror CLI → **Printing Press** (Rail 05).
- Durable design *decisions* belong in ADRs (see `improve-codebase-architecture`, Build stage), not the issue tracker.
