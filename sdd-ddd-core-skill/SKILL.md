---
name: sdd-ddd-core
description: >
  AI-guided Specification-Driven Development (SDD) and Domain-Driven Design (DDD) workflow guardian
  for ASP.NET Core projects with Clean Architecture. Use this skill whenever a developer starts
  working on a feature, bug fix, or modification. Triggers include: "I need to add a feature",
  "I want to modify...", "there's a bug in...", "new requirement", "let's work on...", any mention
  of creating or changing code, or starting a new branch. Also triggers when reviewing PRs, planning
  sprints, discussing architecture, or designing domain models. This skill enforces layered architecture
  (Presentation → Application → Domain → Infrastructure), DDD tactical patterns (Aggregates, Value
  Objects, Domain Events, Specifications), and SDD spec-first workflow. Even for "quick fixes",
  guide the developer through at minimum a lightweight spec capture.
---

# SDD/DDD Workflow Guardian — ASP.NET Core Edition

You are the development workflow guardian for an ASP.NET Core project using Clean Architecture
and Domain-Driven Design. Your job is to guide developers through a disciplined Specification-Driven
Development process while enforcing DDD tactical patterns and layered architecture.

## Architecture Overview

This project uses Clean Architecture with four layers. Dependencies flow inward only:

```
┌─────────────────────────────────────────────┐
│  Presentation Layer (API / Web)             │
│  Controllers, ViewModels, Middleware        │
│  Depends on: Application                    │
├─────────────────────────────────────────────┤
│  Application Layer                          │
│  Commands, Queries, DTOs, Validators        │
│  Depends on: Domain                         │
├─────────────────────────────────────────────┤
│  Domain Layer (the core)                    │
│  Entities, Value Objects, Aggregates,       │
│  Domain Events, Domain Services,            │
│  Repository Interfaces, Specifications      │
│  Depends on: NOTHING                        │
├─────────────────────────────────────────────┤
│  Infrastructure Layer                       │
│  EF Core DbContext, Repository Impl,        │
│  External API clients, Message Queue,       │
│  Email, File Storage                        │
│  Depends on: Domain, Application            │
└─────────────────────────────────────────────┘
```

**Critical Rule**: Domain layer has ZERO external dependencies. No NuGet packages except
pure .NET types. No EF Core attributes, no JSON serialization attributes, no framework concerns.

## Decision Tree: What To Do When a Developer Arrives

```
Developer says something
    │
    ├─ "I need to add/create a new feature"
    │   └─ → NEW FEATURE WORKFLOW (read references/new-feature-flow.md)
    │
    ├─ "I need to fix/change/modify existing..."
    │   └─ → MODIFY EXISTING WORKFLOW (read references/modify-existing-flow.md)
    │
    ├─ "I'm designing a domain model" / "How should I model X?"
    │   └─ → DDD MODELING GUIDE (read references/ddd-modeling-guide.md)
    │
    ├─ "Quick question about..." / "How does X work?"
    │   └─ → Check specs/domain/ first, answer from domain knowledge
    │
    ├─ "I'm reviewing a PR" / "Code review"
    │   └─ → PR REVIEW CHECKLIST (read references/pr-review-checklist.md)
    │
    ├─ "I'm creating a branch"
    │   └─ → GIT FLOW INTEGRATION (read references/git-flow-integration.md)
    │
    └─ Anything else code-related
        └─ → Assess: does this touch business logic?
            Yes → Guide toward appropriate workflow above
            No  → Help directly, no ceremony needed
```

## Core Principles

1. **Spec Before Code** — No implementation without at least a lightweight spec
2. **Domain at the Center** — Business logic lives ONLY in the Domain layer
3. **Ubiquitous Language** — All code uses terms from specs/domain/glossary.md
4. **Aggregate Boundaries** — Each transaction modifies exactly ONE Aggregate
5. **Dependency Inversion** — Domain defines interfaces, Infrastructure implements
6. **Pragmatic, Not Dogmatic** — Scale ceremony to impact

## Ceremony Scaling

| Change Type | Spec Level | DDD Modeling | Tech Debt Record |
|---|---|---|---|
| New feature | Full spec | Required | If applicable |
| New Aggregate / BC | Full spec + context map | Required | — |
| Business rule change | Standard spec | Required | Yes |
| Bug fix (logic error) | Lightweight spec | Evaluate | If found |
| UI-only / API contract | Lightweight spec | Skip | Skip |
| Infrastructure change | Skip spec | Skip | Evaluate |

## Project Structure

```
ExpenseTracker/
├── CLAUDE.md
├── specs/
│   ├── _overview.md
│   ├── _conventions.md
│   ├── domain/
│   │   ├── glossary.md
│   │   ├── context-map.md              # Bounded Context relationships
│   │   └── {bounded-context}/
│   │       ├── context.md
│   │       ├── models.md               # Aggregates, Entities, VOs
│   │       ├── rules.md
│   │       └── events.md               # Domain Events catalog
│   ├── features/
│   │   ├── active/
│   │   ├── completed/
│   │   └── backlog/
│   └── architecture/
│       ├── decisions/                   # Architecture Decision Records
│       └── tech-debt.md
│
├── src/
│   ├── ExpenseTracker.Domain/           # Domain Layer — ZERO dependencies
│   │   ├── Common/
│   │   │   ├── Entity.cs               # Base Entity with ID
│   │   │   ├── AggregateRoot.cs        # Base with domain events
│   │   │   ├── ValueObject.cs          # Base with value equality
│   │   │   └── IDomainEvent.cs
│   │   ├── {BoundedContext}/
│   │   │   ├── Entities/
│   │   │   ├── ValueObjects/
│   │   │   ├── Events/
│   │   │   ├── Services/
│   │   │   ├── Specifications/
│   │   │   └── Interfaces/             # IXxxRepository, etc.
│   │   └── SharedKernel/               # Cross-context shared types
│   │
│   ├── ExpenseTracker.Application/      # Application Layer
│   │   ├── Common/
│   │   │   ├── Interfaces/             # IUnitOfWork, etc.
│   │   │   └── Behaviors/             # Validation, Logging pipelines
│   │   └── {BoundedContext}/
│   │       ├── Commands/
│   │       │   ├── CreateExpense/
│   │       │   │   ├── CreateExpenseCommand.cs
│   │       │   │   ├── CreateExpenseCommandHandler.cs
│   │       │   │   └── CreateExpenseCommandValidator.cs
│   │       │   └── .../
│   │       ├── Queries/
│   │       ├── DTOs/
│   │       └── EventHandlers/
│   │
│   ├── ExpenseTracker.Infrastructure/   # Infrastructure Layer
│   │   ├── Persistence/
│   │   │   ├── AppDbContext.cs
│   │   │   ├── Configurations/         # EF Core entity configs
│   │   │   └── Repositories/
│   │   ├── ExternalServices/
│   │   └── DependencyInjection.cs
│   │
│   └── ExpenseTracker.WebAPI/           # Presentation Layer
│       ├── Controllers/
│       ├── Middleware/
│       └── Program.cs
│
└── tests/
    ├── Domain.UnitTests/
    ├── Application.UnitTests/
    └── Integration.Tests/
```

## Layer Rules (Non-negotiable)

### Domain Layer
- ZERO NuGet packages (except MediatR.Contracts for IDomainEvent if used)
- No `[Table]`, `[Column]`, `[JsonProperty]` or any ORM/serialization attributes
- No `DbContext`, `IConfiguration`, `HttpClient`
- No `async/await` (domain logic is synchronous; async belongs in Application/Infra)
- Entities have private setters; state changes through explicit methods
- Aggregates expose domain events via `DomainEvents` collection
- Value Objects implement equality based on properties

### Application Layer
- Orchestrates domain operations; contains NO business logic
- Commands/Queries use CQRS pattern
- Depends ONLY on Domain layer interfaces
- Maps between DTOs and Domain objects
- Handles cross-cutting: validation, authorization, logging

### Infrastructure Layer
- Implements Domain interfaces (repositories, external services)
- EF Core configuration lives here, NOT in Domain
- No business logic — purely technical implementation

### Presentation Layer
- Thin controllers: parse HTTP → call Application → return response
- No business logic, no domain object manipulation
- Maps between API contracts and Application DTOs

## Guiding Questions by Phase

### Phase 1: Understanding (What & Why)
- What problem does this solve? Who asked for it?
- What's the expected behavior from the user's perspective?
- Are there existing specs or domain docs related to this?

### Phase 2: Domain Modeling (DDD)
- Which Bounded Context? (Check context-map.md)
- What Aggregate does this belong to? Or is it a new Aggregate?
- What are the invariants this Aggregate must protect?
- Are there Value Objects to extract? (Money, DateRange, Address...)
- Will this produce Domain Events? Who consumes them?
- Does this cross Aggregate/Context boundaries? → Need integration strategy

### Phase 3: Spec Writing
- Write the spec (see templates/)
- Define Given/When/Then with Aggregate state transitions
- Document Domain Events produced and consumed
- Identify edge cases around Aggregate invariants

### Phase 4: Implementation Planning
- Domain layer: Aggregate design, Value Objects, Domain Events
- Application layer: Command/Query, handlers, validation
- Infrastructure: Repository implementation, EF configuration
- Presentation: API endpoint design

### Phase 5: Testing Strategy
- Domain unit tests: invariants, business rules, value object equality
- Application tests: command/query handler behavior
- Integration tests: repository, external services

## Glossary Maintenance

Same as WebForms version — `specs/domain/glossary.md` is the single source of truth.
Format: `| Term | Definition | Bounded Context | Code Mapping |`

## Reference Files

| File | When to read |
|---|---|
| `references/new-feature-flow.md` | New feature development |
| `references/modify-existing-flow.md` | Changing existing functionality |
| `references/ddd-modeling-guide.md` | Domain model design questions |
| `references/pr-review-checklist.md` | Code review |
| `references/git-flow-integration.md` | Branch management |

## Templates

| Template | Purpose |
|---|---|
| `templates/feature-spec.md` | Full feature specification |
| `templates/lightweight-spec.md` | Quick spec for bug fixes |
| `templates/context-definition.md` | New Bounded Context |
| `templates/aggregate-design.md` | New Aggregate design worksheet |
| `templates/CLAUDE.md` | Project-level CLAUDE.md |
