# Role Parity

Six roles, mirrored across both harnesses. This file is the contract between
[`config/claude/`](../claude/) and [`config/codex/`](../codex/): it states the
mapping, and — more importantly — names every place the mapping breaks down.

## The roles

| Role | Claude | Model / effort | Codex | Model / effort | Mode |
|---|---|---|---|---|---|
| Orchestrator | main session, no agent file | `claude-fable-5` | main session, no agent file | `gpt-5.6-sol` / xhigh | — |
| Code discovery, call paths, evidence | `explorer.md` | `claude-sonnet-5` / medium | `explorer.toml` | `gpt-5.6-terra` / medium | read-only |
| Bounded implementation, tests, fixes | `worker.md` | `claude-sonnet-5` / high | `worker.toml` | `gpt-5.6-terra` / high | write |
| Library / API doc verification | `docs-researcher.md` | `claude-sonnet-5` / medium | `docs_researcher.toml` | `gpt-5.6-terra` / medium | read-only |
| Correctness, security, conventions | `standards-reviewer.md` | `claude-opus-5` / **max** | `standards_reviewer.toml` | `gpt-5.6-sol` / xhigh | read-only |
| Spec / ticket / acceptance compliance | `spec-reviewer.md` | `claude-opus-5` / **max** | `spec_reviewer.toml` | `gpt-5.6-sol` / xhigh | read-only |
| Over-engineering, dead paths | `simplification-reviewer.md` | `claude-opus-5` / **max** | `simplification_reviewer.toml` | `gpt-5.6-sol` / xhigh | read-only |

Claude file names use hyphens, Codex uses underscores. That is a harness
convention on each side, not an inconsistency.

## Routing rules

These hold on both sides.

- **Architecture stays on the main thread.** There is no architect subagent.
  Decisions that need the whole picture are made where the whole picture is.
- **One write-owning `worker` per file scope.** Never two writers on the same
  files. This is the single rule most likely to produce a mess when broken.
- **Standards and Spec review run as separate fresh-context agents.** Not one
  agent asked to do both — the two questions interfere.
- **Simplification runs last**, after correctness and spec compliance are
  established. Simplifying code that is still wrong optimizes the wrong thing.
- **Read-only roles must not write**, even when the session's permission mode
  would allow it. `Bash` stays available for tests and logs; treat it as read-only.

## Where the two harnesses genuinely differ

Three asymmetries. Each is a real difference in enforcement, not a naming gap,
and each needs a different mitigation on the Claude side.

### 1. Read-only is enforced on Codex, requested on Claude

Codex sets `sandbox_mode = "read-only"` — the harness refuses the write. Claude
sets `disallowedTools: Write, Edit, NotebookEdit` — the tools are withheld, but
there is no sandbox, so a determined agent can still write via `Bash`.

**Mitigation:** every Claude reviewer's instructions restate the constraint in
prose. Belt and braces, because the braces are the only real ones.

### 2. Claude has no default-subagent-model fallback

Codex sets `default_subagent_model` and `default_subagent_reasoning_effort` in
`[agents]`, so an unpinned dispatch still lands somewhere sane. Claude has no
equivalent: an agent definition without `model:` inherits the **session** model.

**Consequence:** a subagent dispatched from a Fable 5 session with no pin runs
on Fable 5 — the most expensive model in the lineup, for a task that wanted
Sonnet.

**Mitigation:** pin `model:` in every agent's frontmatter, and pass `model:`
explicitly on every `general-purpose` dispatch and every Workflow `agent()` call.

### 3. Claude has no per-session concurrency cap

Codex caps at `max_concurrent_threads_per_session = 4`. Claude has no setting
for this; concurrency is whatever the orchestrator dispatches.

**Mitigation:** none available in config. It is a prompting discipline, not a
guarantee.

## Model constraints that will bite

Both are Opus 5 behaviors that silently degrade a reviewer if ignored.

**Reviewers run at `max`, so reviewers may never disable thinking.**
`thinking: {"type": "disabled"}` with effort `xhigh` or `max` returns a **400**,
enforced per request. This is a breaking change from Opus 4.8, where the two
were independent.

**`max_tokens` caps thinking *plus* response text.** At `xhigh`/`max`, keep it
at 64k or above so thinking and tool calls both fit. Any config carried over
from a thinking-off Opus 4.8 setup needs re-baselining, or you get a
mostly-thinking response truncated with `stop_reason: "max_tokens"`.

## Client floor

**Claude Code ≥ v2.1.220.** Below it, the `opus` alias silently resolved to
`claude-opus-4-8` regardless of what the frontmatter said — so a reviewer you
believed was on Opus 5 was not. The agent files pin the full `claude-opus-5` ID,
which is verified serving correctly at and above that version.

## Refusal fallback

Both Opus 5 and Sonnet 5 ship cybersecurity classifiers and decline with
`stop_reason: "refusal"` — an HTTP **200**, not an error. Fable 5 carries a wider
surface (`cyber`, `bio`, `frontier_llm`, `reasoning_extraction`, `general_harms`).

- Branch on `stop_reason == "refusal"`, never on `stop_details` — it can be `null`.
- `cyber` falls back to `claude-opus-4-8`. This is the only surviving use of 4.8
  anywhere in this stack.
- Other Fable 5 categories fall back to `claude-opus-5`.
- Simplest wiring: `fallbacks: "default"` plus the beta header
  `server-side-fallback-2026-07-01`, which lets Anthropic route per category.
- Configure it on **every** request path. `fallbacks` does not propagate into
  tool-execution model calls.

A refusal before any output is not billed.
