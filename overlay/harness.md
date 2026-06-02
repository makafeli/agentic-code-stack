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

Persist durable decisions in-repo — ADRs (via `improve-codebase-architecture`) and the codebase knowledge graph (`graphify-out/`). Don't scatter `TODO.md` / `MEMORY.md` files.
