## Code Review Checklist

- [ ] The change implements the ticket scope and meets every acceptance criterion. Nothing out of scope has been added. - P0
- [ ] Edge cases and error paths are handled (invalid input, empty results, failure states). Those paths are covered by tests or demonstrated on the PR. - P0
- [ ] Names follow the core conventions and reveal intent: booleans read as questions (`is` / `has` / `can`), functions start with a verb, collections are plural, units appear in numeric names. - P0
- [ ] Functions have one clear responsibility, stay at one level of abstraction, avoid unexpected side effects, and do not use boolean flags to select behaviour. - P0
- [ ] Control flow is easy to follow: conditions are intention-revealing, guard clauses keep the happy path clear, and clever or deeply nested branching is avoided. - P0
- [ ] The code reads top to bottom. Comments explain why, not what. Commented-out code is removed. Business-significant magic values have meaningful names. - P0
- [ ] Shared business rules are not duplicated. New abstractions represent one concept, not two blocks that merely look similar. - P0
- [ ] Formatting is consistent. There are no lint errors. The change builds without warnings or errors. - P0
- [ ] Architecture is respected: SOLID and Clean Architecture apply, dependencies flow inward, and domain/application code does not leak infrastructure or UI concerns. - P0
- [ ] Errors are handled, not ignored. Messages are meaningful. Exceptions are not used for normal control flow. User-facing errors do not expose internals or secrets. - P0
- [ ] No security flaws were introduced: authorization is enforced, inputs are validated at boundaries, and secrets or PII are not committed, logged, or returned in responses. - P0
- [ ] Performance is satisfactory: no unnecessary N+1 work, list endpoints/views are paginated where needed, payloads and renders stay small, and complexity is justified. - P0
- [ ] Unit tests cover domain/application logic. Integration tests cover I/O. Assertions check behaviour, not implementation details. All tests pass locally and on CI/CD. - P0
- [ ] Observability is in place where the change needs it: structured logs, correlation IDs, and useful metrics or traces. Logs do not contain secrets or PII. - P0
- [ ] The PR is reviewable: proof is attached (screenshot for UI, short video for flow changes), accessibility is preserved on UI changes, and any cleanup follows the Boy Scout Rule without expanding the ticket. - P0
