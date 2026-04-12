# PR Review Checklist — ASP.NET Core

## Spec Compliance

- [ ] Spec exists for this change
- [ ] Implementation matches Given/When/Then scenarios
- [ ] All business rules (BR-*) implemented
- [ ] Edge cases (EC-*) handled

## Domain Layer Quality

- [ ] **Zero external dependencies** — check .csproj, no NuGet beyond base .NET
- [ ] **No ORM attributes** — no [Table], [Column], [Key] on domain classes
- [ ] **No serialization attributes** — no [JsonProperty], [JsonIgnore]
- [ ] **Private setters** — state changes through methods only
- [ ] **Invariants enforced** — constructor and methods reject invalid state
- [ ] **Value Objects immutable** — using `record` or readonly properties
- [ ] **Domain Events raised** — significant state changes produce events
- [ ] **Other Aggregates referenced by ID** — not by direct object reference

## Application Layer Quality

- [ ] **No business logic** — handlers only orchestrate, not decide
- [ ] **CQRS respected** — Commands for writes, Queries for reads
- [ ] **Validation in Validator** — not in handler or controller
- [ ] **No Domain objects in DTOs** — proper mapping between layers
- [ ] **Event handlers are idempotent** — safe to replay

## Infrastructure Layer Quality

- [ ] **EF config in Fluent API** — not attributes on Domain entities
- [ ] **Repository only for Aggregate Roots** — not for child entities
- [ ] **No business logic in SQL/LINQ** — complex filtering via Specifications
- [ ] **External service behind interface** — mockable for tests

## Presentation Layer Quality

- [ ] **Thin controllers** — parse, dispatch, respond
- [ ] **No domain objects exposed** — only DTOs/ViewModels in API
- [ ] **Proper status codes** — 201 Created, 404 Not Found, 422 Unprocessable
- [ ] **No business logic** — not even validation beyond format checking

## Cross-Cutting

- [ ] **Glossary consistency** — new terms documented?
- [ ] **Context boundaries respected** — no reaching into another context's internals
- [ ] **Domain Events documented** — events.md updated?
- [ ] **Tests cover invariants** — not just happy path

## Architecture Score

- **A**: Clean layer separation, Domain-first design, full spec, comprehensive tests
- **B**: Mostly clean, minor layer bleed, spec exists, good test coverage
- **C**: Some business logic in wrong layer, spec exists
- **D**: Working code but architecture concerns, needs refactoring
- **F**: Business logic in controller/infrastructure, no spec — push back
