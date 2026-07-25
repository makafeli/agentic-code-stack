# Claude Opus 5

How to prompt, migrate to, and scaffold for **Claude Opus 5** (`claude-opus-5`),
the successor to Opus 4.8. Distilled from Anthropic's *Migrating from Claude Opus
4.8 to Claude Opus 5*. Shares most behavioral patterns with the Claude 5 frontier
pair ([fable-5.md](fable-5.md)) — this file covers what is **specific to Opus 5**.

## What It Is

Drop-in upgrade for Opus 4.8 — the strongest general-purpose model for
architecture, deep review, and hard agentic coding.

- **Specs:** **1M context** (default and maximum, no beta header), 128k max output —
  same as Fable 5 and Sonnet 5. Knowledge cutoff **May 2026**, the most recent in
  the lineup.
- **Pricing:** `$5/$25` per MTok — unchanged from 4.8. Half the cost of Fable 5.
- **Fast mode** (research preview, Claude API only): higher output speed at
  `$10/$50` per MTok.
- **Prompt cache minimum drops to 512 tokens** (was 1,024) — shorter prefixes now cache.
- **Not available:** the web fetch tool, Priority Tier.

## Breaking Changes from Opus 4.8

1. **Thinking is ON by default** (adaptive). Requests with no `thinking` field now
   think. `max_tokens` is a hard cap on thinking **+** response text together —
   re-baseline it for anything that ran thinking-off on 4.8, or you get a
   mostly-thinking response truncated with `stop_reason: "max_tokens"`.
2. **`thinking: {type: "disabled"}` is only legal at effort `high` or below.**
   Pairing it with `xhigh` or `max` returns **400**, enforced per request.
   With thinking disabled, Opus 5 can occasionally write a tool call into text
   output or leak internal XML tags — prefer thinking on + lower effort.

Unchanged 400s carried over from 4.8: non-default `temperature`/`top_p`/`top_k`,
manual `thinking: {budget_tokens: N}`, assistant-message prefill.

## Effort

Defaults to `high`.

- `xhigh` — long-running agentic and coding work.
- `max` — maximum capability, but it **overthinks simple tasks**. Don't make it the default.
- `medium` / `low` — routine work, faster loops. `low`/`medium` scope tightly to what was asked.

Run a **fresh effort sweep** rather than carrying 4.8 settings over; the tiers do
not map one-to-one. At `xhigh`/`max` leave `max_tokens` ≥ 64k so thinking and tool
calls fit.

## Prompting Changes

Two things actually change how you write prompts for Opus 5:

- **Responses and deliverables run longer by default.** If you want brevity, ask for
  it explicitly — a target length, a section budget, or "lead with the outcome."
  Positive framing ("communicate at this level of concision") beats "don't ramble."
- **Remove verification and self-check instructions.** Opus 5 self-verifies. Explicit
  "double-check your work / re-read the file / confirm before answering" scaffolding
  causes *over*-verification and wasted turns. Delete it when migrating a 4.8 prompt.

Also expect more progress narration in agentic sessions and readier subagent
delegation in multi-agent frameworks — tune with prompts only if unwanted.

Everything else — act-when-you-have-enough, no unrequested tidying, checkpoint only
when needed, ground progress claims — carries over from
[fable-5.md](fable-5.md#prompting-patterns).

## Refusals & Fallback

Opus 5 ships **cybersecurity** classifiers. It does *not* carry Fable 5's wider
`bio` / `frontier_llm` / `reasoning_extraction` surface. A decline arrives as an
HTTP 200 with `stop_reason: "refusal"`; `stop_details.category` names the classifier
(and can be `null` — branch on `stop_reason`).

- Fall back to **Opus 4.8** for cyber-category refusals.
- Server-side: simplest is `fallbacks: "default"` + beta header
  `server-side-fallback-2026-07-01` (Anthropic routes per category); or name an
  explicit list.
- Configure fallback on **every** request path; `fallbacks` does not propagate into
  tool-execution model calls.
- A refusal before any output is not billed.

## Other Beta Headers

- `mid-conversation-tool-changes-2026-07-01` — change the tool set mid-conversation
  without invalidating the cache prefix.

## Migration Checklist (4.8 → 5)

- [ ] `claude-opus-4-8` → `claude-opus-5`.
- [ ] Re-baseline `max_tokens` (thinking now counts against it); ≥ 64k at `xhigh`/`max`.
- [ ] Any `thinking: {type: "disabled"}` path must run at effort ≤ `high`.
- [ ] Fresh effort sweep — don't carry 4.8 effort settings.
- [ ] Strip verification/self-check instructions from prompts.
- [ ] Add an explicit concision instruction where output length matters.
- [ ] Point refusal fallback at `claude-opus-4-8` with `server-side-fallback-2026-07-01`.
- [ ] Drop any dependency on the web fetch tool or Priority Tier.
