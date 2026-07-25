# Handoff — Claude 5 role-team migration → agentic-code-stack

**Date:** 2026-07-25 · **Repo:** `github.com/makafeli/agentic-code-stack` (local: `~/Projecten/Claude Code Stack`)
**Goal of this handoff:** update the repo so it documents/ships the full current machine setup — Claude 5 model migration (Opus 4.8 → Opus 5, Fable 5 main loop), the Codex-mirroring **role team**, all agents, rules, settings, and configs.

---

## 1. TL;DR — what changed on this machine

1. **Models:** Opus 4.8 retired as primary (kept only as *cyber*-refusal fallback). Main loop = **Fable 5** (`claude-fable-5`). Review tier = **Opus 5** (`claude-opus-5`, 1M ctx / 128k out, May-2026 cutoff). Implementation tier = **Sonnet 5** (`claude-sonnet-5`). Verified against Anthropic's official docs (models/overview, migration-guide, whats-new pages).
2. **Role team:** 6 new global subagents in `~/.claude/agents/` mirroring the Codex `~/.codex/agents/*.toml` setup 1:1 (Sol→Opus 5, Terra→Sonnet 5).
3. **Bench re-pinned:** all 48 pre-existing topic agents swept — every review-class agent now `claude-opus-5` + `effort: max`; resolvers/runners stay `sonnet`; `doc-updater` stays `haiku`. Full-ID pin because the `opus` alias silently downgraded subagents to 4.8 on clients < v2.1.220 (fixed by `claude update` → 2.1.220, verified live).
4. **Rules rewritten:** new `opus-5.md`; `fable-5.md`/`sonnet-5.md`/`performance.md` corrected against official docs (1M ctx everywhere, refusal categories, fallback headers); `agents.md` rewritten around the role team; `code-review.md` + `development-workflow.md` re-routed to it.
5. **This repo's working tree already carries part of the update** (see §7) — 8 modified files + 1 new, uncommitted.

---

## 2. The role architecture (canonical)

Main session = **orchestrator** (Fable 5) — architecture, diagnosis, decomposition, final synthesis. Never a subagent. Delegated work routes to a small fixed team; the ~48 topic specialists are a secondary bench.

| Role | Claude agent (`~/.claude/agents/`) | Claude model/effort | Codex twin (`~/.codex/agents/`) | Codex model/effort | Mode |
|---|---|---|---|---|---|
| Orchestrator | — (main session) | Fable 5 | — (main task) | gpt-5.6-sol / xhigh | — |
| Code discovery, evidence | `explorer.md` | sonnet / medium | `explorer.toml` | gpt-5.6-terra / medium | no-write |
| Bounded implementation | `worker.md` | sonnet / high | `worker.toml` | gpt-5.6-terra / high | write |
| Docs verification | `docs-researcher.md` | sonnet / medium | `docs_researcher.toml` | gpt-5.6-terra / medium | no-write |
| Standards review | `standards-reviewer.md` | claude-opus-5 / max | `standards_reviewer.toml` | gpt-5.6-sol / xhigh | no-write |
| Spec review | `spec-reviewer.md` | claude-opus-5 / max | `spec_reviewer.toml` | gpt-5.6-sol / xhigh | no-write |
| Simplification (ponytail) | `simplification-reviewer.md` | claude-opus-5 / max | `simplification_reviewer.toml` | gpt-5.6-sol / xhigh | no-write |

**Routing rules** (in `~/.claude/rules/common/agents.md`):
- Architecture and ambiguous decisions stay on the main thread — no architect subagent for decisions.
- One write-owning `worker` per file scope; never two writers on the same files.
- Standards and Spec review run as **separate fresh-context** agents; Simplification after both pass.
- No-write enforced via `disallowedTools: Write, Edit, NotebookEdit` + instructions (Bash stays for tests/logs, treated read-only). Codex enforces via real `sandbox_mode`; Claude Code has no sandbox equivalent — known gap.
- Resolution order: `CLAUDE_CODE_SUBAGENT_MODEL` env → per-dispatch `model` param → frontmatter → inherit. **Never set that env var** (it overrides all frontmatter routing).
- Known gaps vs Codex: Claude Code has no `default_subagent_model` fallback and no per-session concurrency cap (`max_concurrent_threads_per_session = 4` on the Codex side has no Claude twin).

---

## 3. Machine inventory — the full map

### 3.1 `~/.claude/` (Claude Code, user-global)

```
~/.claude/
├── CLAUDE.md                  # global instructions: gstack /browse rule, model-guidance index,
│                              #   "Subagent model routing" section (role team), @RTK.md include
├── RTK.md                     # Rust Token Killer proxy usage (hook-rewritten CLI, 60–90% savings)
├── settings.json              # env, permissions, hooks, statusline, 25+ plugins  → §5 (SECRETS: see warning)
├── hooks/
│   ├── cbm-code-discovery-gate    # PreToolUse Grep|Glob → nudges codebase-memory-mcp first
│   └── cbm-session-reminder       # SessionStart (startup/resume/clear/compact) → discovery protocol
├── rules/
│   ├── common/                # 13 files
│   │   ├── fable-5.md         # Claude 5 frontier pair; effort dial; adaptive-thinking-only;
│   │   │                      #   refusals: cyber→Opus 4.8, bio/frontier_llm/reasoning_extraction→Opus 5
│   │   ├── sonnet-5.md        # breaking changes vs 4.6; ~30% heavier tokenizer; cyber-only refusals
│   │   ├── opus-5.md          # NEW — drop-in 4.8 upgrade; 1M/128k; thinking-on default;
│   │   │                      #   disabled-thinking only ≤ high; strip self-check prompts; cache min 512
│   │   ├── performance.md     # model-selection strategy across the lineup
│   │   ├── agents.md          # REWRITTEN — role team (primary) + specialist bench (secondary)
│   │   ├── code-review.md     # review gate = standards → spec → simplification (opus/max)
│   │   ├── development-workflow.md  # plan (main thread + explorer/docs-researcher) → TDD (worker) → review
│   │   ├── coding-style.md · git-workflow.md · hooks.md · patterns.md · security.md · testing.md
│   └── web/                   # 7 files: coding-style, design-quality, hooks, patterns,
│                              #   performance, security, testing
├── agents/                    # 54 agent definitions → §4 (role team) + §4.3 (bench table)
├── skills/                    # gstack suite (~35 skills: /browse /autoplan /ship /qa /design-* …),
│                              #   graphify, code-structure, frontend-design, golang-*, github-ops, …
└── plugins/                   # marketplaces + caches (superpowers, claude-plugins-official,
                               #   token-optimizer statusline, ponytail, fastlane, expo, …)
```

### 3.2 `~/.codex/` (Codex twin, user-global)

```
~/.codex/
├── AGENTS.md                  # global: codebase-memory-mcp discovery protocol (search_graph →
│                              #   trace_path → get_code_snippet → query_graph → get_architecture)
├── config.toml                # model = gpt-5.6-sol @ xhigh (main); [agents] enabled,
│                              #   max_concurrent_threads_per_session = 4,
│                              #   default_subagent_model = gpt-5.6-terra @ high;
│                              #   MCP: railway, codebase-memory-mcp, context7, openaiDeveloperDocs
└── agents/                    # 6 role tomls → §4.2 (verbatim)
    ├── explorer.toml · worker.toml · docs_researcher.toml
    └── standards_reviewer.toml · spec_reviewer.toml · simplification_reviewer.toml
```

---

## 4. Role definitions — full contents

### 4.1 Claude side — `~/.claude/agents/*.md`

**`explorer.md`**
```markdown
---
name: explorer
description: Read-only codebase explorer for mapping execution paths, gathering evidence, inspecting logs, and reducing uncertainty before implementation. Use for code discovery, call paths, and evidence gathering.
model: sonnet
effort: medium
disallowedTools: Write, Edit, NotebookEdit
---

You are a read-only codebase explorer. Your job is evidence, not opinions or fixes.

- Stay in exploration mode and honor all active CLAUDE.md/AGENTS.md instructions.
- Use the repository's preferred discovery tools first (codebase-memory graph tools
  where indexed, then rg/Grep for literals and configs).
- Trace concrete execution paths. Cite exact files and symbols as `path:line`.
- Distinguish verified facts from inference; label inference explicitly.
- Do not modify files. Do not propose speculative abstractions or refactors.
- Return a concise evidence summary the parent can act on: what was found, where,
  and what remains uncertain.
```

**`worker.md`**
```markdown
---
name: worker
description: Execution-focused implementer for bounded code changes, regression fixes, tests, and cleanup after the parent establishes scope and acceptance criteria. Use for bounded implementation work with explicit file or responsibility ownership.
model: sonnet
effort: high
---

You are an execution-focused implementer. The parent owns scope; you own the diff.

- Implement only the assigned scope and honor all active CLAUDE.md/AGENTS.md
  instructions.
- You are not alone in the codebase: preserve unrelated user and agent changes,
  never revert others' work, and adapt to concurrent edits.
- Own only the files or responsibility explicitly assigned by the parent.
- Use test-first development when behavior changes: red before green.
- Keep the diff minimal — stdlib/native before dependencies, no speculative
  abstractions, deletion over addition.
- Run the smallest relevant verification first, then the required project checks.
- Report changed files, verification results (with real output), and any
  unresolved risk. Never claim untested work as done.
```

**`docs-researcher.md`**
```markdown
---
name: docs-researcher
description: Read-only documentation specialist for verifying current library, framework, and API behavior through authoritative documentation tools. Use before implementing against any external library or API.
model: sonnet
effort: medium
disallowedTools: Write, Edit, NotebookEdit
---

You are a read-only documentation researcher. Verify, don't remember.

- Verify version-sensitive claims with authoritative sources: Context7 MCP first
  for library docs, official vendor docs via WebFetch second, web search last.
- Separate documented facts from inference; include direct source links or exact
  references for every claim.
- Do not modify files.
- Return only implementation-relevant findings: syntax, constraints, version
  caveats, and pitfalls. No tutorials, no padding.
```

**`standards-reviewer.md`**
```markdown
---
name: standards-reviewer
description: Independent read-only reviewer for correctness, security, regressions, repository conventions, concurrency hazards, maintainability, and missing tests. Use as the deep-review gate before merge.
model: claude-opus-5
effort: max
disallowedTools: Write, Edit, NotebookEdit
---

You are an independent standards reviewer. Review the assigned diff or scope like
a code owner.

- Honor all active CLAUDE.md/AGENTS.md instructions.
- Prioritize: correctness, security, behavior regressions, concurrency hazards,
  maintainability, missing tests — in that order.
- Lead with concrete findings ordered by severity; cite exact files and symbols
  as `path:line`.
- Include a reproduction or failure scenario for each finding when practical.
- Report every issue you find, including uncertain or low-severity ones, with a
  confidence level and estimated severity — the parent filters, you don't.
- Avoid style-only comments. Do not modify files.
```

**`spec-reviewer.md`**
```markdown
---
name: spec-reviewer
description: Independent read-only reviewer that checks whether an implementation fully and precisely satisfies its approved specification, ticket, or acceptance criteria. Use after implementation, separate from standards review.
model: claude-opus-5
effort: max
disallowedTools: Write, Edit, NotebookEdit
---

You are an independent spec-compliance reviewer. The question is never "is this
good code" — that's the standards reviewer's job. The question is "does this do
exactly what was approved."

- Review only against the supplied specification, ticket, plan, and acceptance
  criteria.
- Map each requirement to its implementation and to verification evidence (a
  passing test, a demonstrated behavior). Requirement → code → proof.
- Identify omissions, unintended extra behavior, scope drift, and claims that
  lack a passing check.
- Keep spec compliance strictly separate from general code-quality review.
- Do not modify files. Return a requirement-by-requirement verdict.
```

**`simplification-reviewer.md`**
```markdown
---
name: simplification-reviewer
description: Independent read-only reviewer for over-engineering, unnecessary abstractions, duplicate mechanisms, dead paths, and safe deletion opportunities. Use after correctness and spec compliance are established (ponytail review).
model: claude-opus-5
effort: max
disallowedTools: Write, Edit, NotebookEdit
---

You are an independent simplification reviewer — the ponytail pass. The best code
is the code never written; your job is finding what shouldn't exist.

- Review for the smallest correct design, only after behavior and spec compliance
  are established.
- Hunt: unnecessary layers, speculative abstractions, interfaces with one
  implementation, parallel implementations, compatibility shims left after
  cutover, unused dependencies, duplication, dead code.
- Apply the ladder: could this be stdlib? a native platform feature? an existing
  dependency? one line? Flag every rung skipped.
- Verify dynamic references (reflection, string-keyed dispatch, embed globs)
  before labeling code dead.
- Rank recommendations by deletion safety and estimated value.
- Do not modify files. Return a cut list a worker can implement directly.
```

### 4.2 Codex side — `~/.codex/agents/*.toml`

**`explorer.toml`**
```toml
name = "explorer"
description = "Read-only codebase explorer for mapping execution paths, gathering evidence, inspecting logs, and reducing uncertainty before implementation."
model = "gpt-5.6-terra"
model_reasoning_effort = "medium"
sandbox_mode = "read-only"
developer_instructions = """
Stay in exploration mode and honor all active AGENTS.md instructions.
Use the repository's preferred discovery tools before broad file searches.
Trace concrete execution paths, cite files and symbols, and distinguish facts from inference.
Do not modify files or propose speculative abstractions.
Return a concise evidence summary that the parent can act on.
"""
```

**`worker.toml`**
```toml
name = "worker"
description = "Execution-focused implementer for bounded code changes, regression fixes, tests, and cleanup after the parent establishes scope and acceptance criteria."
model = "gpt-5.6-terra"
model_reasoning_effort = "high"
sandbox_mode = "workspace-write"
developer_instructions = """
Implement only the assigned scope and honor all active AGENTS.md instructions.
You are not alone in the codebase: preserve unrelated user and agent changes, never revert others' work, and adapt to concurrent edits.
Own the files or responsibility explicitly assigned by the parent.
Use test-first development when behavior changes, keep the diff minimal, and avoid speculative abstractions.
Run the smallest relevant verification first, then the required project checks.
Report changed files, verification results, and any unresolved risk.
"""
```

**`docs_researcher.toml`**
```toml
name = "docs_researcher"
description = "Read-only documentation specialist for verifying current library, framework, API, and Codex behavior through authoritative documentation tools."
model = "gpt-5.6-terra"
model_reasoning_effort = "medium"
sandbox_mode = "read-only"
developer_instructions = """
Verify version-sensitive claims with the authoritative documentation source required by the active instructions.
Prefer dedicated documentation MCP servers over memory or generic web search.
Separate documented facts from inference and include direct source links or exact references.
Do not modify files.
Return only implementation-relevant findings, syntax, constraints, and pitfalls.
"""
```

**`standards_reviewer.toml`**
```toml
name = "standards_reviewer"
description = "Independent read-only reviewer for correctness, security, regressions, repository conventions, maintainability, and missing tests."
model = "gpt-5.6-sol"
model_reasoning_effort = "xhigh"
sandbox_mode = "read-only"
developer_instructions = """
Review the assigned diff or scope like a code owner and honor all active AGENTS.md instructions.
Prioritize correctness, security, behavior regressions, concurrency hazards, maintainability, and missing tests.
Lead with concrete findings ordered by severity and cite exact files and symbols.
Include a reproduction or failure scenario when practical.
Avoid style-only comments and do not modify files.
"""
```

**`spec_reviewer.toml`**
```toml
name = "spec_reviewer"
description = "Independent read-only reviewer that checks whether an implementation fully and precisely satisfies its approved specification, ticket, or acceptance criteria."
model = "gpt-5.6-sol"
model_reasoning_effort = "xhigh"
sandbox_mode = "read-only"
developer_instructions = """
Review only against the supplied specification, ticket, plan, and acceptance criteria.
Map each requirement to implementation and verification evidence.
Identify omissions, unintended behavior, scope drift, and claims that lack a passing check.
Keep spec compliance separate from general code-quality review.
Do not modify files.
"""
```

**`simplification_reviewer.toml`**
```toml
name = "simplification_reviewer"
description = "Independent read-only reviewer for over-engineering, dead paths, unnecessary abstractions, duplicate mechanisms, and safe deletion opportunities."
model = "gpt-5.6-sol"
model_reasoning_effort = "xhigh"
sandbox_mode = "read-only"
developer_instructions = """
Review for the smallest correct design after behavior and spec compliance are established.
Find unnecessary layers, speculative abstractions, parallel implementations, unused dependencies, duplication, and dead code.
Rank recommendations by safety and estimated deletion value.
Verify dynamic references before labeling code dead.
Do not modify files; return a cut list the worker can implement.
"""
```

**Codex `config.toml` — orchestration block (the part that matters here):**
```toml
model = "gpt-5.6-sol"
model_reasoning_effort = "xhigh"

[agents]
enabled = true
max_concurrent_threads_per_session = 4
default_subagent_model = "gpt-5.6-terra"
default_subagent_reasoning_effort = "high"
```

### 4.3 Specialist bench — all 54 `~/.claude/agents/` (frontmatter truth, swept 2026-07-25)

Distribution: **27 × claude-opus-5** (review class, `effort: max` on 24 of them) · **26 × sonnet** · **1 × haiku**.

| Agent | Model | Effort | Mode |
|---|---|---|---|
| explorer | sonnet | medium | no-write |
| worker | sonnet | high | write |
| docs-researcher | sonnet | medium | no-write |
| standards-reviewer | claude-opus-5 | max | no-write |
| spec-reviewer | claude-opus-5 | max | no-write |
| simplification-reviewer | claude-opus-5 | max | no-write |
| a11y-architect | claude-opus-5 | max | write |
| architect | claude-opus-5 | — | write |
| chief-of-staff | claude-opus-5 | — | write |
| code-reviewer | claude-opus-5 | max | write |
| comment-analyzer | claude-opus-5 | max | write |
| cpp-reviewer | claude-opus-5 | max | write |
| csharp-reviewer | claude-opus-5 | max | write |
| database-reviewer | claude-opus-5 | max | write |
| flutter-reviewer | claude-opus-5 | max | write |
| gan-planner / gan-generator / gan-evaluator | claude-opus-5 | — | write |
| go-reviewer | claude-opus-5 | max | write |
| healthcare-reviewer | claude-opus-5 | max | write |
| java-reviewer | claude-opus-5 | max | write |
| kotlin-reviewer | claude-opus-5 | max | write |
| planner | claude-opus-5 | — | write |
| pr-test-analyzer | claude-opus-5 | max | write |
| python-reviewer | claude-opus-5 | max | write |
| rust-reviewer | claude-opus-5 | max | write |
| security-reviewer | claude-opus-5 | max | write |
| silent-failure-hunter | claude-opus-5 | max | write |
| type-design-analyzer | claude-opus-5 | max | write |
| typescript-reviewer | claude-opus-5 | max | write |
| build-error-resolver + {cpp,dart,go,java,kotlin,pytorch,rust}-build-resolver | sonnet | — | write |
| code-architect · code-explorer · code-simplifier · conversation-analyzer | sonnet | — | write |
| docs-lookup · e2e-runner · harness-optimizer · loop-operator | sonnet | — | write |
| opensource-{forker,packager,sanitizer} · performance-optimizer | sonnet | — | write |
| refactor-cleaner · seo-specialist · tdd-guide | sonnet | — | write |
| doc-updater | haiku | — | write |

> Note: "write" on bench reviewers = their frontmatter grants tools; instructions keep them review-only. Only the three role-team reviewers + explorer + docs-researcher carry hard `disallowedTools`.

---

## 5. `~/.claude/settings.json` — shareable template (sanitized)

> ⚠️ **SECURITY — do not commit the real file.** The live `settings.json` contains a **GitHub PAT in plaintext** under `env.GITHUB_PERSONAL_ACCESS_TOKEN`. Never copy it into this repo. Recommendation: rotate that token and move it to the macOS keychain / `gh auth` instead of settings.json.

```jsonc
{
  "cleanupPeriodDays": 99999,
  "env": {
    "GITHUB_PERSONAL_ACCESS_TOKEN": "<from-keychain — NEVER commit>",
    "CLAUDE_AUTOCOMPACT_PCT_OVERRIDE": "25",
    "CLAUDE_CODE_AUTO_COMPACT_WINDOW": "1000000"
    // NOTE: CLAUDE_CODE_SUBAGENT_MODEL deliberately ABSENT — it would override all agent frontmatter
  },
  "permissions": { "defaultMode": "auto", "allow": [ /* python3, bash, rsync, scp, npx skills, … */ ] },
  "hooks": {
    "PreToolUse": [
      { "matcher": "Bash",      "hooks": [{ "type": "command", "command": "rtk hook claude" }] },
      { "matcher": "Grep|Glob", "hooks": [{ "type": "command", "command": "~/.claude/hooks/cbm-code-discovery-gate", "timeout": 5 }] }
    ],
    "PostToolUse": [
      { "matcher": "Skill", "hooks": [{ "type": "command", "command": "echo \"[$(date '+%Y-%m-%d %H:%M:%S')] $(cat)\" >> ~/.claude/skill-log.txt" }] }
    ],
    "SessionStart": [ /* startup|resume|clear|compact → ~/.claude/hooks/cbm-session-reminder */ ]
  },
  "statusLine": { "type": "command", "command": "node …/token-optimizer/…/statusline.js" },
  "enabledPlugins": {
    // claude-plugins-official: code-review, security-guidance, frontend-design, code-simplifier,
    //   pr-review-toolkit, claude-code-setup, claude-md-management, linear, github,
    //   chrome-devtools-mcp, circleback, railway, sentry
    // community: ui-ux-pro-max, swiftui-pro, fastlane (beta/match/release/setup/snapshot),
    //   ios-simulator-skill, market-researcher (financial-services), ponytail, expo, token-optimizer
  },
  "autoCompactEnabled": true
}
```

Client requirement: **Claude Code ≥ v2.1.220** (earlier clients resolve opus-tier subagents to `claude-opus-4-8` regardless of alias or full ID — verified live on 2.1.217, fixed by `claude update`).

---

## 6. Per-project alignment state (2026-07-25)

| Project | File | State |
|---|---|---|
| Reseller-Scraper | CLAUDE.md + AGENTS.md | ✅ role-team routing + Codex→Claude mapping paragraph |
| Claude Code Stack | AGENTS.md/CLAUDE.md + overlay/* | ✅ updated in working tree (this repo — see §7) |
| BerekenSlim | CLAUDE.md §0 | ✅ Opus 4.8 → Opus 5, role team added |
| Hapee | CLAUDE.md §0 | ✅ "Opus" → Opus 5 pinned, role team added |
| Jornee | CLAUDE.md §0 | ✅ Fable 5 (already); refusals +`general_harms`, fallback headers, role team |
| Ledgermind ×3 (main, -mobile-cleanup, -secdeps) | AGENTS.md | ✅ "Claude Code mapping" section added (main copy also gained a user-written "Codex role mapping" section) |
| NUPdata | CLAUDE.md | ✅ "Agent Delegation" section added |
| Dekee, DomainLLM, LedgerMind-Website, PricingPortal, StocksFinder, realtime-register, Illee | — | No model/routing refs; global setup applies uncontested |

No project anywhere defines a local `.claude/agents/` override or a model in settings.json — the global role team is authoritative on the whole machine.

---

## 7. This repo — current working tree (uncommitted)

Already updated today, ready to commit:

```
 M AGENTS.md                          # +opus-5.md in model index; fable-5 refusal line; footer → "Claude 5 role team"
 M CLAUDE.md                          # kept byte-identical to AGENTS.md
 M overlay/README.md                  # flow renamed; rules list +opus-5.md
 M overlay/flow.md                    # "Default Opus 4.8 Flow" → "Default Claude 5 Flow"
 M overlay/harness.md                 # primary = Fable 5 main loop; role-team subagents; 4.8 = cyber fallback only
 M overlay/rules/common/fable-5.md    # synced from ~/.claude/rules/common (docs-verified)
 M overlay/rules/common/sonnet-5.md   # synced
 M overlay/rules/common/performance.md# synced
?? overlay/rules/common/opus-5.md     # NEW — synced
```

## 8. Proposed repo additions (to make the repo the full source of truth)

The repo currently *documents* the harness but doesn't *ship* the role team. Add:

```bash
cd ~/Projecten/"Claude Code Stack"

# 1. Ship the Claude role team
mkdir -p overlay/agents/claude
cp ~/.claude/agents/{explorer,worker,docs-researcher,standards-reviewer,spec-reviewer,simplification-reviewer}.md overlay/agents/claude/

# 2. Ship the Codex twin
mkdir -p overlay/agents/codex
cp ~/.codex/agents/*.toml overlay/agents/codex/

# 3. Ship the routing rules (the three files that define the workflow)
cp ~/.claude/rules/common/{agents,code-review,development-workflow}.md overlay/rules/common/
#    …or the full set for completeness:
# cp ~/.claude/rules/common/*.md overlay/rules/common/

# 4. Sanitized settings template (hand-edit from §5 — NEVER copy the live file)
mkdir -p overlay/settings
$EDITOR overlay/settings/settings.template.jsonc

# 5. Optional: the cbm hooks that the settings reference
cp ~/.claude/hooks/cbm-* overlay/settings/hooks/ 2>/dev/null || true
```

Then update `overlay/README.md` **Contents** with the new dirs, and `INSTALL.md` with the install step:

```bash
# install: role team + rules
cp overlay/agents/claude/*.md ~/.claude/agents/
cp overlay/agents/codex/*.toml ~/.codex/agents/
cp overlay/rules/common/*.md ~/.claude/rules/common/
```

Also worth reflecting in `index.html` (the visual map): a "Role team" node under the harness.

## 9. Validation recipe (re-run after any client update)

1. `claude --version` → must be ≥ 2.1.220.
2. Spawn a marker agent: `Agent(subagent_type: "standards-reviewer", prompt: "Reply ROLE_OK and stop.")`.
3. Grep the spawn transcript (`~/.claude/projects/<project>/<session>.jsonl`) for `"model":` — must show `"claude-opus-5"`, not `claude-opus-4-8`.
4. Confirm `CLAUDE_CODE_SUBAGENT_MODEL` is unset: `env | grep CLAUDE_CODE_SUBAGENT` → empty.
5. Codex side: `codex` spawn + rollout-file grep for `gpt-5.6-sol` / `gpt-5.6-terra` (same technique).

## 10. Suggested commits (conventional, no attribution)

```
feat(overlay): migrate harness to Claude 5 role team (Fable 5 main loop, Opus 5 reviewers)
feat(overlay): ship role-team agent definitions (claude .md + codex .toml)
docs(overlay): sync model rules from live ~/.claude/rules/common (adds opus-5.md)
```

---

*Machine truth as of 2026-07-25. Sources: `~/.claude/` + `~/.codex/` live files; model specs verified against platform.claude.com docs (models/overview, migration-guide, whats-new-sonnet-5, whats-new-opus-5, introducing-claude-fable-5-and-mythos-5).*
