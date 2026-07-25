# Claude Harness

How this machine is wired. Assume these defaults unless a project overrides them.

## Model & orchestration

- **Primary model:** Fable 5 (`claude-fable-5`) for orchestration, architecture, and the hardest long-horizon work — main loop only, never a subagent. Opus 4.8 survives only as the cyber-refusal fallback.
- **Role-team subagents** (`~/.claude/agents/`): `explorer` / `worker` / `docs-researcher` (Sonnet 5) scout + implement; `standards-reviewer` / `spec-reviewer` / `simplification-reviewer` (Opus 5 `claude-opus-5`, max effort, read-only) review. Haiku 4.5 (`claude-haiku-4-5-20251001`) for frequent, narrow workers.
- **Planning:** `/autoplan` — gstack's auto-review pipeline. Expands a plan and runs CEO / design / eng / DX reviews with auto-decisions, surfacing only taste calls at a final gate.
- **Multi-agent orchestration:** the **Workflow** tool — deterministic fan-out / pipeline of subagents for comprehensive sweeps, adversarial verification, and work too large for one context. With "ultracode" on, author a workflow per substantive phase by default.
- **Parallel subagents:** dispatch independent work with the Agent tool in a single message so it runs concurrently.

## Token efficiency

- **RTK (Rust Token Killer):** a hook-rewritten CLI proxy (`rtk`) wrapping dev commands for 60–90% token savings — transparent (`git status` → `rtk git status`). `rtk gain` shows savings.
- **Caveman** + **graphify** + **Pitlane** — see Rails 01–02.

## Hooks (recommended order)

format → lint → typecheck → build, on `Write`/`Edit`, using project-local tooling. See `~/.claude/rules/web/hooks.md`.

## Agents & reviewers (`~/.claude/agents`)

Role team first (explorer, worker, docs-researcher, standards-/spec-/simplification-reviewer — see above). Specialist bench for domain depth: code-reviewer, security-reviewer, language reviewers (typescript / python / go / rust / java / kotlin / csharp / cpp / flutter), build resolvers (build-error-resolver + go / rust / java / kotlin / cpp / dart), refactor-cleaner, doc-updater, tdd-guide, e2e-runner, planner, architect. Invoke proactively per `~/.claude/rules/common/agents.md`.

## Memory

Track work as issues — GitHub Issues (`gh`) or Linear — not `TODO.md` / `MEMORY.md` files. Durable design decisions go in ADRs (via `improve-codebase-architecture`); the codebase graph lives in `graphify-out/`.
