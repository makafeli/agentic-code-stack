# Design: Build Stage Skill Additions

## Summary

Add four tools to Pipeline Stage 5 (Build) in the Agentic Code Stack:
1. **Vercel Skills CLI** — package manager for the agent skills ecosystem
2. **code-structure** — Service Layer Architecture pattern enforcement
3. **improve-codebase-architecture** — architectural deepening and ADR generation
4. **check-pr / greploop** — automated PR review iteration loop

## Placement

All four go into the **Build** stage, ordered to mirror the development flow:

```
Build:
├── npx skills add              ← package manager for all skills
├── /superpowers:subagent-driven-development
├── code-structure              ← service layer architecture pattern
├── improve-codebase-architecture ← architectural deepening + ADRs
├── /superpowers:requesting-code-review
├── check-pr / greploop         ← automated PR review iteration
├── /superpowers:finishing-a-development-branch
├── TDD
└── Apple-platform tools
```

## Tool descriptions

| Tool | Description | Setup |
|------|-------------|-------|
| `npx skills` | Install skills into any agent across 55+ platforms — the npm of the agent skills ecosystem | `npx skills add <repo>` |
| `code-structure` | Service Layer Architecture: Actions orchestrate domain rules, Service Layer centralizes reusable operations. Prevents duplicated operational logic | `npx skills add michaelshimeles/skills --skill code-structure` |
| `improve-codebase-architecture` | Find shallow modules, propose deepening refactors, document decisions as ADRs. Uses precise vocabulary: depth, seams, locality, leverage | `npx skills add mattpocock/skills --skill improve-codebase-architecture` |
| `check-pr` / `greploop` | Automated PR review: check unresolved comments, failing CI, incomplete descriptions. greploop loops review→fix→re-review until 5/5 confidence | `git clone https://github.com/greptileai/skills.git ~/.claude/skills/greptile` |

## Files changed

| File | Change |
|------|--------|
| `AGENTS.md` | Add 4 tool entries to Build section |
| `CLAUDE.md` | Mirror same additions |
| `index.html` | Add 4 entries to Build stage card |
| `pipeline/05-build.md` | Add detailed references for each tool |

No new files. No new rails. No structural changes.

## Why these fit in Build

- **npx skills**: The gateway — installs all other skills. Used during development when new capabilities are needed.
- **code-structure**: A pattern to apply during implementation. "Write in action first → see repeated ops → extract to service."
- **improve-codebase-architecture**: Post-build architectural review. Finds friction after you have working code, proposes deepening refactors.
- **check-pr / greploop**: Pre-merge automation. The last step before finishing a branch — automated review, fix, re-review loop.
