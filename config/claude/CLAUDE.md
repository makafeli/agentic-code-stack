# Global Claude guidance

Applies to every repository. Repository-level `CLAUDE.md` files add or override
project-specific conventions.

Peer of [`config/codex/AGENTS.md`](../codex/AGENTS.md) — same scope, same rules,
expressed for this harness.

## Code discovery

When the current repository has a ready codebase-memory-mcp index, prefer its
graph tools for code discovery:

1. `search_graph` for functions, classes, routes, and variables.
2. `trace_path` for callers, callees, dependencies, and data flow.
3. `get_code_snippet` for source after resolving the exact symbol.
4. `query_graph` for complex structural queries.
5. `get_architecture` for a high-level map.

Use `Grep`, `Glob`, and `Read` for string literals, error messages,
configuration, scripts, generated files, or when the graph is insufficient.
Check `index_status` before relying on an unfamiliar repository index. Run
`index_repository` if the project is not indexed.

Always `Read` a file before editing it. The graph tells you where something is,
not what the current bytes are.

## Subagent routing

Agent definitions live in `~/.claude/agents/`, with model and effort pinned in
frontmatter. Project instructions decide *when* to use which agent; they do not
decide the model.

| Work | Agent | Model / effort | Mode |
|---|---|---|---|
| Orchestration, architecture, diagnosis | main session | `claude-fable-5` | — |
| Code discovery, call paths, evidence | `explorer` | sonnet / medium | no-write |
| Bounded implementation, tests, fixes | `worker` | sonnet / high | write |
| Library and API doc verification | `docs-researcher` | sonnet / medium | no-write |
| Correctness, security, conventions | `standards-reviewer` | `claude-opus-5` / max | no-write |
| Spec and acceptance compliance | `spec-reviewer` | `claude-opus-5` / max | no-write |
| Over-engineering, dead paths | `simplification-reviewer` | `claude-opus-5` / max | no-write |

Rules:

- **Architecture stays on the main thread.** No architect subagent for decisions.
- **One write-owning `worker` per file scope.** Never two writers on the same
  files.
- **Standards and Spec review are separate fresh-context agents.**
  Simplification runs after both pass.
- **Fable 5 is main-loop only** — never a subagent.
- **Always pin `model:`.** An agent with no `model:` inherits the *session*
  model. Pass `model:` explicitly on `general-purpose` dispatches and Workflow
  `agent()` calls too.
- Opus-tier agents pin the full `claude-opus-5` ID. Requires Claude Code
  ≥ `2.1.220`; below that the `opus` alias silently resolved to
  `claude-opus-4-8`.
- Do **not** set `CLAUDE_CODE_SUBAGENT_MODEL` — it overrides all frontmatter
  routing. Resolution order is env → per-dispatch `model` → frontmatter →
  inherit.
- No-write roles are enforced by `disallowedTools: Write, Edit, NotebookEdit`
  plus instructions. `Bash` stays available for tests and logs — treat it as
  read-only there.

## Model constraints

Reviewers run at `effort: max`. On Opus 5, `thinking: {"type": "disabled"}` with
effort `xhigh` or `max` returns a **400**, so reviewers never disable thinking.
`max_tokens` caps thinking plus response text together — keep it at 64k or above
at those effort levels.

Per-model prompting rules live in [`rules/common/`](rules/common/): `fable-5.md`,
`sonnet-5.md`, `opus-5.md`, `performance.md`.

## Refusals

Opus 5 and Sonnet 5 decline on cybersecurity with `stop_reason: "refusal"` — an
HTTP 200, not an error. Fable 5 carries a wider surface. Branch on `stop_reason`,
never on `stop_details` (it can be `null`). `cyber` falls back to
`claude-opus-4-8`; other Fable 5 categories fall back to `claude-opus-5`.
Configure fallback on every request path — it does not propagate into
tool-execution model calls.
