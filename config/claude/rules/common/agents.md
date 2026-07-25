# Agent Orchestration

## Role Team (primary)

The main session is the orchestrator: it owns requirements, design, architecture,
task decomposition, diagnosis, and final synthesis. Route delegated work by role
(all in `~/.claude/agents/`, model/effort pinned in frontmatter):

| Work | Agent | Model/effort | Mode |
|---|---|---|---|
| Code discovery, call paths, logs, evidence | `explorer` | sonnet/medium | no-write |
| Bounded implementation, tests, fixes, cleanup | `worker` | sonnet/high | write |
| Library, framework, API doc verification | `docs-researcher` | sonnet/medium | no-write |
| Correctness, security, regressions, conventions, test gaps | `standards-reviewer` | opus/max | no-write |
| Approved spec, ticket, acceptance-criteria compliance | `spec-reviewer` | opus/max | no-write |
| Over-engineering, duplication, dead paths, deletions | `simplification-reviewer` | opus/max | no-write |

Routing rules:

- Architecture and ambiguous decisions stay on the main thread — do not spawn an
  architect duplicate for decisions.
- One write-owning `worker` per file scope; never multiple writers on the same files.
- Run Standards and Spec review as separate fresh-context agents. Add
  Simplification review after correctness and spec compliance are established.
- No-write roles must not edit files even when the session's permission mode
  would allow it.
- The main thread waits for delegated results, resolves disagreements, and owns
  the final answer.

## Specialist Bench (secondary)

Topic-depth agents, dispatched when the domain matches (language reviewers and
analyzers = opus/max; build resolvers, tdd-guide, e2e-runner, etc. = sonnet;
doc-updater = haiku):

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| planner | Implementation planning docs | When a written plan artifact is needed |
| tdd-guide | Test-driven development | New features, bug fixes |
| security-reviewer | Security analysis | Security-sensitive diffs (with standards-reviewer) |
| go-reviewer / typescript-reviewer / rust-reviewer / … | Language-specific review depth | Language-heavy diffs |
| build-error-resolver (+ per-language resolvers) | Fix build errors | When build fails |
| e2e-runner | E2E testing | Critical user flows |
| refactor-cleaner | Dead code cleanup | Applying a simplification cut list |
| doc-updater | Documentation | Updating docs |

## Parallel Task Execution

ALWAYS use parallel Task execution for independent operations:

```markdown
# GOOD: Parallel execution
Launch 3 agents in parallel:
1. Agent 1: Security analysis of auth module
2. Agent 2: Performance review of cache system
3. Agent 3: Type checking of utilities

# BAD: Sequential when unnecessary
First agent 1, then agent 2, then agent 3
```

## Multi-Perspective Analysis

For complex problems, use split role sub-agents:
- Factual reviewer
- Senior engineer
- Security expert
- Consistency reviewer
- Redundancy checker
