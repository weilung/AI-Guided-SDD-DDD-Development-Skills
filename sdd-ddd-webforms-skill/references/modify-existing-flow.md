# Modify Existing Feature Workflow

Step-by-step guide for when a developer triggers `/dflow:modify-existing` or `/dflow:bug-fix` (or natural language implying a modification task — see SKILL.md § Workflow Transparency for the auto-trigger safety net).

**Phase Gates** in this flow (stop-and-confirm before proceeding):
- Step 2 → Step 3 (baseline captured → analyze code-behind)
- Step 4 → Step 5 (extraction decision → start implementation)
- Step 5 → Step 6 (implementation done → update artifacts)

All other step transitions are **step-internal**: announce "Step N complete, entering Step N+1" and proceed without waiting. See SKILL.md § Workflow Transparency for the full transparency protocol and confirmation signals.

**Ceremony adjustment when triggered by `/dflow:bug-fix`**: treat as lightweight — use the Lightweight Spec Template at the end of this file instead of the full spec, and Step 4 (extraction) may default to "defer and record in tech-debt.md" unless the bug itself is in extractable logic.

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

**→ Transition (step-internal)**: Step 1 complete. Announce "Step 1 complete (change assessed, ceremony level decided). Entering Step 2: Document Current Behavior." and continue.

## Step 2: Document Current Behavior (if no spec exists)

This is critical. Before changing anything, capture what currently exists:

```
"Before we change this, let me help you document the current behavior.
This way we have a baseline and the change is traceable."
```

Create a spec with status `in-progress` that includes:
- Current behavior description
- Current business rules (extracted from Code-Behind)
- The proposed change clearly marked — use the **Delta** format below

### Delta Spec Format (for modifications)

Use ADDED / MODIFIED / REMOVED / RENAMED + an optional UNCHANGED section. Keep Given/When/Then for each rule; the Delta section lives inside the spec and does not accumulate into `specs/domain/{context}/behavior.md` (git history already covers the trail).

```markdown
## 行為變更（Delta）

### ADDED — 新增的行為
#### 規則：BR-NN 規則名稱
Given {狀態}
When {操作}
Then {新的預期結果}

### MODIFIED — 修改的行為
#### 規則：BR-NN 規則名稱
**原本**：Given … When … Then {舊結果}
**改為**：Given … When … Then {新結果}
**原因**：{為什麼改}

### REMOVED — 移除的行為
#### 規則：BR-NN 規則名稱
**原因**：{為什麼移除}

### RENAMED — 重新命名
#### 規則：{舊名稱} → {新名稱}
**原因**：{為什麼改名，例如術語演化 / 對齊 glossary}

### UNCHANGED — 明確不受影響（可選）
- BR-003 金額上限
- BR-005 提交後不可修改
```

**Section rules**:
- Use **ADDED / MODIFIED / REMOVED / RENAMED** for every behavioral change; skip a sub-section if it has no entries.
- `MODIFIED` must keep the "原本 / 改為" pair so reviewers see the before/after without guessing.
- `RENAMED` is only about naming (e.g., 「簽核」→「審批」). If the behavior also changed, split into RENAMED + MODIFIED entries.
- `UNCHANGED` is **recommended but optional**; fill it when regression risk is high or MODIFIED entries are many.
- Always pair with `## Reason for Change` (why this PR exists — ticket / stakeholder ask).

### Systematic Baseline Capture (when no prior spec exists)

When the feature being modified has no existing spec, take the opportunity to do a broader baseline capture — not just the single behavior being changed. Proactively:

1. Read the related Code-Behind files (the page being modified + pages that share logic)
2. Extract all business rules found (if/else conditions, calculations, validations)
3. Identify domain concepts (potential Entities, Value Objects, Services)
4. Check for duplicated logic across pages
5. Record findings in the appropriate domain docs (`models.md`, `rules.md`) and `tech-debt.md`

This is an **opportunistic** strategy — "capture while we're already here." Do not force a full codebase scan; scope it to the modified feature and its immediate neighbors. Share what you find:

```
"Since there's no spec for this feature yet, I took a broader look at
the related Code-Behind. I found:
- 3 business rules in {PageName}.aspx.cs (documented in rules.md)
- Duplicated validation logic shared with {OtherPage}.aspx.cs (recorded in tech-debt.md)
- A potential Money value object hiding in the calculation at line {N}
This gives us a better baseline before we make our change."
```

**→ Phase Gate: Step 2 → Step 3**

Announce to developer:
> "Baseline captured — current behavior is documented and the proposed change is marked. Ready to analyze the Code-Behind to identify business logic and tech debt? `/dflow:next` or reply 'OK' to continue."

Wait for confirmation before entering Step 3.

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

**→ Transition (step-internal)**: Step 3 complete. Announce "Step 3 complete (code-behind analyzed, tech debt recorded). Entering Step 4: Evaluate Extraction Opportunity." and continue.

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

### Generate Implementation Tasks List (full spec only)

If the modification uses the full feature-spec template (not the lightweight template), AI generates a concrete task list and writes it into the spec's `實作任務` section. Use the same `[LAYER]-[NUMBER]：description` format as new-feature-flow Step 5 (DOMAIN / PAGE / DATA / TEST). Skip this step for bug-fix mode using lightweight-spec.

**→ Phase Gate: Step 4 → Step 5**

Announce to developer:
> "Extraction decision made — {extract now / defer and record}. Ready to start implementation? `/dflow:next` to proceed, or adjust the extraction scope first."

Wait for confirmation before entering Step 5.

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

**→ Phase Gate: Step 5 → Step 6**

Announce to developer:
> "Implementation appears complete. Ready to update artifacts (spec, rules.md, models.md, glossary, tech-debt)? `/dflow:next` to proceed."

Wait for confirmation before entering Step 6. This phase gate is where the completion checklist is triggered — do not skip.

## Step 6: Update Artifacts

Triggered by the Step 5 → Step 6 Phase Gate. AI runs the completion checklist in the order below; do **not** skip a section. For bug-fix mode with lightweight-spec, items marked `(full spec only)` are skipped.

### 6.1 Verification — AI runs independently

Items marked *(post-6.3)* are re-verified after the documentation merge in 6.3 lands:

- [ ] Every ADDED / MODIFIED / REMOVED / RENAMED entry in the Delta section is covered by implementation or tests
- [ ] Domain layer has **no** `System.Web` references (grep `src/Domain/`)
- [ ] Extracted logic (if Step 4 decided "extract now") lives under `src/Domain/` with pure C# only
- [ ] `實作任務` section: all tasks checked, or unchecked items explicitly labelled as follow-up *(full spec only)*
- [ ] *(post-6.3)* `specs/domain/{context}/behavior.md` has a section anchor for every `BR-*` in ADDED / MODIFIED entries; REMOVED entries' anchors have been deleted (mechanical input for `/dflow:verify`)
- [ ] *(post-6.3)* `specs/domain/{context}/behavior.md` `last-updated` is later than this spec's `created` date (mechanical drift guard)

If any item fails, report the gap and pause — don't proceed to 6.2.

### 6.2 Verification — needs developer confirmation

- [ ] Does the fix faithfully express the **intent** of the Delta entries? (AI lists delta → impl location; developer judges fit)
- [ ] Did we miss any tech debt worth recording during the Step 3 analysis pass?
- [ ] If extraction was deferred, is the tech-debt entry in `tech-debt.md` clear enough for a future picker?
- [ ] Do the scenarios merged into `behavior.md` faithfully express the Delta's final-state behavior? (AI lists updated anchors; developer judges)
- [ ] Should the `實作任務` section in the spec be collapsed / removed now that it's complete? *(full spec only — team convention)*

Ask these one-by-one.

### 6.3 Documentation updates

- [ ] Update or create the feature / bug spec; set `status: completed`
- [ ] `specs/domain/{context}/rules.md` — business rules updated
- [ ] `specs/domain/{context}/models.md` — domain model updated
- [ ] `specs/domain/glossary.md` — new / renamed terms (mirror any RENAMED delta entries here)
- [ ] `specs/domain/{context}/behavior.md` — update scenarios to reflect Delta result (merge final state, not Delta markup). Sub-steps:
      - Promote any Phase 3 draft sections (from B3 mid-sync) to formal sections
      - Update the corresponding `rules.md` anchor's `last-updated` date (B4)
- [ ] `behavior.md` draft cleanup — if the Delta was abandoned mid-way, keep the `## 提案中變更` section's history or explicitly REMOVE it
- [ ] `specs/migration/tech-debt.md` — findings recorded

### 6.4 Archival

- [ ] Spec file moved to `specs/features/completed/` (if a spec was opened in this flow)

Only announce "change complete" after 6.4 is done.

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
