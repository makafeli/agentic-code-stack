# Philosophy

## The problem

AI coding agents are powerful but unstructured by default. Without a shared mental model, a session can degrade quickly: the agent reads whole files instead of navigating by symbol, jumps to code on a vague one-liner, or thrashes between tools without knowing what belongs where. The human does not know what to ask for. The agent does not know what tools it can reach for.

## The model: Pipeline + Rails

The stack organizes agent work into two categories:

**Pipeline** -- sequential stages the agent moves through: Align (discovery before code), Spec and Plan (write it down), Design (when UI is involved), Build (implement with reviews). Pipeline stages give the agent *stage awareness*: it knows where it is in the process and what the current job is.

**Rails** -- always-on capabilities that run alongside every pipeline stage: documentation lookups (Context7, Ref MCP), token efficiency (Caveman, graphify), memory and task tracking (Beads), external data access (Bright Data CLI), and agent interfaces (Printing Press). Rails give the agent *tool awareness*: instead of reaching for raw grep or reading whole files, it picks the purpose-built tool.

Pipeline answers "what phase am I in?" Rails answer "what should I use to do it?"

## When to use the full stack

Multi-session features, team environments, complex codebases, and anything with a UI surface benefit from the full pipeline-plus-rails model. The overhead of alignment and planning pays off when the work spans more than a single sitting.

## When to go lightweight

Single-session spikes, trivial bug fixes, and exploratory work do not need the full pipeline. Keep the rails active (they cost almost nothing once installed) and drop the pipeline stages you do not need. Even for a quick fix, `bd create` beats a stray TODO comment.

## How to customize

Delete tools you do not use. Add tools you do. The stack is a starting point, not a cage. Add project-specific rails where it makes sense -- a compliance rail for HIPAA projects, a localization rail for multi-language apps, a performance rail for latency-sensitive work.

## Portable core + personal overlay

The pipeline and rails are the **portable core** -- anyone can install them. The `overlay/` directory is a **personal layer**: a private skill suite (gstack), the maintainer's Claude harness, and per-language profiles. The rule is simple -- public tools are the default; the overlay names the personal equivalent for each stage. Delete `overlay/` and the core still stands.

## The meta point

This stack was built using the stack. The design was aligned with `/superpowers:brainstorming`, the plan was written with `/superpowers:write-plan`, the implementation ran through `/superpowers:subagent-driven-development`, and the work is tracked in Beads. Eat your own dog food.
