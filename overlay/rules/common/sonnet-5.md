# Claude Sonnet 5

How to prompt, migrate to, and scaffold for **Claude Sonnet 5** (`claude-sonnet-5`).
Distilled from Anthropic's *Prompting Claude Sonnet 5*, *What's new in Sonnet 5*, and
the *Sonnet 4.6 → 5 migration* guidance. Behavioral defaults — user instructions still
win. For the Claude 5 frontier pair, see [fable-5.md](fable-5.md); many of those
patterns (act-when-you-have-enough, brevity, ground-progress-claims, subagents) apply
here too. This file covers what is **specific to Sonnet 5**.

## What It Is

Drop-in upgrade for Sonnet 4.6 — strongest on coding and agentic work, good out of the
box on existing 4.6 prompts. An option when you need more than 4.6 without going
Opus-class.

- **Specs:** 1M context (default *and* max — no smaller variant), 128k max output.
- **Pricing:** `$3/$15` per MTok; introductory `$2/$10` through **2026-08-31**.
- **Not available:** Priority Tier. Assistant-message prefilling → 400 (unchanged from 4.6).

## Three Breaking Changes from Sonnet 4.6

1. **Adaptive thinking is ON by default.** Requests with no `thinking` field now think
   (4.6 didn't). Disable with `thinking: {type: "disabled"}`. To surface it,
   `thinking: {type: "adaptive", display: "summarized"}`. `max_tokens` caps thinking +
   response text together — re-baseline it for anything that ran thinking-off on 4.6
   (else you risk a mostly-thinking response truncated with `stop_reason: "max_tokens"`).
2. **Sampling params rejected.** `temperature`, `top_p`, `top_k` at any non-default
   value → **400 error**. Remove them; steer tone/variety via the system prompt instead.
3. **Manual extended thinking removed.** `thinking: {type: "enabled", budget_tokens: N}`
   → **400 error**. Use adaptive thinking + the effort parameter.

## New Tokenizer

Same input text produces **≈30% more tokens** than 4.6. Not an API change (shapes are
identical, no code change), but it affects anything measured in tokens:

- Recount prompts with token-counting — don't reuse 4.6 counts.
- The 1M window holds less text than the same window on 4.6.
- `max_tokens` limits sized near expected output may now truncate — revisit them.
- Per-token price is unchanged, so an equivalent request can cost more.

## Effort

Defaults to `high` (same as 4.6). `xhigh` for the hardest coding/agentic work; `max`
for uncapped capability. Cross-model mapping when migrating: Sonnet 5 `medium` ≈ 4.6
`high`; Sonnet 5 `high` ≈ 4.6 `max` — when benchmarking, match by observed thinking
length, not effort name.

Sonnet 5 respects effort **strictly** at the low end: at `low`/`medium` it scopes to
exactly what was asked (good for latency/cost, but risks under-thinking on moderately
complex tasks). Fix shallow reasoning by raising effort, not by prompting around it. If
you must hold `low` for latency, nudge directly: *"This task involves multi-step
reasoning. Think carefully through the problem before responding."*

Leave `max_tokens` headroom at `high`/`xhigh`/`max` so thinking and tool calls fit.

## Behavioral Tuning Levers

- **More literal instruction-following**, especially at low effort — it won't
  generalize an instruction across items or infer unmade requests. State scope
  explicitly ("apply to every section, not just the first"). Upside: precision — great
  for structured extraction and predictable pipelines.
- **Response length calibrates to task complexity** — shorter on lookups, longer on
  analysis. If you depend on a fixed voice/verbosity, prompt for it; positive examples
  ("communicate at this level of concision") beat "don't" instructions.
- **More agentic tool use** by default, and runs self-verification loops readily. But
  with thinking **disabled** it reaches for tools less — add an explicit nudge if you
  rely on tool calls thinking-off. `high`/`xhigh` show substantially more tool usage.
- **Higher-quality interim progress updates** in long agentic traces — remove old
  "summarize every N tool calls" scaffolding; if the updates aren't calibrated, describe
  what they should look like with examples.
- **Prose style may shift** — re-evaluate voice prompts against the new baseline. With
  `temperature` gone, guide tone via the system prompt.

## Code-Review Harnesses

A recall dip on a review harness tuned for an older model is usually a **harness
effect, not a regression**: Sonnet 5 obeys "only high-severity" / "be conservative" /
"don't nitpick" more faithfully — same investigation depth, fewer findings converted to
reports. For coverage, split finding from filtering:

> Report every issue you find, including ones you are uncertain about or consider
> low-severity. Do not filter for importance or confidence at this stage — a separate
> verification step will do that. For each finding, include your confidence level and an
> estimated severity so a downstream filter can rank them.

For single-pass self-filtering, set a concrete bar ("bugs that could cause incorrect
behavior, a test failure, or a misleading result; omit pure style/naming nits") rather
than a qualitative word like "important."

## Frontend / Design

Sonnet 5 settles into a consistent default house style on open-ended briefs — fine for
some, wrong for dashboards, dev tools, fintech, healthcare, enterprise. Generic negatives
("make it clean") just swap one fixed palette for another. Two reliable breaks:

1. **Specify a concrete direction** (exact palette hex, typeface, radius, spacing) — it
   follows explicit specs precisely.
2. **Have it propose 3–4 directions first**, user picks one, then build. With
   `temperature` variety gone, this is *the* way to vary output across runs.

Anti-slop directive that works alongside the above:

```text
<frontend_aesthetics>
NEVER use generic AI-generated aesthetics: overused fonts (Inter, Roboto, Arial, system
fonts), cliched schemes (purple gradients on white/dark), predictable layouts, and
cookie-cutter patterns. Use unique fonts, cohesive themes, and micro-interactions.
</frontend_aesthetics>
```

## Refusals

Sonnet 5 is the first Sonnet tier with real-time **cybersecurity** safeguards — declines
arrive as `stop_reason: "refusal"` (HTTP 200, not an error). Narrower surface than Fable
5's four categories. Branch on `stop_reason == "refusal"`; fall back to another model
(see [fable-5.md](fable-5.md) → Refusals & Fallback).

## Interactive Coding Products

For token efficiency and performance: run `xhigh`/`high`, add autonomous features (auto
mode), and minimize required user interactions. Specify task, intent, and constraints
**upfront in the first turn** — ambiguous asks dribbled across turns reduce token
efficiency and sometimes performance.

## Computer Use

Supports `computer_20251124`, up to 2576px / 3.75MP. 1080p is a good performance/cost
balance; 720p or 1366×768 for cost-sensitive workloads.

## Migration Checklist (4.6 → 5)

- [ ] `claude-sonnet-4-6` → `claude-sonnet-5`.
- [ ] Replace `thinking: {type: "enabled", budget_tokens: N}` with
      `thinking: {type: "adaptive"}` + `effort`.
- [ ] Remove `temperature` / `top_p` / `top_k`.
- [ ] Explicitly set `thinking: {type: "disabled"}` if you need thinking off.
- [ ] Re-run token counting; re-baseline `max_tokens` for the ~30% heavier tokenizer.
- [ ] Add `display: "summarized"` if you show thinking to users.
- [ ] Test in dev before production.
