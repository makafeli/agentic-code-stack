# Available Skill Stack

You are working on a project with a curated stack of skills, plugins, and MCP servers. Read this at session start so you know the toolbox before reaching for raw bash or naive file reads.

Two parts: a **pipeline** (sequential stages, idea → shipped code) and **rails** (always-on capabilities that tap every stage). A private **overlay** (`overlay/`) layers personal tooling (gstack, harness, language profiles) on top — delete it and the core still works.

**Rule:** superpowers + public tools are the portable default. When **gstack** is installed, prefer the gstack skill for that stage (see `overlay/`).

---

## Pipeline (run roughly left-to-right)

### 1. Align — _before any code_
Reach shared understanding before implementation. Pick one:
- **`/grill-me`** — interrogates you down a decision tree, one question at a time, recommending answers
- **`/grill-with-docs`** — same, checked against CONTEXT.md / ADRs in the repo
- **`/superpowers:brainstorming`** — Socratic discovery that flows into spec → plan → TDD
- `↳ Overlay (gstack):` **`/office-hours`** — YC-style forcing questions for product/idea work

Do **not** skip to code on a vague brief.

### 2. Spec & Plan
Write the aligned idea down so it survives context loss:
- **`/superpowers:write-plan`** — design → junior-engineer-grade task list
- **`/superpowers:execute-plan`** — run the plan with review checkpoints
- `↳ Overlay (gstack):` **`/autoplan`** (auto CEO/design/eng/DX review) · **`/plan-ceo-review` `/plan-eng-review` `/plan-design-review` `/plan-devex-review`** · **`/cso`**

### 3. Design (when UI is involved)
- **Get Design** — if a brand style is named ("make it Stripe-like"), use the exact `DESIGN.md` tokens. Pull with `npx getdesign@latest add <slug>`. Don't invent values.
- **Claude Design** — the built-in **`frontend-design`** skill for distinctive, production-grade UI.
- `↳ Overlay (gstack):` **`/design-review` `/design-html` `/design-consultation`**

### 4. Build
Implement the spec. Discipline > velocity. TDD always.

**Implement**
- **`/superpowers:subagent-driven-development`** — parallel implementation with built-in review
- **`code-structure`** — Service Layer Architecture: actions own product rules, services own reusable mechanics
- **`/tdd`** — failing test first, minimal code, refactor. No exceptions.
- `↳ Overlay (gstack):` **`/codex`** (second engine) · **`/careful`**

**Review & Finish**
- **`improve-codebase-architecture`** — find shallow modules, deepen them, write ADRs
- **Fallow** (`npx fallow`) — static code-health: `dead-code`, `dupes`, `health` (complexity), `fix --dry-run`. `--format json` or the `fallow-mcp` server for agent access
- **`/superpowers:requesting-code-review`** — review against plan; critical issues block
- **CodeRabbit + `/autofix`** — CodeRabbit reviews the PR on GitHub; `/autofix` applies its review threads with per-change approval
- **`/superpowers:finishing-a-development-branch`** — verify tests, then merge / PR / discard
- `↳ Overlay (gstack):` **`/review` `/qa` `/ship` `/land-and-deploy`**

> Language-specific reviewers and test tools live in `overlay/languages/<lang>.md` (React, Swift, Svelte, Java, …).

---

## Rails (always-on, use throughout)

### 01 · Documentation & Code Context
Reach for these **before** raw `grep`, `find`, or whole-file reads.
- **Context7** — `use context7`, or `/context7:docs <query>`; `use library /supabase/supabase`
- **Ref MCP** — auto-invoked for doc lookups (`ref_search_documentation`, `ref_read_url`)
- **Pitlane MCP** — `ensure_project_ready` once at session start, then `investigate`, `locate_code`, `search_content`, `search_files`, `trace_path`, `read_code_unit`. Avoid whole-file reads.

### 02 · Token & Output Efficiency
- **`/caveman`** (`lite` / `full` / `ultra`) — compress prose output ~65–75%; code/commits stay normal. `/caveman-stats` for savings.
- **`/graphify`** — codebase → knowledge graph in `graphify-out/`; `graphify query "<q>"` instead of re-reading files. If `graphify-out/graph.json` exists, treat codebase questions as graph queries.
- **Pitlane MCP** — shrink input: retrieve symbols, not whole files.

### 03 · Memory & Task Tracking
Track work in a real issue tracker, not markdown TODOs. Pick what the project uses:
- **GitHub Issues** (portable default) — `gh issue list/create/view/close`, or the GitHub MCP (`list_issues`, `issue_write`). Reference `#<n>` in commits to auto-close.
- **Linear** — via the Linear MCP or the Printing Press `linear` CLI (local-mirror SQL, Rail 05). Use `ENG-123` IDs in branches/PRs.
- File a follow-up issue when you discover work mid-task; close with a real reason. No `MEMORY.md`.

### 04 · External Data
- **Bright Data CLI (`bdata`)** — `bdata scrape <url>`, `bdata search "<q>"`, `bdata pipelines <type>` (40+ platforms), `bdata budget`. One-time `bdata login`. Use for live web/SERP/platform data.

### 05 · Agent Interfaces
- **Printing Press** — generate agent-native Go CLIs from API specs; library of 70+ pre-built CLIs (Stripe, Linear, Notion, Slack…).

---

## Recommended order of operations

1. Read this file.
2. Non-trivial task → `/grill-me` or `/superpowers:brainstorming` (gstack: `/office-hours`).
3. Capture the plan → `/superpowers:write-plan` (gstack: `/autoplan`).
4. Pull docs through Context7 / Ref before writing library code.
5. Navigate code with Pitlane / `/graphify`, not whole-file reads.
6. Track work as issues (GitHub Issues / Linear); file discovery issues as you go.
7. TDD when implementing; `code-structure` + `improve-codebase-architecture` for structure.
8. `/superpowers:requesting-code-review`, then CodeRabbit + `/autofix` on the PR.
9. `/superpowers:finishing-a-development-branch` and close the issue when done.

> **Personal overlay:** `overlay/` documents the gstack skills, the Claude harness (Opus 4.8 + `/autoplan` + Workflow), and per-language profiles. Featured Opus 4.8 flow: `/caveman → /graphify → code-structure → improve-codebase-architecture → /tdd → Fallow → CodeRabbit`. See `overlay/README.md`.

---

## Detail files

- `pipeline/01-align.md … pipeline/04-build.md`
- `rails/01-docs-context.md … rails/05-agent-interfaces.md`
- `overlay/README.md`, `overlay/harness.md`, `overlay/flow.md`, `overlay/languages/*`
- `INSTALL.md` for one-time setup
- `index.html` for the visual map (open in a browser; hover for commands) — published at https://makafeli.github.io/agentic-code-stack/
