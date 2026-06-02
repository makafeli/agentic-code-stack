# Rail 05 — Agent Interfaces

**Always on. Give agents structured, compound-query access to external services.**

## The problem

Agents are bad at raw HTTP. They guess endpoints, fumble auth, and can't maintain state across calls. Scraping works for read-only data (Rail 04), but when an agent needs to *do* something — book a flight, query a CRM, trigger a deploy — it needs a purpose-built interface.

## The solution: Printing Press

[Printing Press](https://printingpress.dev/) generates agent-native Go CLIs from API specs, HAR files, or URLs. One command outputs four formats: Go CLI binary, Claude Code skill, OpenClaw skill, and MCP server. Every CLI ships with a local SQLite mirror, compound queries, and structured output — the shape agents actually need.

**Philosophy:** A local SQLite mirror beats a remote API call for agents. Compound commands beat ten round trips. Agent-native structured output beats raw HTTP.

## Two modes

### 1. Install from the Library (70+ CLIs)

Browse the [community library](https://github.com/mvanhorn/printing-press-library) for pre-built CLIs across commerce, travel, payments, media, developer tools, productivity, and more.

```bash
# Starter pack (recommended first install)
npx -y @mvanhorn/printing-press install starter-pack

# Specific CLI from the library
npx -y @mvanhorn/printing-press install flight-goat
```

Library highlights:
- **flight-goat** — Google Flights + Kayak + FlightAware in one CLI
- **stripe** — Every Stripe feature with local SQLite mirror and cross-entity SQL
- **notion** — Cross-workspace SQL joins, stale detection, offline queries
- **slack** — Send messages, search conversations, monitor channels
- **linear** — Compound SQL queries the Linear API can't answer (50ms against local mirror)
- **amazon-seller** — FBA inventory, orders, sales reports, listings
- **github** — Full GitHub API surface as a CLI
- **hn** — Hacker News with local SQLite store and snapshot history

### 2. Print a new CLI

When a service doesn't have a library entry, print one from its API spec or URL:

```bash
# Install the generator binary
go install github.com/mvanhorn/cli-printing-press/v4/cmd/printing-press@latest

# Generate a new CLI
printing-press generate --source https://api.example.com/openapi.json --name my-service

# Or from a HAR file (capture browser traffic)
printing-press generate --source captured-traffic.har --name my-service
```

Every generated CLI automatically includes: local SQLite mirror, compound command support, structured JSON output, and MCP server mode.

## Key patterns

- **Local SQLite mirror** — sub-50ms queries, offline-capable, cross-entity SQL joins
- **Compound commands** — one CLI call replaces 10 API round trips
- **Structured agent output** — no parsing, no guesswork, no hallucinated fields
- **Cross-source joins** — join flight data with sports schedules, recipes with grocery delivery, contacts with CRM history

## Setup

```bash
# 1. Install the generator binary
go install github.com/mvanhorn/cli-printing-press/v4/cmd/printing-press@latest

# 2. Verify
printing-press --version

# 3. Clone the skills repo (Claude Code skills + library)
git clone https://github.com/mvanhorn/cli-printing-press.git

# 4. Install starter pack (recommended)
npx -y @mvanhorn/printing-press install starter-pack
```

For updates: `go install` / `git pull` / `npx install` respectively.

## When to engage

- ✅ Any service the agent will interact with more than once
- ✅ Services with complex auth (OAuth, session tokens, API keys)
- ✅ Multi-source workflows (join data from two or more APIs)
- ✅ Offline-first / local-mirror use cases
- ✅ Compound queries the native API can't express
- ❌ One-off API calls the agent can handle with a single curl
- ❌ Read-only web scraping (use the Bright Data CLI — Rail 04)

## Cross-reference

- For read-only web data retrieval → use the **Bright Data CLI** (Rail 04), not Printing Press
- For documentation lookups → use **Ref MCP** or **Context7** (Rail 01)
- For codebase navigation → use **Pitlane MCP** (Rail 01)
- Generated CLIs can output MCP servers → add them to your MCP config with `claude mcp add`
