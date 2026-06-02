# React / Web Profile

| Aspect | Tool |
|---|---|
| **Reviewer** | `typescript-reviewer` |
| **Test / TDD** | Vitest or Jest + Testing Library; Playwright for E2E; `/tdd` |
| **Build-error resolver** | `build-error-resolver` |
| **UI / design** | `frontend-design`, shadcn MCP, `ui-ux-pro-max`; Get Design tokens |
| **Rules / gotchas** | `~/.claude/rules/web/*` (coding-style, performance, security, patterns, design-quality) |

## Notes

- Immutable updates only; never mutate in place.
- Animate compositor-friendly properties (transform/opacity), not layout-bound ones.
- Server state (TanStack Query/SWR) ≠ client state (Zustand/Jotai) ≠ URL state — don't duplicate.
- Anti-template policy: avoid default card grids and stock hero sections.
