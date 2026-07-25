# Global Codex guidance

Applies to every repository. Repository and nested `AGENTS.md` files add or
override project-specific conventions.

## Code discovery

When the current repository has a ready codebase-memory-mcp index, prefer its
graph tools for code discovery:

1. `search_graph` for functions, classes, routes, and variables.
2. `trace_path` for callers, callees, dependencies, and data flow.
3. `get_code_snippet` for source after resolving the exact symbol.
4. `query_graph` for complex structural queries.
5. `get_architecture` for a high-level map.

Use `rg` or file reads for string literals, error messages, configuration,
scripts, generated files, or when the graph is insufficient. Check
`index_status` before relying on an unfamiliar repository index.
