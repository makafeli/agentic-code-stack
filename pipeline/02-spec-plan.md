# Stage 2 — Spec & Plan

**Goal:** Write the aligned idea down so it survives context loss.

Context windows end. Sessions get compacted. Whatever isn't written down is
gone. A plan is not bureaucracy — it is the only part of the alignment that
outlives the conversation that produced it.

## Tools

### Wayfinder tickets — `mattpocock/skills`

For multi-session work, **the map is the plan**. There is no separate plan
document.

- The map body holds Destination, Notes, and the **Decisions so far** index.
- Each decision lives in exactly one place — its ticket. The map gists and
  links; it never restates.
- Resolving a ticket means posting the answer as a resolution comment, closing
  the issue, and appending one line to Decisions so far.
- Resolving also **clears fog**: whatever the answer made specifiable graduates
  from "Not yet specified" into fresh tickets. Create them, then wire blocking
  in a second pass — issues need ids before they can reference each other.

Refer to maps and tickets by **name**, not by bare number. A wall of `#42, #43,
#44` is illegible; titles read at a glance.

### Improve — `shadcn/improve`

Surveys a codebase as a senior advisor and emits **prioritized, self-contained
implementation plans for another model to execute**. Strictly read-only on
source — it never implements, fixes, or refactors anything itself.

| Command | Purpose |
|---|---|
| `/improve` | Full audit → prioritized plans |
| `/improve quick` | Faster, shallower pass |
| `/improve security` | Security-focused audit |
| `/improve execute <plan>` | Hand a plan to a cheaper model to run |

The split is the value: an expensive model finds the work and writes the plan,
a cheap model does it. Use it for bugs, tech debt, test coverage, migrations,
DX, and roadmap questions ("where should this project go next?").

### Codebase design — `mattpocock/skills`

`/codebase-design` for design work that has to fit an existing structure rather
than start clean. Reach for it when the constraint is "match what's already
here," not "what's the best shape in the abstract."

## What a plan needs

- **The decision, not the discussion.** A plan that recounts how you got there
  is a transcript. Write the conclusion.
- **Enough for a junior engineer.** If a step needs context that only exists in
  the session that wrote it, the plan has failed.
- **Explicit blocking.** What can't start until what finishes.
- **A stated scope boundary.** What is deliberately *not* in this effort. Scope
  is cheaper to defend in writing than in an argument later.

## When to use which

| Situation | Tool |
|---|---|
| Multi-session effort with unclear route | `/wayfinder` map + tickets |
| Existing codebase, want a work queue | `/improve` |
| Security-specific sweep | `/improve security` |
| New structure that must fit old structure | `/codebase-design` |
| One-session change with a clear shape | Skip — write the issue and build it |

## Anti-patterns

- ❌ Planning work that fits in one session. The plan costs more than the work.
- ❌ Restating a decision in the map that already lives in its ticket. One
  decision, one home.
- ❌ Letting `/improve` implement. It is read-only by design; the moment it
  writes code, you have lost the cheap-model handoff.
- ❌ A plan with no scope boundary. Every unbounded plan grows.
- ❌ Markdown TODO files. Track work as GitHub Issues — see
  [rails/03-memory-tracking.md](../rails/03-memory-tracking.md).
