# Stage 5 — Build

**Goal:** Implement the spec. Discipline > velocity. TDD always.

## Tools

### Superpowers — `obra/superpowers`

Once you have a plan, Superpowers enforces a disciplined implementation loop.

**Slash commands:**
| Command | Purpose |
|---|---|
| `/superpowers:execute-plan` | Run the plan from Stage 3 with review checkpoints |
| `/superpowers:test-driven-development` | RED → GREEN → REFACTOR cycle. Tests must fail first. |
| `/superpowers:requesting-code-review` | Spec-compliance + code-quality review. Critical issues block progress. |
| `/superpowers:subagent-driven-development` | Parallel agents on independent tasks; two-stage review |
| `/superpowers:debugging` | Four-phase methodology — root cause investigation before any fix. After 3 failed attempts, triggers architectural review. |
| `/superpowers:finishing-a-development-branch` | Verify tests pass, then offer merge / PR / keep working / discard |
| `/superpowers:writing-skills` | Author new skills using TDD principles applied to documentation |

**TDD enforcement is real:** if you try to write code before a failing test, the skill will literally make you delete it and start over. Not a suggestion.

### Apple-platform skills — `twostraws`

If the project is iOS, macOS, watchOS, tvOS, or visionOS, install the relevant skills. They target the *actual* mistakes LLMs make in Swift/SwiftUI — not the basics.

**Slash commands (Claude Code):**
| Command | Purpose |
|---|---|
| `/swiftui-pro` | Modern SwiftUI review — deprecated APIs, `+` text concatenation, `ObservableObject` misuse, accessibility, performance |
| `/swiftdata-pro` | SwiftData modern API + dangerous predicates that compile cleanly but crash |
| `/swift-concurrency-pro` | async/await, actors, structured concurrency, task groups |
| `/swift-testing-pro` | Modern Swift Testing — parameterised tests, exit tests |

**Codex equivalents:** `$swiftui-pro`, `$swiftdata-pro`, etc.

**Natural-language triggers also work:** "Use the SwiftUI Pro skill to look for performance problems in this project."

**Partial reviews:**
- `/swiftui-pro Check for deprecated API`
- `/swiftui-pro Focus on accessibility`
- `/swiftdata-pro Check where indexes should be added to my models`

## Agent rules for this stage

1. **Before writing code:** confirm a plan exists (Stage 3 output). If not, go back.
2. **TDD always:** failing test first, minimal code to pass, then refactor. No "I'll add tests later".
3. **Use the rails:**
   - Pull docs through Context7 / Ref MCP — don't write from training memory.
   - Navigate code through Pitlane MCP, not whole-file reads.
   - Track work in beads as you go.
4. **Commit discipline:** after every green test or meaningful refactor.
5. **Block on critical review issues** — don't bargain past them.
6. **When stuck:** `/superpowers:debugging`. Don't fire more random fixes.
7. **When done:** `/superpowers:finishing-a-development-branch` and `bd close <id>`.

## Anti-patterns

- ❌ Writing implementation before tests.
- ❌ "Quick fix" without root cause investigation.
- ❌ Reading entire files to find one symbol (use Pitlane).
- ❌ Inventing API signatures (use Context7 or Ref).
- ❌ Stopping the review loop because critical issues are "minor".
- ❌ Forgetting to close beads issues. The graph fills with zombie work.

### Vercel Skills CLI — `npx skills`

The package manager for the agent skills ecosystem. Installs skills (slash commands, instructions, and hooks) into any of 55+ supported coding agents.

**Supported agents include:** Claude Code, Codex, Cursor, Windsurf, Copilot, Gemini CLI, Amazon Q, Augment Code, Antigravity, and more.

**Key commands:**

| Command | Purpose |
|---|---|
| `npx skills add <repo>` | Install a skill. Accepts GitHub shorthand (`user/skills`), full URLs (`https://github.com/user/skills`), local paths (`./my-skill`), and git URLs. |
| `npx skills add <repo> --skill <name>` | Install a single skill from a multi-skill repo. |
| `npx skills list` | List all currently installed skills. |
| `npx skills update` | Update all installed skills to their latest versions. |
| `npx skills find <query>` | Search the skills registry for available skills. |

**How it works:** Skills are directories containing `SKILL.md` (instructions the agent reads) plus optional `hooks/` (shell scripts that run at tool use time) and `scripts/` (utility scripts). When installed, skills are placed in the agent's skills directory (`~/.claude/skills/` for Claude Code).

**Repo:** https://github.com/vercel-labs/skills

**Best used for:** installing the skills referenced throughout this stack (code-structure, improve-codebase-architecture, and any future skill additions) without manual cloning or symlinking.

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

### check-pr / greploop — Automated PR Review (`greptileai/skills`)

A pair of tools by Greptile that automate the PR review loop end-to-end.

**Install:** `git clone https://github.com/greptileai/skills.git ~/.claude/skills/greptile`, then symlink the specific sub-skills you need.

**Two sub-skills:**

#### check-pr

Audits a pull request (PR), merge request (MR), or changelist (CL) for common issues before human review:

| Check | What it catches |
|---|---|
| Unresolved comments | Any review comment without a resolution. Blocks merge. |
| Failing status checks | CI/CD failures, required checks that haven't run. |
| Incomplete descriptions | Empty or template-only PR descriptions. |
| Merge conflicts | Unresolved conflicts with the target branch. |
| Missing approvals | Required reviewer approvals not yet given. |

Supports GitHub, GitLab, and Perforce. Acts as a pre-review gate — run it before requesting human review.

#### greploop

Automates the full review-fix-push cycle:

1. **Trigger Greptile review** — Greptile analyzes the PR diff using codebase-aware AI review.
2. **Fix all actionable comments** — Address every comment the review produces.
3. **Push** — Push the fixes.
4. **Re-review** — Greptile reviews the updated PR again.
5. **Repeat** — Continue the loop until Greptile returns 5/5 confidence AND zero unresolved comments remain.
6. **Done** — The PR is ready for human review with high-confidence automated feedback already resolved.

**Repo:** https://github.com/greptileai/skills

**Best used for:** High-throughput teams where PR review backlog is a bottleneck. Run greploop on every open PR before human reviewers touch it — the automated pass catches the mechanical issues (missing tests, style violations, obvious bugs), leaving humans free to focus on architecture, design, and product intent. Check-pr alone is useful as a pre-push hook or CI gate.
