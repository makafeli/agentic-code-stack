# Stage 3 — Design

**Goal:** Produce UI that doesn't look AI-generated. Stop shipping random
primary colours, system fonts, and rounded-card-with-left-border layouts.

The fix is not better prompting — it's better context. Real design systems are
defined by exact values: `letter-spacing: -2.125px`,
`rgba(50,50,93,0.25) 0px 30px 45px -30px`, `font-weight: 300`. Get those values
into the agent's context and the output stops feeling generic.

## Tools

### Design an interface — `mattpocock/skills`

`/design-an-interface` treats interface design as a first-class step rather than
something that happens incidentally while writing components. Use it when the
shape of the UI is the open question.

Pairs naturally with `/prototype` from Stage 1: prototype to find out what it
should be, design to decide what it is.

### Get Design — `getdesign.md`

A library of `DESIGN.md` files distilled from 342+ brand design systems (Apple,
Stripe, Notion, Linear, Vercel, Airbnb, BMW, Tesla, and so on). Each follows a
9-section schema: colour, typography, spacing, layout, components, motion,
voice, brand, anti-patterns.

**Two ways to use:**

1. **Manual** — grab the `DESIGN.md` for the brand you want, drop it in the
   project root. The agent reads it natively.
2. **Via skill** — prompts like "build a hero section in Apple style" trigger
   `npx getdesign@latest add <slug>` and read the spec at runtime. Always the
   latest version.

**Useful combos:** "Notion's warm colours + Linear's minimal layout, build a
feature section." "Apple's whitespace system + Claude's terracotta accent,
build a hero."

### Claude Design — built-in `frontend-design`

Anthropic's built-in skill for distinctive, production-grade interfaces where
visual direction matters as much as code quality.

It pairs with Get Design rather than competing: pull the brand tokens with Get
Design, then let `frontend-design` compose the UI.

**Trigger:** describe the surface to build. The skill engages when visual
quality matters.

## Agent rules for this stage

- ✅ If a `DESIGN.md` exists in the project root, read it first and use the
  exact tokens. Do not "interpret" them.
- ✅ When the user names a brand style, check for an existing `DESIGN.md` before
  fetching one.
- ✅ Discovery first — pin surface, audience, tone, and brand before writing a
  pixel.
- ✅ Honest placeholders — write `—` or a labelled grey block, never fabricated
  data.
- ❌ Don't invent hex values. Use what's in the spec.
- ❌ Don't ship Inter, Roboto, or `system-ui` as the display font unless the spec
  says so.
- ❌ No purple gradients on white, no generic emoji icons, no
  rounded-card-with-left-border. These are the AI tells.
- ❌ Don't make every card `border-radius: 8px`. Uniform radius everywhere reads
  as template.

## Cross-reference

Per-language UI tooling — shadcn for React, liquid-glass for Apple platforms —
lives in [`config/shared/languages/<lang>.md`](../config/shared/languages/).
