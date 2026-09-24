# ECM Backend Standards

**Parent:** [ECM Core Software Development Standards](../core/STANDARDS.md)  
**Applies to:** New C# / .NET and Java backend code and code that is materially modified

---

## Table of Contents

- [Clean Architecture in Practice](#clean-architecture-in-practice)
- [C# / .NET Best Practices](#c--net-best-practices)
- [Java Best Practices](#java-best-practices)
- [SOLID and Patterns](#solid-and-patterns)
- [Security](#security)
- [Performance and Reliability](#performance-and-reliability)

---

## Clean Architecture in Practice

- **Domain:** entities/value objects, domain services, invariants (no framework dependencies).
- **Application:** use cases, commands/queries, validation, transactions, orchestration.
- **Infrastructure:** persistence, external services, message bus, file storage.
- **API/UI:** controllers, DTO mapping, auth, rate limiting, request logging.
- Only the outer layers reference the inner layers; inner layers never reference infrastructure.

### Suggested folder layout

```text
/src
  /Domain
  /Application
  /Infrastructure
  /Api
```

---

## C# / .NET Best Practices

- Enable nullable reference types. Treat warnings as errors in CI.
- Use `async`/`await` end-to-end; avoid blocking calls (`.Result`, `.Wait()`).
- Prefer dependency injection, logging abstractions, and configuration via the options pattern.
- Use structured logging (with correlation IDs). Don’t log secrets or PII.
- Use validation at boundaries (FluentValidation or equivalent) and return consistent error contracts.

### Example

✔ Good

```csharp
public async Task<UserDto> GetUserAsync(Guid id, CancellationToken ct)
{
    var user = await _repo.GetByIdAsync(id, ct)
        ?? throw new NotFoundException($"User {id} not found");

    return _mapper.Map<UserDto>(user);
}
```

Why:

- Async all the way reduces thread starvation.
- Clear error semantics and mapping boundaries.

❌ Avoid

```csharp
public UserDto GetUser(Guid id)
{
    // Blocks thread pool + hides latency + can deadlock in some contexts
    var user = _repo.GetByIdAsync(id).Result;
    if (user == null) return null;
    return new UserDto { Name = user.Name };
}
```

Why not:

- Blocking async can deadlock and hurts scalability.
- Returning null increases NRE risk and forces callers to guess.

---

## Java Best Practices

- Prefer dependency injection frameworks consistently (e.g., Spring). Avoid manual service locators.
- Use immutable DTOs where practical; validate inputs with Bean Validation (Jakarta Validation).
- Use SLF4J with parameterized logging; never concatenate strings in log calls for dynamic values.
- Use checked exceptions sparingly; map exceptions to API errors at the boundary.
- Prefer composition; keep classes small and cohesive.

### Example

✔ Good

```java
// SLF4J parameterized logging
log.info("User {} logged in from {}", userId, ipAddress);
```

Why:

- More efficient and consistent logging; avoids unnecessary string building.

❌ Avoid

```java
// Avoid: string concatenation executes even if level disabled
log.info("User " + userId + " logged in from " + ipAddress);
```

Why not:

- Wastes CPU; makes log parsing harder.

---

## SOLID and Patterns

- Use interfaces at boundaries (repositories, gateways) to support testing and replace implementations.
- Prefer command/query separation (CQRS) for complex domains: reads optimized for queries, writes for invariants.
- Use unit tests for domain/application logic; integration tests for infrastructure; contract tests for APIs.

---

## Security

- Authenticate and authorize every request; enforce authorization in the application layer, not only in controllers.
- Use least-privilege service accounts. Separate read/write DB users where feasible.
- Never trust client IDs/roles. Always derive from the access token/session.
- Protect secrets using a secret manager or environment variables; never commit secrets to git.
- Use rate limiting, input validation, and safe error messages (no stack traces in production responses).

---

## Performance and Reliability

- Use connection pooling (default in most drivers) and keep connections short-lived.
- Avoid N+1 queries; use batching and proper joins; use pagination for list endpoints.
- Cache carefully: start with in-memory/response caching for read-heavy data; invalidate explicitly.
- Use timeouts and retries only with idempotent operations; add circuit breakers for remote calls.

Measure: add metrics (latency, error rates), tracing, and logs with correlation IDs.
