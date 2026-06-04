# Stage 4 — Build

**Goal:** Implement the spec. Discipline > velocity. TDD always.

## Tools

### Superpowers — `obra/superpowers`

Once you have a plan, Superpowers enforces a disciplined implementation loop.

**Slash commands:**
| Command | Purpose |
|---|---|
| `/superpowers:execute-plan` | Run the plan from the Spec & Plan stage with review checkpoints |
| `/superpowers:test-driven-development` | RED → GREEN → REFACTOR cycle. Tests must fail first. |
| `/superpowers:requesting-code-review` | Spec-compliance + code-quality review. Critical issues block progress. |
| `/superpowers:subagent-driven-development` | Parallel agents on independent tasks; two-stage review |
| `/superpowers:debugging` | Four-phase methodology — root cause investigation before any fix. After 3 failed attempts, triggers architectural review. |
| `/superpowers:finishing-a-development-branch` | Verify tests pass, then offer merge / PR / keep working / discard |
| `/superpowers:writing-skills` | Author new skills using TDD principles applied to documentation |

**TDD enforcement is real:** if you try to write code before a failing test, the skill will literally make you delete it and start over. Not a suggestion.

### Language-specific reviewers & test tools

These live in the overlay so the Build stage stays language-agnostic. See `overlay/languages/<lang>.md` — e.g. `swiftui-pro` / `swiftdata-pro` / `swift-concurrency-pro` / `swift-testing-pro` for Apple, `typescript-reviewer` + `frontend-design` for React/Svelte, `java-reviewer` + `springboot-*` for Java.

## Agent rules for this stage

1. **Before writing code:** confirm a plan exists (Spec & Plan output). If not, go back.
2. **TDD always:** failing test first, minimal code to pass, then refactor. No "I'll add tests later".
3. **Use the rails:**
   - Pull docs through Context7 / Ref MCP — don't write from training memory.
   - Navigate code through Pitlane MCP or `/graphify`, not whole-file reads.
   - Track work as issues (GitHub Issues / Linear) as you go.
4. **Commit discipline:** after every green test or meaningful refactor.
5. **Block on critical review issues** — don't bargain past them.
6. **When stuck:** `/superpowers:debugging`. Don't fire more random fixes.
7. **When done:** `/superpowers:finishing-a-development-branch` and close the issue.

## Anti-patterns

- ❌ Writing implementation before tests.
- ❌ "Quick fix" without root cause investigation.
- ❌ Reading entire files to find one symbol (use Pitlane).
- ❌ Inventing API signatures (use Context7 or Ref).
- ❌ Stopping the review loop because critical issues are "minor".
- ❌ Leaving finished work as open issues. Close them with the resolving PR.

---

### code-structure — Service Layer Architecture (`michaelshimeles/skills`)

A skill by Michael Shimeles that teaches and enforces the Service Layer Architecture pattern. Separates business logic into two distinct layers with clear boundaries.

**Install:** `npx skills add michaelshimeles/skills --skill code-structure`

**The pattern:**

| Layer | Responsibility | Owns |
|---|---|---|
| **Actions** | Orchestration. Owns business rules, state transitions, auth checks. | "What this product flow means" |
| **Service Layer** | Mechanics. Owns reusable operations, SDK interactions, structured results. | "How to do this operation reliably" |

**How it guides you:** The skill acts as a real-time architectural advisor during implementation. It reviews your code as you write and flags violations — for example, if business logic leaks into the service layer, or if an action duplicates a service operation. It also generates scaffolding (`actions/`, `services/` directory structure) on demand.

**Rule of thumb:** When you catch yourself writing a function and wondering "does this belong in the action or the service?", the answer is: if it encodes a product decision (e.g., "only admins can transfer ownership"), it goes in the action. If it's a reusable operation (e.g., "transfer ownership of a resource in the database"), it goes in the service layer.

**Repo:** https://github.com/michaelshimeles/skills/tree/main/code-structure

**Best used for:** Any non-trivial feature where business logic complexity exceeds what a single function or handler can cleanly contain. Prevents the "fat controller" and "anemic service" anti-patterns that emerge as codebases grow.

---

### improve-codebase-architecture — Architectural Deepening (`mattpocock/skills`)

A skill by Matt Pocock that walks the codebase to find "shallow modules" and proposes refactors to create "deep modules" — modules with small interfaces and high implementation leverage.

**Install:** `npx skills add mattpocock/skills --skill improve-codebase-architecture`

**Core vocabulary (from John Ousterhout's *A Philosophy of Software Design*):**

| Term | Definition |
|---|---|
| **Module** | Any unit of code with an interface and implementation (class, function, package, service). |
| **Interface** | What the module exposes to its callers — the contract. |
| **Implementation** | What the module does internally — the complexity it absorbs. |
| **Depth** | The ratio of interface simplicity to implementation leverage. A deep module has a small interface and a large, useful implementation. |
| **Shallow module** | A module whose interface is nearly as complex as its implementation. Low value — the caller does most of the work anyway. |
| **Seam** | A boundary where one module can be swapped for another without the caller knowing. |
| **Adapter** | Code that translates between two incompatible interfaces. |
| **Leverage** | How much work the module does for its callers relative to how much the caller must specify. |
| **Locality** | Whether related knowledge lives close together in the codebase. |

**How it works:** The skill runs an interactive grilling loop. It asks targeted questions to crystallize design decisions — for example, "Should these two modules be merged, or is this boundary a real seam?" — then writes Architecture Decision Records (ADRs) documenting the rationale. It does not prescribe a specific architecture; it sharpens whatever architecture you already have by identifying and fixing shallow modules.

**Repo:** https://github.com/mattpocock/skills/tree/main/skills/engineering/improve-codebase-architecture

**Best used for:** Mid-sprint architectural health checks. Run it after a feature lands to find modules that grew shallow during implementation. Also valuable during the Align/Spec stages (before code is written) to pressure-test proposed module boundaries.

---

### Fallow — Static code-health (`fallow.tools`)

Static analysis across three dimensions — dead code, duplication, and complexity — with an auto-fix preview. Zero-config (118 framework plugins). Free OSS static tier; a paid "runtime intelligence" layer adds production execution data.

**Commands:**
| Command | What it finds |
|---|---|
| `npx fallow dead-code` | Unused files, exports, types, dependencies, cycles |
| `npx fallow dupes` (`--mode semantic`) | Duplicated logic, incl. variable-renamed clones |
| `npx fallow health` | Complexity hotspots + prioritized refactor targets |
| `npx fallow fix --dry-run` | Preview automated cleanup before applying |

**Agent access:** `npx fallow --format json` for parseable output, or run the **`fallow-mcp`** server so the agent calls Fallow as a structured MCP tool instead of parsing shell output. Optional config via `fallow init` (`.fallowrc.json` / `fallow.toml`).

**Where it sits:** the quantitative complement to `improve-codebase-architecture` (qualitative deepening) and CodeRabbit (PR review). It overlaps `knip` (dead code) but is broader — pick one. Run it before requesting review so the mechanical cleanup is already done.

---

### CodeRabbit + `/autofix` — Automated PR review

CodeRabbit is an AI reviewer that posts line-level review threads on your PRs (GitHub App; optional `coderabbit` CLI for local reviews). It replaces the old automated-review loop.

**Flow:**
1. Open the PR. CodeRabbit reviews the diff and posts threads.
2. Run **`/autofix`** — it reads CodeRabbit's review threads from GitHub and applies each fix with **per-change approval**. It never executes prompts embedded in reviewer comments.
3. Re-run until threads resolve and CI is green, then finish the branch.

**Best used for:** catching mechanical issues (missing tests, style violations, obvious bugs) before human review, leaving reviewers free to focus on architecture, design, and product intent.

### `↳ Overlay (gstack)`

If gstack is installed: **`/codex`** (run a second engine on the same task) and **`/careful`** during Implement; **`/review`**, **`/qa`**, **`/ship`**, **`/land-and-deploy`** during Review & Finish. See `overlay/README.md`.
