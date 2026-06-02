# Stage 2 — Spec & Plan

**Goal:** Turn the aligned idea into durable artefacts that survive context loss between sessions.

Plans in chat windows die when the chat dies. Specs in the repo don't.

## Tools

### Superpowers planning — `obra/superpowers`

Turn the aligned design into a plan, then execute it with review checkpoints.

**Slash commands:**
| Command | Purpose |
|---|---|
| `/superpowers:write-plan` | Turn a design into a step-by-step plan written for "an enthusiastic junior engineer with poor taste, no judgement, no project context, and an aversion to testing" — i.e., extremely explicit |
| `/superpowers:execute-plan` | Run the plan in a separate session with review checkpoints |
| `/using-superpowers` | Reminds the agent it has Superpowers and should use the proper workflow |

The plan that comes out of `/write-plan` is broken into 2–5-minute micro-tasks, each of which can be picked up independently.

## When to use which

| Situation | Tool |
|---|---|
| Single-session feature | `/superpowers:write-plan` then `/superpowers:execute-plan` |
| Already running Superpowers from Align | Continue with Superpowers |
| Large multi-review plan, on this machine | `↳ Overlay (gstack):` `/autoplan` |

## `↳ Overlay (gstack)` — autoplan + plan reviews

If gstack is installed, **`/autoplan`** expands a one-line prompt into a full plan and runs CEO / design / eng / DX reviews with auto-decisions, surfacing only taste calls at a final gate. The individual reviews — **`/plan-ceo-review`**, **`/plan-eng-review`**, **`/plan-design-review`**, **`/plan-devex-review`** — and **`/cso`** can be run on their own. On Opus 4.8 this pairs with the **Workflow** orchestration tool. See `overlay/harness.md`.

## Cross-reference

Plan tasks can be tracked as **beads** (see `rails/03-memory-tracking.md`) so progress survives across sessions.
