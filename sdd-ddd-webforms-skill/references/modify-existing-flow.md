# Modify Existing Feature Workflow

Step-by-step guide for when a developer says "I need to change/fix/modify existing functionality."

## Mindset

Modifying existing features is your best opportunity to progressively extract domain knowledge
and business logic. Treat every modification as a chance to:
1. Document what currently exists (if no spec exists yet)
2. Extract business logic from Code-Behind to Domain layer
3. Record tech debt for future migration

## Step 1: Assess the Change

Ask:
1. **What needs to change?** Get the specific modification.
2. **Why?** Bug fix, new requirement, or behavior change?
3. **How big is this change?** Determine ceremony level:
   - UI-only fix → Skip spec, help directly
   - Business logic change → Standard spec
   - Bug in calculation/rule → Lightweight spec

Check existing assets:
- Is there already a spec in `specs/features/completed/` for this feature?
- Are the involved domain concepts documented in `specs/domain/`?

## Step 2: Document Current Behavior (if no spec exists)

This is critical. Before changing anything, capture what currently exists:

```
"Before we change this, let me help you document the current behavior.
This way we have a baseline and the change is traceable."
```

Create a spec with status `in-progress` that includes:
- Current behavior description
- Current business rules (extracted from Code-Behind)
- The proposed change clearly marked

Use this pattern in the spec:

```markdown
## Current Behavior
Given [current state]
When [user action]
Then [current result]

## Changed Behavior (this PR)
Given [same or modified state]
When [user action]
Then [NEW expected result]

## Reason for Change
[Why the behavior is changing - link to ticket/request if available]
```

## Step 3: Analyze the Code-Behind

Read the existing Code-Behind and identify:

### Business Logic to Extract
Look for:
- **Calculations** — anything with math, comparisons, or transformations
- **Validation rules** — any if/else that checks business conditions
- **State transitions** — status changes, approval flows
- **Data transformations** — converting between formats, currencies, units

### Tech Debt to Record
Look for:
- Direct SQL queries in Code-Behind
- Business logic duplicated across multiple pages
- Magic numbers (e.g., `if (status == 3)`)
- Session/ViewState storing business state
- Try/catch blocks swallowing exceptions silently
- String concatenation for SQL (SQL injection risk)

Record each finding in `specs/migration/tech-debt.md` with:
```markdown
- [ ] {File}:{Line} — {Description} — Severity: {High|Medium|Low}
```

## Step 4: Evaluate Extraction Opportunity

For the code being modified, ask:

```
"The business logic for [X] is currently in {PageName}.aspx.cs.
Since we're already touching this code, should we extract it to
src/Domain/{Context}/? This would:
- Make it testable
- Make it reusable
- Make it ready for ASP.NET Core migration"
```

Decision framework:
- **Extract now** if: the logic is being significantly modified anyway
- **Extract now** if: the logic is duplicated elsewhere and we need the single source of truth
- **Defer extraction** if: the change is a one-line fix and the surrounding code is too tangled
- **Always record** the extraction opportunity in tech-debt.md even if deferring

## Step 5: Implement the Change

If extracting to Domain layer:

```csharp
// BEFORE (Code-Behind)
protected void Calculate()
{
    decimal amount = decimal.Parse(txtAmount.Text);
    decimal rate = GetExchangeRate(ddlCurrency.SelectedValue);
    decimal result = Math.Round(amount * rate, 0); // JPY has no decimals
    lblResult.Text = result.ToString("N0");
}

// AFTER (Domain layer)
// src/Domain/Expense/ValueObjects/Money.cs
public record Money(decimal Amount, Currency Currency)
{
    public Money ConvertTo(Currency target, ExchangeRate rate)
    {
        var converted = Amount * rate.Rate;
        return new Money(target.Round(converted), target);
    }
}

// Code-Behind becomes thin:
protected void Calculate()
{
    var money = new Money(decimal.Parse(txtAmount.Text), selectedCurrency);
    var rate = _exchangeRateService.GetRate(selectedCurrency, Currency.TWD, reportDate);
    var result = money.ConvertTo(Currency.TWD, rate);
    lblResult.Text = result.Amount.ToString("N0");
}
```

## Step 6: Update Artifacts

After the change:
1. Update or create the feature spec
2. Update `specs/domain/{context}/rules.md` if business rules changed
3. Update `specs/domain/{context}/models.md` if domain model changed
4. Update `specs/domain/glossary.md` if new terms emerged
5. Update `specs/migration/tech-debt.md` with findings
6. Update spec status

## Lightweight Spec Template (for bug fixes)

For small bug fixes, a lightweight spec is enough:

```markdown
---
id: BUG-042
title: Fix rounding inconsistency in expense calculation
status: in-progress
bounded-context: Expense
created: 2025-02-12
---

## Problem
Page A uses Math.Round(amount, 0, MidpointRounding.AwayFromZero) (四捨五入)
Page B uses Math.Floor(amount) (無條件捨去)
They should both use the same rounding rule.

## Expected Behavior
Given an expense amount of 123.5 TWD
When displayed on any page
Then it should show 124 (四捨五入 per accounting standard)

## Root Cause
Duplicated calculation logic — recorded in tech-debt.md

## Fix
Extract rounding to Money.Round() in Domain layer, both pages call it.
```
