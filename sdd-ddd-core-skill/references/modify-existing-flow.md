# Modify Existing Feature Workflow — ASP.NET Core

Step-by-step guide for changing or fixing existing functionality.

Triggered by `/dflow:modify-existing` or `/dflow:bug-fix` (or natural language implying a modification task — see SKILL.md § Workflow Transparency for the auto-trigger safety net).

**Phase Gates** in this flow (stop-and-confirm before proceeding):
- Step 2 → Step 3 (baseline captured → assess DDD impact)
- Step 3 → Step 4 (DDD impact decision → implement)
- Step 4 → Step 5 (implementation done → update documentation)

All other step transitions are **step-internal**: announce "Step N complete, entering Step N+1" and proceed without waiting. See SKILL.md § Workflow Transparency for the full transparency protocol and confirmation signals.

**Note on step count**: Core version has 5 steps (WebForms has 6) because Clean Architecture's layered structure already separates concerns — there's no "extract from Code-Behind" step to perform.

**Ceremony adjustment when triggered by `/dflow:bug-fix`**: treat as lightweight — use the Lightweight Spec Template (see `templates/lightweight-spec.md`) instead of the full spec, and Step 3 may default to "no DDD impact, fix in place" unless the bug itself is in Domain logic.

## Step 1: Assess the Change

1. **What needs to change?** Get specifics.
2. **Why?** Bug fix, new requirement, or behavior change?
3. **Which layer is affected?**
   - Domain invariant broken → Domain fix + full spec
   - Application orchestration issue → Application fix + lightweight spec
   - Infrastructure bug (DB, external service) → Infrastructure fix + lightweight spec
   - API contract change → Presentation fix + spec if behavior changes

**→ Transition (step-internal)**: Step 1 complete. Announce "Step 1 complete (change assessed, affected layer identified). Entering Step 2: Check Documentation." and continue.

## Step 2: Check Documentation

- Spec in `specs/features/completed/`?
- Domain model in `specs/domain/{context}/models.md`?
- Business rules in `rules.md`?
- Domain events in `events.md`?

If no documentation exists, capture current behavior BEFORE changing — use the **Delta** format below.

### Delta Spec Format (for modifications)

Use ADDED / MODIFIED / REMOVED / RENAMED + an optional UNCHANGED section. Keep Given/When/Then for each rule; the Delta section lives inside the spec and does not accumulate into `specs/domain/{context}/behavior.md` (git history already covers the trail).

```markdown
## 行為變更（Delta）

### ADDED — 新增的行為
#### 規則：BR-NN 規則名稱
Given {Aggregate 初始狀態}
When {呼叫的 Command 或 Aggregate 方法}
Then {新的 Aggregate 狀態}
And {產生的 Domain Event}

### MODIFIED — 修改的行為
#### 規則：BR-NN 規則名稱
**原本**：Given … When … Then {舊結果} / {舊事件}
**改為**：Given … When … Then {新結果} / {新事件}
**原因**：{為什麼改}

### REMOVED — 移除的行為
#### 規則：BR-NN 規則名稱
**原因**：{為什麼移除}

### RENAMED — 重新命名
#### 規則：{舊名稱} → {新名稱}
**原因**：{為什麼改名，例如術語演化 / Aggregate 拆分 / 對齊 glossary}

### UNCHANGED — 明確不受影響（可選）
- BR-003 金額上限
- BR-005 提交後不可修改
```

**Section rules**:
- Use **ADDED / MODIFIED / REMOVED / RENAMED** for every behavioral change; skip a sub-section if it has no entries.
- `MODIFIED` must keep the "原本 / 改為" pair so reviewers see the before/after without guessing.
- `RENAMED` is only about naming (e.g., 「簽核」→「審批」、「Order → CustomerOrder」). If the behavior also changed, split into RENAMED + MODIFIED entries.
- `UNCHANGED` is **recommended but optional**; fill it when regression risk is high or MODIFIED entries are many.
- Always pair with `## Reason for Change` (why this PR exists — ticket / stakeholder ask).
- For Aggregate state transitions and Domain Events, include them in the Given/When/Then — this is how `/dflow:pr-review` Step 0 understands the intent.

**→ Phase Gate: Step 2 → Step 3**

Announce to developer:
> "Baseline captured — existing documentation reviewed, current behavior is documented and the proposed change is marked. Ready to assess the DDD impact (Aggregate design, Domain Events, Value Objects)? `/dflow:next` or reply 'OK' to continue."

Wait for confirmation before entering Step 3.

## Step 3: Assess DDD Impact

### Is the Aggregate design still correct?

Changes that require Aggregate redesign:
- New invariant that spans objects currently in different Aggregates
- Performance issue from too-large Aggregate
- Concurrency conflict from too-large Aggregate
- Business rule that now crosses Aggregate boundary

```
"This change affects [invariant]. Does the current Aggregate
boundary still make sense, or do we need to split/merge?"
```

### Do we need new Domain Events?

If the behavior change means other parts of the system need to react differently:
- Add new events
- Modify event payloads (careful: backward compatibility)
- Add new event handlers

### Are Value Objects still valid?

If constraints change:
- Update Value Object validation
- Check all usages of the Value Object

### Generate Implementation Tasks List (full spec only)

If the modification uses the full feature-spec template (not the lightweight template), AI generates a concrete task list and writes it into the spec's `實作任務` section. Use the same `[LAYER]-[NUMBER]：description` format as new-feature-flow Step 5 (DOMAIN / APP / INFRA / API / TEST). Skip this step for bug-fix mode using lightweight-spec.

**→ Phase Gate: Step 3 → Step 4**

Announce to developer:
> "DDD impact analysis done — {Aggregate boundary OK / needs redesign}, {no new events / new events needed}. Ready to implement? `/dflow:next` to proceed, or adjust the design first."

Wait for confirmation before entering Step 4.

## Step 4: Implement

Follow the layer order: Domain → Application → Infrastructure → Presentation.

Even for bug fixes, verify:
- [ ] Fix is in the correct layer
- [ ] Aggregate invariants still hold
- [ ] Domain Events still fire correctly
- [ ] Tests updated to cover the fix
- [ ] No business logic leaked to wrong layer

**→ Phase Gate: Step 4 → Step 5**

Announce to developer:
> "Implementation appears complete. Ready to update documentation (spec, models.md, rules.md, events.md, glossary, tech-debt)? `/dflow:next` to proceed."

Wait for confirmation before entering Step 5. This phase gate is where the completion checklist is triggered — do not skip.

## Step 5: Update Documentation

Triggered by the Step 4 → Step 5 Phase Gate. AI runs the completion checklist in the order below; do **not** skip a section. For bug-fix mode with lightweight-spec, items marked `(full spec only)` are skipped.

### 5.1 Verification — AI runs independently

Items marked *(post-5.3)* are re-verified after the documentation merge in 5.3 lands:

- [ ] Every ADDED / MODIFIED / REMOVED / RENAMED entry in the Delta section is covered by implementation or tests
- [ ] The fix is in the correct layer (Domain / Application / Infrastructure / Presentation)
- [ ] Domain layer project has **no** external NuGet dependencies (check `*.Domain.csproj`)
- [ ] Aggregate invariants still hold; state changes go through methods
- [ ] Any new / changed Domain Events are raised in the implementation
- [ ] EF Core configuration uses Fluent API only (no attributes on Domain entities)
- [ ] `實作任務` section: all tasks checked, or unchecked items explicitly labelled as follow-up *(full spec only)*
- [ ] *(post-5.3)* `specs/domain/{context}/behavior.md` has a section anchor for every `BR-*` in ADDED / MODIFIED entries; REMOVED entries' anchors have been deleted (mechanical input for `/dflow:verify`)
- [ ] *(post-5.3)* `specs/domain/{context}/behavior.md` `last-updated` is later than this spec's `created` date (mechanical drift guard)

If any item fails, report the gap and pause — don't proceed to 5.2.

### 5.2 Verification — needs developer confirmation

- [ ] Does the fix faithfully express the **intent** of the Delta entries? (AI lists delta → impl location; developer judges fit)
- [ ] Is the Aggregate boundary still correct after this change (especially for MODIFIED / RENAMED entries)?
- [ ] Are Domain Event payloads and handler placements still correct?
- [ ] Did we miss any tech debt worth recording?
- [ ] Do the scenarios merged into `behavior.md` (incl. Aggregate transitions + Events) faithfully express the Delta's final-state behavior? (AI lists updated anchors; developer judges)
- [ ] Should the `實作任務` section in the spec be collapsed / removed now that it's complete? *(full spec only — team convention)*

Ask these one-by-one.

### 5.3 Documentation updates

- [ ] Update or create the feature / bug spec; set `status: completed`
- [ ] `specs/domain/{context}/models.md` — Aggregate structure updates
- [ ] `specs/domain/{context}/rules.md` — business rule updates
- [ ] `specs/domain/{context}/events.md` — Domain Event updates
- [ ] `specs/domain/glossary.md` — new / renamed terms (mirror any RENAMED delta entries here)
- [ ] `specs/domain/{context}/behavior.md` — update scenarios to reflect Delta result (merge final state, not Delta markup). Sub-steps:
      - Promote any Phase 3 draft sections (from B3 mid-sync) to formal sections
      - Update the corresponding `rules.md` anchor's `last-updated` date (B4)
- [ ] `behavior.md` draft cleanup — if the Delta was abandoned mid-way, keep the `## 提案中變更` section's history or explicitly REMOVE it
- [ ] `specs/domain/context-map.md` — updated if cross-context interaction changed
- [ ] `specs/architecture/tech-debt.md` — findings recorded

### 5.4 Archival

- [ ] Spec file moved to `specs/features/completed/` (if a spec was opened in this flow)

Only announce "change complete" after 5.4 is done.
