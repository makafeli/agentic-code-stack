# Rail 02 — Token & Output Efficiency

**Always-on. Use during long sessions, agent-to-agent context handoffs, and any
time the context window is getting tight.**

Verbose output costs money and crowds the context. This rail trims it without
losing technical content. Constraining models to brief responses can *improve*
accuracy on some benchmarks — verbose ≠ better.

Two sides, and they are different problems:

- **Caveman** trims the *prose* — what the agent says.
- **Ponytail** trims the *code* — what the agent builds.

Plus the input side: retrieve symbols, not files.

## Tools

### Caveman — `mattpocock/skills`

Compresses explanation text by roughly 65–75%: drops filler, articles, and
pleasantries while keeping full technical accuracy.

| Command | Effect |
|---|---|
| `/caveman lite` | Light trim |
| `/caveman full` | Default |
| `/caveman ultra` | Maximum compression |
| `/caveman-stats` | Report savings |

**Code and commit messages stay normal.** Compression applies to prose only —
a caveman-compressed commit message is a bad commit message.

Triggers: "caveman mode", "less tokens", "be brief".

### Ponytail — `DietrichGebert/ponytail`

Caveman's code-side counterpart. A lazy senior developer: the best code is the
code never written. Roughly 80% less code on a typical task.

| Command | Effect |
|---|---|
| `/ponytail lite` \| `full` \| `ultra` | Set the intensity (`full` default) |
| `/ponytail-review` | Review a diff for over-engineering |
| `/ponytail-audit` | Whole-repo sweep, ranked deletion plan |

The ladder — stop at the first rung that holds:

1. Does this need to exist at all? (YAGNI)
2. Does the stdlib do it?
3. Does a native platform feature cover it?
4. Does an already-installed dependency solve it?
5. Can it be one line?
6. Only then: the minimum code that works.

**Never simplify away:** input validation at trust boundaries, error handling
that prevents data loss, security controls, accessibility basics, or anything
explicitly requested.

Deliberate simplifications get a `ponytail:` comment naming the ceiling and the
upgrade path — `# ponytail: global lock, per-account locks if throughput matters`
— so a shortcut reads as intent rather than ignorance.

### codebase-memory-mcp — the input side

The biggest single lever, and it works on the half Caveman and Ponytail don't
touch: what goes *into* context.

A whole-file read costs the whole file. `get_code_snippet` on a qualified name
costs the symbol. On a large repo that is the difference between a session that
fits and one that compacts halfway through.

See [rail 01](01-docs-context.md) for the full tool set.

### RTK — Rust Token Killer

A hook-rewritten CLI proxy. `git status` transparently becomes `rtk git status`,
trimming 60–90% of the tokens on dev command output. Zero overhead to invoke —
the hook does the rewriting.

`rtk gain` reports savings; `rtk discover` finds missed opportunities.

## Decision matrix

| Problem | Tool |
|---|---|
| Agent explains too much | `/caveman` |
| Agent builds too much | `/ponytail` |
| Reading whole files to answer a code question | codebase-memory-mcp |
| Shell command output flooding context | RTK (automatic) |
| "Is this over-built?" on a diff | `/ponytail-review` |
| Repo has accreted layers over time | `/ponytail-audit` |

## Anti-patterns

- ❌ Compressing code or commit messages with Caveman.
- ❌ Using Ponytail to justify skipping validation, error handling, or security.
- ❌ Reading whole files when a symbol query answers the question.
- ❌ Writing paragraphs defending a simplification. If the explanation is longer
  than the code, delete the explanation.
