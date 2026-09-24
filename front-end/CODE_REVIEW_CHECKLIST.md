## Front-End Code Review Checklist

- [ ] The change implements the ticket scope and meets every acceptance criterion. Empty, loading, error, and invalid-input states are handled. - P0
- [ ] TypeScript is strict: no `any`. Type assertions, non-null assertions, and suppressions (`@ts-ignore`, `@ts-nocheck`) are local, justified, and unavoidable. Untrusted data is validated at the boundary. - P0
- [ ] Types make invalid states hard to represent. Discriminated unions are used where variants differ, closed sets are narrow, and important unions are handled exhaustively. - P0
- [ ] JavaScript follows the ES6+ standards: `const` by default, no `var`, no accidental globals, and comparisons use `===` / `!==`. Optional chaining does not hide required data. - P0
- [ ] Collections and objects communicate intent: array methods match the operation, `map` is used for transformations, plain-data updates avoid surprising mutation, and external contract field names stay intact. - P0
- [ ] Async work is deliberate: promises are awaited or intentionally handled, independent calls are not serialised, and errors are handled at a meaningful boundary. - P0
- [ ] React components have one UI responsibility. Rendering is separated from data access. API DTOs are mapped before they reach UI components. - P0
- [ ] State lives at the lowest useful level. Loading, empty, error, and success states are explicit. Effects are used for synchronisation, not event logic. Custom hooks return a deliberate contract. - P0
- [ ] Component contracts are clear: callback props use `onX`, internal handlers use `handleX`, props and state are typed, DOM mechanics are not leaked, and list keys are stable. - P0
- [ ] ESLint and the project formatter pass. The production build completes without warnings or errors. New runtime APIs are supported by the project target. - P0
- [ ] Unit tests cover happy path, edge cases, and user-visible behaviour. Queries prefer role/label/text over implementation details. Coverage meets the project threshold and has not decreased. All tests pass locally and on CI/CD. - P0
- [ ] Accessibility is preserved: semantic HTML, labelled controls, and keyboard interaction. Error boundaries are reserved for unexpected render failures. - P0
- [ ] Performance is satisfactory. Memoisation (`React.memo`, `useMemo`, `useCallback`) is used only where justified. There is no unnecessary work on the main thread. - P0
- [ ] No security flaws were introduced: no untrusted HTML, no secrets in client code, and no `eval` / `new Function`. - P0
- [ ] The PR is reviewable: a screenshot is attached for UI changes, a short video for flow changes, and any cleanup stays within the ticket. - P0
