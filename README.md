# Agentic Code Stack

**🔗 https://makafeli.github.io/agentic-code-stack/** — visual map of the stack.

A portable reference package you drop into the root of any project so your AI coding agent (Claude Code, Codex, Cursor, Gemini CLI, etc.) immediately knows what skills, plugins, and MCP servers are available, when to use them, and how to invoke them.

Read [PHILOSOPHY.md](PHILOSOPHY.md) for the principles behind this stack. The core idea: every coding session runs on two tracks. The **pipeline** defines sequential stages (align, plan, design, build). The **rails** are always-on guardrails (docs context, token efficiency, memory tracking, external data, agent interfaces) that keep the agent on track at every stage. A personal **overlay** (`overlay/`) adds private tooling on top — delete it to keep a pure portable stack.

## What's inside

```
agentic-code-stack/
├── AGENTS.md               # Primary entry point — agents read this first
├── CLAUDE.md               # Symlink (or copy) of AGENTS.md for Claude Code compatibility
├── README.md               # You are here
├── INSTALL.md              # One-time setup commands per tool
├── PHILOSOPHY.md           # The principles and mental model behind the stack
├── index.html              # Visual map (hover any tool for commands)
├── community-recipes/      # User-contributed setups for different stacks and workflows
├── pipeline/
│   ├── 01-align.md
│   ├── 02-spec-plan.md
│   ├── 03-design.md
│   └── 04-build.md
├── rails/
│   ├── 01-docs-context.md
│   ├── 02-token-efficiency.md
│   ├── 03-memory-tracking.md
│   ├── 04-external-data.md
│   └── 05-agent-interfaces.md
└── overlay/                # personal layer (gstack, harness, language profiles)
    ├── README.md
    ├── harness.md
    ├── flow.md
    └── languages/
```

## How to use

1. **Copy this folder to the root of every new project.** Most agents (Claude Code, Codex, Cursor) auto-read `AGENTS.md` at session start. If your agent uses `CLAUDE.md` instead, the included copy works as-is. Both files exist so you do not need to choose.

2. **First session in a new project:** ask the agent to read `AGENTS.md` (or `CLAUDE.md`) and acknowledge the stack. Then run any first-time setup from `INSTALL.md`.

3. **Open `index.html`** in a browser when *you* (the human) want a visual reminder of what's available — hover any pipeline stage or rail tool to see the slash commands.

4. **Edit freely.** Remove tools you don't use. Add ones you do. The package is meant to be customized per-project.

## Links

- **[GitHub Discussions](https://github.com/makafeli/agentic-code-stack/discussions)** — Share your setup, ask questions, and see how others configure their stack.
- **[community-recipes/](community-recipes/)** — Contribute a recipe for your toolchain. PRs welcome.

## The mental model

**Pipeline** (sequential): `Align → Spec & Plan → Design → Build`

**Rails** (always-on, parallel to the pipeline):
- Docs & Context — Ref MCP, Context7, Pitlane MCP
- Token Efficiency — Caveman, graphify, Pitlane MCP
- Memory & Tracking — GitHub Issues / Linear
- External Data — Bright Data CLI
- Agent Interfaces — Printing Press

**Overlay** (personal, optional): gstack skills, the Claude harness, and per-language profiles in `overlay/`. Delete it to keep a pure portable stack.

The pipeline tells the agent *what stage* of work it's in. The rails tell it *which tool* to reach for at each stage.

## Credits & sources

- **Grill-me / write-a-skill** — [mattpocock/skills](https://github.com/mattpocock/skills)
- **Superpowers** — [obra/superpowers](https://github.com/obra/superpowers)
- **Get Design (DESIGN.md)** — [getdesign.md](https://getdesign.md/) · skill wrapper [zephyrwang6/brand-design-md](https://github.com/zephyrwang6/brand-design-md)
- **Claude Design** — Anthropic's built-in `frontend-design` skill
- **SwiftUI Agent Skill** — [twostraws/SwiftUI-Agent-Skill](https://github.com/twostraws/SwiftUI-Agent-Skill)
- **Caveman** — [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman)
- **graphify** — `/graphify` knowledge-graph skill
- **Pitlane MCP** — [eresende/pitlane-mcp](https://github.com/eresende/pitlane-mcp)
- **Ref MCP** — [ref-tools/ref-tools-mcp](https://github.com/ref-tools/ref-tools-mcp)
- **Context7** — [upstash/context7](https://github.com/upstash/context7)
- **Bright Data CLI** — [brightdata.com](https://brightdata.com/)
- **CodeRabbit** — [coderabbit.ai](https://coderabbit.ai/) · applied via the `autofix` skill
- **Fallow** — [fallow.tools](https://fallow.tools/) · static code-health (dead code, dupes, complexity)
- **improve** — [shadcn/improve](https://github.com/shadcn/improve) · audit → plans for a cheaper model
- **ponytail** — [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) · lazy-senior-dev code discipline

All third-party tools belong to their respective authors. This package is a reference layer, not a redistribution.

Community contributions are credited in the individual recipe files and in our GitHub Discussions.
