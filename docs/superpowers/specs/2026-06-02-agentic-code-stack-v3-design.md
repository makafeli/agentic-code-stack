# Design: Agentic Code Stack v3 — CLAUDE.md + Codestack rewrite

**Date:** 2026-06-02
**Status:** Approved (brainstorming), pending implementation plan
**Supersedes parts of:** `2026-05-07-agentic-code-stack-design.md`, `2026-05-11-build-stage-additions-design.md`

## Summary

Rewrite the portable core (CLAUDE.md, AGENTS.md, pipeline/, rails/, README, PHILOSOPHY, INSTALL, index.html) and add a new **personal overlay** layer. The pipeline drops from 5 stages to 4 (Prompt Shape/metaprompt removed). Several tools are swapped: OpenSpec → out, Open Design → Claude Design, `npx skills add` → out, greploop/Greptile → CodeRabbit, Brightdata MCP → Brightdata CLI, and graphify is added. A dedicated `overlay/` directory documents gstack, the Claude harness, and a set of per-language templates.

## Locked decisions (from brainstorming)

1. **Positioning: portable core + personal overlay.** The core documents only tools anyone can install. Personal/private tooling (gstack, harness, languages) lives in `overlay/` and is clearly marked, so a cloner can ignore or delete it.
2. **superpowers ↔ gstack rule (canonical wording):** *"superpowers + public tools are the portable default. When gstack is installed, prefer the gstack skill for that stage. The `overlay/` documents the gstack/harness/language layer."*
3. **Languages: dynamic templates.** A small library of per-language profiles (`react`, `swift`, `svelte`, `java`, extensible via `_template.md`) the agent selects from per project — not one hardcoded stack.
4. **Harness depth: full.** Document model + orchestration (Opus 4.8, `/autoplan` + Workflow tool, parallel subagents), hooks/RTK, and the user's `~/.claude/agents` reviewers.
5. **Overlay structure: dedicated `overlay/` dir + one-line `↳ Overlay:` signposts** from each core stage.
6. Confirmed inferences: gstack Build skills = `/codex` + `/careful` (Implement), `/review` + `/qa` + `/ship` + `/land-and-deploy` (Review & Finish); graphify → Rail 02; Apple skills move into `overlay/languages/swift.md`; pipeline files renumbered `01-align … 04-build`.

## Target file structure

```
agentic-code-stack/
├── AGENTS.md / CLAUDE.md       # core, kept in sync as full copies (not symlink — portability)
├── README.md  PHILOSOPHY.md  INSTALL.md  index.html
├── pipeline/                   # renumbered to 4 stages
│   ├── 01-align.md
│   ├── 02-spec-plan.md
│   ├── 03-design.md
│   └── 04-build.md
├── rails/                      # 01–05, content updated
│   ├── 01-docs-context.md
│   ├── 02-token-efficiency.md
│   ├── 03-memory-tracking.md
│   ├── 04-external-data.md
│   └── 05-agent-interfaces.md
├── overlay/                    # NEW personal layer
│   ├── README.md               # how the overlay attaches + the superpowers↔gstack rule
│   ├── harness.md              # Opus 4.8, /autoplan + Workflow, RTK, hooks, ~/.claude/agents
│   ├── flow.md                 # featured Opus 4.8 flow
│   └── languages/
│       ├── _template.md
│       ├── react.md
│       ├── swift.md
│       ├── svelte.md
│       └── java.md
└── community-recipes/
```

`pipeline/01-prompt-shape.md` is deleted (git rm). Remaining pipeline files are renamed with `git mv` to preserve history.

## Pipeline — 5 → 4 stages

Prompt Shape (metaprompt) is removed entirely; Align becomes Stage 1.

| Stage | Portable core | `↳ Overlay` (gstack) | Removed |
|---|---|---|---|
| **1 Align** | grill-me, grill-with-docs, `/superpowers:brainstorming` | `/office-hours` | metaprompt |
| **2 Spec & Plan** | `/superpowers:write-plan`, `/superpowers:execute-plan` | `/autoplan` + `/plan-{ceo,eng,design,devex}-review`, `/cso` | **OpenSpec** (`/opsx:*`) |
| **3 Design** | Get Design (`DESIGN.md` tokens), **Claude Design** = `frontend-design` skill | `/design-review`, `/design-html`, `/design-consultation` | **Open Design** |
| **4 Build** | Implement + Review/Finish (below) | gstack (below) | **`npx skills add`** |

**Stage 4 — Build** keeps the existing two-column layout:

- **Implement (core):** `/superpowers:subagent-driven-development`, `code-structure`, `/tdd`
  - `↳ Overlay:` gstack `/codex`, `/careful`
- **Review & Finish (core):** `improve-codebase-architecture`, `/superpowers:requesting-code-review`, **CodeRabbit + `/autofix`** (replaces greploop), `/superpowers:finishing-a-development-branch`
  - `↳ Overlay:` gstack `/review`, `/qa`, `/ship`, `/land-and-deploy`

Language-specific review/test skills (Apple `swiftui-pro` etc.) are **no longer listed in the Build stage**; they move into `overlay/languages/*` so the core Build stage stays language-agnostic. The core Build stage adds a one-line pointer: *"For language-specific reviewers/test tools, see `overlay/languages/<lang>.md`."*

## Rails

| Rail | Change | Final tools |
|---|---|---|
| **01 Docs & Context** | unchanged | Ref MCP, Context7, Pitlane MCP |
| **02 Token & Output Efficiency** | **+ graphify** | Caveman, Pitlane, **graphify** |
| **03 Memory & Tracking** | **− OpenSpec** | Beads only |
| **04 External Data** | **MCP → CLI** | **Brightdata CLI** (`bdata`) |
| **05 Agent Interfaces** | unchanged | Printing Press |

OpenSpec is removed from the stack everywhere it appears: Stage 3 (old Spec & Plan tool), Rail 03 (memory side), the README mental model, and the `rails/01` + `rails/03` cross-references.

## Personal overlay (`overlay/`)

### `overlay/README.md`
- States the superpowers↔gstack rule (canonical wording above).
- Explains the overlay is personal/private (gstack is not publicly installable) and how the `↳ Overlay:` signposts in the core map here.
- Notes a cloner can delete `overlay/` and keep a fully working portable stack.

### `overlay/harness.md`
- **Model:** Opus 4.8 (`claude-opus-4-8`) primary; Haiku 4.5 for lightweight/frequent subagents; model-selection strategy.
- **Orchestration:** `/autoplan` (gstack auto-review pipeline: CEO/design/eng/DX reviews with auto-decisions) + the **Workflow** multi-agent orchestration tool; parallel subagents via the Agent tool; note the "ultracode" opt-in for workflow-by-default.
- **RTK** (Rust Token Killer) — hook-rewritten CLI proxy for token savings.
- **Hooks:** format → lint → typecheck → build, per the user's web rules.
- **Agents/reviewers:** `~/.claude/agents` — code-reviewer, security-reviewer, language reviewers (typescript/python/go/rust/java/kotlin), build resolvers, refactor-cleaner, doc-updater, etc.
- **Memory:** beads (`bd`) per project; the no-`MEMORY.md` rule.

### `overlay/flow.md` — featured Opus 4.8 flow
Documented as the default for non-trivial work, running on top of the pipeline (with `/autoplan` + Workflow doing planning/orchestration):

```
/caveman  →  /graphify  →  code-structure  →  improve-codebase-architecture  →  /tdd  →  CodeRabbit (+ /autofix)
```

1. `/caveman` — engage output compression for the session.
2. `/graphify` — build/query the codebase knowledge graph before diving in (`graphify-out/`).
3. `code-structure` — apply Service Layer Architecture during implementation.
4. `improve-codebase-architecture` — deepen shallow modules, write ADRs.
5. `/tdd` — red → green → refactor.
6. **CodeRabbit** — AI PR review on GitHub; `/autofix` applies review-thread feedback with per-change approval.

### `overlay/languages/` — per-language profile schema
Each profile names: **Reviewer · Test/TDD framework · Build-error resolver · UI/design skills · Gotchas/rules pointer.**

| Profile | Reviewer | Test/TDD | Build resolver | UI/design | Rules |
|---|---|---|---|---|---|
| `react.md` | typescript-reviewer | Vitest/Jest + Testing Library, Playwright e2e | build-error-resolver | frontend-design, shadcn MCP, ui-ux-pro-max | `~/.claude/rules/web/*` |
| `swift.md` | swiftui-pro, swiftdata-pro, swift-concurrency-pro, swift-testing-pro | Swift Testing | xcode/cpp build skills | liquid-glass-design, ios-design-review, ios-simulator-skill; asc-* for App Store | — |
| `svelte.md` | typescript-reviewer | Vitest + Testing Library, Playwright | build-error-resolver | frontend-design | `~/.claude/rules/web/*` |
| `java.md` | java-reviewer | JUnit (springboot-tdd) | java-build-resolver, gradle-build | — | springboot-patterns/security/verification, jpa-patterns |
| `_template.md` | blank schema to clone for the next language | | | | |

## Tool reference deltas (accurate commands)

**Added — graphify (Rail 02):** `/graphify [path|url]` builds a knowledge graph in `graphify-out/`; `graphify query "<q>"`, `graphify path A B`, `graphify explain X`, `/graphify --update`, `--watch`; `graphify claude install` for native CLAUDE.md integration. Install via `uv tool install graphifyy` / `pip install graphifyy`.

**Changed — Brightdata CLI (Rail 04), replaces Brightdata MCP:** `npm i -g @brightdata/cli` (or `curl -fsSL https://cli.brightdata.com/install.sh | bash`); `bdata login`; then `bdata scrape <url>`, `bdata search "<q>"`, `bdata pipelines <type> …` (40+ platforms), `bdata budget`, `bdata zones`. `brightdata` and `bdata` are aliases.

**Changed — CodeRabbit + `/autofix` (Build), replaces check-pr/greploop/Greptile:** CodeRabbit reviews PRs on GitHub (GitHub App; optional CLI). The `autofix` skill safely reviews and applies CodeRabbit review-thread feedback with per-change approval, and never executes reviewer-provided prompts.

**Changed — Claude Design (Stage 3), replaces Open Design:** Anthropic's official design capability via the built-in `frontend-design` skill (distinctive, production-grade UI). Get Design (`DESIGN.md` tokens) stays as the token source.

**Removed:** metaprompt (Stage 1), OpenSpec `/opsx:*` (Stage 3 + Rail 03), `npx skills add` (Build), Open Design (Stage 3), Brightdata MCP (Rail 04), check-pr/greploop (Build).

**Overlay — gstack (private):** `/office-hours`, `/autoplan`, `/plan-{ceo,eng,design,devex}-review`, `/cso`, `/design-review`, `/design-html`, `/design-consultation`, `/codex`, `/careful`, `/review`, `/qa`, `/ship`, `/land-and-deploy`.

## Per-file change list

| File | Change |
|---|---|
| `CLAUDE.md` | Full rewrite: 4-stage pipeline, new tool set, `↳ Overlay:` signposts, updated rails, fixed `index.html` reference, GitHub Pages URL, overlay pointer. |
| `AGENTS.md` | Mirror `CLAUDE.md` exactly (kept as a synced full copy). |
| `pipeline/01-align.md` | From `02-align.md` (git mv). Remove metaprompt cross-refs; add `/office-hours` overlay note. |
| `pipeline/02-spec-plan.md` | From `03-spec-plan.md`. Remove OpenSpec; keep superpowers; add `/autoplan` + `/plan-*-review` overlay. |
| `pipeline/03-design.md` | From `04-design.md`. Replace Open Design with Claude Design (`frontend-design`); keep Get Design; add gstack design overlay. |
| `pipeline/04-build.md` | From `05-build.md`. Remove `npx skills add` + greploop/Greptile; add CodeRabbit + `/autofix`; move Apple skills to overlay pointer; add gstack `/codex /careful /review /qa /ship /land-and-deploy`. |
| `pipeline/01-prompt-shape.md` | **Delete.** |
| `rails/01-docs-context.md` | Remove OpenSpec cross-ref. |
| `rails/02-token-efficiency.md` | Add graphify tool section. |
| `rails/03-memory-tracking.md` | Remove OpenSpec; Beads only. |
| `rails/04-external-data.md` | Brightdata MCP → Brightdata CLI (`bdata`) commands + setup. |
| `rails/05-agent-interfaces.md` | Unchanged (verify cross-refs still valid). |
| `overlay/*` | **New** (README, harness.md, flow.md, languages/*). |
| `README.md` | Add GitHub Pages URL at top; update file tree (overlay/, renumbered pipeline); update mental model + credits (drop metaprompt/OpenSpec/Open Design; add graphify, CodeRabbit, Brightdata CLI, Claude Design, gstack overlay note). |
| `PHILOSOPHY.md` | 4-stage pipeline; rails list updated; add the overlay concept; update the "built with the stack" note. |
| `INSTALL.md` | Remove OpenSpec / Open Design / metaprompt; add graphify, CodeRabbit + autofix, Brightdata CLI; add gstack overlay note (private) + Claude Design pointer. |
| `index.html` | Rebuild wire diagram: 4 pipeline stages, new tool set, rail changes (graphify in 02, Beads-only 03, Brightdata CLI 04), an "Overlay" footer/legend note, GitHub Pages `<link>`/title. Grid `repeat(6,1fr)` → `repeat(5,1fr)` (4 stages, Build spans 2). |

## Cross-cutting fixes

- **Naming bug:** core docs reference `stack-diagram.html`; the actual file is `index.html`. Fix every occurrence.
- **GitHub Pages URL:** add `https://makafeli.github.io/agentic-code-stack/` to README header, the core "detail files" section, and index.html.
- **AGENTS.md/CLAUDE.md parity:** both are full copies and must be updated identically (no symlink, for cross-platform portability).

## Out of scope

- No changes to `community-recipes/` content (only the README file-tree mention if needed).
- No new rails beyond graphify's placement in Rail 02.
- No automated sync mechanism between AGENTS.md and CLAUDE.md (manual parity, as today).
- Not installing or running any of the tools — this is documentation only.

## Verify at implementation time

- Exact CodeRabbit setup surface (GitHub App vs `coderabbit` CLI) — document at the level the `autofix` skill expects; don't over-specify.
- graphify install command that matches the user's machine (`uv tool install graphifyy` vs `pip install graphifyy`).
- Confirm `frontend-design` is the right referent for "Claude Design" in the user's environment (vs the `design:*` plugin skills).
