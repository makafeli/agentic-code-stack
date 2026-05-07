# Stage 5 — Build

**Goal:** Implement the spec. Discipline > velocity. TDD always.

## Tools

### Superpowers — `obra/superpowers`

Once you have a plan, Superpowers enforces a disciplined implementation loop.

**Slash commands:**
| Command | Purpose |
|---|---|
| `/superpowers:execute-plan` | Run the plan from Stage 3 with review checkpoints |
| `/superpowers:test-driven-development` | RED → GREEN → REFACTOR cycle. Tests must fail first. |
| `/superpowers:requesting-code-review` | Spec-compliance + code-quality review. Critical issues block progress. |
| `/superpowers:subagent-driven-development` | Parallel agents on independent tasks; two-stage review |
| `/superpowers:debugging` | Four-phase methodology — root cause investigation before any fix. After 3 failed attempts, triggers architectural review. |
| `/superpowers:finishing-a-development-branch` | Verify tests pass, then offer merge / PR / keep working / discard |
| `/superpowers:writing-skills` | Author new skills using TDD principles applied to documentation |

**TDD enforcement is real:** if you try to write code before a failing test, the skill will literally make you delete it and start over. Not a suggestion.

### Apple-platform skills — `twostraws`

If the project is iOS, macOS, watchOS, tvOS, or visionOS, install the relevant skills. They target the *actual* mistakes LLMs make in Swift/SwiftUI — not the basics.

**Slash commands (Claude Code):**
| Command | Purpose |
|---|---|
| `/swiftui-pro` | Modern SwiftUI review — deprecated APIs, `+` text concatenation, `ObservableObject` misuse, accessibility, performance |
| `/swiftdata-pro` | SwiftData modern API + dangerous predicates that compile cleanly but crash |
| `/swift-concurrency-pro` | async/await, actors, structured concurrency, task groups |
| `/swift-testing-pro` | Modern Swift Testing — parameterised tests, exit tests |

**Codex equivalents:** `$swiftui-pro`, `$swiftdata-pro`, etc.

**Natural-language triggers also work:** "Use the SwiftUI Pro skill to look for performance problems in this project."

**Partial reviews:**
- `/swiftui-pro Check for deprecated API`
- `/swiftui-pro Focus on accessibility`
- `/swiftdata-pro Check where indexes should be added to my models`

## Agent rules for this stage

1. **Before writing code:** confirm a plan exists (Stage 3 output). If not, go back.
2. **TDD always:** failing test first, minimal code to pass, then refactor. No "I'll add tests later".
3. **Use the rails:**
   - Pull docs through Context7 / Ref MCP — don't write from training memory.
   - Navigate code through Pitlane MCP, not whole-file reads.
   - Track work in beads as you go.
4. **Commit discipline:** after every green test or meaningful refactor.
5. **Block on critical review issues** — don't bargain past them.
6. **When stuck:** `/superpowers:debugging`. Don't fire more random fixes.
7. **When done:** `/superpowers:finishing-a-development-branch` and `bd close <id>`.

## Anti-patterns

- ❌ Writing implementation before tests.
- ❌ "Quick fix" without root cause investigation.
- ❌ Reading entire files to find one symbol (use Pitlane).
- ❌ Inventing API signatures (use Context7 or Ref).
- ❌ Stopping the review loop because critical issues are "minor".
- ❌ Forgetting to close beads issues. The graph fills with zombie work.
