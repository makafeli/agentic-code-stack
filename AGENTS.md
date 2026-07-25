# Agentic Code Stack

Read this at session start. It tells you which skill owns which stage of work,
so you reach for the right tool instead of raw `grep` and whole-file reads.

Three layers:

- **Pipeline** — sequential stages, idea → merged code. One golden path.
- **Rails** — always-on capabilities that tap every stage.
- **Config** — how the agents themselves are wired (`config/`). Claude and Codex
  sit side by side as peers, each with its own entry document, subagents, and
  folder structure.

The skill set is **Matt Pocock's**, plus a short list of tools that do something
his set doesn't. If a skill isn't listed here, it isn't part of the stack.

---

## The loop

```text
grill → spec → tickets → implement (TDD) → review → merge → sweep
```

Everything below is that loop at higher resolution. When you don't know where
you are in it, you are at the start: go grill.

---

## Pipeline

### 1. Align — before any code

Reach shared understanding first. Never jump to code on a vague brief.

| Situation | Use |
|---|---|
| Effort too big for one session, route unclear | **`/wayfinder`** — charts a map on GitHub Issues, then works its tickets one at a time |
| A plan or design to stress-test | **`/grill-me`** — interrogates you down a decision tree, one question at a time |
| Same, but checked against repo docs | **`/grill-with-docs`** |
| Terminology, ubiquitous language, an ADR | **`/grilling`** + **`/domain-modeling`** |
| "How should it look / behave?" is the real question | **`/prototype`** — cheap rough artifact to react to |

`/prototype` is available *before* speccing. A rough thing to argue with beats
another round of abstract discussion.

### 2. Spec & Plan

Write the aligned idea down so it survives context loss.

- **`/wayfinder`** — the map is the plan for anything multi-session. Tickets are
  child issues; the map indexes decisions and links to them.
- **`/improve`** — audits the codebase and emits prioritized, self-contained
  plans for a cheaper model to execute (`/improve execute <plan>`). Variants:
  `quick`, `security`. Strictly read-only on source.
- **`/codebase-design`** — design work that has to fit existing structure.

### 3. Design — when UI is involved

- **`/design-an-interface`** — interface design as a first-class step.
- **`frontend-design`** — the built-in skill, for distinctive production UI.
- **Get Design** — when a brand style is named ("make it Stripe-like"), pull the
  exact tokens with `npx getdesign@latest add <slug>`. Don't invent values.

### 4. Build

TDD always. Discipline over velocity.

**Implement**

- **`/tdd`** — failing test first, minimal code, refactor. No exceptions.
- **`code-structure`** — Service Layer Architecture: actions own product rules,
  services own reusable mechanics.
- **`/diagnose`** — the diagnosis loop for hard bugs and performance
  regressions. Reach for it before guessing.

**Review & finish**

Run in this order. Each gate assumes the one before it passed.

1. **`/code-review`** — Standards and Spec in parallel sub-agents, reported side
   by side. The two questions interfere when one agent is asked both.
2. **`/ponytail-review`** — over-engineering only. What to delete, not what to
   add. Runs after correctness, because simplifying wrong code optimizes the
   wrong thing.
3. **Fallow** — `npx fallow health` / `dead-code` / `dupes`. Static, cheap,
   `--format json` for agent consumption.
4. **CodeRabbit + `/autofix`** — CodeRabbit reviews the PR on GitHub; `/autofix`
   applies its threads with per-change approval.

**Sweep** — `/ponytail-audit` and `/deadcode` on a whole repo, periodically or
after a phase lands.

---

## Rails — always-on

### 01 · Docs & Code Context

Reach for these **before** raw `grep`, `find`, or whole-file reads.

- **codebase-memory-mcp** — `index_repository` once, then `search_graph`,
  `trace_path`, `get_code_snippet`, `query_graph`, `get_architecture`,
  `search_code`. Retrieve symbols, not files.
- **Context7** — `use context7`, or `use library /supabase/supabase`. Library
  and framework docs, current rather than remembered.

Grep and Glob stay fine for text, configs, and non-code files. Always read a
file before editing it.

### 02 · Token & Output Efficiency

- **`/caveman`** (`lite` / `full` / `ultra`) — compresses prose ~65–75%. Code
  and commit messages stay normal.
- **`/ponytail`** (`lite` / `full` / `ultra`) — the code-side counterpart. Lazy
  senior dev: stdlib and native platform features before dependencies, shortest
  working diff.
- **codebase-memory-mcp** — the biggest single lever. Symbol retrieval instead
  of file reads.

### 03 · Memory & Task Tracking

**GitHub Issues.** Not markdown TODOs, not `MEMORY.md`.

- `gh issue list/create/view/close`, or the GitHub MCP (`list_issues`,
  `issue_write`, `sub_issue_write`).
- Reference `#<n>` in commits to auto-close.
- `/wayfinder` maps and their tickets live here too — a map is an issue labelled
  `wayfinder:map`, its tickets are child issues.
- File a follow-up issue when you discover work mid-task. Close with a real
  reason.

### 04 · External Data

- **Bright Data CLI (`bdata`)** — `bdata scrape <url>`, `bdata search "<q>"`,
  `bdata pipelines <type>` (40+ platforms), `bdata budget`.
- **Webshare** — the proxy pool behind it. The `proxy-manager` skill handles
  provisioning, rotation, and replacing dead proxies.

### 05 · Agent Interfaces

- **Printing Press** — generate agent-native Go CLIs from API specs; library of
  70+ pre-built CLIs.

---

## Config

`config/` holds the harness itself — read it when the question is about *how the
agents are wired*, not what work they do.

- [`config/README.md`](config/README.md) — why two peer trees instead of one merged one.
- [`config/shared/role-parity.md`](config/shared/role-parity.md) — the six roles,
  both harnesses, and the three places the mapping genuinely breaks down.
- [`config/shared/external-tools.md`](config/shared/external-tools.md) — every
  upstream, pinned.

### Model policy

| Where | Model | Effort |
|---|---|---|
| Main loop / orchestrator | `claude-fable-5` | high |
| All implementation subagents | `claude-sonnet-5` | high (medium for read-only discovery) |
| All review and audit subagents | `claude-opus-5` | **max** |

Fable 5 is **main-loop only** — never a subagent. Claude has no
`default_subagent_model` fallback, so an unpinned agent inherits the *session*
model: pass `model:` explicitly on every dispatch and every Workflow `agent()`
call. No Haiku in this stack.

Reviewers run at `max`, and on Opus 5 `thinking: {"type": "disabled"}` with
effort `xhigh` or `max` is a **400**. Reviewers never disable thinking. Keep
`max_tokens` at 64k or above so thinking and tool calls both fit.

Per-model prompting rules live in
[`config/claude/rules/common/`](config/claude/rules/common/): `fable-5.md`,
`sonnet-5.md`, `opus-5.md`, `performance.md`. Match the effort and thinking
config to the model actually in use before applying older prompt patterns.

---

## Order of operations

1. Read this file.
2. Non-trivial work → `/wayfinder` if it needs a map, `/grill-me` if it needs a
   decision tree. `/prototype` when the question is really about shape.
3. Pull library docs through Context7 before writing code against them.
4. Navigate with codebase-memory-mcp, not whole-file reads.
5. Track everything as GitHub Issues. File discovery issues as you go.
6. `/tdd` to implement. `code-structure` for where things belong.
7. `/code-review` → `/ponytail-review` → Fallow → CodeRabbit + `/autofix`.
8. Merge, close the issue, sweep with `/ponytail-audit`.

---

## Detail files

- `pipeline/01-align.md` … `pipeline/04-build.md`
- `rails/01-docs-context.md` … `rails/05-agent-interfaces.md`
- `config/README.md`, `config/shared/role-parity.md`, `config/shared/external-tools.md`
- `INSTALL.md` for one-time setup
- `index.html` — visual map, published at https://makafeli.github.io/agentic-code-stack/
