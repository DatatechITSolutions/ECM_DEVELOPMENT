## Back-End Code Review Checklist

- [ ] The change implements the ticket scope and meets every acceptance criterion. Edge cases and error paths are handled with clear, consistent error contracts. - P0
- [ ] Clean Architecture is respected. Domain, Application, Infrastructure, and API stay in their layers. Only outer layers reference inner layers; inner layers never reference infrastructure. - P0
- [ ] Domain code owns entities, value objects, domain services, and invariants. It has no framework, persistence, or transport dependencies. - P0
- [ ] Application code owns use cases, commands/queries, validation, transactions, and orchestration. Controllers stay thin and map DTOs at the API boundary. - P0
- [ ] Dependencies are injected. Interfaces sit at boundaries (repositories, gateways) so implementations can be replaced and tested. Manual service locators are not introduced. - P0
- [ ] C# / .NET changes keep nullable reference types enabled, treat warnings as errors, and use `async`/`await` end-to-end with a `CancellationToken`. There are no blocking calls (`.Result`, `.Wait()`). - P0
- [ ] C# / .NET configuration uses the options pattern. Validation runs at boundaries (FluentValidation or equivalent). Callers are not left guessing from returned `null`. - P0
- [ ] Java changes use the project DI framework consistently, prefer immutable DTOs, validate inputs with Bean Validation, and keep classes small and cohesive. Composition is preferred over inheritance. - P0
- [ ] Java logging uses SLF4J parameterized messages. Exceptions are used sparingly and are mapped to API errors at the boundary, not used as ordinary control flow. - P0
- [ ] For complex domains, reads and writes are separated where it helps (CQRS): queries are optimized for reading, writes protect invariants. - P0
- [ ] Every request is authenticated and authorized in the application layer, not only in controllers. Client IDs and roles are derived from the access token or session, never trusted from the client. - P0
- [ ] Secrets stay in a secret manager or environment variables and are not committed. Service accounts follow least privilege. Production responses have no stack traces. Rate limiting and input validation are in place where required. - P0
- [ ] Performance is satisfactory: no N+1 queries, list endpoints are paginated, connections are pooled and short-lived, and caching is explicit with a clear invalidation path. - P0
- [ ] Remote calls have timeouts. Retries are used only for idempotent operations. Circuit breakers protect unstable dependencies. - P0
- [ ] Unit tests cover domain and application logic. Integration tests cover infrastructure. Contract tests cover APIs. Structured logs include correlation IDs and never contain secrets or PII. Metrics and traces are added where the change needs them. All tests pass locally and on CI/CD. - P0
