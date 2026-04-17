---
name: sdd-ddd-core
description: >
  AI-guided Specification-Driven Development (SDD) and Domain-Driven Design (DDD) workflow guardian
  for ASP.NET Core projects with Clean Architecture.
  PRIMARY TRIGGERS — /dflow: slash commands: /dflow:new-feature, /dflow:modify-existing,
  /dflow:bug-fix, /dflow:pr-review, /dflow:verify, /dflow:status, /dflow:next, /dflow:cancel.
  SECONDARY TRIGGERS (auto-trigger safety net) — natural language:
  "I need to add a feature", "I want to modify...", "there's a bug in...", "new requirement",
  "let's work on...", any mention of creating or changing code, or starting a new branch.
  Also triggers when reviewing PRs, planning sprints, discussing architecture, or designing domain models.
  When triggered by natural language, DO NOT auto-enter a workflow — judge the intent, suggest
  the matching /dflow: command, and wait for developer confirmation before proceeding.
  This skill enforces layered architecture (Presentation → Application → Domain → Infrastructure),
  DDD tactical patterns (Aggregates, Value Objects, Domain Events, Specifications), and SDD spec-first workflow.
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

## Decision Tree: What To Do When Developer Input Arrives

```
Developer input arrives
    │
    ├─ /dflow: command (explicit entry — route directly to workflow)
    │   ├─ /dflow:new-feature     → NEW FEATURE WORKFLOW (references/new-feature-flow.md)
    │   ├─ /dflow:modify-existing → MODIFY EXISTING WORKFLOW (references/modify-existing-flow.md)
    │   ├─ /dflow:bug-fix         → MODIFY EXISTING WORKFLOW (lightweight ceremony)
    │   ├─ /dflow:pr-review       → PR REVIEW CHECKLIST (references/pr-review-checklist.md)
    │   ├─ /dflow:verify [<bc>]    → DRIFT VERIFICATION (references/drift-verification.md)
    │   ├─ /dflow:status          → Report current workflow state (see § Workflow Transparency)
    │   ├─ /dflow:next            → Confirm proceeding to next phase (active workflow only)
    │   └─ /dflow:cancel          → Abort current workflow, return to free conversation
    │
    ├─ Natural language implying a development task (auto-trigger safety net)
    │   └─ DO NOT auto-enter a workflow. Instead (see § Workflow Transparency):
    │       1. State your judgment: "I think this is a [new-feature / modify / bug-fix] task"
    │       2. Suggest the matching /dflow: command
    │       3. Wait for developer confirmation before entering the workflow
    │
    ├─ "I'm designing a domain model" / "How should I model X?"
    │   └─ → DDD MODELING GUIDE (read references/ddd-modeling-guide.md)
    │
    ├─ "Quick question about..." / "How does X work?"
    │   └─ → Check specs/domain/ first, answer from domain knowledge
    │
    ├─ "I'm creating a branch"
    │   └─ → GIT FLOW INTEGRATION (read references/git-flow-integration.md)
    │
    └─ Anything else code-related
        └─ → Assess: does this touch business logic?
            Yes → Auto-trigger safety net (suggest /dflow: command, wait for confirmation)
            No  → Help directly, no ceremony needed
```

## Workflow Transparency

The Skill uses a **Hybrid design**: slash commands as the primary entry, natural-language auto-trigger as a safety net, and tiered transparency so developers always know where they are in the workflow.

### Slash Commands

**Flow entry commands** — start a workflow:
- `/dflow:new-feature` — enter new-feature-flow
- `/dflow:modify-existing` — enter modify-existing-flow
- `/dflow:bug-fix` — enter modify-existing-flow with lightweight ceremony
- `/dflow:pr-review` — enter PR review checklist

**Control commands** — manage an active workflow:
- `/dflow:status` — report current workflow, step, and progress
- `/dflow:next` — confirm proceeding to the next phase (equivalent to "OK" / "continue")
- `/dflow:verify [<bc>]` — run drift verification on rules.md ↔ behavior.md (standalone, no active workflow needed)
- `/dflow:cancel` — abort current workflow, return to free conversation (artifacts created so far are kept as-is)

### Auto-Trigger Safety Net

When natural language implies a development task, the Skill still detects the intent — but must NOT auto-enter a workflow. Follow this pattern:

1. State your judgment clearly:
   > "I think this is a new-feature task."
2. Offer three options to the developer:
   - Type `/dflow:new-feature` to start explicitly
   - Reply "OK" / "繼續" to confirm this workflow
   - Or correct the workflow (e.g., "no, this is a bug fix")
3. Wait for confirmation before entering any workflow.

This addresses three failure modes of pure auto-trigger: missed triggers, wrong workflow selection, and invisible state.

### Three-Tier Transparency

During an active workflow, communicate at three levels — no more, no less:

| Level | Trigger Point | AI Behavior |
|---|---|---|
| **Flow entry (must confirm)** | After judging the workflow from NL | Stop and wait for confirmation (command, "OK", or implicit) |
| **Phase gate (notify + optional confirm)** | Before major milestones | Announce the transition; if developer provides next-phase input, treat as implicit confirmation |
| **Step-internal (notify only)** | Step N → Step N+1 | Announce "Step N complete, entering Step N+1" — do not wait |

**Phase gate positions:**

new-feature-flow (8 steps):
- Step 4 → 5 (spec written → plan implementation)
- Step 6 → 7 (branch created → start implementation)
- Step 7 → 8 (implementation done → completion)

modify-existing-flow (5 steps — Core version condenses extraction/analysis into one step since layers are already separated):
- Step 2 → 3 (baseline captured → assess DDD impact)
- Step 3 → 4 (DDD impact decision → implement)
- Step 4 → 5 (implementation done → update documentation)

### Completion Checklist Execution

The Step 7 → Step 8 Phase Gate (new-feature-flow) and Step 4 → Step 5 Phase Gate (modify-existing-flow) are the **completion checklist triggers**. Do not run the checklist opportunistically — wait until the developer crosses this gate via `/dflow:next`, a confirmation word, or implicit confirmation.

Once triggered, execute the checklist in strict order:

1. **AI-independent verification** (Section 8.1 / 5.1): run every item without asking the developer; report `✓` / `✗` as a single list. Items fall into two timing categories:
   - **Pre-merge** (default): verified before touching any docs — Given/When/Then and BR/EC coverage, Domain Events raised, Domain project purity (zero external NuGet), Aggregate invariants preserved, EF Fluent API only, `實作任務` section completeness.
   - **Post-8.3 / Post-5.3** (marked `*(post-...)*`): re-verified after the 8.3 / 5.3 merge step lands — `behavior.md` BR-* anchor correspondence (incl. deletions for REMOVED deltas) and `last-updated` date (mechanical input for `/dflow:verify`).
   If any item fails, pause and fix before continuing.
2. **Developer-confirmation verification** (Section 8.2 / 5.2): ask one question at a time (intent fit, Aggregate boundary sanity, Domain Event placements, missed tech debt); wait for the developer's judgment. Do **not** dump all questions at once. P005b adds two questions: whether the scenarios merged into `behavior.md` (incl. Aggregate transitions + Events) faithfully express the intended behavior, and whether the spec's `實作任務` section should be collapsed / removed per team convention.
3. **Documentation updates** (Section 8.3 / 5.3): update glossary / models / rules / events / context-map / tech-debt. The `behavior.md` merge includes two sub-steps: promote any Phase 3 draft sections (B3 mid-sync) to formal sections, and update the corresponding `rules.md` anchor's `last-updated` date (B4). If the spec was abandoned mid-way, clean up the `## 提案中變更` section (keep history or explicitly REMOVE).
4. **Archival** (Section 8.4 / 5.4): move spec to `completed/`, flip `status`.

Only announce "feature complete" / "change complete" after archival is done. If the developer skips the Phase Gate and commits directly, the auto-trigger safety net should prompt "It looks like you're wrapping up — should I run the Step 8 checklist?" before allowing commit guidance.

### Confirmation Signals (NL ↔ Command Equivalence)

Any of these count as "proceed to next phase" — pick whichever the developer uses:

- **Command**: `/dflow:next`
- **Verbal (English)**: OK / yes / continue / go ahead / sounds good / proceed
- **Verbal (Chinese)**: 好 / 對 / 繼續 / 可以 / 沒問題
- **Implicit**: Developer provides the information needed for the next phase
  (e.g., AI asks "Which Aggregate?" and developer answers with the Aggregate name → implicit confirmation)

The implicit-confirmation rule is important — avoid turning every transition into a ceremony where the developer must say "OK" before every sentence.

### `/dflow:status` Response Format

When the developer types `/dflow:status` or asks in NL ("where are we?", "current status?"), report in this format:

```
Current workflow: new-feature-flow
Current step: Step 3 — Domain Concept Discovery

Completed:
- [x] Step 1: Intake — requirements understood
- [x] Step 2: Identify BC — assigned to Expense context

In progress:
- [ ] Step 3: Domain Concept Discovery — identifying Aggregate / VO / Events

Remaining:
- [ ] Step 4: Write Spec
- [ ] Step 5: Plan Implementation
- [ ] Step 6: Git Branch
- [ ] Step 7: Implementation
- [ ] Step 8: Completion
```

If no workflow is active, reply: "No active workflow. Use `/dflow:new-feature`, `/dflow:modify-existing`, `/dflow:bug-fix`, or `/dflow:pr-review` to start one."

---

## Core Principles

1. **Spec Before Code** — No implementation without at least a lightweight spec
2. **Domain at the Center** — Business logic lives ONLY in the Domain layer
3. **Ubiquitous Language** — All code uses terms from specs/domain/glossary.md
4. **Aggregate Boundaries** — Each transaction modifies exactly ONE Aggregate
5. **Dependency Inversion** — Domain defines interfaces, Infrastructure implements
6. **Pragmatic, Not Dogmatic** — Scale ceremony to impact

## Ceremony Scaling

| Change Type | Spec Level | DDD Modeling Depth | Tech Debt Record |
|---|---|---|---|
| New Aggregate / BC | Full spec | Full: Aggregate design + Events + Context Map | — |
| New feature (within existing BC) | Full spec | Standard: confirm Aggregate ownership + new VO/Event | If applicable |
| Business rule change | Standard spec | Standard: update rules.md + check invariants | Yes |
| Bug fix (logic error) | Lightweight spec | Lightweight: confirm fix is in the correct layer | If found |
| UI-only / API contract | Lightweight spec | Skip | Skip |
| Infrastructure change | Skip spec | Skip | Evaluate |

**DDD Modeling Depth definitions:**
- **Full** — use `templates/aggregate-design.md`, update `context-map.md`, define Domain Events in `events.md`
- **Standard** — confirm Aggregate ownership, update `models.md` and `rules.md`
- **Lightweight** — only confirm the fix is in the correct architectural layer
- **Skip** — no domain logic involved

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
│   │       ├── rules.md                 # Business rules index (BR-ID + one-line)
│   │       ├── behavior.md             # Consolidated behavior (Given/When/Then)
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

## Behavior Source of Truth (rules.md + behavior.md)

Each Bounded Context has two complementary files that together describe the system's current behavior:

- **`rules.md`** — declarative index: lists each BR-ID with a one-line summary. Quick lookup, easy to scan.
- **`behavior.md`** — scenario-level detail: the full Given/When/Then scenarios for each BR-ID, including Aggregate state transitions and Domain Events. This is the consolidated source of truth for "what does the system actually do right now?"

`specs/features/completed/` is a historical archive (individual change records). `behavior.md` is the **merged current state** — when a feature is completed, AI merges its scenarios into `behavior.md`; when behavior is modified, AI updates the corresponding section to reflect the new behavior (git preserves history). See `templates/behavior.md` for the template.

This is analogous to how OpenSpec's `specs/` directory serves as the system behavior source of truth, but organized by Bounded Context rather than by capability.

## Guiding Questions by Phase

**Phase markers in feature-spec template**: each section in `templates/feature-spec.md` carries an HTML comment (e.g., `<!-- 填寫時機：Phase 2 -->`) indicating the phase in which that section should be filled. These markers align with the phases below and are used by `/dflow:status` and the completion checklist to track progress. When guiding a developer, fill sections in phase order; do not jump ahead to Phase 4 (implementation planning) before Phase 3 (spec writing) is agreed. The `實作任務` section at the end of the template is produced by AI at the end of Phase 4 — see new-feature-flow.md Step 5 / modify-existing-flow.md Step 3.

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
| `references/drift-verification.md` | `/dflow:verify` — rules.md ↔ behavior.md consistency check |

## Templates

| Template | Purpose |
|---|---|
| `templates/feature-spec.md` | Full feature specification |
| `templates/lightweight-spec.md` | Quick spec for bug fixes |
| `templates/context-definition.md` | New Bounded Context |
| `templates/behavior.md` | Consolidated behavior spec for a Bounded Context |
| `templates/aggregate-design.md` | New Aggregate design worksheet |
| `templates/CLAUDE.md` | Project-level CLAUDE.md |
