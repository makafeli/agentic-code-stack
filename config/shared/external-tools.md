# External Tools

Every upstream this stack depends on, pinned. Versions verified 2026-07-25.

Nothing here is vendored. These are dependencies with recorded sources, so a
clean machine can reproduce the setup — which is the whole point of writing
them down.

## Skills

### Matt Pocock — the core skill set

```bash
npx skills add mattpocock/skills
```

| | |
|---|---|
| Repo | [mattpocock/skills](https://github.com/mattpocock/skills) |
| Latest release | `v1.1.0` (2026-07-08) |
| Head | `ed37663` (2026-07-21) |
| Installed | `grilling`, `domain-modeling`, `wayfinder`, `prototype`, `tdd`, `diagnosing-bugs`, `code-review`, `codebase-design`, `deadcode`, `design-an-interface`, `request-refactor-plan`, `write-a-skill`, `scaffold-exercises`, `writing-guidelines`, `writing-shape`, `writing-fragments` |

Track the release tag, not `main` — the head runs ahead of the tag by a couple
of weeks and these skills are load-bearing for the whole pipeline.

### Caveman

```bash
npx skills add mattpocock/skills
```

Ships in the same repo. Prose compression, roughly 65–75% on explanation text;
code and commit messages stay normal. Levels: `lite` / `full` / `ultra`.

### Ponytail

```bash
claude plugin marketplace add DietrichGebert/ponytail
claude plugin install ponytail@ponytail
```

| | |
|---|---|
| Repo | [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) |
| Latest release | `v4.8.4` (2026-06-29) |
| Head | `16f2980` (2026-07-15) |

Caveman's code-side counterpart. `/ponytail-review` on a diff, `/ponytail-audit`
on a whole repo.

### SEO / GEO — **the source repo moved**

```bash
npx skills add aaron-he-zhu/aaron-marketing-skills
```

| | |
|---|---|
| Repo | [aaron-he-zhu/aaron-marketing-skills](https://github.com/aaron-he-zhu/aaron-marketing-skills) |
| Latest release | `v19.0.0` (2026-07-24) |
| Installed | `19.0.0` — matches |

> **`aaron-he-zhu/seo-geo-claude-skills` is now a signpost repo.** Its own
> description points here; the old standalone 20-skill line is frozen at tag
> `v9.9.12`. Installing from the old URL gets you a year-old skill set. The 16
> SEO/GEO skills this stack uses live in `aaron-marketing-skills`.

The 16, all at `19.0.0`: `competitor-analysis`, `content-gap-analysis`,
`content-quality-auditor`, `content-writer`, `domain-authority-auditor`,
`geo-content-optimizer`, `keyword-research`, `offsite-signal-analyzer`,
`on-page-seo-checker`, `page-play-builder`, `performance-monitor`,
`rank-tracker`, `serp-analysis`, `serp-markup-builder`,
`site-structure-optimizer`, `technical-seo-checker`.

`edit-article` is a separate skill and is **not** one of the 16.

## MCP servers

### codebase-memory-mcp

```bash
npm install -g codebase-memory-mcp
```

| | |
|---|---|
| Docs | [deusdata.github.io/codebase-memory-mcp](https://deusdata.github.io/codebase-memory-mcp/) |
| Latest | `0.9.0` (2026-07-08) |
| Installed | **`0.8.1` — one minor behind** |

Rails 01 and 02 both run on this. It replaced Pitlane for navigation and
graphify for the code graph, so it is now the only code-intelligence server in
the stack — worth keeping current.

## CLIs

### Fallow

```bash
npx fallow health
```

| | |
|---|---|
| Docs | [docs.fallow.tools/quickstart](https://docs.fallow.tools/quickstart) |
| npm | `fallow@3.9.1` |
| Installed | npx-only, no global install |

Static code health: `dead-code`, `dupes`, `health` (complexity),
`fix --dry-run`. `--format json` for agent consumption, or the `fallow-mcp`
server.

### Google Search Console CLI

```bash
npm install -g google-search-console-cli
```

| | |
|---|---|
| Repo | [Bin-Huang/google-search-console-cli](https://github.com/Bin-Huang/google-search-console-cli) |
| npm | `1.1.1` |
| Installed | `1.1.1` — current |
| Head | `c3e7190` (2026-04-07) — no releases cut |

Credential lives at `~/.config/google-search-console-cli/credentials.json`,
symlinked to a Google service-account JSON. **The JSON never enters this repo.**
Only the symlink command is documented; see [INSTALL.md](../../INSTALL.md).

### Bright Data

```bash
npm install -g @brightdata/cli && bdata login
```

`bdata scrape <url>`, `bdata search "<q>"`, `bdata pipelines <type>` (40+
platforms), `bdata budget`. Paired with a Webshare proxy pool — see
[rails/04-external-data.md](../../rails/04-external-data.md).

### RTK (Rust Token Killer)

Installed at `0.42.0`. Hook-rewritten CLI proxy: `git status` becomes
`rtk git status` transparently, for 60–90% fewer tokens on dev commands.
`rtk gain` reports savings.

## Toolchain floors

| | Installed | Note |
|---|---|---|
| Claude Code | `2.1.220` | **Hard floor.** Below it the `opus` alias silently resolves to `claude-opus-4-8` regardless of the pin — see [role-parity.md](role-parity.md). |
| Codex CLI | `0.145.0` | Desktop ships its own version and updates separately |
| Node | `22.22.3` | |

## Dropped

**gstack** and **superpowers** are out of the stack. Both may still be installed
on a given machine; neither is documented here or routed to anywhere in
`pipeline/` or `rails/`. If you are cleaning a machine, they are the two
marketplace entries to remove.

**Pitlane MCP** and **graphify** are superseded by codebase-memory-mcp.

**Linear** is out of Rail 03 — GitHub Issues only.
