# Default Opus 4.8 Flow

For non-trivial work, this loop runs on top of the pipeline (with `/autoplan` + Workflow handling planning and orchestration):

```
/caveman → /graphify → code-structure → improve-codebase-architecture → /tdd → Fallow → CodeRabbit (+ /autofix)
```

1. **`/caveman`** — engage output compression for the session (prose only; code/commits stay normal).
2. **`/graphify`** — build or query the codebase knowledge graph (`graphify-out/`) before diving in. If `graphify-out/graph.json` exists, treat codebase questions as `graphify query` calls.
3. **`code-structure`** — apply Service Layer Architecture: actions own product rules, services own reusable mechanics.
4. **`improve-codebase-architecture`** — find shallow modules, deepen them, write ADRs.
5. **`/tdd`** — red → green → refactor. Failing test first, always.
6. **Fallow** — `npx fallow dead-code` · `dupes` · `health` to catch dead code, duplication, and complexity before the PR. `fix --dry-run` previews cleanup.
7. **CodeRabbit** — open the PR; CodeRabbit reviews it on GitHub. Run **`/autofix`** to apply its review-thread feedback with per-change approval.

The rails stay active throughout (docs context, token efficiency, external data).
