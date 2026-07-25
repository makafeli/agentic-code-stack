# Swift / Apple Profile

| Aspect | Tool |
|---|---|
| **Reviewer** | `swiftui-pro`, `swiftdata-pro`, `swift-concurrency-pro`, `swift-testing-pro` |
| **Test / TDD** | Swift Testing (`swift-testing-pro`); `/tdd` |
| **Build-error resolver** | xcode build flows (`ios-build`, `ios-fix`) |
| **UI / design** | `liquid-glass-design`, `ios-design-review`, `ios-simulator-skill` |
| **Rules / gotchas** | App Store: `asc-*` skills (metadata, screenshots, release) |

## Notes

- Modern APIs: `@Observable` over `ObservableObject`; avoid `+` Text concatenation.
- SwiftData predicates can compile cleanly but crash — review with `swiftdata-pro`.
- Structured concurrency: actors, task groups — review with `swift-concurrency-pro`.
- Partial reviews work: `/swiftui-pro Check for deprecated API`.
