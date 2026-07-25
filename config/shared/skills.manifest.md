# Skills Manifest

What is installed, where it came from, and — importantly — what can't be
reproduced on a clean machine.

This is a **manifest, not a mirror.** No skill source is vendored into this
repo. Install commands are in
[`external-tools.md`](external-tools.md) and [INSTALL.md](../../INSTALL.md).

## Layout

Skills live in `~/.agents/skills/` as real directories, and `~/.claude/skills/`
symlinks into that. One physical copy, two names.

```text
~/.agents/skills/<name>/          real directory
~/.claude/skills/<name>  ->  ../../.agents/skills/<name>
```

Counted 2026-07-25: **119** physical shared skill directories, **245** entries
reachable under `~/.claude/skills`.

## The stack skills

Everything the pipeline and rails actually route to.

### Matt Pocock — `mattpocock/skills`

| Skill | Stage |
|---|---|
| `wayfinder` | Align, Spec & Plan |
| `grilling`, `domain-modeling` | Align |
| `prototype` | Align, Design |
| `codebase-design` | Spec & Plan |
| `design-an-interface` | Design |
| `tdd` | Build |
| `diagnosing-bugs` (`/diagnose`) | Build |
| `code-review` | Build — review gate 1 |
| `deadcode` | Build — sweep |
| `request-refactor-plan` | Build |
| `caveman` | Rail 02 |
| `write-a-skill` | Meta |
| `writing-guidelines`, `writing-shape`, `writing-fragments` | Content |
| `scaffold-exercises` | Meta |

`/grill-me` and `/grill-with-docs` come from the Anthropic skill set and are
listed under Align alongside these.

### Everything else in the stack

| Skill / tool | Source | Stage |
|---|---|---|
| `ponytail`, `ponytail-review`, `ponytail-audit` | `DietrichGebert/ponytail` | Build, Rail 02 |
| `improve` | `shadcn/improve` | Spec & Plan |
| `autofix` | CodeRabbit integration | Build |
| `code-structure` | — | Build |
| `frontend-design` | Anthropic built-in | Design |
| `codebase-memory` | `deusdata/codebase-memory-mcp` | Rails 01, 02 |
| `proxy-manager` | Webshare | Rail 04 |
| `brightdata-cli` | Bright Data | Rail 04 |
| `google-search-console-cli` | `Bin-Huang/...` | Rail 04 |

## Categories kept outside the stack

Installed and used, but not routed to by `pipeline/` or `rails/` — they answer
domain questions, not workflow ones.

| Category | Examples |
|---|---|
| Apple / iOS | `asc-*` (App Store Connect), `swiftui-*`, `ios-*`, `liquid-glass-design` |
| Android / Kotlin | `android-*`, `kotlin-*`, `navigation-3`, `r8-analyzer` |
| SEO / GEO / content | the 16 at `19.0.0` — see [external-tools.md](external-tools.md) |
| Language + framework packs | `python-*`, `golang-*`, `rust-*`, `laravel-*`, `springboot-*`, `nestjs-*`, `perl-*` |
| Business / vertical ops | `healthcare-phi-compliance`, `hipaa-compliance`, `inventory-demand-planning`, `logistics-*`, `production-scheduling`, `investor-*`, `lead-intelligence` |
| Meta / tooling | `skillify`, `write-a-skill`, `prompt-optimizer`, `hookify-rules`, `token-budget-advisor` |

## ⚠️ Reproducibility gap

`~/.agents/.skill-lock.json` holds **87** entries against **119** physical skill
directories.

**32 skills have no recorded source.** A clean machine cannot rebuild them — you
would get 87 of 119 and no error telling you which 32 are missing. They were
installed by hand, or from a source that has since moved, or copied in.

This is a real gap, and it is machine hygiene rather than repo scope: this repo
records that the gap exists and what it means. Closing it means auditing the 32
and either recording a source or deleting them.

The same class of problem produced the SEO/GEO surprise — a recorded URL that
had silently become a signpost to somewhere else. Sources drift; unlocked skills
drift invisibly.

## Dropped

**gstack** (30 skills) and **superpowers** are no longer part of this stack.
They may still be installed on a given machine. Nothing in `pipeline/` or
`rails/` routes to either.

To clean a machine, the marketplace entries to remove are
`superpowers-marketplace` on the Claude side and `superpowers@openai-curated`
on the Codex side.
