# Rail 02 — Token & Output Efficiency

**Always-on. Use during long sessions, agent-to-agent context handoffs, and any time the context window is getting tight.**

Verbose output costs money and crowds the context. This rail trims output without losing technical content. A March 2026 paper found that constraining models to brief responses can *improve* accuracy by up to 26 points on certain benchmarks — verbose ≠ better.

## Tools

### Caveman — `JuliusBrussee/caveman`

Compresses prose output ~65–75% by dropping articles, fragments, abbreviations, and pleasantries — while keeping code, function names, API names, and error strings byte-for-byte intact.

**Six intensity levels:**

| Level | Style |
|---|---|
| `lite` | Drop articles, fragments OK, short synonyms. Classic caveman. |
| `full` (default) | Default caveman. Drop articles, filler, full grunt. |
| `ultra` | Maximum compression. Telegraphic. Abbreviations everywhere. |
| `wenyan-lite` | Semi-classical Chinese (文言文). Grammar intact, filler gone. |
| `wenyan-full` | Full classical Chinese. Maximum classical terseness. |
| `wenyan-ultra` | Extreme classical compression. Ancient scholar on a budget. |

**Slash commands:**
| Command | Purpose |
|---|---|
| `/caveman` | Activate at default `full` level |
| `/caveman lite` | Switch to lite |
| `/caveman ultra` | Switch to ultra |
| `/caveman wenyan-full` | Classical Chinese mode |
| `/caveman-commit` | Independent: terse Conventional Commits, ≤50 char subject |
| `/caveman-review` | Independent: one-line PR comments — `L42: 🔴 bug: user null. Add guard.` |
| `/caveman-stats` | Real session token savings + estimated $ saved |
| `/caveman-stats --share` | Tweetable savings line |
| `/caveman-stats --since 7d` | Window the stats |
| `/caveman-stats --all` | Lifetime aggregation |

**Natural-language activation:**
- "talk like caveman" / "caveman mode" / "less tokens" / "be brief" → activate
- "stop caveman" / "normal mode" → deactivate

**Persistence:** level sticks until changed or session ends. Off only on explicit deactivation.

**What stays normal regardless of caveman:**
- Code (always written normally)
- Commit messages and PR descriptions
- Function names, API names, error strings
- Critical warnings ("This will permanently delete...")

**Example:**
> Normal: "The reason your React component is re-rendering is likely because you're creating a new object reference on each render cycle. When you pass an inline object as a prop, React's shallow comparison sees it as a different object every time, which triggers a re-render. I'd recommend using useMemo to memoize the object."
>
> Caveman full: "New object ref each render. Inline object prop = new ref = re-render. Wrap in useMemo."
>
> Caveman ultra: "Inline obj prop → new ref → re-render. Memo it."

### Pitlane MCP (token side)

Pitlane belongs to both this rail and the Docs/Context rail. From a token-efficiency perspective, its job is to **shrink input**: instead of reading 800 lines to find one function, retrieve 30 lines.

See `rails/01-docs-context.md` for tool details.

## When to engage

- ✅ Sessions running > 1 hour
- ✅ Context window > 60% full
- ✅ Sub-agent / parallel agent flows where output gets injected into the parent context
- ✅ Cost-sensitive workloads (background agents, batch processing)
- ❌ User-facing chat where readability matters more than token count
- ❌ First-time onboarding flows

## Anti-patterns

- ❌ Engaging caveman mid-explanation when the user is following along.
- ❌ Trying to compress code. Caveman explicitly leaves code alone — don't second-guess it.
- ❌ Forgetting it's on. If the user seems confused by your terse output, the level is wrong; switch to `lite` or deactivate.
