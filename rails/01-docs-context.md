# Rail 01 — Documentation & Code Context

**Always-on. Use throughout every pipeline stage.**

The agent's training data is stale. Naive file reads burn tokens. This rail fixes both.

## Tools

### Context7 — `upstash/context7`

Pulls **version-specific** documentation from source repositories straight into your context.

**MCP tools:**
| Tool | Purpose |
|---|---|
| `resolve-library-id` | Match a library name → Context7-compatible ID |
| `query-docs` | Fetch docs for a library ID |

**Slash commands (with plugin):**
| Command | Purpose |
|---|---|
| `/context7:docs <query>` | Manual lookup |

**CLI commands:**
| Command | Purpose |
|---|---|
| `ctx7 setup --claude` | One-shot setup with OAuth |
| `ctx7 library <name> <query>` | Resolve a library by name |
| `ctx7 docs <libraryId> <query>` | Fetch docs by ID |
| `ctx7 remove` | Uninstall the generated config |

**Trigger phrases in chat:**
- `use context7`
- `use library /supabase/supabase for API and docs` (slash syntax skips the resolution step)
- Mention a version: "How do I set up Next.js 14 middleware? use context7"

**Auto-trigger:** If installed via plugin, the `documentation-lookup` skill activates automatically when the user mentions framework names (Next.js, React, Prisma, Supabase, etc.) or asks "How do I configure X?" — no `use context7` keyword required.

### Ref MCP — `ref-tools/ref-tools-mcp`

Documentation lookup designed for **token efficiency**. Smart-chunked ahead of time, so a query against Figma's 80k-token API gets you the right ~200 tokens, not the whole page.

**MCP tools:**
| Tool | Purpose |
|---|---|
| `ref_search_documentation(query)` | Search across thousands of docs sites and public GitHub repos |
| `ref_read_url(url)` | Fetch a specific page (article-tuned crawler, loads all code tabs) |
| `search_web` | Fallback general web search |

**Behavior:** auto-invoked by the agent when documentation is needed. The user generally doesn't type anything.

### Pitlane MCP — `eresende/pitlane-mcp`

A token-efficient navigation substrate for your **own** codebase. Indexes once with tree-sitter; the agent retrieves only the symbols it needs instead of dumping whole files into context.

**MCP tools:**
| Tool | When to use |
|---|---|
| `ensure_project_ready` | Call once at session start. Indexes if needed and waits for embeddings. |
| `index_project` | Manual index trigger (prefer `ensure_project_ready`) |
| `wait_for_embeddings` | Wait for in-progress embeddings |
| `watch_project` | Only when the repo will change during the session |
| `investigate` | Broad questions — "how does X work?", "where is Y implemented?" Returns source code in one call. |
| `locate_code` | Find a symbol without pulling its source — when you only need an ID |
| `search_content` | You know a text snippet but not which symbol |
| `search_files` | You know a file name, test path, or directory pattern |
| `trace_path` | Source-to-sink or config-to-effect path questions |
| `read_code_unit` | Retrieve the exact implementation of a symbol |
| `find_usages` | Where is this symbol used? |

**Agent rules:**
1. Call `ensure_project_ready` once at the start of each session.
2. For broad code questions → `investigate` first (one call, returns source).
3. Use `read_code_unit` instead of opening whole files.
4. Use `search_files` instead of shell `find` / globbing.
5. Use `search_content` instead of shell `grep`.
6. The composite reads return `read_state.status` (`new` / `unchanged` / `changed`) — if `unchanged`, expand instead of rereading.

**Tool tiers:** by default, Pitlane exposes the curated set above. Set `PITLANE_MCP_TOOL_TIER=all` to expose advanced primitives.

## Decision matrix

| Question | Tool |
|---|---|
| "How does this library's API work?" | Context7 (preferred) or Ref |
| "What's the syntax for this version?" | Context7 (version-aware) |
| "Where is the X function in our codebase?" | Pitlane `locate_code` |
| "How does our auth flow work?" | Pitlane `investigate` |
| "Where is symbol Y used?" | Pitlane `find_usages` |
| "What does this article say?" | Ref `ref_read_url` |
| "Find all files matching `*.test.ts`" | Pitlane `search_files` |

## Anti-patterns

- ❌ Reading whole files when Pitlane can return the relevant symbol.
- ❌ Generating API code from training memory when Context7/Ref can verify.
- ❌ Shell-grep / shell-find when MCP tools answer the question with fewer tokens.
- ❌ Re-reading the same symbol repeatedly. Check `read_state.status` first.
