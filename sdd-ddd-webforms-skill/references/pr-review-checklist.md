# PR Review Checklist

When reviewing code changes or discussing PRs, use this checklist to ensure
the SDD/DDD workflow was followed.

`/dflow:pr-review` enters this checklist starting from **Step 0**. Do not skip Step 0 — reviewing code without first understanding spec intent breaks the SDD feedback loop (all the upstream spec work loses its verification mechanism).

## Step 0: Understand the Change Intent (before code review)

Ground yourself in the spec *before* looking at the diff:

- [ ] Read the spec referenced by this PR (`specs/features/active/{id}-*.md` or an accompanying lightweight/bug spec)
- [ ] If the spec has a `行為變更（Delta）` section (modify-existing), read **ADDED / MODIFIED / REMOVED / RENAMED**; note any **UNCHANGED** scope declaration
- [ ] State in one sentence: "This PR intends to {change} because {reason}." (If you can't, pause and ask the author.)
- [ ] Only then proceed to the code-review sections below

If the PR has no spec:
```
"I don't see a spec for this PR. Before I review the code, can you
point me to it, or create a lightweight spec (`templates/lightweight-spec.md`)
capturing problem / behavior change / reason? SDD relies on the spec
being the review anchor."
```

## Spec Compliance

- [ ] **Spec exists** — Is there a spec in `specs/features/` for this change?
- [ ] **Spec matches code** — Does the implementation match the Given/When/Then scenarios?
- [ ] **Business rules covered** — Are all BR-* rules from the spec implemented?
- [ ] **Edge cases handled** — Are EC-* edge cases from the spec addressed?

If the spec is missing or incomplete:
```
"I notice this PR doesn't have a matching spec. Let's create one
retroactively — it'll help with documentation and future migration.
Can you describe what this change does?"
```

## Domain Layer Quality

- [ ] **No System.Web in Domain** — `src/Domain/` must have zero WebForms dependencies
- [ ] **No direct DB access in Domain** — All data access through interfaces
- [ ] **No HttpContext/Session/ViewState in Domain** — Pure business logic only
- [ ] **Testable without web infrastructure** — Could you unit test this without IIS?

Common violations to flag:
```csharp
// BAD: Domain code depending on WebForms
using System.Web;
using System.Web.UI;
HttpContext.Current.Session["key"]
Page.ViewState["key"]

// BAD: Direct DB in Domain
using System.Data.SqlClient;
new SqlConnection(connectionString)

// GOOD: Interface-based
public class ExpenseService
{
    private readonly IExpenseRepository _repo;
    public ExpenseService(IExpenseRepository repo) => _repo = repo;
}
```

## Code-Behind Thickness

Evaluate whether Code-Behind files are appropriately thin:

**Acceptable Code-Behind responsibilities:**
- Parse UI inputs (TextBox → typed values)
- Call Domain layer services
- Bind results to UI controls
- Handle UI-specific events (page load, button click)
- UI validation (RequiredFieldValidator, etc.)

**Should be in Domain layer instead:**
- Calculations (math, conversions, aggregations)
- Business validation (rules, constraints, limits)
- State transitions (status changes, workflow steps)
- Data transformations (business meaning, not UI formatting)

If Code-Behind is too thick:
```
"This Code-Behind has [calculation/validation/transformation] logic
that could be extracted to src/Domain/{Context}/. Since we're already
reviewing this, should we extract it now or record it in tech-debt.md
for later?"
```

## Glossary Consistency

- [ ] **New terms documented** — Any new business concept added to glossary.md?
- [ ] **Consistent naming** — Do class/method/variable names match glossary terms?
- [ ] **No ambiguous terms** — Are domain-specific terms used precisely?

Example check:
```
"I see you're using 'reimbursement' in the code but the glossary
uses '報銷 (Expense Claim)'. Should we align the naming?"
```

## Domain Documentation Updates

- [ ] **models.md updated** — New entities/VOs/services documented?
- [ ] **rules.md updated** — New or changed business rules recorded?
- [ ] **context.md boundaries respected** — Does this change stay within its context?

## Tech Debt Awareness

- [ ] **New debt recorded** — Any shortcuts or compromises documented in tech-debt.md?
- [ ] **Existing debt not worsened** — Did this change make existing tech debt worse?
- [ ] **Debt reduced** — Did this change fix any existing tech-debt.md items? If so, check them off.

## Migration Readiness Score

Rate the PR on a quick migration readiness scale:

- **A**: New business logic entirely in Domain layer, fully testable, spec complete
- **B**: Mostly in Domain layer, some minor coupling, spec exists
- **C**: Mixed — some extraction done, some logic still in Code-Behind
- **D**: All logic in Code-Behind, but at least documented in spec/tech-debt
- **F**: No spec, no extraction, no documentation — push back

Share the score with the developer constructively:
```
"This PR is a solid B — the exchange rate logic is cleanly extracted
to Domain, and the spec covers the key scenarios. One thing that would
make it an A: the date parsing logic in the Code-Behind could move to
a DateRange value object. Want to do that now or add it to tech-debt?"
```
