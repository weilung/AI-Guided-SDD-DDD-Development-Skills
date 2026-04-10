# New Feature Workflow

Step-by-step guide for when a developer says "I need to add a new feature."

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

## Step 6: Git Branch

After the spec is written and reviewed:

```
Branch naming: feature/{SPEC-ID}-{short-description}
Example: feature/EXP-001-jpy-currency-support
```

The spec ID links the branch to its specification document.

## Step 7: Implementation

During implementation, continuously check:
- [ ] Is business logic going into src/Domain/ (not Code-Behind)?
- [ ] Are new terms in the glossary?
- [ ] Does Code-Behind only do UI binding?
- [ ] Are interfaces used for external dependencies?
- [ ] Did we discover any tech debt? → Record in tech-debt.md

## Step 8: Completion

When the feature is done:
1. Update spec status to `completed`
2. Move spec from `active/` to `completed/`
3. Verify all new terms are in glossary
4. Verify domain models.md and rules.md are updated
5. Record any tech debt discovered during implementation
