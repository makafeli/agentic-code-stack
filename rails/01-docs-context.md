# Rail 01 — Documentation & Code Context

**Always-on. Use throughout every pipeline stage.**

The agent's training data is stale. Naive file reads burn tokens. This rail
fixes both — one tool for *other people's* code, one for *yours*.

## Tools

### Context7 — `upstash/context7`

Pulls **version-specific** documentation from source repositories straight into
context. Use it before writing code against any library, framework, SDK, or CLI
— including ones you think you know. Training data lags releases.

**MCP tools:**

| Tool | Purpose |
|---|---|
| `resolve-library-id` | Match a library name → Context7 ID |
| `query-docs` | Fetch docs for a library ID |

**Trigger phrases in chat:**

- `use context7`
- `use library /supabase/supabase for API and docs` — slash syntax skips
  resolution
- Mention a version: "How do I set up Next.js 14 middleware? use context7"

**Auto-trigger:** installed as a plugin, it engages when the user mentions a
framework name or asks "how do I configure X?" — no keyword needed.

**Don't use it for:** refactoring, scripts written from scratch, business-logic
debugging, or general programming concepts. It answers "what's the API," not
"what should I build."

### codebase-memory-mcp — `deusdata/codebase-memory-mcp`

A persistent knowledge graph over your **own** codebase. Indexes with
tree-sitter across 158 languages; the agent retrieves the symbols it needs
instead of dumping whole files into context.

This replaced both Pitlane (navigation) and graphify (code graph). It is now the
only code-intelligence server in the stack.

**MCP tools:**

| Tool | When to use |
|---|---|
| `index_repository` | Once per project, before anything else |
| `index_status` | Check whether the index is current |
| `detect_changes` | Re-index only what moved |
| `search_graph` | Find functions, classes, routes by name pattern, label, or qualified name |
| `trace_path` | Call chains — `mode=calls \| data_flow \| cross_service` |
| `get_code_snippet` | Exact source for a qualified name, precise ranges |
| `query_graph` | Complex Cypher patterns |
| `get_architecture` | Project structure and layering |
| `search_code` | Text search, graph-augmented |
| `manage_adr` | Architecture decision records |

**Agent rules:**

1. If the project isn't indexed, run `index_repository` **first**.
2. Code exploration goes through these tools before `Grep` / `Glob` / `Read`.
3. `get_code_snippet` instead of opening a whole file.
4. `search_graph` instead of grepping for a function name.
5. `trace_path` instead of manually following call sites.

**Grep and Glob stay fine** for text, configs, and non-code files. And always
`Read` a file before editing it — the graph tells you where something is, not
what the current bytes are.

## Decision matrix

| Question | Tool |
|---|---|
| "How does this library's API work?" | Context7 |
| "What's the syntax in this version?" | Context7 (version-aware) |
| "Where is the X function in our codebase?" | `search_graph` |
| "How does our auth flow work?" | `get_architecture` then `trace_path` |
| "What calls this, and what does it call?" | `trace_path` |
| "Show me the implementation of Y" | `get_code_snippet` |
| "Find all files matching `*.test.ts`" | `Glob` — it's a file question, not a code question |
| "What does this config file say?" | `Read` |

## Anti-patterns

- ❌ Reading whole files when a symbol query answers the question.
- ❌ Generating API code from training memory when Context7 can verify it.
- ❌ Shell `grep` / `find` for code questions.
- ❌ Working in an unindexed project and falling back to grep for everything.
- ❌ Editing a file you retrieved as a snippet without reading it first.
