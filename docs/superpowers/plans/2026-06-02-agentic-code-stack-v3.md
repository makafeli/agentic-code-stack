# Agentic Code Stack v3 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rewrite the portable core (CLAUDE.md, AGENTS.md, pipeline/, rails/, README, PHILOSOPHY, INSTALL, index.html) and add a personal `overlay/` layer, per the approved spec.

**Architecture:** Pipeline drops 5→4 stages (metaprompt removed). Tool swaps: OpenSpec→out, Open Design→Claude Design, `npx skills add`→out, greploop→CodeRabbit+`/autofix`, Brightdata MCP→Brightdata CLI, +graphify. New `overlay/` documents gstack, harness, and per-language profiles. Each core stage signposts its gstack overlay.

**Tech Stack:** Markdown + a single static `index.html` (no build system, no test runner). "Tests" are `grep`/`diff`/`ls` verification commands.

**Spec:** `docs/superpowers/specs/2026-06-02-agentic-code-stack-v3-design.md`

**Conventions for every task:** run all commands from the repo root `"/Users/yasin.boelhouwer/Projecten/Claude Code Stack"`. Attribution is disabled globally — commit messages carry no `Co-Authored-By` line. Commit with `--no-verify` only if a hook blocks (the repo has none expected). Do not push.

---

## Task 1: Restructure pipeline files (rename + delete)

**Files:**
- Delete: `pipeline/01-prompt-shape.md`
- Rename: `pipeline/02-align.md` → `pipeline/01-align.md`
- Rename: `pipeline/03-spec-plan.md` → `pipeline/02-spec-plan.md`
- Rename: `pipeline/04-design.md` → `pipeline/03-design.md`
- Rename: `pipeline/05-build.md` → `pipeline/04-build.md`

- [ ] **Step 1: Perform the deletion and renames with git (preserves history)**

```bash
git rm pipeline/01-prompt-shape.md
git mv pipeline/02-align.md   pipeline/01-align.md
git mv pipeline/03-spec-plan.md pipeline/02-spec-plan.md
git mv pipeline/04-design.md  pipeline/03-design.md
git mv pipeline/05-build.md   pipeline/04-build.md
```

- [ ] **Step 2: Verify the new structure**

Run: `ls pipeline/`
Expected exactly:
```
01-align.md
02-spec-plan.md
03-design.md
04-build.md
```

- [ ] **Step 3: Commit**

```bash
git add -A pipeline/
git commit -m "refactor: drop Prompt Shape stage, renumber pipeline 01-align..04-build"
```

---

## Task 2: Rewrite `pipeline/01-align.md`

The file (formerly `02-align.md`) already covers grill-me / grill-with-docs / superpowers brainstorm. Remove any "Prompt Shape / metaprompt" references and add the gstack `/office-hours` overlay signpost.

**Files:**
- Modify: `pipeline/01-align.md`

- [ ] **Step 1: Update the stage number/heading**

Replace the top heading:

old:
```
# Stage 2 — Align
```
new:
```
# Stage 1 — Align
```

- [ ] **Step 2: Add the gstack overlay signpost after the "Superpowers brainstorm" section**

Find the end of the `### Superpowers brainstorm — \`obra/superpowers\`` section (just before `## When to use which`) and insert:

```markdown
### `↳ Overlay (gstack)` — Office Hours

If gstack is installed, **`/office-hours`** runs YC-style forcing questions (startup mode) or design-thinking brainstorming (builder mode) and saves a design doc. Use it for product/idea-level discovery; use grill-me / brainstorm for implementation-level alignment. See `overlay/README.md`.
```

- [ ] **Step 3: Fix the "flow into a full plan" cross-reference**

In the `## When to use which` table, ensure the brainstorm row references the renumbered Spec & Plan stage. Replace any mention of "Stage 2 (Align)" elsewhere with "Align"; the metaprompt fallback line (if present) is removed.

- [ ] **Step 4: Verify**

Run: `grep -in "metaprompt\|stage 2\|prompt shape" pipeline/01-align.md`
Expected: no output (exit 1).
Run: `grep -c "office-hours" pipeline/01-align.md`
Expected: `1` or more.

- [ ] **Step 5: Commit**

```bash
git add pipeline/01-align.md
git commit -m "docs(pipeline): align stage 1, add gstack /office-hours overlay"
```

---

## Task 3: Rewrite `pipeline/02-spec-plan.md` (remove OpenSpec)

**Files:**
- Modify: `pipeline/02-spec-plan.md`

- [ ] **Step 1: Update heading**

old: `# Stage 3 — Spec & Plan` → new: `# Stage 2 — Spec & Plan`

- [ ] **Step 2: Delete the entire OpenSpec section**

Remove everything from the line `### OpenSpec — \`Fission-AI/OpenSpec\`` up to (but not including) `### Superpowers planning — \`obra/superpowers\``. This deletes the `/opsx:*` command table and the `openspec/` file-layout block.

- [ ] **Step 3: Replace the "When to use which" table and cross-reference**

old (the whole `## When to use which` table referencing OpenSpec) → new:

```markdown
## When to use which

| Situation | Tool |
|---|---|
| Single-session feature | `/superpowers:write-plan` then `/superpowers:execute-plan` |
| Already running Superpowers from Align | Continue with Superpowers |
| Large multi-review plan, on this machine | `↳ Overlay (gstack):` `/autoplan` |

## `↳ Overlay (gstack)` — autoplan + plan reviews

If gstack is installed, **`/autoplan`** expands a one-line prompt into a full plan and runs CEO / design / eng / DX reviews with auto-decisions, surfacing only taste calls at a final gate. The individual reviews — **`/plan-ceo-review`**, **`/plan-eng-review`**, **`/plan-design-review`**, **`/plan-devex-review`** — and **`/cso`** can be run on their own. On Opus 4.8 this pairs with the **Workflow** orchestration tool. See `overlay/harness.md`.
```

- [ ] **Step 4: Remove the old beads/OpenSpec cross-reference**

Delete the `## Cross-reference` block that says "OpenSpec's tasks can be tracked as beads". Replace with:

```markdown
## Cross-reference

Plan tasks can be tracked as **beads** (see `rails/03-memory-tracking.md`) so progress survives across sessions.
```

- [ ] **Step 5: Verify**

Run: `grep -in "openspec\|opsx\|fission" pipeline/02-spec-plan.md`
Expected: no output (exit 1).
Run: `grep -c "autoplan" pipeline/02-spec-plan.md`
Expected: `1` or more.

- [ ] **Step 6: Commit**

```bash
git add pipeline/02-spec-plan.md
git commit -m "docs(pipeline): remove OpenSpec, add gstack /autoplan overlay to Spec & Plan"
```

---

## Task 4: Rewrite `pipeline/03-design.md` (Open Design → Claude Design)

**Files:**
- Modify: `pipeline/03-design.md`

- [ ] **Step 1: Update heading**

old: `# Stage 4 — Design` → new: `# Stage 3 — Design`

- [ ] **Step 2: Replace the "Open Design" section with "Claude Design"**

Remove the entire `### Open Design — \`nexu-io/open-design\`` section (including its Setup, "What it produces", and "Built-in guardrails" subsections) and replace with:

```markdown
### Claude Design — built-in `frontend-design`

Anthropic's official design capability, available as the built-in **`frontend-design`** skill. Use it to create distinctive, production-grade interfaces where visual direction matters as much as code quality. It pairs with Get Design: pull the brand tokens with Get Design, then let `frontend-design` compose the UI.

**Trigger:** describe the surface to build ("a pricing page", "a dashboard hero") — the skill engages when visual quality matters.

**Keep these guardrails in any project (good rules regardless of tool):**
- Discovery first — pin surface, audience, tone, brand before writing a pixel.
- No AI slop — no purple gradients, generic emoji icons, rounded-card-with-left-border, or Inter-as-display-font defaults.
- Honest placeholders — write `—` or a labelled grey block, never fabricated data.
```

- [ ] **Step 3: Add the gstack overlay signpost**

After the new Claude Design section, before `## Agent rules for this stage`, insert:

```markdown
### `↳ Overlay (gstack)`

If gstack is installed: **`/design-review`** (audits a built UI), **`/design-html`** (generates HTML mockups), **`/design-consultation`** (direction before building). See `overlay/README.md`.
```

- [ ] **Step 4: Fix the closing cross-reference**

old (the `## Cross-reference` paragraph mentioning frontend-design "built into Claude.ai's environment") → new:

```markdown
## Cross-reference

`frontend-design` (Claude Design, above) is the primary UI skill. For per-language UI tooling (shadcn for React, liquid-glass for Apple), see `overlay/languages/<lang>.md`.
```

- [ ] **Step 5: Verify**

Run: `grep -in "open design\|nexu-io\|pnpm tools-dev" pipeline/03-design.md`
Expected: no output (exit 1).
Run: `grep -c "frontend-design\|Claude Design" pipeline/03-design.md`
Expected: `2` or more.

- [ ] **Step 6: Commit**

```bash
git add pipeline/03-design.md
git commit -m "docs(pipeline): replace Open Design with Claude Design, add gstack design overlay"
```

---

## Task 5: Rewrite `pipeline/04-build.md` (npx skills + greploop out; CodeRabbit in; Apple → overlay)

**Files:**
- Modify: `pipeline/04-build.md`

- [ ] **Step 1: Update heading**

old: `# Stage 5 — Build` → new: `# Stage 4 — Build`

- [ ] **Step 2: Remove the Apple-platform skills section, replace with an overlay pointer**

Delete the `### Apple-platform skills — \`twostraws\`` section (its command tables and partial-review examples). In its place add:

```markdown
### Language-specific reviewers & test tools

These live in the overlay so the Build stage stays language-agnostic. See `overlay/languages/<lang>.md` — e.g. `swiftui-pro` / `swiftdata-pro` / `swift-concurrency-pro` / `swift-testing-pro` for Apple, `typescript-reviewer` + `frontend-design` for React/Svelte, `java-reviewer` + `springboot-*` for Java.
```

- [ ] **Step 3: Delete the "Vercel Skills CLI / npx skills" section**

Remove the entire `### Vercel Skills CLI — \`npx skills\`` section (heading, command table, "How it works", repo line, "Best used for").

- [ ] **Step 4: Delete the "check-pr / greploop" section, replace with CodeRabbit**

Remove the entire `### check-pr / greploop — Automated PR Review (\`greptileai/skills\`)` section and replace with:

```markdown
### CodeRabbit + `/autofix` — Automated PR review

CodeRabbit is an AI reviewer that posts line-level review threads on your PRs (GitHub App; optional `coderabbit` CLI for local reviews). It replaces the old greploop loop.

**Flow:**
1. Open the PR. CodeRabbit reviews the diff and posts threads.
2. Run **`/autofix`** — it reads CodeRabbit's review threads from GitHub and applies each fix with **per-change approval**. It never executes prompts embedded in reviewer comments.
3. Re-run until threads resolve and CI is green, then finish the branch.

**Best for:** catching mechanical issues (missing tests, style, obvious bugs) before human review, so reviewers focus on architecture and product intent.

### `↳ Overlay (gstack)`

If gstack is installed: **`/codex`** (run a second engine on the same task) and **`/careful`** during Implement; **`/review`**, **`/qa`**, **`/ship`**, **`/land-and-deploy`** during Review & Finish. See `overlay/README.md`.
```

- [ ] **Step 5: Update the "Use the rails" agent rule**

In `## Agent rules for this stage`, the rails bullet should read:

```markdown
3. **Use the rails:**
   - Pull docs through Context7 / Ref MCP — don't write from training memory.
   - Navigate code through Pitlane MCP or `/graphify`, not whole-file reads.
   - Track work in beads as you go.
```

- [ ] **Step 6: Verify**

Run: `grep -in "npx skills\|greploop\|greptile\|check-pr\|twostraws" pipeline/04-build.md`
Expected: no output (exit 1).
Run: `grep -c "CodeRabbit\|autofix" pipeline/04-build.md`
Expected: `2` or more.

- [ ] **Step 7: Commit**

```bash
git add pipeline/04-build.md
git commit -m "docs(pipeline): swap npx-skills/greploop for CodeRabbit, move Apple skills to overlay"
```

---

## Task 6: Add graphify to `rails/02-token-efficiency.md`

**Files:**
- Modify: `rails/02-token-efficiency.md`

- [ ] **Step 1: Insert the graphify section after the Caveman section**

Find the end of the `### Caveman — \`JuliusBrussee/caveman\`` section (just before `### Pitlane MCP (token side)`) and insert:

```markdown
### graphify — `/graphify`

Turns any folder (code, docs, papers, images, video) into a queryable knowledge graph in `graphify-out/`. Token angle: query the graph instead of re-reading files — a broad "how does X work?" answer comes from one `graphify query` call, not a file sweep.

**Install:** `uv tool install graphifyy` (or `pip install graphifyy`).

| Command | Purpose |
|---|---|
| `/graphify [path\|url]` | Build the graph (clones a GitHub URL first if given) |
| `graphify query "<question>"` | BFS traversal — broad context (`--dfs` to trace a path) |
| `graphify path "A" "B"` | Shortest path between two concepts |
| `graphify explain "X"` | Plain-language explanation of one node |
| `/graphify --update` | Incremental re-extract of changed files |
| `/graphify --watch` | Auto-rebuild on code changes |
| `graphify claude install` | Write an always-on `## graphify` block into the project `CLAUDE.md` |

**Agent rule:** if `graphify-out/graph.json` exists and the user asks a codebase question, run `graphify query` directly — don't re-extract, don't read whole files.
```

- [ ] **Step 2: Verify**

Run: `grep -c "graphify" rails/02-token-efficiency.md`
Expected: `5` or more.

- [ ] **Step 3: Commit**

```bash
git add rails/02-token-efficiency.md
git commit -m "docs(rails): add graphify to token efficiency rail"
```

---

## Task 7: Strip OpenSpec from `rails/03-memory-tracking.md`

**Files:**
- Modify: `rails/03-memory-tracking.md`

- [ ] **Step 1: Delete the OpenSpec subsection**

Remove the `### OpenSpec (memory side)` section entirely.

- [ ] **Step 2: Delete the synergy section**

Remove the `## Synergy: beads + OpenSpec` section (heading and its bullet list / "typical flow" paragraph).

- [ ] **Step 3: Verify**

Run: `grep -in "openspec\|opsx" rails/03-memory-tracking.md`
Expected: no output (exit 1).
Run: `grep -c "bd ready\|beads" rails/03-memory-tracking.md`
Expected: `1` or more (Beads content intact).

- [ ] **Step 4: Commit**

```bash
git add rails/03-memory-tracking.md
git commit -m "docs(rails): remove OpenSpec from memory rail, beads-only"
```

---

## Task 8: Rewrite `rails/04-external-data.md` (Brightdata MCP → CLI)

**Files:**
- Modify: `rails/04-external-data.md` (full replacement)

- [ ] **Step 1: Replace the entire file with the CLI version**

```markdown
# Rail 04 — External Data

**Engaged on demand. Use when training data is stale or missing.**

For tasks that need live web content, SERP results, or platform-specific data (Amazon, LinkedIn, Maps, Reddit, etc.).

## Tool

### Bright Data CLI (`bdata`)

Terminal access to Bright Data's web platform — auth, proxy zones, anti-bot bypass, CAPTCHA solving, and JS rendering are handled automatically. `brightdata` and `bdata` are aliases.

**Install:**
```bash
curl -fsSL https://cli.brightdata.com/install.sh | bash   # macOS / Linux
# or
npm install -g @brightdata/cli                            # any platform (Node >= 20)
# or one-off
npx --yes --package @brightdata/cli brightdata <command>
```

**One-time auth:**
```bash
bdata login            # OAuth in browser; saves key, auto-creates proxy zones
bdata login --device   # headless / SSH
bdata config           # verify
```

**Core commands:**
| Command | Purpose |
|---|---|
| `bdata scrape <url>` | Scrape a URL — markdown (default), `-f html`, `-f json`, or `-f screenshot -o page.png` |
| `bdata search "<query>"` | SERP search (Google/Bing/Yandex); `--json` to pipe; `--engine bing`; `--country de` |
| `bdata pipelines <type> [params]` | Structured extraction from 40+ platforms (LinkedIn, Amazon, Instagram, TikTok, YouTube, Reddit, Maps…) |
| `bdata pipelines list` | List all pipeline types |
| `bdata status <job-id> --wait` | Poll an async job |
| `bdata budget` | Account balance and costs |
| `bdata zones` | List proxy zones |

**Examples:**
```bash
bdata scrape https://example.com -o page.md
bdata search "web scraping best practices" --json | jq -r '.organic[].link'
bdata pipelines linkedin_person_profile "https://linkedin.com/in/username"
bdata pipelines amazon_product "https://amazon.com/dp/B09V3KXJPB" --format csv -o product.csv
```

**Output:** default human-readable; `--json` compact, `--pretty` indented, `-o <path>` writes to file (format from extension). Pipe-friendly — colors/spinners auto-off when stdout isn't a TTY.

## When to engage

- ✅ Competitor research, pricing analysis, lead/account research, SERP monitoring, real-time market data
- ❌ Anything the agent's existing browse tool can already fetch — try that first
- ❌ Personal-data scraping that violates ToS or privacy law

## Compliance

Scrape only publicly accessible data. Respect `robots.txt` where instructed. Never aggregate personal data without explicit direction. Never circumvent paywalls or auth walls. Surface privacy concerns before bulk operations.

## Cross-reference

- Documentation lookups → **Ref MCP** / **Context7** (Rail 01), not Bright Data.
- Codebase navigation → **Pitlane MCP** (Rail 01).
- Bright Data is for the *external* web only.
- Structured service interaction (CLI access to APIs) → **Printing Press** (Rail 05).
```

- [ ] **Step 2: Verify**

Run: `grep -in "mcp\|sse\|claude mcp add" rails/04-external-data.md`
Expected: no output (exit 1).
Run: `grep -c "bdata" rails/04-external-data.md`
Expected: `5` or more.

- [ ] **Step 3: Commit**

```bash
git add rails/04-external-data.md
git commit -m "docs(rails): replace Brightdata MCP with Brightdata CLI"
```

---

## Task 9: Fix rail cross-references (rails/01, rails/05)

**Files:**
- Modify: `rails/01-docs-context.md`
- Modify: `rails/05-agent-interfaces.md`

- [ ] **Step 1: Scan rails/01 for OpenSpec mentions**

Run: `grep -in "openspec\|opsx" rails/01-docs-context.md`
If any match, remove those lines. (Expected: none — this is a safety check.)

- [ ] **Step 2: Update Brightdata naming in rails/05 cross-references**

In `rails/05-agent-interfaces.md`, replace each `Brightdata MCP` / `Brightdata` reference in the "When to engage" and "Cross-reference" sections with `Bright Data CLI`:

old: `Read-only web scraping (use Brightdata — Rail 04)` → new: `Read-only web scraping (use the Bright Data CLI — Rail 04)`
old: `For read-only web data retrieval → use **Brightdata MCP** (Rail 04), not Printing Press` → new: `For read-only web data retrieval → use the **Bright Data CLI** (Rail 04), not Printing Press`

- [ ] **Step 3: Verify**

Run: `grep -in "brightdata mcp" rails/05-agent-interfaces.md`
Expected: no output (exit 1).

- [ ] **Step 4: Commit**

```bash
git add rails/01-docs-context.md rails/05-agent-interfaces.md
git commit -m "docs(rails): fix Brightdata CLI cross-references"
```

---

## Task 10: Create `overlay/README.md`

**Files:**
- Create: `overlay/README.md`

- [ ] **Step 1: Write the file**

```markdown
# Personal Overlay

The portable core of this stack (`CLAUDE.md`, `pipeline/`, `rails/`) documents only tools anyone can install. This `overlay/` is the personal layer — gstack (a private skill suite), the Claude harness, and per-language profiles specific to this machine.

**Cloning this repo? Delete `overlay/` and the core still works as a standalone portable stack.**

## The rule: superpowers ↔ gstack

> superpowers + public tools are the **portable default**. When **gstack** is installed, prefer the gstack skill for that stage. This `overlay/` documents the gstack / harness / language layer.

Each core pipeline stage carries a one-line `↳ Overlay:` signpost pointing here.

| Core stage | Portable default | `↳ Overlay` (gstack) |
|---|---|---|
| Align | grill-me, grill-with-docs, `/superpowers:brainstorming` | `/office-hours` |
| Spec & Plan | `/superpowers:write-plan`, `/superpowers:execute-plan` | `/autoplan`, `/plan-{ceo,eng,design,devex}-review`, `/cso` |
| Design | Get Design, Claude Design (`frontend-design`) | `/design-review`, `/design-html`, `/design-consultation` |
| Build · Implement | subagent-driven-development, code-structure, `/tdd` | `/codex`, `/careful` |
| Build · Review & Finish | improve-codebase-architecture, requesting-code-review, CodeRabbit + `/autofix` | `/review`, `/qa`, `/ship`, `/land-and-deploy` |

## Contents

- `harness.md` — model, orchestration, hooks, agents.
- `flow.md` — the default Opus 4.8 working flow.
- `languages/` — per-language profiles the agent selects from per project (`react`, `swift`, `svelte`, `java`, + `_template.md`).
```

- [ ] **Step 2: Verify**

Run: `test -f overlay/README.md && grep -c "superpowers ↔ gstack\|↳ Overlay" overlay/README.md`
Expected: `1` or more.

- [ ] **Step 3: Commit**

```bash
git add overlay/README.md
git commit -m "docs(overlay): add overlay README with superpowers/gstack rule"
```

---

## Task 11: Create `overlay/harness.md`

**Files:**
- Create: `overlay/harness.md`

- [ ] **Step 1: Write the file**

```markdown
# Claude Harness

How this machine is wired. Assume these defaults unless a project overrides them.

## Model & orchestration

- **Primary model:** Opus 4.8 (`claude-opus-4-8`) for main development and orchestration.
- **Lightweight subagents:** Haiku 4.5 (`claude-haiku-4-5-20251001`) for frequent, narrow workers.
- **Planning:** `/autoplan` — gstack's auto-review pipeline. Expands a plan and runs CEO / design / eng / DX reviews with auto-decisions, surfacing only taste calls at a final gate.
- **Multi-agent orchestration:** the **Workflow** tool — deterministic fan-out / pipeline of subagents for comprehensive sweeps, adversarial verification, and work too large for one context. With "ultracode" on, author a workflow per substantive phase by default.
- **Parallel subagents:** dispatch independent work with the Agent tool in a single message so it runs concurrently.

## Token efficiency

- **RTK (Rust Token Killer):** a hook-rewritten CLI proxy (`rtk`) wrapping dev commands for 60–90% token savings — transparent (`git status` → `rtk git status`). `rtk gain` shows savings.
- **Caveman** + **graphify** + **Pitlane** — see Rails 01–02.

## Hooks (recommended order)

format → lint → typecheck → build, on `Write`/`Edit`, using project-local tooling. See `~/.claude/rules/web/hooks.md`.

## Agents & reviewers (`~/.claude/agents`)

code-reviewer, security-reviewer, language reviewers (typescript / python / go / rust / java / kotlin / csharp / cpp / flutter), build resolvers (build-error-resolver + go / rust / java / kotlin / cpp / dart), refactor-cleaner, doc-updater, tdd-guide, e2e-runner, planner, architect. Invoke proactively per `~/.claude/rules/common/agents.md`.

## Memory

Per-project issue tracking uses **beads** (`bd`). Persistent insights go in `bd remember`. Do **not** create `MEMORY.md` / `TODO.md` files.
```

- [ ] **Step 2: Verify**

Run: `test -f overlay/harness.md && grep -c "Opus 4.8\|autoplan\|Workflow\|RTK" overlay/harness.md`
Expected: `4` or more.

- [ ] **Step 3: Commit**

```bash
git add overlay/harness.md
git commit -m "docs(overlay): add harness profile (model, orchestration, hooks, agents)"
```

---

## Task 12: Create `overlay/flow.md`

**Files:**
- Create: `overlay/flow.md`

- [ ] **Step 1: Write the file**

````markdown
# Default Opus 4.8 Flow

For non-trivial work, this loop runs on top of the pipeline (with `/autoplan` + Workflow handling planning and orchestration):

```
/caveman → /graphify → code-structure → improve-codebase-architecture → /tdd → CodeRabbit (+ /autofix)
```

1. **`/caveman`** — engage output compression for the session (prose only; code/commits stay normal).
2. **`/graphify`** — build or query the codebase knowledge graph (`graphify-out/`) before diving in. If `graphify-out/graph.json` exists, treat codebase questions as `graphify query` calls.
3. **`code-structure`** — apply Service Layer Architecture: actions own product rules, services own reusable mechanics.
4. **`improve-codebase-architecture`** — find shallow modules, deepen them, write ADRs.
5. **`/tdd`** — red → green → refactor. Failing test first, always.
6. **CodeRabbit** — open the PR; CodeRabbit reviews it on GitHub. Run **`/autofix`** to apply its review-thread feedback with per-change approval.

The rails stay active throughout (docs context, token efficiency, beads memory).
````

- [ ] **Step 2: Verify**

Run: `test -f overlay/flow.md && grep -c "graphify\|code-structure\|CodeRabbit" overlay/flow.md`
Expected: `3` or more.

- [ ] **Step 3: Commit**

```bash
git add overlay/flow.md
git commit -m "docs(overlay): add featured Opus 4.8 flow"
```

---

## Task 13: Create the language templates

**Files:**
- Create: `overlay/languages/_template.md`
- Create: `overlay/languages/react.md`
- Create: `overlay/languages/swift.md`
- Create: `overlay/languages/svelte.md`
- Create: `overlay/languages/java.md`

- [ ] **Step 1: Write `_template.md`**

```markdown
# <Language / Platform> Profile

Clone this file to add a language. The agent reads the matching profile when a project uses this language.

| Aspect | Tool |
|---|---|
| **Reviewer** | <review skill/agent> |
| **Test / TDD** | <test framework + TDD skill> |
| **Build-error resolver** | <build resolver skill> |
| **UI / design** | <design skills, if any> |
| **Rules / gotchas** | <pointer to rules file or key pitfalls> |

## Notes

<Language-specific pitfalls the agent should watch for.>
```

- [ ] **Step 2: Write `react.md`**

```markdown
# React / Web Profile

| Aspect | Tool |
|---|---|
| **Reviewer** | `typescript-reviewer` |
| **Test / TDD** | Vitest or Jest + Testing Library; Playwright for E2E; `/tdd` |
| **Build-error resolver** | `build-error-resolver` |
| **UI / design** | `frontend-design`, shadcn MCP, `ui-ux-pro-max`; Get Design tokens |
| **Rules / gotchas** | `~/.claude/rules/web/*` (coding-style, performance, security, patterns, design-quality) |

## Notes

- Immutable updates only; never mutate in place.
- Animate compositor-friendly properties (transform/opacity), not layout-bound ones.
- Server state (TanStack Query/SWR) ≠ client state (Zustand/Jotai) ≠ URL state — don't duplicate.
- Anti-template policy: avoid default card grids and stock hero sections.
```

- [ ] **Step 3: Write `swift.md`**

```markdown
# Swift / Apple Profile

| Aspect | Tool |
|---|---|
| **Reviewer** | `swiftui-pro`, `swiftdata-pro`, `swift-concurrency-pro`, `swift-testing-pro` |
| **Test / TDD** | Swift Testing (`swift-testing-pro`); `/tdd` |
| **Build-error resolver** | xcode build flows (`ios-build`, `ios-fix`) |
| **UI / design** | `liquid-glass-design`, `ios-design-review`, `ios-simulator-skill` |
| **Rules / gotchas** | App Store: `asc-*` skills (metadata, screenshots, release) |

## Notes

- Modern APIs: `@Observable` over `ObservableObject`; avoid `+` Text concatenation.
- SwiftData predicates can compile cleanly but crash — review with `swiftdata-pro`.
- Structured concurrency: actors, task groups — review with `swift-concurrency-pro`.
- Partial reviews work: `/swiftui-pro Check for deprecated API`.
```

- [ ] **Step 4: Write `svelte.md`**

```markdown
# Svelte Profile

| Aspect | Tool |
|---|---|
| **Reviewer** | `typescript-reviewer` |
| **Test / TDD** | Vitest + Testing Library; Playwright for E2E; `/tdd` |
| **Build-error resolver** | `build-error-resolver` |
| **UI / design** | `frontend-design`; Get Design tokens |
| **Rules / gotchas** | `~/.claude/rules/web/*` |

## Notes

- Svelte 5 runes (`$state`, `$derived`, `$effect`) — keep reactivity explicit.
- Same web rules as React (immutability, compositor-friendly motion, anti-template).
- No dedicated Svelte reviewer — lean on `typescript-reviewer` + web rules.
```

- [ ] **Step 5: Write `java.md`**

```markdown
# Java / Spring Boot Profile

| Aspect | Tool |
|---|---|
| **Reviewer** | `java-reviewer` |
| **Test / TDD** | JUnit; `springboot-tdd`; `/tdd` |
| **Build-error resolver** | `java-build-resolver`, `gradle-build` |
| **UI / design** | — (backend) |
| **Rules / gotchas** | `springboot-patterns`, `springboot-security`, `springboot-verification`, `jpa-patterns` |

## Notes

- Layered architecture: controller → service → repository; keep controllers thin.
- JPA: watch N+1 queries; use fetch joins / batching — review with `jpa-patterns`.
- Validate at boundaries; never trust external input (`springboot-security`).
```

- [ ] **Step 6: Verify**

Run: `ls overlay/languages/`
Expected: `_template.md  java.md  react.md  svelte.md  swift.md`
Run: `grep -l "Reviewer" overlay/languages/*.md | wc -l`
Expected: `5`.

- [ ] **Step 7: Commit**

```bash
git add overlay/languages/
git commit -m "docs(overlay): add language profiles (template, react, swift, svelte, java)"
```

---

## Task 14: Rewrite `CLAUDE.md`

**Files:**
- Modify: `CLAUDE.md` (full replacement)

- [ ] **Step 1: Replace the entire file**

```markdown
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
This project uses **beads (`bd`)** instead of markdown TODOs. Persistent across sessions.
- `bd ready` · `bd show <id>` · `bd update <id> --claim` · `bd close <id> --reason "..."`
- `bd create "title" -t bug|feature|task -p 0-4` · `bd remember "insight"` (no `MEMORY.md`)
- `bd dep add <discovered> <parent> --type discovered-from` · `bd prime` at session start
- Slash: `/beads:ready`, `/beads:create`, `/beads:workflow`, `@task-agent`

### 04 · External Data
- **Bright Data CLI (`bdata`)** — `bdata scrape <url>`, `bdata search "<q>"`, `bdata pipelines <type>` (40+ platforms), `bdata budget`. One-time `bdata login`. Use for live web/SERP/platform data.

### 05 · Agent Interfaces
- **Printing Press** — generate agent-native Go CLIs from API specs; library of 70+ pre-built CLIs (Stripe, Linear, Notion, Slack…).

---

## Recommended order of operations

1. Read this file.
2. `bd prime` for memory, `bd ready` for current state.
3. Non-trivial task → `/grill-me` or `/superpowers:brainstorming` (gstack: `/office-hours`).
4. Capture the plan → `/superpowers:write-plan` (gstack: `/autoplan`).
5. Pull docs through Context7 / Ref before writing library code.
6. Navigate code with Pitlane / `/graphify`, not whole-file reads.
7. Track work in beads; file discovery issues as you go.
8. TDD when implementing; `code-structure` + `improve-codebase-architecture` for structure.
9. `/superpowers:requesting-code-review`, then CodeRabbit + `/autofix` on the PR.
10. `/superpowers:finishing-a-development-branch` and `bd close` when done.

> **Personal overlay:** `overlay/` documents the gstack skills, the Claude harness (Opus 4.8 + `/autoplan` + Workflow), and per-language profiles. Featured Opus 4.8 flow: `/caveman → /graphify → code-structure → improve-codebase-architecture → /tdd → CodeRabbit`. See `overlay/README.md`.

---

## Detail files

- `pipeline/01-align.md … pipeline/04-build.md`
- `rails/01-docs-context.md … rails/05-agent-interfaces.md`
- `overlay/README.md`, `overlay/harness.md`, `overlay/flow.md`, `overlay/languages/*`
- `INSTALL.md` for one-time setup
- `index.html` for the visual map (open in a browser; hover for commands) — published at https://makafeli.github.io/agentic-code-stack/
```

- [ ] **Step 2: Verify**

Run: `grep -in "metaprompt\|opsx\|openspec\|open design\|npx skills\|greploop\|stack-diagram\|brightdata mcp" CLAUDE.md`
Expected: no output (exit 1).
Run: `grep -c "↳ Overlay\|graphify\|CodeRabbit\|bdata\|makafeli.github.io" CLAUDE.md`
Expected: `5` or more.

- [ ] **Step 3: Commit**

```bash
git add CLAUDE.md
git commit -m "docs: rewrite CLAUDE.md for v3 (4-stage pipeline, overlay signposts, tool swaps)"
```

---

## Task 15: Sync `AGENTS.md` to `CLAUDE.md`

**Files:**
- Modify: `AGENTS.md` (exact copy of CLAUDE.md)

- [ ] **Step 1: Copy**

```bash
cp CLAUDE.md AGENTS.md
```

- [ ] **Step 2: Verify they are identical**

Run: `diff CLAUDE.md AGENTS.md && echo IDENTICAL`
Expected: `IDENTICAL` (diff prints nothing).

- [ ] **Step 3: Commit**

```bash
git add AGENTS.md
git commit -m "docs: sync AGENTS.md with rewritten CLAUDE.md"
```

---

## Task 16: Update `README.md`

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Add the GitHub Pages URL under the title**

old:
```
# Agentic Code Stack

A portable reference package you drop into the root of any project
```
new:
```
# Agentic Code Stack

**🔗 https://makafeli.github.io/agentic-code-stack/** — visual map of the stack.

A portable reference package you drop into the root of any project
```

- [ ] **Step 2: Update the file tree**

In the ```` ``` ```` tree block, replace the `pipeline/` and add `overlay/`:

old:
```
├── pipeline/
│   ├── 01-prompt-shape.md
│   ├── 02-align.md
│   ├── 03-spec-plan.md
│   ├── 04-design.md
│   └── 05-build.md
└── rails/
    ├── 01-docs-context.md
    ├── 02-token-efficiency.md
    ├── 03-memory-tracking.md
    ├── 04-external-data.md
    └── 05-agent-interfaces.md
```
new:
```
├── pipeline/
│   ├── 01-align.md
│   ├── 02-spec-plan.md
│   ├── 03-design.md
│   └── 04-build.md
├── rails/
│   ├── 01-docs-context.md
│   ├── 02-token-efficiency.md
│   ├── 03-memory-tracking.md
│   ├── 04-external-data.md
│   └── 05-agent-interfaces.md
└── overlay/                # personal layer (gstack, harness, language profiles)
    ├── README.md
    ├── harness.md
    ├── flow.md
    └── languages/
```

- [ ] **Step 3: Update the mental model section**

old:
```
**Pipeline** (sequential): `Prompt Shape → Align → Spec & Plan → Design → Build`

**Rails** (always-on, parallel to the pipeline):
- Docs & Context — Ref MCP, Context7, Pitlane MCP
- Token Efficiency — Caveman, Pitlane MCP
- Memory & Tracking — Beads, OpenSpec
- External Data — Brightdata MCP
```
new:
```
**Pipeline** (sequential): `Align → Spec & Plan → Design → Build`

**Rails** (always-on, parallel to the pipeline):
- Docs & Context — Ref MCP, Context7, Pitlane MCP
- Token Efficiency — Caveman, graphify, Pitlane MCP
- Memory & Tracking — Beads
- External Data — Bright Data CLI
- Agent Interfaces — Printing Press

**Overlay** (personal, optional): gstack skills, the Claude harness, and per-language profiles in `overlay/`. Delete it to keep a pure portable stack.
```

- [ ] **Step 4: Update the credits**

In `## Credits & sources`, remove the OpenSpec, Open Design, and Metaprompt lines and add:
```
- **graphify** — `/graphify` knowledge-graph skill
- **CodeRabbit** — [coderabbit.ai](https://coderabbit.ai/) · applied via the `autofix` skill
- **Bright Data CLI** — [brightdata.com](https://brightdata.com/)
- **Claude Design** — Anthropic's built-in `frontend-design` skill
```

- [ ] **Step 5: Verify**

Run: `grep -in "prompt shape\|openspec\|open design\|brightdata mcp\|05-build" README.md`
Expected: no output (exit 1).
Run: `grep -c "makafeli.github.io\|overlay" README.md`
Expected: `2` or more.

- [ ] **Step 6: Commit**

```bash
git add README.md
git commit -m "docs: update README for v3 (4 stages, overlay, GitHub Pages URL, credits)"
```

---

## Task 17: Update `PHILOSOPHY.md`

**Files:**
- Modify: `PHILOSOPHY.md`

- [ ] **Step 1: Update the pipeline description**

old:
```
**Pipeline** -- sequential stages the agent moves through: Shape (the prompt arrives structured), Align (discovery before code), Spec and Plan (write it down), Design (when UI is involved), Build (implement with reviews).
```
new:
```
**Pipeline** -- sequential stages the agent moves through: Align (discovery before code), Spec and Plan (write it down), Design (when UI is involved), Build (implement with reviews).
```

- [ ] **Step 2: Update the rails description**

old:
```
**Rails** -- always-on capabilities that run alongside every pipeline stage: documentation lookups (Context7, Ref MCP), token efficiency (Caveman), memory and task tracking (Beads), and external data access (Brightdata MCP).
```
new:
```
**Rails** -- always-on capabilities that run alongside every pipeline stage: documentation lookups (Context7, Ref MCP), token efficiency (Caveman, graphify), memory and task tracking (Beads), external data access (Bright Data CLI), and agent interfaces (Printing Press).
```

- [ ] **Step 3: Add an overlay paragraph after "How to customize"**

After the `## How to customize` section, insert:
```markdown
## Portable core + personal overlay

The pipeline and rails are the **portable core** — anyone can install them. The `overlay/` directory is a **personal layer**: a private skill suite (gstack), the maintainer's Claude harness, and per-language profiles. The rule is simple — public tools are the default; the overlay names the personal equivalent for each stage. Delete `overlay/` and the core still stands.
```

- [ ] **Step 4: Update the meta point**

old:
```
This stack was built using the stack. The design was aligned with `/brainstorm`, the plan was written with `/write-plan`, the implementation ran through `/subagent-driven-development`, and the tasks are tracked in Beads. Eat your own dog food.
```
new:
```
This stack was built using the stack. The design was aligned with `/superpowers:brainstorming`, the plan was written with `/superpowers:write-plan`, the implementation ran through `/superpowers:subagent-driven-development`, and the work is tracked in Beads. Eat your own dog food.
```

- [ ] **Step 5: Verify**

Run: `grep -in "shape (the prompt\|brightdata mcp\|/brainstorm\b" PHILOSOPHY.md`
Expected: no output (exit 1).
Run: `grep -c "overlay\|graphify" PHILOSOPHY.md`
Expected: `2` or more.

- [ ] **Step 6: Commit**

```bash
git add PHILOSOPHY.md
git commit -m "docs: update PHILOSOPHY for v3 (4 stages, overlay, rails)"
```

---

## Task 18: Update `INSTALL.md`

**Files:**
- Modify: `INSTALL.md`

- [ ] **Step 1: Remove the OpenSpec section**

Delete the `### OpenSpec (Fission-AI)` block (heading + commands + note).

- [ ] **Step 2: Replace the Open Design section with Claude Design**

old (the `### Open Design (nexu-io)` block) → new:
```markdown
### Claude Design

No install — Anthropic's built-in **`frontend-design`** skill is available in Claude Code. Pair it with Get Design tokens. (gstack adds `/design-review`, `/design-html`, `/design-consultation` if installed.)
```

- [ ] **Step 3: Add graphify, CodeRabbit, and Brightdata CLI under Rail tools**

After the existing `### Brightdata MCP` block — which you replace — add these. Replace the `### Brightdata MCP` block with:
```markdown
### Bright Data CLI
```bash
curl -fsSL https://cli.brightdata.com/install.sh | bash   # or: npm install -g @brightdata/cli
bdata login
```
Free tier: 5,000 requests/month. `bdata pipelines list` shows the 40+ platform extractors.

### graphify
```bash
uv tool install graphifyy   # or: pip install graphifyy
```
Optional always-on integration: `graphify claude install` writes a `## graphify` block into the project `CLAUDE.md`.

### CodeRabbit + autofix
Install the CodeRabbit GitHub App on your repo at [coderabbit.ai](https://coderabbit.ai/). The `autofix` skill (applies CodeRabbit review threads) ships with the stack — no separate install.
```

- [ ] **Step 4: Add a gstack overlay note at the top of Pipeline tools**

After the intro `> **Important:**` blockquote, insert:
```markdown
> **Personal overlay:** the `overlay/` files reference **gstack** (a private skill suite) and harness/RTK config that are not publicly installable. A cloner can ignore or delete `overlay/`; the portable core below stands alone.
```

- [ ] **Step 5: Update the sanity-check list**

In `## Sanity check`, replace the OpenSpec / brightdata MCP expectations:
old lines:
```
- `/opsx:propose`, `/opsx:apply`, `/opsx:archive`
```
removed. And under MCP servers, replace `- brightdata` with nothing (the CLI is not an MCP server); add `- (Bright Data CLI: run \`bdata config\`)` under a CLI note. Concretely, change the MCP list to:
```
And these MCP servers should be listed under `/mcp`:
- `ref`
- `context7`
- `pitlane`
- `beads` (if plugin)

CLI tools (verify in a terminal): `bdata config`, `graphify --help`, `rtk --version`.
```

- [ ] **Step 6: Verify**

Run: `grep -in "openspec\|opsx\|nexu-io\|brightdata mcp\|pnpm tools-dev" INSTALL.md`
Expected: no output (exit 1).
Run: `grep -c "bdata\|graphify\|coderabbit\|overlay" INSTALL.md`
Expected: `4` or more.

- [ ] **Step 7: Commit**

```bash
git add INSTALL.md
git commit -m "docs: update INSTALL for v3 (Brightdata CLI, graphify, CodeRabbit; drop OpenSpec/Open Design)"
```

---

## Task 19: Rebuild `index.html` — header, grid, and pipeline cards

The visual map must show 4 stages, the new tools, an overlay note, and the GitHub Pages link. Work top-down. The current file is the reference for surrounding markup; edits below are surgical.

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Update the title and add the canonical link in `<head>`**

old: `<title>Agentic Code Stack — Skill Stack Wire Diagram</title>`
new:
```html
<title>Agentic Code Stack — Skill Stack Wire Diagram</title>
<link rel="canonical" href="https://makafeli.github.io/agentic-code-stack/">
```

- [ ] **Step 2: Bump the version + add the URL in the header meta**

old:
```html
<strong>Wire Diagram</strong>
      Workflow · v2 · hover-enabled
```
new:
```html
<strong>Wire Diagram</strong>
      Workflow · v3 · hover-enabled
```
And in the `.head .sub` line, append the URL:
old:
```html
<div class="sub">From a vague idea to shipped code — pipeline stages flow left → right; support rails run continuously underneath.</div>
```
new:
```html
<div class="sub">From a vague idea to shipped code — pipeline stages flow left → right; support rails run continuously underneath. · <a href="https://makafeli.github.io/agentic-code-stack/" style="color:var(--accent);">makafeli.github.io/agentic-code-stack</a></div>
```

- [ ] **Step 3: Change the pipeline grid from 6 to 5 columns**

In the `.pipeline { … }` CSS rule:
old: `grid-template-columns: repeat(6, 1fr);`
new: `grid-template-columns: repeat(5, 1fr);`

- [ ] **Step 4: Delete the entire Stage 1 (Prompt Shape) card**

Remove the whole `<!-- Stage 1 -->` block — from `<!-- Stage 1 -->` through its closing `</div>` (the one before `<!-- Stage 2 -->`). It contains the Metaprompt.com item.

- [ ] **Step 5: Renumber the remaining stage cards**

In the four remaining cards, update the `<div class="num">N</div>` values and the `<!-- Stage N -->` comments:
- `<!-- Stage 2 -->` / `num">2` (Align) → `<!-- Stage 1 -->` / `num">1`
- `<!-- Stage 3 -->` / `num">3` (Spec & Plan) → `<!-- Stage 2 -->` / `num">2`
- `<!-- Stage 4 -->` / `num">4` (Design) → `<!-- Stage 3 -->` / `num">3`
- `<!-- Stage 5 -->` / `num">5` (Build, `.stage.wide`) → `<!-- Stage 4 -->` / `num">4`

- [ ] **Step 6: Verify structure so far**

Run: `grep -c 'class="num"' index.html`
Expected: `4`.
Run: `grep -in "metaprompt" index.html`
Expected: no output (exit 1).

- [ ] **Step 7: Commit**

```bash
git add index.html
git commit -m "feat(index): 4-stage pipeline grid, v3 header, GitHub Pages link"
```

---

## Task 20: Rebuild `index.html` — tool cards (Spec&Plan, Design, Build) and rails

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Spec & Plan card — remove OpenSpec item, retitle the Superpowers item, add gstack note**

In the (now Stage 2) Spec & Plan card, delete the `<li class="item">` whose `<span class="name">OpenSpec</span>` block lists the `/opsx:*` commands. Update the remaining item to include the overlay note. Replace the Superpowers item's `.blurb` end with an added note line inside its `.cmds`:
```html
<div class="cmds-note">↳ Overlay (gstack): /autoplan runs CEO/design/eng/DX reviews with auto-decisions.</div>
```

- [ ] **Step 2: Design card — Open Design → Claude Design**

Replace the `<li class="item">` containing `<span class="name">Open Design</span>` with:
```html
<li class="item">
  <span class="name">Claude Design</span>
  <span class="blurb">Anthropic's built-in frontend-design skill — distinctive, production-grade UI.</span>
  <div class="cmds">
    <div class="cmds-label">Skill</div>
    <code>frontend-design (auto-engages on UI work)</code>
    <div class="cmds-note">↳ Overlay (gstack): /design-review · /design-html · /design-consultation</div>
  </div>
</li>
```

- [ ] **Step 3: Build card · Implement column — remove "npx skills add", add overlay note**

Delete the `<li class="item">` whose name is `npx skills add`. At the end of the Implement `<ul>`, the remaining items are subagent-driven-development, code-structure, TDD. Update the `SwiftUI Agent Skill` item: replace that whole `<li>` with a language-overlay pointer:
```html
<li class="item">
  <span class="name">Language profiles</span>
  <span class="blurb">Per-language reviewers/test tools in overlay/languages/ — Swift (swiftui-pro…), React (typescript-reviewer + frontend-design), Java (java-reviewer + springboot-*).</span>
</li>
```

- [ ] **Step 4: Build card · Review & Finish column — greploop → CodeRabbit, add gstack note**

Replace the `<li class="item">` containing `<span class="name">check-pr / greploop</span>` with:
```html
<li class="item">
  <span class="name">CodeRabbit + /autofix</span>
  <span class="blurb">CodeRabbit reviews the PR on GitHub; /autofix applies its review threads with per-change approval.</span>
  <div class="cmds">
    <div class="cmds-label">Slash command</div>
    <code>/autofix</code>
    <div class="cmds-note">↳ Overlay (gstack): /review · /qa · /ship · /land-and-deploy</div>
  </div>
</li>
```

- [ ] **Step 5: Rail 02 — add graphify tool**

In the `<!-- Rail 02 — Token Efficiency -->` `.rail-body`, after the Caveman `.tool` block, insert:
```html
<div class="tool">
  <b>graphify</b>
  <div class="where">/graphify · Skill</div>
  <div class="what">Codebase → knowledge graph in graphify-out/. Query the graph instead of re-reading files.</div>
  <div class="cmds">
    <div class="cmds-label">Commands</div>
    <code>/graphify [path|url]</code>
    <code>graphify query "&lt;question&gt;"</code>
    <code>graphify explain "&lt;node&gt;"</code>
    <code>/graphify --update</code>
    <div class="cmds-note">If graphify-out/graph.json exists, treat codebase questions as graph queries.</div>
  </div>
</div>
```

- [ ] **Step 6: Rail 03 — remove the OpenSpec tool**

In the `<!-- Rail 03 — Memory & Tracking -->` `.rail-body`, delete the entire `.tool` block whose `<b>OpenSpec</b>`. Leave the Beads tool as the only one.

- [ ] **Step 7: Rail 04 — Brightdata MCP → Bright Data CLI**

Replace the Brightdata `.tool` block's body. Set `<b>Bright Data CLI</b>`, `<div class="where">bdata · CLI</div>`, and replace its `.cmds` with:
```html
<div class="cmds">
  <div class="cmds-label">Commands</div>
  <code>bdata login</code>
  <code>bdata scrape &lt;url&gt;</code>
  <code>bdata search "&lt;query&gt;" --json</code>
  <code>bdata pipelines &lt;type&gt; …</code>
  <code>bdata budget</code>
  <div class="cmds-label" style="margin-top:8px;">Install</div>
  <code>npm i -g @brightdata/cli</code>
</div>
```

- [ ] **Step 8: Add an overlay note to the footer**

Replace the `.footer-note` inner text span:
old:
```html
<span>Read left → right · rails active throughout · hover for commands</span>
```
new:
```html
<span>Read left → right · rails active throughout · ↳ overlay = gstack/personal layer · hover for commands</span>
```

- [ ] **Step 9: Verify**

Run: `grep -in "openspec\|opsx\|npx skills add\|greploop\|brightdata mcp" index.html`
Expected: no output (exit 1).
Run: `grep -c "graphify\|CodeRabbit\|Bright Data CLI\|Claude Design" index.html`
Expected: `4` or more.
Run (structural sanity — counts of opening/closing div should balance): `python3 -c "t=open('index.html').read(); print('open', t.count('<div'), 'close', t.count('</div>'))"`
Expected: open and close counts equal.

- [ ] **Step 10: Commit**

```bash
git add index.html
git commit -m "feat(index): update tool cards and rails for v3 (graphify, CodeRabbit, Bright Data CLI, overlay)"
```

---

## Task 21: Final acceptance check

**Files:** none (verification only)

- [ ] **Step 1: Global removed-tool sweep across the portable core**

Run:
```bash
grep -rin "metaprompt\|openspec\|opsx\|open design\|nexu-io\|npx skills add\|greploop\|greptile\|brightdata mcp\|stack-diagram" \
  CLAUDE.md AGENTS.md README.md PHILOSOPHY.md INSTALL.md index.html pipeline/ rails/
```
Expected: no output (exit 1). If anything prints, fix that file and re-commit.

- [ ] **Step 2: New-tool presence sweep**

Run:
```bash
for term in graphify CodeRabbit autofix bdata "Claude Design" overlay makafeli.github.io; do
  printf "%s: " "$term"; grep -rl "$term" CLAUDE.md README.md index.html | wc -l
done
```
Expected: each term resolves to `1` or more files.

- [ ] **Step 3: AGENTS↔CLAUDE parity**

Run: `diff CLAUDE.md AGENTS.md && echo PARITY_OK`
Expected: `PARITY_OK`.

- [ ] **Step 4: File structure**

Run: `ls pipeline/ rails/ overlay/ overlay/languages/`
Expected: pipeline 01-align..04-build; rails 01..05; overlay README/harness/flow/languages; languages _template/react/svelte/swift/java.

- [ ] **Step 5: Internal markdown link check (detail-file references resolve)**

Run:
```bash
for f in pipeline/01-align.md pipeline/02-spec-plan.md pipeline/03-design.md pipeline/04-build.md \
         rails/01-docs-context.md rails/02-token-efficiency.md rails/03-memory-tracking.md \
         rails/04-external-data.md rails/05-agent-interfaces.md \
         overlay/README.md overlay/harness.md overlay/flow.md; do
  test -f "$f" && echo "OK $f" || echo "MISSING $f"
done
```
Expected: all `OK`.

- [ ] **Step 6: Open the visual map for a human eyeball pass**

Run: `open index.html`
Confirm: 4 stage cards numbered 1–4, no Prompt Shape, rails show graphify (02), Beads-only (03), Bright Data CLI (04), footer shows the overlay note, header shows v3 + the GitHub Pages URL.

- [ ] **Step 7: Final commit (only if Step 1/2 required fixes)**

```bash
git add -A
git commit -m "docs: v3 acceptance-check fixes"
```

---

## Self-review notes (for the implementer)

- This plan is **documentation only** — no app code, no test runner. Verification = `grep`/`diff`/`ls`.
- Order matters: Task 1 (renames) first, core entry docs (14–15) after the pipeline/rails content they reference, supporting docs (16–18) next, `index.html` (19–20) last, acceptance check (21) final.
- If a `grep` "removed term" check fails because the term legitimately appears in prose (e.g. explaining what was *replaced*), reword to avoid the bare term, or scope the grep — don't leave a stale tool reference.
- gstack, RTK, and the harness are **private/overlay** — never move them into the portable core files.
