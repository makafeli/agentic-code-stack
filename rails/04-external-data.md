# Rail 04 — External Data

**Engaged on demand. Use when training data is stale or missing.**

For tasks that need live web content, SERP results, or platform-specific data (Amazon, LinkedIn, Maps, Reddit, etc.).

## Tool

### Bright Data CLI (`bdata`)

Terminal access to Bright Data's web platform — auth, proxy zones, anti-bot bypass, CAPTCHA solving, and JS rendering are handled automatically. `brightdata` and `bdata` are aliases.

**Install:**
```bash
curl -fsSL https://cli.brightdata.com/install.sh | bash   # macOS / Linux
# or
npm install -g @brightdata/cli                            # any platform (Node >= 20)
# or one-off
npx --yes --package @brightdata/cli brightdata <command>
```

**One-time auth:**
```bash
bdata login            # OAuth in browser; saves key, auto-creates proxy zones
bdata login --device   # headless / SSH
bdata config           # verify
```

**Core commands:**
| Command | Purpose |
|---|---|
| `bdata scrape <url>` | Scrape a URL — markdown (default), `-f html`, `-f json`, or `-f screenshot -o page.png` |
| `bdata search "<query>"` | SERP search (Google/Bing/Yandex); `--json` to pipe; `--engine bing`; `--country de` |
| `bdata pipelines <type> [params]` | Structured extraction from 40+ platforms (LinkedIn, Amazon, Instagram, TikTok, YouTube, Reddit, Maps…) |
| `bdata pipelines list` | List all pipeline types |
| `bdata status <job-id> --wait` | Poll an async job |
| `bdata budget` | Account balance and costs |
| `bdata zones` | List proxy zones |

**Examples:**
```bash
bdata scrape https://example.com -o page.md
bdata search "web scraping best practices" --json | jq -r '.organic[].link'
bdata pipelines linkedin_person_profile "https://linkedin.com/in/username"
bdata pipelines amazon_product "https://amazon.com/dp/B09V3KXJPB" --format csv -o product.csv
```

**Output:** default human-readable; `--json` compact, `--pretty` indented, `-o <path>` writes to file (format from extension). Pipe-friendly — colors/spinners auto-off when stdout isn't a TTY.

## When to engage

- ✅ Competitor research, pricing analysis, lead/account research, SERP monitoring, real-time market data
- ❌ Anything the agent's existing browse tool can already fetch — try that first
- ❌ Personal-data scraping that violates ToS or privacy law

## Compliance

Scrape only publicly accessible data. Respect `robots.txt` where instructed. Never aggregate personal data without explicit direction. Never circumvent paywalls or auth walls. Surface privacy concerns before bulk operations.

## Cross-reference

- Documentation lookups → **Ref MCP** / **Context7** (Rail 01), not Bright Data.
- Codebase navigation → **Pitlane MCP** (Rail 01).
- Bright Data is for the *external* web only.
- Structured service interaction (CLI access to APIs) → **Printing Press** (Rail 05).
