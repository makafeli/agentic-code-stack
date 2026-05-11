# Design: Printing Press Integration into Agentic Code Stack

## Summary

Add Printing Press (printingpress.dev) to the Agentic Code Stack as a new rail: **Rail 05 — Agent Interfaces**. Printing Press generates agent-native Go CLIs, Claude Code skills, OpenClaw skills, and MCP servers from API specs, HAR files, URLs, or descriptions. It ships with a community library of 70+ pre-built CLIs.

## Why a new rail

Printing Press solves a different problem than existing rails:

| Rail | Purpose |
|------|---------|
| Rail 04 (External Data) | Read-only web data retrieval (Brightdata) |
| **Rail 05 (Agent Interfaces)** | Structured service interaction through generated CLIs |

The distinction: Brightdata scrapes pages. Printing Press gives agents purpose-built interfaces to *act* on services (book flights, query CRMs, trigger deploys, join data across APIs).

## Files changed/created

### New files
- `rails/05-agent-interfaces.md` — Full rail documentation (~80 lines)

### Modified files
- `AGENTS.md` — Add Rail 05 to the always-on rails summary
- `CLAUDE.md` — Same change (mirror of AGENTS.md)
- `index.html` — Add 5th rail card, let grid reflow to 3+2 layout
- `README.md` — Add rail 05 to directory tree
- `rails/04-external-data.md` — Add cross-reference to Rail 05

## Rail content outline

`rails/05-agent-interfaces.md` covers:

1. **The problem** — Agents fumble raw HTTP; scraping is read-only; they need structured interfaces to *act*
2. **The solution** — Printing Press generates CLIs with local SQLite, compound queries, structured output
3. **Two modes:**
   - Install from library (70+ pre-built CLIs via `npx`)
   - Print new CLIs from specs/URLs (`printing-press generate`)
4. **Key patterns** — Local SQLite mirror, compound commands, structured output, cross-source joins
5. **When to engage** — Multi-use services, complex auth, multi-source workflows, offline-first needs
6. **Setup** — `go install` for binary, `git clone` for skills
7. **Cross-references** — When to use Brightdata vs Printing Press

## Diagram changes

The Rails grid in `index.html` currently shows 4 rails in one row. Adding the 5th rail means the flexbox grid naturally wraps to a 3+2 layout:

```
Row 1: [Docs & Context]  [Token Efficiency]  [Memory & Tracking]
Row 2: [External Data]   [Agent Interfaces]
```

No CSS changes needed — the `.rail` items already use flexbox with `min-width` that allows wrapping. The new rail card follows the same structure as existing ones (rail-head with tag/heading/desc, rail-body with tool entries).

## Naming

No naming changes. The rail is titled "Agent Interfaces" to stay conceptually clear — it's about interfaces agents use to interact with services, not just another data source.

## Scope

Single rail addition. No changes to pipeline stages, no changes to other rails (beyond cross-reference links), no community-recipes additions in this change.
