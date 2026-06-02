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
