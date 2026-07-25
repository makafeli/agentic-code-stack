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

## Reproducibility gap

`~/.agents/.skill-lock.json` holds **87** entries against **119** physical skill
directories, so **38 skills sit outside the lockfile**.

Audited 2026-07-25. The lockfile only tracks what `npx skills add` installed —
it is not a record of everything present, so most of the 38 are *unlocked*
rather than *unknown*:

| Count | Source | How it was identified |
|---|---|---|
| 17 | `android/skills` (Google) | Referenced in-skill; `developer.android.com` throughout |
| 16 | `aaron-he-zhu/aaron-marketing-skills` `19.0.0` | `homepage:` declared in their own `SKILL.md` |
| 2 | `DietrichGebert/ponytail` | Installed as a plugin, real copy under `~/.claude/plugins/cache/ponytail/` |
| 1 | `mattpocock/skills` (`deadcode`) | In the Pocock set, missed by the lock |
| **2** | **unattributed** | `brand-voice` (`origin: ECC`), `human-writer` (`license: MIT`) |

Reinstalling the first three groups through their own channel is what closes
most of the gap:

```bash
npx skills add android/skills
npx skills add aaron-he-zhu/aaron-marketing-skills
claude plugin install ponytail@ponytail
```

That leaves **two** skills genuinely unaccounted for. Both work; neither can be
rebuilt from a recorded source.

The residue matters less than the mechanism: a lockfile that covers one install
channel reads as a complete inventory when it is not. The same class of problem
produced the SEO/GEO surprise — a recorded URL that had silently become a
signpost. Sources drift; skills outside the lock drift invisibly.

## Dropped

**gstack** (30 skills) and **superpowers** are no longer part of this stack.
They may still be installed on a given machine. Nothing in `pipeline/` or
`rails/` routes to either.

To clean a machine, the marketplace entries to remove are
`superpowers-marketplace` on the Claude side and `superpowers@openai-curated`
on the Codex side.
