# Agentic Code Stack

**🔗 https://makafeli.github.io/agentic-code-stack/** — visual map of the stack.

A portable reference package you drop into the root of any project so your AI
coding agent (Claude Code, Codex, Cursor, Gemini CLI) immediately knows which
skills, servers, and commands exist, when to use them, and how to invoke them.

The skill set is **[Matt Pocock's](https://github.com/mattpocock/skills)**, plus
a short list of tools that do something his set doesn't. Nothing else. The
constraint is the feature — a stack you can hold in your head is a stack you
actually use.

Read [PHILOSOPHY.md](PHILOSOPHY.md) for the reasoning.

## The loop

```text
grill → spec → tickets → implement (TDD) → review → merge → sweep
```

## What's inside

```text
agentic-code-stack/
├── CLAUDE.md               # Entry point — agents read this first
├── AGENTS.md               # Byte-identical copy, for agents that look for this name
├── README.md               # You are here
├── PHILOSOPHY.md           # The reasoning behind the shape
├── INSTALL.md              # One-time setup per machine
├── index.html              # Visual map (hover any tool for commands)
├── pipeline/               # Sequential stages
│   ├── 01-align.md         #   wayfinder · grill-me · prototype
│   ├── 02-spec-plan.md     #   wayfinder tickets · improve
│   ├── 03-design.md        #   design-an-interface · Get Design · frontend-design
│   └── 04-build.md         #   tdd · code-structure · diagnose · review gates
├── rails/                  # Always-on capabilities
│   ├── 01-docs-context.md  #   Context7 · codebase-memory-mcp
│   ├── 02-token-efficiency.md  # caveman · ponytail · RTK
│   ├── 03-memory-tracking.md   # GitHub Issues
│   ├── 04-external-data.md     # Bright Data · Webshare · GSC
│   └── 05-agent-interfaces.md  # Printing Press
├── config/                 # The harness itself
│   ├── claude/             #   CLAUDE.md · agents/ · rules/ · hooks/ · settings template
│   ├── codex/              #   AGENTS.md · agents/ · rules/ · config template
│   └── shared/             #   role-parity · external-tools · languages/
└── community-recipes/      # User-contributed setups
```

## Claude and Codex are peers

`config/` holds two trees side by side. Codex reads `AGENTS.md`, Claude reads
`CLAUDE.md`, and each owns its own subagents, rules, and settings. Neither loads
the other. Neither is canonical over the other.

They stay separate on purpose. The six roles mirror 1:1, but the harnesses
enforce them differently — Codex has a real `sandbox_mode`, Claude has only
`disallowedTools`; Codex has a default-subagent-model fallback and a concurrency
cap, Claude has neither. A merged definition would have to paper over exactly
those gaps, and the merge point is where mistakes happen.
[`config/shared/role-parity.md`](config/shared/role-parity.md) states the mapping
and names every place it breaks down.

## How to use

1. **Copy this folder to the root of a project.** Agents auto-read `CLAUDE.md`
   or `AGENTS.md` at session start. Both exist so you don't have to choose;
   they are byte-identical.

2. **Run the one-time setup** from [INSTALL.md](INSTALL.md). Per machine, not per
   project.

3. **Open `index.html`** when *you* want a visual reminder — hover any stage or
   rail to see its commands.

4. **Edit freely.** Remove what you don't use, add what you do.

## Keeping the two entry files in sync

```bash
cp CLAUDE.md AGENTS.md && diff -q CLAUDE.md AGENTS.md
```

Harness-specific content never goes in either root file — it lives in that
harness's own tree under `config/`. That's what keeps them identical without
either becoming a stub.

## Links

- **[GitHub Discussions](https://github.com/makafeli/agentic-code-stack/discussions)** — share your setup, see how others configure theirs.
- **[community-recipes/](community-recipes/)** — contribute a recipe. PRs welcome.

## Credits & sources

Every upstream is pinned in
[`config/shared/external-tools.md`](config/shared/external-tools.md).

- **Skills** — [mattpocock/skills](https://github.com/mattpocock/skills) · wayfinder, grilling, domain-modeling, prototype, tdd, diagnosing-bugs, code-review, codebase-design, deadcode, design-an-interface, caveman, and the writing set
- **ponytail** — [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail)
- **codebase-memory-mcp** — [deusdata.github.io/codebase-memory-mcp](https://deusdata.github.io/codebase-memory-mcp/)
- **Fallow** — [fallow.tools](https://docs.fallow.tools/quickstart)
- **Context7** — [upstash/context7](https://github.com/upstash/context7)
- **improve** — [shadcn/improve](https://github.com/shadcn/improve)
- **CodeRabbit** — [coderabbit.ai](https://coderabbit.ai/) · applied via `/autofix`
- **Get Design** — [getdesign.md](https://getdesign.md/)
- **Claude Design** — Anthropic's built-in `frontend-design` skill
- **SEO / GEO** — [aaron-he-zhu/aaron-marketing-skills](https://github.com/aaron-he-zhu/aaron-marketing-skills) (the old `seo-geo-claude-skills` repo is now a signpost)
- **Google Search Console CLI** — [Bin-Huang/google-search-console-cli](https://github.com/Bin-Huang/google-search-console-cli)
- **Bright Data** — [brightdata.com](https://brightdata.com/)

All third-party tools belong to their authors. This package is a reference
layer, not a redistribution.
