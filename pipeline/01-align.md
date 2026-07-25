# Stage 1 — Align

**Goal:** Reach shared understanding *before* writing any code. Surface
assumptions, edge cases, and dependencies between decisions.

The most common failure mode in AI-assisted dev is misalignment. The user thinks
the agent understands; the agent thinks the user has decided. Neither is true.
This stage fixes that.

Alignment has two sizes. A single decision that fits in one session gets grilled.
An effort too big for one session — where the *route* to the destination isn't
visible yet — gets a map.

## Tools

### Wayfinder — `mattpocock/skills`

For work that won't fit in one session and whose path is still foggy. Charts a
**map** as a GitHub issue, then works its tickets one at a time.

**Trigger:** `/wayfinder`, or a loose idea plus "this is too big for one go".

**Mechanics:**

- The map is one issue labelled `wayfinder:map`. Its sections are Destination,
  Notes, Decisions so far, Not yet specified, Out of scope.
- Tickets are **child issues** of the map, each labelled `wayfinder:research`,
  `wayfinder:prototype`, `wayfinder:grilling`, or `wayfinder:task`.
- Blocking uses GitHub's native dependency relation, so the frontier renders in
  GitHub's own UI without opening the map.
- The **frontier** is the open, unblocked, unclaimed tickets. Claim one by
  assigning it to yourself *before* any work.
- **Never resolve more than one ticket per session.** The limit is the point —
  one ticket is one session's worth of context.

**Fog of war:** don't chart what you can't see yet. "Not yet specified" holds the
in-scope questions you can't phrase sharply. The test for ticket-vs-fog is
whether you can *state* the question now, not whether you can answer it.

**Default is plan, don't do.** A ticket resolves a decision. If an effort wants
execution carried into the map, say so explicitly in the map's Notes.

### Grill-me — `mattpocock/skills`

Flips the script: instead of waiting for the user to specify, the agent
interrogates them down a decision tree, **one question at a time**, recommending
an answer for each.

**Trigger:** `/grill-me`, or "grill me on this", "poke holes in my design".

**Variants:** `/grill-with-docs` — same, but checks proposed terms against
`CONTEXT.md`, ADRs in `docs/adr/`, and the codebase. Surfaces glossary conflicts
immediately and updates `CONTEXT.md` as decisions crystallise.

**Behavior rules:**

- One question at a time. Wait for an answer before moving on.
- Recommend an answer with each question. Don't just ask — guide.
- If a question can be answered by exploring the codebase, explore instead.
- Never accept "I'll figure that out later" as a way to advance the branch.
- End with a structured summary of decisions.

### Grilling + Domain modeling — `mattpocock/skills`

`/grilling` is the general conversational stress-test; `/domain-modeling` pins
down terminology and the ubiquitous language, and records ADRs. Reach for the
pair when the disagreement is about *what things are called* rather than what to
build.

### Prototype — `mattpocock/skills`

**`/prototype` is available before speccing**, and that is deliberate. When the
real question is "how should this look" or "how should this behave," another
round of abstract discussion is worse than a rough artifact to argue with.

Produces something cheap and concrete — an outline, a stub, a rough UI — and
links it as an asset. Raise the fidelity of the discussion, not the fidelity of
the code.

## When to use which

| Situation | Tool |
|---|---|
| Effort spans multiple sessions, route unclear | `/wayfinder` |
| Greenfield project, no existing docs | `/grill-me` |
| Brownfield project with `CONTEXT.md` / ADRs | `/grill-with-docs` |
| Argument is about naming or concepts | `/grilling` + `/domain-modeling` |
| "How should it look / behave?" | `/prototype` |
| Quick yes/no on a small change | Skip — go to Spec & Plan |

## Anti-patterns

- ❌ Skipping this stage on anything non-trivial.
- ❌ Asking three questions at once. Multi-question turns lose 2 of the 3 answers.
- ❌ Telling the user what to do when they ask "what should I do?" — ask them,
  and recommend.
- ❌ Charting a wayfinder map for work that fits in one session. If grilling the
  destination surfaces no fog, you don't need a map.
- ❌ Pre-slicing the fog into ticket-sized pieces. Fog is coarser than a ticket
  on purpose.
- ❌ Resolving several wayfinder tickets in one session because they seemed small.
