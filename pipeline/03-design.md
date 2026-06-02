# Stage 3 — Design

**Goal:** Produce UI that doesn't look AI-generated. Stop using random primary colours, system fonts, and rounded-card-with-left-border layouts.

The fix is not better prompting — it's better context. Real design systems are defined by exact values: `letter-spacing: -2.125px`, `rgba(50,50,93,0.25) 0px 30px 45px -30px`, `font-weight: 300`. Get those values into the agent's context and the output stops feeling generic.

## Tools

### Get Design — `getdesign.md`

A library of `DESIGN.md` files distilled from 342+ brand design systems (Apple, Stripe, Notion, Linear, Vercel, Airbnb, BMW, Tesla, Lamborghini, etc.). Each file follows a 9-section schema: colour, typography, spacing, layout, components, motion, voice, brand, anti-patterns.

**Two ways to use:**

1. **Manual** — visit `getdesign.md`, grab the `DESIGN.md` for the brand you want, drop it in the project root. The agent reads it natively.
2. **As a Claude skill** — install `zephyrwang6/brand-design-md`. Then prompts like:
   - "Build a hero section in Apple style"
   - "Make a pricing page that looks like Stripe"
   - "Create a Notion-style card component"
   
   ...auto-trigger the skill, which calls `npx getdesign@latest add <slug>` and reads the spec at runtime. Always the latest version.

**Useful combos:**
- "Notion's warm colours + Linear's minimal layout, build a feature section"
- "Apple's whitespace system + Claude's terracotta accent, build a Hero"

### Claude Design — built-in `frontend-design`

Anthropic's official design capability, available as the built-in **`frontend-design`** skill. Use it to create distinctive, production-grade interfaces where visual direction matters as much as code quality. It pairs with Get Design: pull the brand tokens with Get Design, then let `frontend-design` compose the UI.

**Trigger:** describe the surface to build ("a pricing page", "a dashboard hero") — the skill engages when visual quality matters.

**Keep these guardrails in any project (good rules regardless of tool):**
- Discovery first — pin surface, audience, tone, brand before writing a pixel.
- No AI slop — no purple gradients, generic emoji icons, rounded-card-with-left-border, or Inter-as-display-font defaults.
- Honest placeholders — write `—` or a labelled grey block, never fabricated data.

### `↳ Overlay (gstack)`

If gstack is installed: **`/design-review`** (audits a built UI), **`/design-html`** (generates HTML mockups), **`/design-consultation`** (direction before building). See `overlay/README.md`.

## Agent rules for this stage

- ✅ If a `DESIGN.md` exists in the project root, read it first and use the exact tokens (colours, type scale, spacing, shadows). Do not "interpret".
- ✅ When the user names a brand style ("make it Stripe-like"), check for an existing `DESIGN.md` before fetching one. If none, use `npx getdesign@latest add <slug>` to pull the spec into the context.
- ✅ Apply the AI-slop blacklist even without a design skill installed — the rules are good in any project.
- ❌ Don't invent hex values. Use what's in the spec.
- ❌ Don't ship Inter, Roboto, or system-ui as the display font unless the spec says so.
- ❌ Don't make every card `border-radius: 8px` with a left border. It's the AI tell.

## Cross-reference

`frontend-design` (Claude Design, above) is the primary UI skill. For per-language UI tooling (shadcn for React, liquid-glass for Apple), see `overlay/languages/<lang>.md`.
