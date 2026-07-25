# Philosophy

## The problem

AI coding agents are powerful but unstructured by default. Without a shared
mental model a session degrades fast: the agent reads whole files instead of
navigating by symbol, jumps to code on a vague one-liner, or thrashes between
tools without knowing what belongs where. The human doesn't know what to ask
for. The agent doesn't know what it can reach for.

## Fewer tools, used properly

The first version of this stack collected everything that looked useful. That
was the mistake. A stack with three overlapping options per stage doesn't make
the agent better — it makes every stage a decision, and decisions are where
sessions go sideways.

So: **one skill set**, Matt Pocock's, plus a short list of tools that do
something his set genuinely doesn't. Anything that duplicates an existing tool
is out, even when it's good. Superpowers went. gstack went. Pitlane and graphify
both went, replaced by one code-intelligence server instead of two.

The test for adding anything is not "is this useful?" — almost everything is.
It's "does this do something nothing else here does?"

## Pipeline + Rails

**Pipeline** — sequential stages: Align (discovery before code), Spec & Plan
(write it down), Design (when UI is involved), Build (implement, then the review
gates). The pipeline gives *stage awareness*: the agent knows where it is and
what the current job is.

**Rails** — always-on capabilities running alongside every stage: docs and code
context, token efficiency, issue tracking, external data, agent interfaces.
Rails give *tool awareness*: instead of reaching for raw grep, the agent picks
the purpose-built thing.

Pipeline answers "what phase am I in?" Rails answer "what should I use?"

## The gates are the point

Anyone can get an agent to write code. The difference between a stack that
works and a stack that produces plausible-looking garbage is what happens
between "it compiles" and "it merges."

So the review half of Build is ordered, and the order is load-bearing:
correctness before simplification, because simplifying wrong code optimizes the
wrong thing. Standards and Spec review run as **separate agents**, because one
agent asked both questions reliably lets one drown out the other.

And the reviewers get the best model available at maximum effort. Review is the
cheapest place to spend compute — a bug caught in review costs a comment; the
same bug caught in production costs a day.

## Two harnesses, as peers

Claude and Codex both run this stack. Neither is the primary. Each has its own
entry document, its own six subagents, its own folder structure, sitting side by
side under `config/`.

The tempting move is to merge the two role definitions and generate both from
one source. Don't. The harnesses differ exactly where it matters: Codex enforces
read-only with a real sandbox, Claude only withholds tools; Codex has a
default-subagent-model fallback, Claude silently inherits the session model. A
merged definition has to encode both, and the merge point is where the mistakes
live. Two honest trees plus one document naming every asymmetry is smaller and
truer than one clever abstraction.

That is the same instinct as the rest of the stack: prefer the boring thing that
can't quietly lie to you.

## When to go lightweight

Single-session spikes, trivial fixes, and exploratory work don't need the
pipeline. Keep the rails — they cost nothing once installed — and drop the
stages you don't need. Even for a quick fix, a tracked issue beats a stray TODO
comment.

## How to customize

Delete what you don't use. Add what you do. Add project-specific rails where it
makes sense — a compliance rail for HIPAA work, a localization rail for
multi-language apps.

But hold the line on the addition test. A stack grows back into a junk drawer
one reasonable-seeming tool at a time.

## The meta point

This stack was restructured using itself: the overhaul was charted as a
`/wayfinder` map on this repo's own issue tracker, each decision resolved as a
ticket and recorded where the next session could find it. The map is public. Eat
your own dog food.
