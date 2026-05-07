# Agentic Code Stack — Public Launch Design

## Overview

Transform the existing local "Claude Code Stack" project into a public GitHub repository called **Agentic Code Stack** — a reference guide and community hub for the pipeline + rails mental model of working with AI coding agents. The stack gives agents stage awareness (where am I in the process?) and tool awareness (what should I reach for right now?).

**Tone:** Reference guide — neutral, here-are-your-options. Not a manifesto.

**Primary artifact:** `index.html` (the stack diagram) hosted on GitHub Pages as the landing page.

## Repo structure

```
agentic-code-stack/
├── index.html                 # Renamed from stack-diagram.html (GitHub Pages root)
├── AGENTS.md                  # Full stack reference for multi-agent discovery
├── CLAUDE.md                  # Identical copy of AGENTS.md for Claude Code auto-detection
├── README.md                  # Polished — philosophy hook, quick start, links
├── INSTALL.md                 # Setup commands (updated naming)
├── PHILOSOPHY.md              # The mental model explained
├── pipeline/
│   ├── 01-prompt-shape.md
│   ├── 02-align.md
│   ├── 03-spec-plan.md
│   ├── 04-design.md
│   └── 05-build.md
├── rails/
│   ├── 01-docs-context.md
│   ├── 02-token-efficiency.md
│   ├── 03-memory-tracking.md
│   └── 04-external-data.md
└── community-recipes/
    └── README.md              # Contribution guide for community submissions
```

### Changes from current state

- `stack-diagram.html` renamed to `index.html` (same file content, updated title/header)
- New `CLAUDE.md` (identical copy of AGENTS.md)
- New `PHILOSOPHY.md`
- New `community-recipes/` directory with contribution README
- README gets a light polish — philosophy hook up top, clearer calls to action
- Naming updated throughout: "Claude Code Stack" → "Agentic Code Stack"

## GitHub Pages

- **Config:** Repo Settings → Pages → Deploy from branch → `main`, `/ (root)`
- **Result:** `index.html` served as the landing page. No build step, no framework. Static HTML with inline CSS/JS.
- **Custom domain (optional):** if desired, add CNAME file + DNS config. Default `<user>.github.io/agentic-code-stack` works out of the box.

## Philosophy doc (PHILOSOPHY.md)

~250 words, reference tone. Structure:

1. **The problem** — agents thrash without structure; humans don't know what to ask for; agents don't know what's available
2. **The model: Pipeline + Rails** — pipeline gives stage awareness (sequential), rails give tool awareness (always-on)
3. **When to use the full stack** — multi-session features, teams, complex codebases, anything with UI
4. **When to go lightweight** — single-session spikes, trivial fixes, exploratory work. Drop pipeline stages, keep the rails
5. **How to customize** — remove unused tools, add project-specific rails. The stack is a starting point.
6. **The meta point** — the stack is built using the stack (brainstorm → plan → build → beads). Eat your own dog food.

## Agent entry points

- **AGENTS.md** — read by Codex, Cursor, Gemini CLI, and other agents. Full stack reference.
- **CLAUDE.md** — read automatically by Claude Code at session start. Identical content to AGENTS.md.
- Both files contain the complete stack reference. No pointer files, no "go read the other one." Zero-friction startup on any platform.
- Keep in sync manually for now. Low maintenance burden since content changes infrequently.

## Community infrastructure

### GitHub Discussions (4 categories)

| Category | Purpose |
|----------|---------|
| Show Your Stack | People post their AGENTS.md, tool choices, configs |
| Tool Discoveries | New MCP servers, skills, plugins found useful |
| Workflows | "Here's how I use this for data science / iOS / Django" |
| Help | Troubleshooting, "which tool for X?" |

### community-recipes/ directory

- Open to PRs from anyone
- Organized by use case: `community-recipes/ios-dev/`, `community-recipes/data-engineering/`, etc.
- Light review bar: does it work? Is it documented? Not a formal RFC.
- README explains format and submission bar

### Promotion path

1. Tool/workflow gains traction in Discussions or community-recipes
2. Maintainer (or trusted contributor) promotes it into official pipeline/rails docs
3. Original contributor credited in docs + README credits section

## Naming/branding

- "Claude Code" stays when referring to the CLI tool itself
- "Agentic Code Stack" replaces "Claude Code Stack" when referring to this project/package/methodology
- Files to update: README.md, INSTALL.md, index.html (title + header), CLAUDE.md (new)

## Onboarding flow

1. **Land on diagram** (GitHub Pages) — visual understanding of pipeline + rails. "View on GitHub" badge.
2. **Clone or copy** — git clone into project, or copy individual files. AGENTS.md/CLAUDE.md is the minimum viable drop-in.
3. **Agent reads it** — Claude Code auto-reads CLAUDE.md. Other agents read AGENTS.md. No config needed.
4. **Run setup** — INSTALL.md has one-time commands. Most install incrementally.
5. **First session** — agent reads stack, runs `bd prime`, and they're off. Pipeline gives structure.
6. **Customize** — remove unused tools, check community-recipes for variants, share setup in Discussions.
7. **Contribute** — post tool discoveries in Discussions, PR workflows to community-recipes, join curation loop.

Zero friction until step 4 (actual tool installation). No account creation or API key needed for the repo itself.

## Implementation tasks

1. Rename `stack-diagram.html` → `index.html`, update title/header to "Agentic Code Stack"
2. Create `CLAUDE.md` (copy of AGENTS.md)
3. Write `PHILOSOPHY.md`
4. Create `community-recipes/README.md`
5. Polish `README.md` — add philosophy hook, update naming, add links to discussions and community-recipes
6. Update naming in `INSTALL.md`
7. Spot-check pipeline/ and rails/ docs for stale naming references
8. Init git repo, create GitHub repo, push
9. Enable GitHub Pages in repo settings
10. Enable GitHub Discussions with 4 categories
11. Add `.gitignore` (`.superpowers/`, `.DS_Store`)
