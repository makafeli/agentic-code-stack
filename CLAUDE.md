# Available Skill Stack

You are working on a project that has access to a curated stack of skills, plugins, and MCP servers. Read this file at session start so you know what's in the toolbox before you reach for raw bash or naive file reads.

The stack has two parts: a **pipeline** (sequential stages from idea → shipped code) and **rails** (always-on capabilities that tap into every stage).

---

## Pipeline (run roughly left-to-right)

### 1. Prompt Shape
- The user may have already passed their prompt through **metaprompt.com** before sending it to you. If their first message is unusually structured or specific, that's why. No agent action.

### 2. Align — _before any code_
For any non-trivial change, run a discovery session before implementation. Pick one:
- **`/superpowers:brainstorm`** — Socratic discovery, refines requirements, presents design in chunks for sign-off
- **`/grill-me`** — relentlessly interrogates the user down a decision tree, one question at a time, recommending answers
- **`/grill-with-docs`** — same but checks against existing CONTEXT.md / ADRs in the repo

Do **not** skip to writing code on a vague brief.

### 3. Spec & Plan
Once aligned, write it down:
- **`/opsx:propose "<idea>"`** — generates `proposal.md`, `specs/`, `design.md`, `tasks.md`
- **`/opsx:apply`** — implement against the spec
- **`/opsx:archive`** — archive completed change, update specs
- **`/opsx:onboard`** — first-time walkthrough
- **`/opsx:verify`** — drift check
- **`/superpowers:write-plan`** — turn design into a junior-engineer-grade task list
- **`/superpowers:execute-plan`** — run plan with review checkpoints

### 4. Design (when UI is involved)
- If the user names a brand style ("make it Stripe-like"), look for a `DESIGN.md` in the repo or pull one via `npx getdesign@latest add <slug>`. Use the exact tokens listed (colors, typography, spacing) — do not invent.
- Open Design (`pnpm tools-dev`) is available locally if the user runs it.

### 5. Build
- **`npx skills add`** — install skills into any agent across 55+ platforms; the package manager for the agent skills ecosystem (`npx skills add <repo>`)
- **`/superpowers:subagent-driven-development`** — parallel implementation with built-in review
- **`code-structure`** — Service Layer Architecture: Actions orchestrate domain rules, Service Layer centralizes reusable operational mechanics. Prevents duplicated operational logic
- **`improve-codebase-architecture`** — surface architectural friction: find shallow modules, deepen them, document decisions as ADRs. Uses precise vocabulary: depth, seams, locality, leverage
- **`/superpowers:requesting-code-review`** — review against plan; critical issues block progress
- **`check-pr` / `greploop`** — automated PR review: check for unresolved comments and failing CI, then loop review→fix→re-review until 5/5 confidence
- **`/superpowers:finishing-a-development-branch`** — verify tests pass, then merge / PR / discard
- **TDD is enforced** — write failing test first, then minimal code, then refactor. No exceptions.
- **`/swiftui-pro`** + **`/swiftdata-pro`** + **`/swift-concurrency-pro`** + **`/swift-testing-pro`** — Apple-platform projects only

---

## Rails (always-on, use throughout)

### Documentation & Code Context
Reach for these **before** raw `grep`, `find`, or whole-file reads.
- **Context7** — say `use context7` in your reasoning, or `/context7:docs <query>`. For specific libs: `use library /supabase/supabase`
- **Ref MCP** — auto-invoked when documentation lookups are needed (`ref_search_documentation`, `ref_read_url`)
- **Pitlane MCP** — call `ensure_project_ready` once at session start, then use `investigate`, `locate_code`, `search_content`, `search_files`, `trace_path`, `read_code_unit`. Avoid reading whole files.

### Token & Output Efficiency
- **`/caveman full`** (or `lite` / `ultra`) — compresses your prose output ~65–75% without losing technical accuracy. Engage on long sessions.
- **"stop caveman"** or **"normal mode"** — return to normal output
- **`/caveman-stats`** — see savings
- Note: caveman applies to prose only. Code, commit messages, and PR comments stay normal.

### Memory & Task Tracking
This project uses **beads (`bd`)** for issue tracking instead of markdown TODOs. Persistent across sessions.
- **`bd ready`** — find unblocked work
- **`bd show <id>`** — view an issue
- **`bd update <id> --claim`** — assign to yourself, set in_progress
- **`bd close <id> --reason "..."`** — finish work
- **`bd create "title" -t bug|feature|task -p 0-4`** — new issue
- **`bd remember "insight"`** — persistent project memory (do not create `MEMORY.md` files)
- **`bd dep add <discovered> <parent> --type discovered-from`** — link follow-up work
- **`bd prime`** — get workflow context on session start
- Slash commands also available: `/beads:ready`, `/beads:create`, `/beads:workflow`, `@task-agent`

### External Data
When the task needs live web data:
- **Brightdata MCP** — `search_engine`, `scrape_as_markdown`, `search_engine_batch`, `scrape_batch` (Rapid mode). Pro mode adds 60+ tools for Amazon, LinkedIn, Maps, Reddit, etc.

### Agent Interfaces
- **Rail 05 — Agent Interfaces:** Printing Press (CLI generation from APIs, library of 70+ CLIs)

---

## Recommended order of operations

1. Read this file (you are here).
2. Run `bd prime` for project memory and `bd ready` for current state.
3. If the task is non-trivial: `/superpowers:brainstorm` or `/grill-me`.
4. If it's a feature/change: `/opsx:propose` to capture the spec.
5. Pull docs through Context7/Ref before writing library code.
6. Use Pitlane to navigate code instead of reading whole files.
7. Track work in beads as you go; create discovery issues for things you find.
8. TDD when implementing.
9. `/superpowers:requesting-code-review` before merging.
10. `/opsx:archive` and `bd close` when done.

---

## Detail files

For deeper reference on any tool, see:
- `pipeline/01-prompt-shape.md` … `pipeline/05-build.md` (also covers Vercel Skills, `code-structure`, `improve-codebase-architecture`, `check-pr`, `greploop`)
- `rails/01-docs-context.md` … `rails/05-agent-interfaces.md`
- `INSTALL.md` for one-time setup commands
- `stack-diagram.html` for the visual map (open in a browser; hover for commands)
