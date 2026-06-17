# Install & Setup

One-time commands to get each tool wired into Claude Code (and most other agents). Run these once per machine, not per project. The `bd init` step is per-project.

> **Important:** This stack assumes Claude Code as the primary agent. Most tools also work in Codex, Cursor, Gemini CLI, OpenCode, etc. — see each tool's docs for non-Claude setup.

> **Personal overlay:** the `overlay/` files reference **gstack** (a private skill suite) and harness/RTK config that are not publicly installable. A cloner can ignore or delete `overlay/`; the portable core below stands alone.

---

## Pipeline tools

### Grill-me & friends (mattpocock/skills)
```bash
npx skills add mattpocock/skills --skill grill-me
npx skills add mattpocock/skills --skill grill-with-docs
npx skills add mattpocock/skills --skill write-a-skill
```

### Superpowers (obra)
```
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```
Restart Claude Code. Verify with `/help` — you should see `/superpowers:brainstorming`, `/superpowers:write-plan`, `/superpowers:execute-plan`, etc.

### improve (shadcn)
```bash
npx skills add shadcn/improve
```

### Get Design (brand-design-md skill)
```bash
git clone https://github.com/zephyrwang6/brand-design-md.git
mkdir -p ~/.claude/skills/brand-design-md
cp brand-design-md/SKILL.md ~/.claude/skills/brand-design-md/SKILL.md
```
Restart Claude Code. The skill calls `npx getdesign@latest add <slug>` at runtime so you always get the latest spec.

### Claude Design

No install — Anthropic's built-in **`frontend-design`** skill is available in Claude Code. Pair it with Get Design tokens. (gstack adds `/design-review`, `/design-html`, `/design-consultation` if installed.)

### SwiftUI / SwiftData / Swift Concurrency / Swift Testing skills (twostraws)
```bash
npx skills add https://github.com/twostraws/swiftui-agent-skill --skill swiftui-pro
npx skills add https://github.com/twostraws/swiftdata-agent-skill --skill swiftdata-pro
npx skills add https://github.com/twostraws/swift-concurrency-agent-skill --skill swift-concurrency-pro
npx skills add https://github.com/twostraws/swift-testing-agent-skill --skill swift-testing-pro
```

### Fallow (code-health)
No install needed — run via `npx`. Optional MCP server for structured agent access:
```bash
npx fallow            # run; subcommands: dead-code · dupes · health · fix --dry-run
npx fallow init       # optional .fallowrc.json / fallow.toml
# Structured agent access — add the fallow-mcp server to your MCP config, e.g.:
claude mcp add fallow -- npx -y fallow-mcp
```

---

## Rail tools

### Caveman (JuliusBrussee)
```
/plugin marketplace add JuliusBrussee/caveman
/plugin install caveman@caveman
```
Or for any agent:
```bash
npx skills add JuliusBrussee/caveman
```
Optional: standalone hooks for status line + auto-activation:
```bash
bash <(curl -s https://raw.githubusercontent.com/JuliusBrussee/caveman/main/hooks/install.sh)
```

### ponytail (DietrichGebert)
```
/plugin marketplace add DietrichGebert/ponytail
/plugin install ponytail@ponytail
```

### Memory & Tracking — GitHub Issues / Linear
GitHub Issues needs no install — authenticate the `gh` CLI (`gh auth login`) or use the GitHub MCP. For Linear, connect the Linear MCP, or install the Printing Press `linear` CLI:
```bash
npx -y @mvanhorn/printing-press install linear
```

### Pitlane MCP (eresende)
Install the binary (Arch users: `pitlane-mcp-bin` from AUR; others: build from source at `github.com/eresende/pitlane-mcp`).

Add to Claude Code's MCP config:
```json
{
  "mcpServers": {
    "pitlane": {
      "command": "pitlane-mcp",
      "env": {
        "PITLANE_ALLOWED_ROOTS": "/path/to/your/projects"
      }
    }
  }
}
```

### Ref MCP (ref-tools)
Sign up at [ref.tools](https://ref.tools) for an API key, then:
```bash
claude mcp add --scope user --transport http ref \
  "https://api.ref.tools/mcp?apiKey=YOUR_API_KEY"
```

### Context7 (Upstash)
Easiest path:
```bash
npx ctx7 setup --claude
```
This authenticates via OAuth and installs the appropriate skill. Or manual MCP:
```bash
claude mcp add --scope user --header "CONTEXT7_API_KEY: YOUR_KEY" \
  --transport http context7 https://mcp.context7.com/mcp
```
Or marketplace plugin:
```
/plugin marketplace add upstash/context7
/plugin install context7-plugin@context7-marketplace
```

### Bright Data CLI
```bash
curl -fsSL https://cli.brightdata.com/install.sh | bash   # or: npm install -g @brightdata/cli
bdata login
```
Free tier: 5,000 requests/month. `bdata pipelines list` shows the 40+ platform extractors.

### graphify
```bash
uv tool install graphifyy   # or: pip install graphifyy
```
Optional always-on integration: `graphify claude install` writes a `## graphify` block into the project `CLAUDE.md`.

### CodeRabbit + autofix
Install the CodeRabbit GitHub App on your repo at [coderabbit.ai](https://coderabbit.ai/). The `autofix` skill (applies CodeRabbit review threads) ships with the stack — no separate install.

---

## Sanity check

After setup, verify in Claude Code:

```
/help
```

You should see at least these slash commands available:
- `/superpowers:brainstorming`, `/superpowers:write-plan`, `/superpowers:execute-plan`
- `/grill-me`, `/grill-with-docs`
- `/caveman`, `/caveman-stats`
- `/graphify`
- `/context7:docs` (if plugin installed)

And these MCP servers should be listed under `/mcp`:
- `ref`
- `context7`
- `pitlane`

CLI tools (verify in a terminal): `bdata config`, `graphify --help`, `rtk --version`, `gh auth status`.
