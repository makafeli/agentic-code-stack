# Claude 5 Family: Fable 5 & Mythos 5

How to prompt and scaffold for **Claude Fable 5** and **Claude Mythos 5** (the
Claude 5 frontier pair). Many patterns here also apply to Sonnet 5, but it has its
own API constraints and levers — see **[sonnet-5.md](sonnet-5.md)**. Distilled from
Anthropic's *Prompting Claude Fable 5* guidance. The patterns are behavioral defaults
— user instructions still win.

## Model & Effort Selection

Current model IDs:

| Model | ID | Use for |
|-------|-----|---------|
| Fable 5 | `claude-fable-5` | Hardest, long-horizon, ambiguous, multi-hour/day autonomous work |
| Mythos 5 | (Claude 5 sibling of Fable 5) | Same prompting patterns as Fable 5 |
| Sonnet 5 | `claude-sonnet-5` | Balanced coding and orchestration. Distinct API constraints — see [sonnet-5.md](sonnet-5.md). |
| Opus 4.8 | `claude-opus-4-8` | Strong general default; the refusal-fallback target for Fable 5 |
| Haiku 4.5 | `claude-haiku-4-5-20251001` | Cheap, high-frequency worker agents |

**Effort is the primary control** on Fable 5 (intelligence vs latency vs cost):

- `high` — default for most tasks.
- `xhigh` — the most capability-sensitive work.
- `medium` / `low` — routine work. Lower effort on Fable 5 still often beats
  `xhigh` on prior models. Drop effort if a task completes but takes longer than
  needed, or you want a quicker, more interactive loop.

**Thinking:** Fable 5 uses adaptive thinking only — summarized thinking output,
**no** extended-thinking token budgets. `MAX_THINKING_TOKENS` / budget caps do
not apply. Do not tell the model to echo, transcribe, or explain its internal
reasoning as response text (triggers the `reasoning_extraction` refusal). If you
need reasoning visibility, read the structured `thinking` blocks.

## Longer Turns by Default

Individual requests can run many minutes at higher effort; autonomous runs can go
for hours. Adjust timeouts and expectations accordingly. Prefer checking on long
runs asynchronously over blocking.

## Prompting Patterns

Instruction-following is strong enough to steer with a brief instruction rather
than enumerating every case. Keep these in the prompt for long or agentic work:

- **Act when you have enough.** When you can act, act. Don't re-derive settled
  facts, re-litigate decided choices, or narrate options you won't pursue. Give a
  recommendation, not a survey. (Does not apply to thinking blocks.)
- **No unrequested tidying at high effort.** Don't add features, refactor, or
  abstract beyond the task. A bug fix needs no surrounding cleanup. Validate only
  at system boundaries; trust internal code. No feature flags or back-comfort
  shims when you can just change the code.
- **Brevity.** Lead with the outcome — first sentence answers "what happened / what
  did you find." Be selective about what to include, not compressed into fragments,
  arrow-chains (`A → B → fails`), or jargon. Readable beats terse.
- **Checkpoint only when needed.** Pause for the user only on a destructive/
  irreversible action, a real scope change, or input only they can provide. Then
  ask and end the turn — don't end on a promise ("I'll…").
- **Ground progress claims.** Before reporting progress, audit each claim against a
  tool result from this session. Report only verified work; say so when something
  isn't verified. If tests fail, say so with output.
- **State boundaries.** When the user is describing a problem, asking, or thinking
  out loud, the deliverable is your assessment — report and stop, don't apply a fix
  until asked. Before a state-changing command, confirm the evidence supports that
  specific action.

## Autonomous / Async Runs

- **No mid-turn dead-ends.** Deep in a long session Fable 5 can occasionally end a
  turn with a stated intent but no tool call, or ask permission it already has. For
  autonomous pipelines: proceed on reversible actions without asking; before ending
  a turn, if the last paragraph is a plan/question/promise, do the work now.
- **Don't surface context-budget countdowns** to the model; if you must, reassure
  it that ample context remains and to continue.
- **Give the reason, not just the request.** Context ("I'm doing X for Y, they need
  Z, so:") lets the model connect the task to relevant information.
- **Subagents.** Fable 5 dispatches parallel subagents readily — delegate
  independent subtasks and keep working; prefer async communication over blocking.
- **Memory.** Fable 5 does well recording lessons and referencing them. One lesson
  per file, one-line summary on top, corrections and confirmed approaches with the
  why; update rather than duplicate, delete what's wrong.
- **Self-verify.** Fresh-context verifier subagents beat self-critique. For long
  runs, verify work against the spec at a set interval.

## Refusals & Fallback

Fable 5 runs safety classifiers that can decline with `stop_reason: "refusal"`
(an HTTP 200, not an error). Categories: `cyber`, `bio`, `frontier_llm`,
`reasoning_extraction`. Benign work in those areas can also trip them. **Sonnet 5**
also refuses via `stop_reason: "refusal"` but only for real-time **cybersecurity**
safeguards (first Sonnet tier to ship them) — narrower surface than Fable 5.

- **Retry on a different model** — usually **Opus 4.8** answers the same request.
- Configure fallback on **every** request path (handlers, workers, sub-agents);
  `fallbacks` does not propagate into tool-execution model calls.
- Branch on `stop_reason == "refusal"`, not on `stop_details` or `content`
  (`stop_details` can be `null`). A refusal before any output is not billed.
- Server-side: `fallbacks: [{model: "claude-opus-4-8"}]` + beta header
  `server-side-fallback-2026-06-01`. Or use the SDK refusal-fallback middleware.

## Scaffolding Notes

- Start at the top of your difficulty range — give Fable 5 a harder task than you'd
  give prior models, and let it scope and execute.
- Refactor old prompts/skills: instructions written for prior models are often too
  prescriptive and can degrade Fable 5. Remove reflection / "show your thinking"
  instructions when migrating.

## Sonnet 5

Sonnet 5 (`claude-sonnet-5`) shares many of the patterns above but has its own API
constraints (adaptive thinking on by default, sampling params and manual extended
thinking now 400, ~30% heavier tokenizer) and tuning levers (literal
instruction-following, complexity-calibrated length, cyber-only refusals). Full
guidance: **[sonnet-5.md](sonnet-5.md)**.
