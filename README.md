# Agentic Code Stack

A portable reference package you drop into the root of any project so your AI coding agent (Claude Code, Codex, Cursor, Gemini CLI, etc.) immediately knows what skills, plugins, and MCP servers are available, when to use them, and how to invoke them.

Read [PHILOSOPHY.md](PHILOSOPHY.md) for the principles behind this stack. The core idea: every coding session runs on two tracks. The **pipeline** defines sequential stages (shape, align, plan, design, build). The **rails** are always-on guardrails (docs context, token efficiency, memory tracking, external data) that keep the agent on track at every stage.

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
│   ├── 01-prompt-shape.md
│   ├── 02-align.md
│   ├── 03-spec-plan.md
│   ├── 04-design.md
│   └── 05-build.md
└── rails/
    ├── 01-docs-context.md
    ├── 02-token-efficiency.md
    ├── 03-memory-tracking.md
    └── 04-external-data.md
```

## How to use

1. **Copy this folder to the root of every new project.** Most agents (Claude Code, Codex, Cursor) auto-read `AGENTS.md` at session start. If your agent uses `CLAUDE.md` instead, the included copy works as-is. Both files exist so you do not need to choose.

2. **First session in a new project:** ask the agent to read `AGENTS.md` (or `CLAUDE.md`) and acknowledge the stack. Then run any first-time setup from `INSTALL.md`.

3. **Open `index.html`** in a browser when *you* (the human) want a visual reminder of what's available — hover any pipeline stage or rail tool to see the slash commands.

4. **Edit freely.** Remove tools you don't use. Add ones you do. The package is meant to be customized per-project.

## Links

- **GitHub Discussions** — Share your setup, ask questions, and see how others configure their stack.
- **[community-recipes/](community-recipes/)** — Contribute a recipe for your toolchain. PRs welcome.

## The mental model

**Pipeline** (sequential): `Prompt Shape → Align → Spec & Plan → Design → Build`

**Rails** (always-on, parallel to the pipeline):
- Docs & Context — Ref MCP, Context7, Pitlane MCP
- Token Efficiency — Caveman, Pitlane MCP
- Memory & Tracking — Beads, OpenSpec
- External Data — Brightdata MCP

The pipeline tells the agent *what stage* of work it's in. The rails tell it *which tool* to reach for at each stage.

## Credits & sources

- **Grill-me / write-a-skill** — [mattpocock/skills](https://github.com/mattpocock/skills)
- **Superpowers** — [obra/superpowers](https://github.com/obra/superpowers)
- **OpenSpec** — [Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec)
- **Get Design (DESIGN.md)** — [getdesign.md](https://getdesign.md/) · skill wrapper [zephyrwang6/brand-design-md](https://github.com/zephyrwang6/brand-design-md)
- **Open Design** — [nexu-io/open-design](https://github.com/nexu-io/open-design)
- **SwiftUI Agent Skill** — [twostraws/SwiftUI-Agent-Skill](https://github.com/twostraws/SwiftUI-Agent-Skill)
- **Caveman** — [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman)
- **Beads** — [gastownhall/beads](https://github.com/gastownhall/beads)
- **Pitlane MCP** — [eresende/pitlane-mcp](https://github.com/eresende/pitlane-mcp)
- **Ref MCP** — [ref-tools/ref-tools-mcp](https://github.com/ref-tools/ref-tools-mcp)
- **Context7** — [upstash/context7](https://github.com/upstash/context7)
- **Brightdata MCP** — [Bright Data](https://brightdata.com/)
- **Metaprompt** — [metaprompt.com](https://metaprompt.com/)

All third-party tools belong to their respective authors. This package is a reference layer, not a redistribution.

Community contributions are credited in the individual recipe files and in our GitHub Discussions.
