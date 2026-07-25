# Rail 03 — Memory & Task Tracking

**Always-on. Solves the "where were we?" problem across sessions.**

LLMs forget. Context windows fill. Sessions end. Markdown TODO files rot in
place and nobody reads them. Track work in a real issue tracker that the whole
team — and every future agent session — can read.

**GitHub Issues. That's the tracker.** No Linear, no `TODO.md`, no `MEMORY.md`.

## Tools

### `gh` CLI

| Command | Purpose |
|---|---|
| `gh issue list` | What's open |
| `gh issue create` | File work |
| `gh issue view <n>` | Read one, `--comments` for the thread |
| `gh issue close <n> --reason completed` | Close with a real reason |
| `gh issue edit <n>` | Update body, labels, assignee |

### GitHub MCP

`list_issues`, `issue_read`, `issue_write`, `sub_issue_write`,
`search_issues`. Prefer these when you want structured results back rather than
text to parse.

**Sub-issues and dependencies** go through the API directly:

```bash
# attach a child issue (note -F, not -f — sub_issue_id must be an integer)
gh api -X POST repos/{owner}/{repo}/issues/{n}/sub_issues -F sub_issue_id=<id>

# declare a blocking relation
gh api -X POST repos/{owner}/{repo}/issues/{n}/dependencies/blocked_by -F issue_id=<id>
```

`-f` sends a string and the API rejects it with a 422. This bites every time.

### Commits close issues

Reference `#<n>` in a commit message and merging closes it. Use it — a closed
issue with a linked commit is a better record than any changelog.

## Wayfinder maps live here too

A `/wayfinder` map is an issue labelled `wayfinder:map`. Its tickets are child
issues labelled `wayfinder:research` / `prototype` / `grilling` / `task`.
Blocking uses GitHub's native dependency relation, which means the frontier
renders in GitHub's own UI — you can see what's takeable without opening the
map.

A ticket is claimed by **assigning it to yourself before starting work**. That
assignee is the claim; an open unassigned ticket is fair game for a concurrent
session.

See [pipeline/01-align.md](../pipeline/01-align.md) for the full mechanics.

## Durable decisions

Issues record *work*. They are a poor home for decisions that outlive it.

- **ADRs** — `manage_adr` in codebase-memory-mcp, or `docs/adr/` in the repo.
- **`CONTEXT.md`** — the project's glossary and ubiquitous language.
  `/grill-with-docs` and `/domain-modeling` both read and update it.

## Agent rules

1. Discovered work mid-task → file an issue, don't inline it into the current
   change.
2. Close with a reason. "Done" is not a reason; "superseded by the peer-tree
   decision" is.
3. Reference the issue number in the commit.
4. Never create a markdown TODO file. If you feel the urge, that's an issue.
5. One decision lives in one place. Link to it; don't restate it.

## Anti-patterns

- ❌ `TODO.md`, `MEMORY.md`, `NOTES.md`, or a task list in a comment block.
- ❌ Closing issues silently at the end of a session.
- ❌ Tracking a multi-session effort as one giant issue instead of a map plus
  tickets.
- ❌ `-f sub_issue_id=...` — it must be `-F`.
