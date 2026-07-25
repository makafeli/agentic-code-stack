# Svelte Profile

| Aspect | Tool |
|---|---|
| **Reviewer** | `typescript-reviewer` |
| **Test / TDD** | Vitest + Testing Library; Playwright for E2E; `/tdd` |
| **Build-error resolver** | `build-error-resolver` |
| **UI / design** | `frontend-design`; Get Design tokens |
| **Rules / gotchas** | `~/.claude/rules/web/*` |

## Notes

- Svelte 5 runes (`$state`, `$derived`, `$effect`) — keep reactivity explicit.
- Same web rules as React (immutability, compositor-friendly motion, anti-template).
- No dedicated Svelte reviewer — lean on `typescript-reviewer` + web rules.
