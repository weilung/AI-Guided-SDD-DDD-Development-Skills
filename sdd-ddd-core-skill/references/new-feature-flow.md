# New Feature Workflow — ASP.NET Core

Step-by-step guide for adding a new feature with full DDD and Clean Architecture.

## Step 1: Intake — Understand the Request

Ask naturally:
1. **What's the feature?** Plain-language description.
2. **Who needs it?** Stakeholder or user role.
3. **Why now?** Priority and urgency.

Check existing assets:
- Search `specs/domain/` for related concepts
- Search `specs/features/` for related features
- Check `specs/domain/glossary.md` and `context-map.md`

## Step 2: Identify the Bounded Context

Check `specs/domain/context-map.md`:

```
"This feature involves [concepts]. It seems to belong in the
[Context] bounded context. Does that match your understanding?"
```

If new context needed → use context-definition template.

If it crosses contexts:
```
"This touches both [Context A] and [Context B]. We need to decide:
- Which context OWNS the operation?
- How does the other context get notified? (Domain Event? Query?)
- Do we need an Anti-Corruption Layer?"
```

## Step 3: Domain Modeling

This is where ASP.NET Core workflow diverges significantly from WebForms.
Read `references/ddd-modeling-guide.md` for detailed patterns.

Walk through:

### Aggregate Identification
```
"What must be consistent in a single transaction?
Those things form an Aggregate. Everything else is eventually consistent."
```

- What are the invariants?
- What is the Aggregate Root?
- What entities belong inside this Aggregate?
- What Value Objects can we extract?

### Domain Events
```
"After this happens, what else in the system needs to know?"
```

- What events does this feature produce?
- What events does it consume?
- Are handlers in the same context (synchronous) or cross-context (async)?

### Interface Definitions
```
"What external data does the domain need to make decisions?"
```

- Repository interfaces for persistence
- Service interfaces for external systems
- Define in Domain layer, implement in Infrastructure

## Step 4: Write the Spec

Create `specs/features/active/{ID}-{feature-name}.md` using feature-spec template.

Key additions compared to WebForms version:
- **Aggregate State Transitions**: Document how Aggregate state changes
- **Domain Events**: List events produced and expected handlers
- **CQRS Split**: Identify which parts are Commands (write) vs Queries (read)

### Behavior Specification
```gherkin
Scenario: Submit expense report
  Given an expense report in Draft status with 3 line items totaling 5,000 TWD
  When the employee submits the report
  Then the report status changes to Submitted
  And a ExpenseReportSubmitted event is raised
  And the report can no longer be modified
```

## Step 5: Plan the Implementation (Layer by Layer)

### Domain Layer (implement first)
```
1. Create/update Aggregate Root with state-changing methods
2. Create Value Objects with validation in constructors
3. Define Domain Events (record types)
4. Define Repository interface
5. Write Domain unit tests
```

### Application Layer (implement second)
```
1. Create Command + CommandHandler (for writes)
2. Create Query + QueryHandler (for reads)
3. Create CommandValidator (FluentValidation)
4. Create Domain Event handlers (if needed)
5. Define DTOs for input/output
```

### Infrastructure Layer (implement third)
```
1. EF Core entity configuration (Fluent API, NOT attributes)
2. Repository implementation
3. External service adapters
4. Migration script
```

### Presentation Layer (implement last)
```
1. API endpoint (Controller or Minimal API)
2. Request/Response models
3. Swagger documentation
```

## Step 6: Git Branch

```
Branch: feature/{SPEC-ID}-{short-description}
Example: feature/EXP-001-submit-expense-report
```

## Step 7: Implementation Checklist

During implementation, continuously verify:

**Domain Layer**
- [ ] Aggregate protects all invariants
- [ ] State changes only through methods (no public setters)
- [ ] Value Objects are immutable with validation
- [ ] Domain Events raised for significant state changes
- [ ] Zero external dependencies (check .csproj)
- [ ] Unit tests cover invariants and business rules

**Application Layer**
- [ ] No business logic in handlers (only orchestration)
- [ ] Command/Query separation maintained
- [ ] Validation in Validator, not Handler
- [ ] DTOs map to/from Domain objects (no Domain objects in API)

**Infrastructure Layer**
- [ ] EF Config in Fluent API (no attributes on Domain entities)
- [ ] Repository implements Domain interface correctly
- [ ] No business logic in queries

**Presentation Layer**
- [ ] Controller is thin (parse → dispatch → respond)
- [ ] No domain objects exposed to API consumers
- [ ] Proper HTTP status codes

## Step 8: Completion

1. Update spec status to `completed`
2. Move spec to `completed/`
3. Verify glossary, models, rules, events docs are updated
4. Verify context-map.md if cross-context interaction was added
5. Ensure test coverage for domain invariants
