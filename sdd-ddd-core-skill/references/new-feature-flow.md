# New Feature Workflow — ASP.NET Core

Step-by-step guide for adding a new feature with full DDD and Clean Architecture.

Triggered by `/dflow:new-feature` (or natural language implying a new-feature task — see SKILL.md § Workflow Transparency for the auto-trigger safety net behavior).

**Phase Gates** in this flow (stop-and-confirm before proceeding):
- Step 4 → Step 5 (spec written → plan implementation)
- Step 6 → Step 7 (branch ready → start implementation)
- Step 7 → Step 8 (implementation done → completion)

All other step transitions are **step-internal**: announce "Step N complete, entering Step N+1" and proceed without waiting. See SKILL.md § Workflow Transparency for the full transparency protocol and confirmation signals.

## Step 1: Intake — Understand the Request

Ask naturally:
1. **What's the feature?** Plain-language description.
2. **Who needs it?** Stakeholder or user role.
3. **Why now?** Priority and urgency.

Check existing assets:
- Search `specs/domain/` for related concepts
- Search `specs/features/` for related features
- Check `specs/domain/glossary.md` and `context-map.md`

**→ Transition (step-internal)**: Step 1 complete. Announce "Step 1 complete (intake). Entering Step 2: Identify the Bounded Context." and continue.

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

**→ Transition (step-internal)**: Step 2 complete. Announce "Step 2 complete (BC identified). Entering Step 3: Domain Modeling." and continue.

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

**→ Transition (step-internal)**: Step 3 complete. Announce "Step 3 complete (Aggregate / VO / Events identified). Entering Step 4: Write the Spec." and continue.

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

**→ Phase Gate: Step 4 → Step 5**

Announce to developer:
> "Spec is drafted — behavior scenarios, Aggregate state transitions, Domain Events, and CQRS split are captured. Ready to plan the layer-by-layer implementation (Domain → Application → Infrastructure → Presentation)? `/dflow:next` or reply 'OK' to continue, or tell me if the spec needs another iteration first."

Wait for confirmation (`/dflow:next`, verbal OK, or implicit — see SKILL.md § Confirmation Signals) before entering Step 5.

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

### Generate Implementation Tasks List

After the layer-by-layer plan is agreed, AI generates a concrete task list and writes it into the spec's `實作任務` section (see feature-spec template). Each task follows `[LAYER]-[NUMBER]：description` and maps to Clean Architecture layers:

- `DOMAIN` — Aggregate / Entity / VO / Domain Event / Domain Service / Repository Interface
- `APP`    — Command/Query / Handler / Validator / DTO / Event Handler
- `INFRA`  — EF Configuration / Repository Impl / external adapter / Migration
- `API`    — Controller / Minimal API / Request/Response / Swagger
- `TEST`   — Tests per layer

Recommended authoring order mirrors implementation order: `DOMAIN → APP → INFRA → API` (with `TEST` interleaved).

Example seed (replace with feature-specific tasks):

```markdown
- [ ] DOMAIN-1：ExpenseReport aggregate with Submit() state transition
- [ ] DOMAIN-2：ExpenseReportSubmitted domain event
- [ ] APP-1：SubmitExpenseReportCommand + Handler
- [ ] INFRA-1：EF config + ExpenseReportRepository
- [ ] API-1：POST /expense-reports/{id}/submit endpoint
- [ ] TEST-1：Aggregate invariants + handler unit tests
```

The list becomes the execution punch-list for Step 7 and the completion checklist in Step 8.

**→ Transition (step-internal)**: Step 5 complete. Announce "Step 5 complete (layer-by-layer plan + task list ready). Entering Step 6: Git Branch." and continue.

## Step 6: Git Branch

```
Branch: feature/{SPEC-ID}-{short-description}
Example: feature/EXP-001-submit-expense-report
```

**→ Phase Gate: Step 6 → Step 7**

Announce to developer:
> "Branch `feature/{SPEC-ID}-{description}` is created. Ready to start layer-by-layer implementation (Domain first)? `/dflow:next` to proceed, or discuss layer order / scope first."

Wait for confirmation before entering Step 7.

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

**→ Phase Gate: Step 7 → Step 8**

Announce to developer:
> "Implementation appears complete across all four layers. Ready to run the completion checklist (verify against spec, update domain docs + context-map, ensure test coverage, archive the spec)? `/dflow:next` to proceed."

Wait for confirmation before entering Step 8. This phase gate is where the completion checklist is triggered — do not skip.

## Step 8: Completion

Triggered by the Step 7 → Step 8 Phase Gate. AI runs the completion checklist in the order below; do **not** skip a section.

### 8.1 Verification — AI runs independently

AI reports `✓` / `✗` for every item before touching docs. Items marked *(post-8.3)* are re-verified after the documentation merge in 8.3 lands:

- [ ] Every `Given/When/Then` scenario in the spec is covered by implementation or tests
- [ ] Every `BR-*` business rule is covered by implementation or tests
- [ ] Every `EC-*` edge case is handled
- [ ] Every Domain Event listed in the spec is raised in the implementation
- [ ] Domain layer project has **no** external NuGet dependencies (check `*.Domain.csproj`)
- [ ] Aggregate invariants still hold after the change (all state changes go through methods, no public setters)
- [ ] EF Core configuration uses Fluent API only (no `[Table]`/`[Column]` on Domain entities)
- [ ] `實作任務` section: all tasks checked, or unchecked items explicitly labelled as follow-up (linked to spec / tech-debt entry)
- [ ] *(post-8.3)* `specs/domain/{context}/behavior.md` contains a section anchor for every `BR-*` introduced by this spec (mechanical input for `/dflow:verify`)
- [ ] *(post-8.3)* `specs/domain/{context}/behavior.md` `last-updated` is later than this spec's `created` date (mechanical drift guard)

If any item fails, report the gap and pause — don't proceed to 8.2.

### 8.2 Verification — needs developer confirmation

AI lists findings one at a time and waits for the developer to confirm each:

- [ ] Does the implementation faithfully express the **intent** of each BR? (AI lists BR → impl location; developer judges fit)
- [ ] Are the edge case handling decisions appropriate? (AI lists EC → handling; developer judges)
- [ ] Are Domain Event payloads and handler placements (same-context sync vs cross-context async) correct? (AI lists; developer confirms)
- [ ] Did we miss any tech debt worth recording?
- [ ] Do the scenarios merged into `behavior.md` (incl. Aggregate transitions + Events) faithfully express the intended behavior? (AI lists merged anchors; developer judges)
- [ ] Should the `實作任務` section in the spec be collapsed / removed now that it's complete? (team convention — developer decides)

Ask these one-by-one; do not dump all six at once.

### 8.3 Documentation updates

- [ ] `specs/domain/glossary.md` — new terms added
- [ ] `specs/domain/{context}/models.md` — model definitions updated
- [ ] `specs/domain/{context}/rules.md` — business rules updated
- [ ] `specs/domain/{context}/behavior.md` — merge completed spec's Given/When/Then scenarios (incl. Aggregate transitions + Events) into consolidated behavior. Sub-steps:
      - Promote any Phase 3 draft sections (from B3 mid-sync) to formal sections
      - Update the corresponding `rules.md` anchor's `last-updated` date (B4)
- [ ] `behavior.md` draft cleanup — if the spec was abandoned mid-way, keep the `## 提案中變更` section's history or explicitly REMOVE it
- [ ] `specs/domain/{context}/events.md` — Domain Events updated
- [ ] `specs/domain/context-map.md` — updated if cross-context interaction was added or changed
- [ ] `specs/architecture/tech-debt.md` — tech debt discovered during implementation recorded

### 8.4 Archival

- [ ] Spec `status` field changed to `completed`
- [ ] Spec file moved from `specs/features/active/` to `specs/features/completed/`

Only announce "feature complete" after 8.4 is done.
