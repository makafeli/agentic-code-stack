# Rail 04 — External Data

**Engaged on demand. Use when training data is stale or missing.**

For tasks that need live web content, structured product data, SERP results, or platform-specific data (Amazon, LinkedIn, Maps, Reddit, etc.).

## Tools

### Brightdata MCP

Access to Bright Data's web data platform: 60+ tools across Rapid (free) and Pro modes.

**Rapid Mode tools (free, 5,000 reqs/month):**
| Tool | Purpose |
|---|---|
| `search_engine` | SERP search across multiple engines |
| `search_engine_batch` | Multiple queries in one call |
| `scrape_as_markdown` | Fetch a page as clean markdown |
| `scrape_batch` | Multiple URLs in one call |

**Pro Mode tools (everything in Rapid plus):**
- 13 browser-automation tools (full browser control)
- Web data APIs for 40+ platforms:
  - **E-commerce:** Amazon, Walmart, eBay, Best Buy, Etsy, Home Depot, Zara, Google Shopping — products, pricing, ratings, reviews
  - **Professional:** LinkedIn (profiles, companies, jobs, posts), Crunchbase, ZoomInfo
  - **Social:** Instagram, TikTok, Facebook, X (Twitter), YouTube, Reddit
  - **Other:** Google Maps, Yahoo Finance, Zillow, Booking.com, Reuters, GitHub, App Store, Play Store
- Web Unlocker (bypass bot detection / captchas)
- Advanced scraping primitives
- Crawl jobs (trigger, check status, download data)
- Unlocker zone management

**Setup:**
```bash
claude mcp add --transport sse brightdata \
  "https://mcp.brightdata.com/sse?token=YOUR_API_TOKEN&pro=1"
```
Drop `&pro=1` for Rapid mode only.

**Natural-language usage:**
- "Search Amazon for wireless headphones, top 5 by rating with prices"
- "Scrape this article as markdown so I can quote it"
- "Get the LinkedIn profile data for this URL"
- "Find Google Maps results for coffee shops in Rotterdam"

## When to engage

- ✅ Competitor research, pricing analysis
- ✅ Lead generation, account research
- ✅ Content scraping for context (with permission)
- ✅ SERP monitoring
- ✅ Real-time market data
- ❌ Anything publicly available via the agent's existing browsing tool — try that first
- ❌ Personal data scraping that violates ToS or privacy law

## Compliance

The agent should:
- Only scrape **publicly accessible** data.
- Respect `robots.txt` where the user instructs.
- Never aggregate personal data without explicit user direction.
- Never circumvent paywalls or auth walls.
- Surface privacy concerns to the user before bulk operations.

## Cross-reference

- For documentation lookups → use **Ref MCP** or **Context7** (Rail 01), not Brightdata.
- For codebase navigation → use **Pitlane MCP** (Rail 01), not Brightdata.
- Brightdata is for the *external* web only.
- For structured service interaction (CLI-based API access, compound queries) → use **Printing Press** (Rail 05), not Brightdata
