# Round 2 Decisions — 變更描述格式

**對應 review 報告**: `reviews/round-2-report.md`
**決議日期**: 2026-04-20
**決議者**: 使用者（Claude 整理）
**審查範圍**: P002（Delta Spec 變更描述格式）、P004（Spec 模板重構 — 精簡版）— 僅英文 Skill

## 決議彙總

| Finding | 嚴重度 | 決議 | 說明 |
|---|---|---|---|
| F-01 | Major | **accept** | 兩版 `new-feature-flow.md` Step 8.1 的「實作任務」驗證未依 PROPOSAL-004 實施紀錄「加入第一項」的位置語移到清單首位；照建議將 tasks 驗證提為 Step 8.1 第一項，其餘項相對順序不動。 |

**統計**：accept 1 / reject 0 / defer 0 / clarify 0 / out-of-scope 0。

---

## Accept 項目的實作指引（交給 Implement session）

### F-01 — Step 8.1 實作任務驗證提為第一項

**背景**（讓 Implement session 不必回翻 review 報告）：

- PROPOSAL-004:112 實施紀錄明寫：「Step 8（Completion）**加入第一項**：驗證『實作任務』段全部勾選或未勾選項標註 follow-up。」
- 這句與上一行「Step 5 **末尾**加入『Generate Implementation Tasks List』子段」形成位置語平行結構（末尾 / 第一項皆為位置指示）。
- 現況：WebForms 版 tasks 驗證落在 Step 8.1 第 5 項、Core 版落在第 8 項，皆未放第一項，與實施紀錄自述不一致。
- 採用 Option A（fail-fast 精神）：tasks 清單作為「計畫是否跑完」的 meta-check，未勾齊就擋住，不讓 AI 繼續後面的 BR/EC/GWT 細比對。符合 P004 評估紀錄「實作任務段是本 proposal 另一核心目的」的定位。

**修改檔案 1**：`sdd-ddd-webforms-skill/references/new-feature-flow.md`

現況 Step 8.1（`line 220–226`）條列順序：

1. Every `Given/When/Then` scenario ...
2. Every `BR-*` business rule ...
3. Every `EC-*` edge case ...
4. Domain layer has **no** `System.Web` references ...
5. **`實作任務` section: all tasks checked, or unchecked items explicitly labelled as follow-up**
6. *(post-8.3)* `specs/domain/{context}/behavior.md` contains a section anchor for every `BR-*` ...
7. *(post-8.3)* `specs/domain/{context}/behavior.md` `last-updated` is later than this spec's `created` date ...

**具體改動**：把第 5 項（`line 224`）整行移到第 1 項位置（原 `line 220` 之前）。移動後順序為：

1. **`實作任務` section: all tasks checked, or unchecked items explicitly labelled as follow-up (linked to spec / tech-debt entry)**
2. Every `Given/When/Then` scenario in the spec is covered by implementation or tests
3. Every `BR-*` business rule is covered by implementation or tests
4. Every `EC-*` edge case is handled
5. Domain layer has **no** `System.Web` references (grep `src/Domain/`)
6. *(post-8.3)* `specs/domain/{context}/behavior.md` contains a section anchor for every `BR-*` introduced by this spec (mechanical input for `/dflow:verify`)
7. *(post-8.3)* `specs/domain/{context}/behavior.md` `last-updated` is later than this spec's `created` date (mechanical drift guard)

---

**修改檔案 2**：`sdd-ddd-core-skill/references/new-feature-flow.md`

現況 Step 8.1（`line 232–241`）條列順序：

1. Every `Given/When/Then` scenario ...
2. Every `BR-*` business rule ...
3. Every `EC-*` edge case ...
4. Every Domain Event listed in the spec is raised ...
5. Domain layer project has **no** external NuGet dependencies ...
6. Aggregate invariants still hold after the change ...
7. EF Core configuration uses Fluent API only ...
8. **`實作任務` section: all tasks checked, or unchecked items explicitly labelled as follow-up**
9. *(post-8.3)* `specs/domain/{context}/behavior.md` contains a section anchor for every `BR-*` ...
10. *(post-8.3)* `specs/domain/{context}/behavior.md` `last-updated` is later than this spec's `created` date ...

**具體改動**：把第 8 項（`line 239`）整行移到第 1 項位置（原 `line 232` 之前）。移動後順序為：

1. **`實作任務` section: all tasks checked, or unchecked items explicitly labelled as follow-up (linked to spec / tech-debt entry)**
2. Every `Given/When/Then` scenario in the spec is covered by implementation or tests
3. Every `BR-*` business rule is covered by implementation or tests
4. Every `EC-*` edge case is handled
5. Every Domain Event listed in the spec is raised in the implementation
6. Domain layer project has **no** external NuGet dependencies (check `*.Domain.csproj`)
7. Aggregate invariants still hold after the change (all state changes go through methods, no public setters)
8. EF Core configuration uses Fluent API only (no `[Table]`/`[Column]` on Domain entities)
9. *(post-8.3)* `specs/domain/{context}/behavior.md` contains a section anchor for every `BR-*` introduced by this spec (mechanical input for `/dflow:verify`)
10. *(post-8.3)* `specs/domain/{context}/behavior.md` `last-updated` is later than this spec's `created` date (mechanical drift guard)

---

**注意事項**：

- 只調整兩版 `new-feature-flow.md` 的 Step 8.1 順序。**不要**動：
  - Step 8.2 / 8.3 的內容與順序
  - `modify-existing-flow.md` 收尾 step 的 tasks 驗證順序（PROPOSAL-004:115-116 未指定「第一項」，維持現況）
  - `feature-spec.md` 模板中的 `## 實作任務` 段
  - 任何 `(post-8.3)` 標記或其他條目文字
- 其他驗證項保留現有文字（只改位置，不改字句）。
- **不要修改繁中版**（`sdd-ddd-webforms-skill-tw/`、`sdd-ddd-core-skill-tw/`）；繁中版延後至 Post-Review Closeout 階段單向同步。
- Implement session 在修改時請確認「實作任務」驗證條目的實際內文完全照搬現有那一行（包含 `(linked to spec / tech-debt entry)` 括號備註），不要在搬家過程中順手改字。

---

## Reject / Defer 項目備忘

本輪無 reject 或 defer 項目。

---

## Out-of-scope 項目

本輪無越界 Finding。Codex 正確遵守了「僅審英文 Skill」的範圍邊界；繁中版與 tutorial 的問題未被列入本輪 Finding。

---

## 跨輪發現備忘

本輪 approve 階段未新增任何跨輪發現。`reviews/cross-round-notes.md` 維持 R1 期間既有兩項備忘（屬 R4、R5 範圍），本輪未變動該檔。
