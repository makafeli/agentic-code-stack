# Stage 4 — Design

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

### Open Design — `nexu-io/open-design`

Open-source local-first alternative to Anthropic's Claude Design. 19–31 composable design skills × 71 brand systems = ~1,300 capability × style combinations. Runs through whichever coding agent you already have on your laptop (Claude Code, Codex, Cursor, Gemini CLI, OpenCode, Qwen).

**Setup:** `pnpm tools-dev` (see `INSTALL.md`).

**What it produces:**
- Magazine-style web PPTs (pitch decks)
- Landing pages
- Dashboards
- Mobile prototypes
- Social carousels
- Editorial pages
- Slide exports as HTML / PDF / PPTX / ZIP / Markdown

**Built-in guardrails worth knowing about:**
- **Discovery form first** — every brief opens with a structured question form (surface, audience, tone, brand context, scale) before the model writes a pixel.
- **5-step brand-asset protocol** — when the user attaches a screenshot or URL, the agent locates the brand's actual colours / typography before guessing.
- **5-dimensional self-critique** — output is scored on philosophy, hierarchy, execution, specificity, restraint. Anything under 3/5 triggers a fix-and-rescore pass.
- **AI-slop blacklist** — explicitly forbidden: purple gradients, generic emoji icons, rounded-card-with-left-border, hand-drawn SVG humans, Inter as display font.
- **Honest placeholders** — when there's no real number, write `—` or a labelled grey block. No fabricated data.

## Agent rules for this stage

- ✅ If a `DESIGN.md` exists in the project root, read it first and use the exact tokens (colours, type scale, spacing, shadows). Do not "interpret".
- ✅ When the user names a brand style ("make it Stripe-like"), check for an existing `DESIGN.md` before fetching one. If none, use `npx getdesign@latest add <slug>` to pull the spec into the context.
- ✅ Apply the AI-slop blacklist even without Open Design installed — the rules are good in any project.
- ❌ Don't invent hex values. Use what's in the spec.
- ❌ Don't ship Inter, Roboto, or system-ui as the display font unless the spec says so.
- ❌ Don't make every card `border-radius: 8px` with a left border. It's the AI tell.

## Cross-reference

If you're building UI with Tailwind / shadcn / Linear-style tokens, the **frontend-design** skill (built into Claude.ai's environment) covers production frontend patterns and pairs well with these tools.
