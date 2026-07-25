# Rail 05 — Agent Interfaces

**Engaged on demand. Give agents structured, compound-query access to external
services.**

## The problem

An agent talking to a REST API burns a turn per call. Ten calls to answer one
question is ten round-trips, ten chunks of JSON in context, and ten chances to
lose the thread. MCP servers help, but writing one per service is its own
project.

## Printing Press

Generates **agent-native Go CLIs** from API specs. One binary per service, one
command per compound question — so the agent asks once and gets the answer,
rather than assembling it from ten responses.

- Library of 70+ pre-built CLIs (Stripe, Notion, Slack, and others).
- Generate a new one from an OpenAPI spec.
- Output is designed for agent consumption: structured, terse, stable flags.

**Use it when** you're reaching for the same service repeatedly and each answer
costs several calls. **Skip it when** a dedicated MCP server already exists and
works — GitHub, Railway, Context7, and codebase-memory-mcp all do.

## Decision matrix

| Situation | Reach for |
|---|---|
| Service has a dedicated MCP server | That server |
| Service has a good official CLI | The CLI |
| Repeated multi-call questions, no MCP | Printing Press |
| One-off API call | Just call it |

## Anti-patterns

- ❌ Generating a CLI for a service you'll call twice.
- ❌ Building an interface layer over something that already has a working MCP
  server.
- ❌ Wrapping a CLI in another CLI.
