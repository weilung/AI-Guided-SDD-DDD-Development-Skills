# New Feature Workflow

Step-by-step guide for when a developer triggers `/dflow:new-feature` (or natural language implying a new-feature task — see SKILL.md § Workflow Transparency for the auto-trigger safety net behavior).

**Phase Gates** in this flow (stop-and-confirm before proceeding):
- Step 4 → Step 5 (spec written → plan implementation)
- Step 6 → Step 7 (branch ready → start implementation)
- Step 7 → Step 8 (implementation done → completion)

All other step transitions are **step-internal**: announce "Step N complete, entering Step N+1" and proceed without waiting. See SKILL.md § Workflow Transparency for the full transparency protocol and confirmation signals.

## Step 1: Intake — Understand the Request

Ask these questions (naturally, not as a checklist dump):

1. **What's the feature?** Get a plain-language description.
2. **Who needs it?** Identify the stakeholder or user role.
3. **Why now?** Understand priority and urgency (affects ceremony level).

Then check existing assets:
- Search `specs/domain/` for related concepts
- Search `specs/features/` for related or overlapping features
- Check `specs/domain/glossary.md` for relevant terms

Share what you found: "I see we already have [X] documented. This new feature seems to extend
that — is that right?"

**→ Transition (step-internal)**: Step 1 complete. Announce "Step 1 complete (intake). Entering Step 2: Identify the Bounded Context." and continue.

## Step 2: Identify the Bounded Context

Guide the developer to place this feature in the right context:

```
"This feature involves [concepts]. Looking at our domain structure,
it seems to fit in the [Context] bounded context. Does that match
your understanding?"
```

If no matching context exists:
1. Propose a new context name
2. Create `specs/domain/{new-context}/context.md` using the context-definition template
3. Get developer confirmation before proceeding

**→ Transition (step-internal)**: Step 2 complete. Announce "Step 2 complete (BC identified). Entering Step 3: Domain Concept Discovery." and continue.

## Step 3: Domain Concept Discovery

Walk through these questions:

- **What are the key nouns?** → Potential Entities or Value Objects
- **What are the key verbs?** → Potential Domain Services or Entity behaviors
- **What are the rules/constraints?** → Business Rules to document
- **What are the states/statuses?** → State machines to model
- **What external data is needed?** → Interfaces to define

For each new concept:
1. Check glossary — add if missing
2. Check if it already exists in models.md — extend if needed
3. If entirely new, add to the appropriate context's models.md

**→ Transition (step-internal)**: Step 3 complete. Announce "Step 3 complete (domain concepts captured). Entering Step 4: Write the Spec." and continue.

## Step 4: Write the Spec

Create `specs/features/active/{ID}-{feature-name}.md` using the feature-spec template.

Guide the developer through each section:

### Behavior Specification (Given/When/Then)
This is the most important part. Help the developer think through scenarios:

```
"Let's walk through the main success scenario first.
Given [initial state], when the user [does action], then [what should happen]?"
```

Then probe for variations:
- "What if [input is invalid]?"
- "What if [related data doesn't exist]?"
- "What happens with [boundary values]?"
- "Are there permission/role requirements?"

### Business Rules
Extract explicit rules from the scenarios:

```
"From what you've described, I see these business rules:
BR-01: [rule]
BR-02: [rule]
Do these capture the logic correctly? Anything I'm missing?"
```

### Edge Cases
Specifically ask about:
- Empty/null inputs
- Concurrent modifications
- Large data volumes
- Currency/decimal precision (if financial)
- Date/timezone boundaries
- Character encoding (CJK, special characters)

**→ Phase Gate: Step 4 → Step 5**

Announce to developer:
> "Spec is drafted — behavior scenarios, business rules, and edge cases are captured. Ready to plan the implementation (Domain layer design, interfaces, thin Code-Behind)? `/dflow:next` or reply 'OK' to continue, or tell me if the spec needs another iteration first."

Wait for confirmation (`/dflow:next`, verbal OK, or implicit — see SKILL.md § Confirmation Signals) before entering Step 5.

## Step 5: Plan the Implementation

### Domain Layer First
Identify what goes into `src/Domain/`:

```csharp
// Example guidance:
// "For this feature, I'd suggest:
//  - A new Value Object: Money(Amount, Currency)
//  - A new interface: IExchangeRateService
//  - A Domain Service: ExpenseCalculationService
//  All pure C#, no WebForms dependencies."
```

### Code-Behind Thin Layer
Plan how the WebForms page will call the Domain layer:

```csharp
// Ideal Code-Behind pattern:
protected void btnSubmit_Click(object sender, EventArgs e)
{
    // 1. Parse UI inputs
    var input = ParseFormInput();

    // 2. Call Domain layer
    var result = _domainService.Process(input);

    // 3. Display result
    BindResult(result);
}
```

### Interface Definitions
For any external dependency (database, API, file system), define an interface
in `src/Domain/{Context}/Interfaces/`:

```csharp
// src/Domain/Expense/Interfaces/IExpenseRepository.cs
public interface IExpenseRepository
{
    ExpenseItem GetById(int id);
    void Save(ExpenseItem item);
}
```

### Generate Implementation Tasks List

After the plan is agreed, AI generates a concrete task list and writes it into the spec's `實作任務` section (see feature-spec template). Each task follows the format `[LAYER]-[NUMBER]：description`:

- `DOMAIN` — Domain classes, VOs, Services, Interfaces
- `PAGE`    — Code-Behind / ASPX changes
- `DATA`    — Table schema or Repository impl
- `TEST`    — Test cases

Example seed (replace with feature-specific tasks):

```markdown
- [ ] DOMAIN-1：Create Money value object with currency conversion
- [ ] DOMAIN-2：Define IExchangeRateService interface
- [ ] PAGE-1：Thin ExpenseForm.aspx.cs to call domain service
- [ ] DATA-1：Add ExchangeRate table + Repository
- [ ] TEST-1：Money equality and conversion unit tests
```

The list becomes the execution punch-list for Step 7 and the completion checklist in Step 8.

**→ Transition (step-internal)**: Step 5 complete. Announce "Step 5 complete (implementation plan + task list ready). Entering Step 6: Git Branch." and continue.

## Step 6: Git Branch

After the spec is written and reviewed:

```
Branch naming: feature/{SPEC-ID}-{short-description}
Example: feature/EXP-001-jpy-currency-support
```

The spec ID links the branch to its specification document.

**→ Phase Gate: Step 6 → Step 7**

Announce to developer:
> "Branch `feature/{SPEC-ID}-{description}` is created. Ready to start implementation? `/dflow:next` to proceed, or discuss implementation order / scope first."

Wait for confirmation before entering Step 7.

## Step 7: Implementation

During implementation, continuously check:
- [ ] Is business logic going into src/Domain/ (not Code-Behind)?
- [ ] Are new terms in the glossary?
- [ ] Does Code-Behind only do UI binding?
- [ ] Are interfaces used for external dependencies?
- [ ] Did we discover any tech debt? → Record in tech-debt.md

**→ Phase Gate: Step 7 → Step 8**

Announce to developer:
> "Implementation appears complete. Ready to run the completion checklist (verify against spec, update domain docs, archive the spec)? `/dflow:next` to proceed."

Wait for confirmation before entering Step 8. This phase gate is where the completion checklist is triggered — do not skip.

## Step 8: Completion

Triggered by the Step 7 → Step 8 Phase Gate. AI runs the completion checklist in the order below; do **not** skip a section.

### 8.1 Verification — AI runs independently

AI reports `✓` / `✗` for every item before touching docs. Items marked *(post-8.3)* are re-verified after the documentation merge in 8.3 lands:

- [ ] `實作任務` section: all tasks checked, or unchecked items explicitly labelled as follow-up (linked to spec / tech-debt entry)
- [ ] Every `Given/When/Then` scenario in the spec is covered by implementation or tests
- [ ] Every `BR-*` business rule is covered by implementation or tests
- [ ] Every `EC-*` edge case is handled
- [ ] Domain layer has **no** `System.Web` references (grep `src/Domain/`)
- [ ] *(post-8.3)* `specs/domain/{context}/behavior.md` contains a section anchor for every `BR-*` introduced by this spec (mechanical input for `/dflow:verify`)
- [ ] *(post-8.3)* `specs/domain/{context}/behavior.md` `last-updated` is later than this spec's `created` date (mechanical drift guard)

If any item fails, report the gap and pause — don't proceed to 8.2.

### 8.2 Verification — needs developer confirmation

AI lists findings one at a time and waits for the developer to confirm each:

- [ ] Does the implementation faithfully express the **intent** of each BR? (AI lists BR → impl location; developer judges fit)
- [ ] Are the edge case handling decisions appropriate? (AI lists EC → handling; developer judges)
- [ ] Did we miss any tech debt worth recording? (AI lists what it saw; developer adds misses)
- [ ] Do the scenarios merged into `behavior.md` faithfully express the intended behavior? (AI lists merged anchors; developer judges)
- [ ] Should the `實作任務` section in the spec be collapsed / removed now that it's complete? (team convention — developer decides)

Ask these one-by-one; do not dump all five at once.

### 8.3 Documentation updates

- [ ] `specs/domain/glossary.md` — new terms added
- [ ] `specs/domain/{context}/models.md` — model definitions updated
- [ ] `specs/domain/{context}/rules.md` — business rules updated
- [ ] `specs/domain/{context}/behavior.md` — merge completed spec's Given/When/Then scenarios into consolidated behavior. Sub-steps:
      - Promote any Phase 3 draft sections (from B3 mid-sync) to formal sections
      - Update the corresponding `rules.md` anchor's `last-updated` date (B4)
- [ ] `behavior.md` draft cleanup — if the spec was abandoned mid-way, keep the `## 提案中變更` section's history or explicitly REMOVE it
- [ ] `specs/migration/tech-debt.md` — tech debt discovered during implementation recorded

### 8.4 Archival

- [ ] Spec `status` field changed to `completed`
- [ ] Spec file moved from `specs/features/active/` to `specs/features/completed/`

Only announce "feature complete" after 8.4 is done.
