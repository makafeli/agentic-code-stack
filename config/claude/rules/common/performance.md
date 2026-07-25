# Performance Optimization

## Model Selection Strategy

> Full prompting/effort guidance: Claude 5 frontier pair → [fable-5.md](fable-5.md); Sonnet 5 → [sonnet-5.md](sonnet-5.md); Opus 5 → [opus-5.md](opus-5.md).

**Fable 5 / Mythos 5** (`claude-fable-5`, Claude 5 frontier pair — deepest reasoning, long-horizon):
- Hardest, ambiguous, multi-hour or multi-day autonomous work
- First-shot correctness on complex, well-specified problems
- Effort is the main dial: `high` default, `xhigh` for the most capability-sensitive work
- Refusal fallback: `cyber` → Opus 4.8; other categories → Opus 5 (or `fallbacks: "default"`)

**Sonnet 5** (`claude-sonnet-5`, balanced coding model — see [sonnet-5.md](sonnet-5.md)):
- Main development work; orchestrating multi-agent workflows
- Effort `high` default, `xhigh` for hardest coding/agentic
- Breaking vs 4.6: adaptive thinking on by default; `temperature`/`top_p`/`top_k` and manual `budget_tokens` now 400; ~30% heavier tokenizer (re-baseline `max_tokens`)
- Refuses only on cybersecurity (`stop_reason: "refusal"`)

**Opus 5** (`claude-opus-5`, strongest general-purpose model — see [opus-5.md](opus-5.md)):
- Architecture, deep review, hard agentic coding
- Effort `high` default, `xhigh` for long-running agentic/coding work
- 1M context, 128k max output — same window as Fable 5/Sonnet 5
- Refuses only on cybersecurity; falls back to Opus 4.8

**Opus 4.8** (`claude-opus-4-8`, legacy general default):
- Kept only as the cyber-refusal fallback for Opus 5 and Fable 5
- Architectural decisions and analysis when Opus 5 is unavailable

**Haiku 4.5** (`claude-haiku-4-5-20251001`, cheap and fast):
- Lightweight agents with frequent invocation
- Worker agents in multi-agent systems

## Context Window Management

Avoid last 20% of context window for:
- Large-scale refactoring
- Feature implementation spanning multiple files
- Debugging complex interactions

Lower context sensitivity tasks:
- Single-file edits
- Independent utility creation
- Documentation updates
- Simple bug fixes

## Extended Thinking + Plan Mode

> **The entire Claude 5 family (Fable 5 / Mythos 5, Sonnet 5, Opus 5) uses adaptive
> thinking only** — summarized thinking output, no budgets. `MAX_THINKING_TOKENS`
> and the 31,999-token reserve below do not apply; `thinking: {budget_tokens: N}`
> is a 400 on all three. Control depth with **effort** instead (see
> [fable-5.md](fable-5.md), [sonnet-5.md](sonnet-5.md), [opus-5.md](opus-5.md)).
> The rest of this section applies to **Opus 4.8 / earlier models only**.

Extended thinking is enabled by default, reserving up to 31,999 tokens for internal reasoning.

Control extended thinking via:
- **Toggle**: Option+T (macOS) / Alt+T (Windows/Linux)
- **Config**: Set `alwaysThinkingEnabled` in `~/.claude/settings.json`
- **Budget cap**: `export MAX_THINKING_TOKENS=10000`
- **Verbose mode**: Ctrl+O to see thinking output

For complex tasks requiring deep reasoning:
1. Ensure extended thinking is enabled (on by default)
2. Enable **Plan Mode** for structured approach
3. Use multiple critique rounds for thorough analysis
4. Use split role sub-agents for diverse perspectives

## Build Troubleshooting

If build fails:
1. Use **build-error-resolver** agent
2. Analyze error messages
3. Fix incrementally
4. Verify after each fix
