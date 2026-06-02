# Stage 1 — Align

**Goal:** Reach shared understanding *before* writing any code. Surface assumptions, edge cases, and dependencies between decisions.

The most common failure mode in AI-assisted dev is misalignment. The user thinks the agent understands; the agent thinks the user has decided. Neither is true. This stage fixes that.

## Tools

### Grill-me — `mattpocock/skills`

A 3-sentence skill that flips the script: instead of waiting for the user to specify, you (the agent) interrogate them down a decision tree, **one question at a time**, and recommend an answer for each.

**Trigger:**
- `/grill-me`
- Or natural language: "grill me on this", "poke holes in my design", "play devil's advocate"

**Variants:**
- `/grill-with-docs` — same, but checks proposed terms against existing `CONTEXT.md`, ADRs in `docs/adr/`, and the codebase. When a term conflicts with the project's glossary, surface it immediately. Updates `CONTEXT.md` inline as decisions crystallise.

**Behavior rules:**
- One question at a time. Wait for an answer before moving on.
- Recommend an answer with each question. Don't just ask — guide.
- If a question can be answered by exploring the codebase, explore instead.
- Never accept "I'll figure that out later" as a way to advance the branch.
- End with a structured summary of decisions.

### Superpowers brainstorm — `obra/superpowers`

The same Socratic discovery, but as part of a larger workflow that continues into spec writing, planning, and TDD implementation. Auto-activates before any creative work (creating features, building components, modifying behaviour).

**Trigger:**
- `/superpowers:brainstorming`
- Or just describe what you want to build — Superpowers detects creative-work intent and auto-engages.

**Output:** refined requirements, presented in chunks short enough to read and sign off on.

### `↳ Overlay (gstack)` — Office Hours

If gstack is installed, **`/office-hours`** runs YC-style forcing questions (startup mode) or design-thinking brainstorming (builder mode) and saves a design doc. Use it for product/idea-level discovery; use grill-me / brainstorm for implementation-level alignment. See `overlay/README.md`.

## When to use which

| Situation | Tool |
|---|---|
| Greenfield project, no existing docs | `/grill-me` |
| Brownfield project with `CONTEXT.md` / ADRs | `/grill-with-docs` |
| You want the alignment to flow into a full plan | `/superpowers:brainstorming` |
| User explicitly says "grill me" or "stress-test this" | `/grill-me` |
| Quick yes/no on a small change | Skip — go to Spec & Plan |

## Anti-patterns

- ❌ Skipping this stage on anything non-trivial.
- ❌ Asking three questions at once. Multi-question turns lose 2 of the 3 answers.
- ❌ Telling the user what to do when they ask "what should I do?" — instead, ask them and recommend.
- ❌ Treating the final summary as optional. Always end with a consolidated view.
