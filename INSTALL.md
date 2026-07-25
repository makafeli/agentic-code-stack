# Install & Setup

One-time commands per machine, not per project. Versions verified 2026-07-25 —
see [`config/shared/external-tools.md`](config/shared/external-tools.md) for the
pinned table.

There are **no install scripts** in this repo, on purpose. A script that
provisions a machine nobody else has is a thing that rots silently. These
commands are short enough to read before you run them.

## Prerequisites

| | Minimum | Why |
|---|---|---|
| Claude Code | **`2.1.220`** | Below it the `opus` alias silently resolves to `claude-opus-4-8` regardless of the pin, so a reviewer you believe is on Opus 5 is not |
| Node | `22.x` | |
| `gh` CLI | any recent | Rail 03 runs on it |

```bash
claude --version    # must be >= 2.1.220
node --version
gh auth status      # keyring OAuth, not a PAT — see Credentials below
```

## 1. Skills

```bash
npx skills add mattpocock/skills
```

Installs the core set: `wayfinder`, `grilling`, `domain-modeling`, `prototype`,
`tdd`, `diagnosing-bugs`, `code-review`, `codebase-design`, `deadcode`,
`design-an-interface`, `request-refactor-plan`, `write-a-skill`, `caveman`, and
the writing set.

**Track the release tag, not `main`.** Head runs a couple of weeks ahead and
these skills are load-bearing for the whole pipeline.

### Ponytail

```bash
claude plugin marketplace add DietrichGebert/ponytail
claude plugin install ponytail@ponytail
```

### SEO / GEO (optional)

```bash
npx skills add aaron-he-zhu/aaron-marketing-skills
```

> ⚠️ Not `aaron-he-zhu/seo-geo-claude-skills` — that repo is now a signpost, and
> installing from it gets a skill set frozen at `v9.9.12`.

## 2. MCP servers

### codebase-memory-mcp — Rails 01 and 02

```bash
npm install -g codebase-memory-mcp
claude mcp add codebase-memory-mcp -- codebase-memory-mcp
```

Then once per project:

```text
index_repository        # via the MCP tool, at first session in a repo
```

The Codex side is already declared in
[`config/codex/config.template.toml`](config/codex/config.template.toml).

### Context7 — Rail 01

```bash
claude mcp add context7 -- npx -y @upstash/context7-mcp
```

## 3. CLIs

```bash
npm install -g @brightdata/cli && bdata login       # Rail 04
npm install -g google-search-console-cli            # Rail 04
# Fallow is npx-only, no install needed:
npx fallow health
```

## 4. Harness config

Both trees are peers — install whichever harnesses you use, in either order.

### Claude

```bash
cp config/claude/settings.template.json ~/.claude/settings.json
cp config/claude/agents/*.md            ~/.claude/agents/
cp -r config/claude/rules/*             ~/.claude/rules/
cp config/claude/hooks/*                ~/.claude/hooks/ && chmod +x ~/.claude/hooks/*
```

Then edit `~/.claude/settings.json`:

- `statusLine` — omitted from the template because it hard-codes a plugin cache
  path with a version number in it. Add your own or leave it out.
- `permissions.allow` — the template carries a conservative subset. Yours will
  differ.
- `skillSources` — machine-specific, omitted.

### Codex

```bash
cp config/codex/agents/*.toml ~/.codex/agents/
cp -r config/codex/rules/*    ~/.codex/rules/
```

Merge [`config/codex/config.template.toml`](config/codex/config.template.toml)
into `~/.codex/config.toml`. It is a **subset**, not a whole file — it
deliberately omits `[projects]` trust levels, `[desktop]` preferences,
marketplace cache paths, and the Codex Desktop runtime MCP servers.

### Verify

```bash
claude --version                                  # >= 2.1.220
ls ~/.claude/agents/ | grep -c reviewer           # expect 3
grep -l 'claude-opus-5' ~/.claude/agents/*.md     # the three reviewers
grep 'effort' ~/.claude/agents/standards-reviewer.md   # expect max
```

## 5. Credentials

**Nothing in this repo contains a credential, and nothing should.**

- **GitHub** — use `gh auth login` (keyring OAuth). Do not put a PAT in
  `settings.json`, in a shell profile, or in an MCP config. A token in a
  settings file is one careless `cp` away from a public repo.
- **Bright Data** — `bdata login`, stored by the CLI.
- **Google Search Console** — symlink your service-account JSON:

  ```bash
  mkdir -p ~/.config/google-search-console-cli
  ln -s /path/to/service-account.json \
        ~/.config/google-search-console-cli/credentials.json
  ```

  The JSON stays outside the repo. Only the symlink command is documented.

### Never commit

`~/.codex/auth.json` · `history.jsonl` · `settings.local.json` ·
`settings.json.bak` · `~/.claude/_backups/` · `projects/` · `sessions/` ·
`tasks/` · `paste-cache/` · `uploads/` · `security/` · Codex sessions,
snapshots, attachments, memories, sqlite, and caches · any `.env`, OAuth token,
service-account JSON, API key, browser profile, or cookie store.

## 6. Per project

```bash
cp -r /path/to/agentic-code-stack/{CLAUDE.md,AGENTS.md,pipeline,rails,config} .
```

Then in the first session, index the repo with codebase-memory-mcp and confirm
the agent has read `CLAUDE.md`.

## Optional

- **RTK** — hook-rewritten CLI proxy, 60–90% fewer tokens on dev command output.
- **Printing Press** — Rail 05, only if you need agent-native CLIs for a service
  with no MCP server.
- **Get Design** — `npx getdesign@latest add <slug>`, on demand, no install.
