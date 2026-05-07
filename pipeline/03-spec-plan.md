# Stage 3 — Spec & Plan

**Goal:** Turn the aligned idea into durable artefacts that survive context loss between sessions.

Plans in chat windows die when the chat dies. Specs in the repo don't.

## Tools

### OpenSpec — `Fission-AI/OpenSpec`

Spec-driven development. Specs (current behaviour) and changes (proposed modifications) are managed separately. Multiple changes can run in parallel without conflict.

**Slash commands:**
| Command | Purpose |
|---|---|
| `/opsx:propose "<idea>"` | Generate `proposal.md`, `specs/`, `design.md`, `tasks.md` |
| `/opsx:apply` | Implement against the active proposal |
| `/opsx:archive` | Archive a completed change; specs are updated incrementally |
| `/opsx:onboard` | 11-phase walkthrough for new users (~15 min) |
| `/opsx:verify` | Drift check — codebase vs. specs |
| `/opsx:continue` | Resume an in-progress change |
| `/opsx:ff` | Fast-forward through trivial steps |
| `/opsx:bulk-archive` | Archive multiple completed changes at once |

**File layout it creates:**
```
openspec/
├── specs/                  # source of truth, organised by capability
├── changes/                # active proposed changes
│   └── <change-name>/
│       ├── proposal.md     # why and what
│       ├── specs/          # requirements + scenarios (Given/When/Then)
│       ├── design.md       # technical approach
│       └── tasks.md        # implementation checklist
└── changes/archive/        # completed changes
```

**Spec language:** RFC 2119 keywords (MUST/SHALL/SHOULD/MAY). Scenarios use Given/When/Then so each is testable.

**Critical:** the `proposal.md` includes "in scope / out of scope". Write down what you are NOT doing — this stops the agent from "helpfully" doing things you didn't ask for.

### Superpowers planning — `obra/superpowers`

If you don't want a full SDD framework, use Superpowers' lighter `/write-plan` skill.

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
| Multi-session feature, brownfield project | OpenSpec |
| Want specs as long-term documentation | OpenSpec |
| Single-session feature, greenfield | Superpowers `/write-plan` |
| Already running Superpowers from Stage 2 | Continue with Superpowers |
| You want both — spec + plan | OpenSpec for spec, Superpowers for execution |

## Cross-reference

OpenSpec's tasks can be tracked as **beads** (see `rails/03-memory-tracking.md`) so progress survives across sessions even better.
