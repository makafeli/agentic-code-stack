# Config

The harness layer: how the agents themselves are wired, as opposed to what work
they do (that is `pipeline/` and `rails/`).

Two harnesses, side by side as **peers**. Codex reads `AGENTS.md`, Claude reads
`CLAUDE.md`, and each owns its own subagents, rules, and settings. Neither loads
the other. Neither is canonical over the other.

```text
config/
├── claude/    CLAUDE.md · agents/ · rules/ · hooks/ · settings.template.json
├── codex/     AGENTS.md · agents/ · rules/ · config.template.toml
└── shared/    what both harnesses agree on
```

## Why two trees instead of one

The six roles are identical in intent and deliberately mirrored, so the obvious
move is to merge them into one definition with two renderers. Don't. The two
harnesses differ in ways that matter at exactly the point where a merged
definition would paper over them:

- Codex enforces read-only with a real `sandbox_mode`. Claude has only
  `disallowedTools`, which is an instruction to the agent, not a sandbox.
- Codex has `default_subagent_model` and a per-session concurrency cap. Claude
  has neither — every Claude dispatch must pin `model:` explicitly or it
  silently inherits the session model.

A merged definition would have to encode both, and the merge point is where the
mistakes happen. Two honest trees plus [`shared/role-parity.md`](shared/role-parity.md)
— which states the mapping and names every place it breaks down — is the smaller,
truer thing.

## What is a template and what is not

Anything ending `.template.*` is sanitized: `$HOME` for paths, placeholders for
credentials, comments where a value is machine-specific. Copy it, fill it in,
and keep your filled-in copy out of git.

The agent definitions, the rules, and the manifests are **not** templates. They
are the real files, and they are meant to be copied verbatim.

## Installing

See [INSTALL.md](../INSTALL.md). There are no install scripts here on purpose —
a script that provisions a machine nobody else has is a thing that rots
silently. The commands are short enough to read before you run them.
