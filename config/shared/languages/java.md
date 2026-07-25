# Java / Spring Boot Profile

| Aspect | Tool |
|---|---|
| **Reviewer** | `java-reviewer` |
| **Test / TDD** | JUnit; `springboot-tdd`; `/tdd` |
| **Build-error resolver** | `java-build-resolver`, `gradle-build` |
| **UI / design** | — (backend) |
| **Rules / gotchas** | `springboot-patterns`, `springboot-security`, `springboot-verification`, `jpa-patterns` |

## Notes

- Layered architecture: controller → service → repository; keep controllers thin.
- JPA: watch N+1 queries; use fetch joins / batching — review with `jpa-patterns`.
- Validate at boundaries; never trust external input (`springboot-security`).
