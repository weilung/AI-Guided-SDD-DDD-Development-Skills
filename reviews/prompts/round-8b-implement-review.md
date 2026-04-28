# Round 8b Implement Review Prompt — 4 Sub-waves 實作品質審查（給 Cursor 使用）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Cursor。
> **產出位置**：Reviewer 寫入 `reviews/round-8b-implement-review-report.md`。
> **禁止事項**：本輪**不修改任何檔**（proposal、skill 檔、CHANGELOG、planning 檔皆不動），只產出 review 報告。
> **R8b Implement Review 的本質**：審 **R8b 4 sub-waves 實施產物 vs approved P013 意圖** 的忠誠度（仿 R1-R6 / R7 「意圖 vs 實作」模式），加上 **跨 sub-wave 一致性** 與 **4 項設計決策執行品質**。
> **與 R8a Implement Review 的差異**：R8a 是單一 wave / 10 檔 / 30-60 分鐘；R8b 是 4 sub-waves / ~55 檔次 / 4 項新概念落地，預期 90-150 分鐘、Finding 8-25 項。
> **模型選擇**：建議使用 frontier 模型（範圍大 + 設計決策密度高）。**Cursor + Claude Opus 4.7**（與 R8a Implement Review reviewer 一致，有 R8a 上下文連續性）或 **Cursor + Codex 5.3 Extra High**（與 R8b implementer 一致，熟悉 implement 細節）皆可。

---BEGIN PROMPT---

你是 Dflow Round 8b Implement Review 的審查者。本輪範圍為 **R8b 4 個 sub-wave 實施產物 vs approved PROPOSAL-013 意圖** 的對照審查（仿 R1-R6 / R7 「意圖 vs 實作」模式），加上 sub-wave 之間的 cascade 一致性與 4 項設計決策的執行品質檢查。

## R8b Implement Review 與其他輪次的差異

| 面向 | R8a Implement Review | R7 Implement Review | R8b Implement Review（本輪）|
|---|---|---|---|
| 範圍 | 單一 wave / 10 檔 | 3 waves / ~25 檔 | 4 sub-waves / ~55 檔次 |
| Finding 預估 | 2-8 | 10-30 | 8-25 |
| 設計決策密度 | 低 | 中 | 高（4 項新概念）|
| 跨 wave 一致性檢查重點 | 無（單 wave）| 跨 3 wave | 跨 4 sub-wave + cascade chain |
| 預估時間 | 30-60 分鐘 | 90-120 分鐘 | 90-150 分鐘 |

## 第一步：讀入必要檔案

### A. 實施權威來源（必讀）

1. `proposals/PROPOSAL-013-system-document-template-coverage.md`（approved；藍圖。特別 §1 canonical English structural language + Anchor naming rules + Design intent；§5 mandatory baseline 修補；§6 references 修改；§7 lightweight Implementation Tasks）

### B. R8b context（finding 解析追溯）

2. `reviews/round-8b-decisions.md`（17 個 finding 的 path 定案；4 項設計決策拍板紀錄；既有機制相容性確認紀錄；R8b Implement sub-wave 切分建議）
3. `reviews/round-8b-report.md`（Cursor 原始 finding，作為背景）

### C. 實施紀錄（了解 sub-wave 邊界）

4. `CHANGELOG.md` 最上方一段：`## 2026-04-28 — R8b 實施：PROPOSAL-013 ...` —— 4 個 sub-wave 全程實施摘要，是**自述**，需要 reviewer 用實際檔案 verify

### D. 流程規範

5. `proposals/review-workflow.md`（§三 Review 報告格式 + §五 輪次執行順序）

### E. 既有 P001-P012 既有機制檢查（必讀，重點檢查 P013 實施是否破壞既有設計）

不需全讀；focus 在被 R8b 觸碰的段落：

6. `proposals/PROPOSAL-003-*.md`（rules.md / behavior.md source-of-truth；R8b decisions C-01 anchor coexistence）
7. `proposals/PROPOSAL-004-*.md`（lightweight-spec 設計理念；R8b decisions C-02 task guard）
8. `proposals/PROPOSAL-008-*.md`（drift-verification；R8b decisions C-03 anchor coexistence）
9. `proposals/PROPOSAL-009-feature-directory-and-phases.md`（特別 § H Lightweight Changes vs R8b decisions C-04）
10. `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md`（mandatory baseline；R8b decisions C-06）
11. `proposals/PROPOSAL-012-distribution-friendly-paths.md`（路徑前提；R8b decisions C-08）

### F. R8b Sub-wave 切分對應 commit chain（必須 verify 接續無漏）

```
12f552e  Round 8b Sub-wave 4 COMPLETE  (lightweight Implementation Tasks + task guard 補完 + CHANGELOG)
61c0df4  Round 8b Sub-wave 3 COMPLETE  (references cascade + SKILL.md cascade)
2aaba8b  Round 8b Sub-wave 2 COMPLETE  (scaffolding canonical English + GLOSSARY +3)
0cd99fb  Round 8b Sub-wave 2 PARTIAL    (templates 全部完成，scaffolding 僅 _conventions.md)
62640c7  Round 8b Sub-wave 1 COMPLETE  (新增 14 檔 templates / scaffolding / 維護契約)
```

### G. 實施產物（依 R8b 4 sub-wave 範圍核對；不需全讀，依 P013 §影響範圍 + decisions 重點抽樣）

**Sub-wave 1 新增（14 檔）**：
- `<repo root>/TEMPLATE-COVERAGE.md`（重點：14 列 matrix + Section anchors 欄）
- `<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md`（13 個 canonical English term）
- 雙版 `templates/glossary.md` / `models.md` / `rules.md` / `context-map.md` / `tech-debt.md`（共 10 檔）
- `sdd-ddd-core-skill/templates/events.md`
- `sdd-ddd-core-skill/scaffolding/architecture-decisions-README.md`

**Sub-wave 2 修改（既有 templates / scaffolding canonical English + anchors，~17 檔次）**：
- 雙版 `templates/_index.md` / `phase-spec.md` / `lightweight-spec.md` / `behavior.md` / `context-definition.md` / `CLAUDE.md`
- `sdd-ddd-core-skill/templates/aggregate-design.md`
- 雙版 `scaffolding/_overview.md`（檢查級別 verify pass）/ `_conventions.md` / `Git-principles-{gitflow,trunk}.md` / `CLAUDE-md-snippet.md`
- `<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md`（+3 term：Feature Goal / Related BR-IDs / Phase Count）

**Sub-wave 3 修改（references cascade + SKILL.md，19 檔）**：
- 雙版 `references/init-project-flow.md` / `new-feature-flow.md` / `modify-existing-flow.md` / `finish-feature-flow.md` / `drift-verification.md` / `pr-review-checklist.md` / `git-integration.md` / `new-phase-flow.md`
- `sdd-ddd-core-skill/references/ddd-modeling-guide.md`
- 雙版 `SKILL.md`

**Sub-wave 4 修改（5 檔）**：
- 雙版 `templates/lightweight-spec.md`（補 Implementation Tasks 段內容）
- 雙版 `references/modify-existing-flow.md`（補 bug-fix path task 確認）
- `CHANGELOG.md`（新增 R8b 實施段）

不需精讀每檔——對 templates / scaffolding / references 抽樣，對維護契約檔（TEMPLATE-COVERAGE / TEMPLATE-LANGUAGE-GLOSSARY）細讀，對 SKILL.md cascade 重點 verify。

### H. 不要讀的東西

- 任何繁中版檔（`sdd-ddd-*-skill-tw/`）— 繁中版同步延到 Closeout C1
- `tutorial/` 目錄
- R1-R7 的歷史 review 報告
- `E:\工作區\SDD-Starter-Kit\`（V2）— 與本輪實施品質無關
- `reviews/round-8a-*.md`（R8a 已收尾，本輪不重審）

## 第二步：審查維度（6 個 lens）

### Dimension 1 — 對 PROPOSAL-013 §影響範圍 + R8b decisions 的忠誠度（每 sub-wave 核對）

對每 sub-wave 範圍內每個檔案，verify：

- **是否真的被修改**（git diff vs commit `c9480f8` 之前的狀態應顯示變動）
- **修改範圍是否符合 P013 §影響範圍 描述 + R8b decisions 對應 finding**
- **是否有未列入範圍的檔案被改動**（合理連帶 vs 越界，需 reviewer 判斷）

特別注意：

- **Sub-wave 1**: 14 檔皆需存在 + 含 Dflow marker；TEMPLATE-COVERAGE.md 應有 14 rows + Section anchors 欄；TEMPLATE-LANGUAGE-GLOSSARY.md 應有 ≥10 個初始 term
- **Sub-wave 2**: scaffolding/_overview.md 雙版若 verify pass 沒動是合理的（per resume prompt §3.1(c)）；scaffolding/_conventions.md 雙版若已英文化也是合理的；anchor 注入應限定 templates 層、不在 scaffolding
- **Sub-wave 3**: references/git-integration.md 應加 traceability comment「`<!-- R8b verified: no Chinese structural terms in scope; per F-17 Path A. -->`」；新-phase-flow.md 是 conditional cascade（spec 預期外，但合理）
- **Sub-wave 4**: lightweight-spec.md Implementation Tasks 段 layer tags 必須對版本（WebForms `DOMAIN/PAGE/DATA/TEST/DOC`；Core `DOMAIN/APP/INFRA/API/TEST/DOC`）

### Dimension 2 — 跨 Sub-wave Cascade 一致性（本輪重點）

R8b 4 sub-wave 之間有強 cascade 依賴：Sub-wave 1 新增 templates → Sub-wave 2 為這些 templates 加 anchor + 為既有 templates 改英文 → Sub-wave 3 references / SKILL.md 引用兩波結果 → Sub-wave 4 lightweight Implementation Tasks 對齊 _index.md heading。

逐項檢查：

**CW-01 · Sub-wave 1 新 templates ↔ Sub-wave 3 SKILL.md cascade**：
- Sub-wave 1 新增 12 templates（雙版各 5 + Core 2）。Sub-wave 3 SKILL.md Templates 表是否完整列出？是否漏列？

**CW-02 · Sub-wave 1 新 templates ↔ Sub-wave 3 references cascade**：
- references/init-project-flow.md 應加「← templates/<xxx>.md」cascade source 註記指向 Sub-wave 1 新 templates；對照 P013 §5 baseline 比對表，是否所有 mandatory baseline 都有來源指向？

**CW-03 · Sub-wave 2 anchor 注入 ↔ Sub-wave 4 lightweight 引用**：
- Sub-wave 2 在 lightweight-spec.md 注入 `<!-- dflow:section implementation-tasks -->` anchor；Sub-wave 4 在該 anchor 之下補 Implementation Tasks 段內容。Verify anchor 與內容兩者**緊接**（無夾雜其他段落）

**CW-04 · Sub-wave 2 canonical English heading ↔ Sub-wave 3 references 引用**：
- Sub-wave 2 將 _index.md / phase-spec.md 等 templates 的 H2/H3 改英文；Sub-wave 3 references / SKILL.md 內所有對這些 section name 的引用是否同步 cascade？特別檢查：
  - `_index.md` 的「目標與範圍」→ `Goals & Scope`、「接續入口」→ `Resume Pointer`、「輕量修改紀錄」→ `Lightweight Changes`、「行為變更（Delta）」→ `Behavior Delta`
  - `phase-spec.md` 的「實作任務」→ `Implementation Tasks`、「行為規格」→ `Behavior Scenarios`、「業務規則」→ `Business Rules`

**CW-05 · Sub-wave 2 GLOSSARY +3 term ↔ Sub-wave 3 references 使用**：
- Sub-wave 2 新增 `Feature Goal` / `Related BR-IDs` / `Phase Count`；Sub-wave 3 finish-feature-flow.md Step 5 Integration Summary 段是否使用這些 canonical labels？

**CW-06 · Sub-wave 4 Implementation Tasks DOC-1 ↔ Sub-wave 2 _index.md heading**：
- Sub-wave 4 lightweight-spec.md Implementation Tasks 段 DOC-1 提到「Update `_index.md` Lightweight Changes and Current BR Snapshot」；verify 這兩個 heading 確實在 Sub-wave 2 已英文化的 _index.md 上**完全一致**（per F-12）

**CW-07 · TEMPLATE-COVERAGE.md ↔ 實際 templates / scaffolding 對齊**：
- TEMPLATE-COVERAGE.md 14 rows 列出的每個 template 是否實際存在於 templates/ 或 scaffolding/？反之，所有 templates / scaffolding 是否都被 COVERAGE 矩陣涵蓋？

**CW-08 · TEMPLATE-LANGUAGE-GLOSSARY.md ↔ 實際 templates 使用**：
- GLOSSARY 13 term 是否實際出現於 templates / scaffolding（作為 heading 或 label）？是否有 templates 使用了 canonical English term 但 GLOSSARY 未列入？

### Dimension 3 — R8b Approve 決議落實確認（17 findings 全程 verify）

對 R8b Decisions 中的 17 個 finding（11 accept + 6 accept-with-choice）逐一 verify：

特別重點：

- **F-01 (Path A) Canonical English on templates/CLAUDE.md**：雙版 templates/CLAUDE.md 是否全 H2/H3 英文化？
- **F-02 §4 矩陣 14 rows**：實際 TEMPLATE-COVERAGE.md 是否 14 rows？
- **F-04 finish-feature-flow Integration Summary labels**：是否含 Feature Goal / Change Scope / Related BR-IDs / Phase Count / Lightweight Changes / Phase List / Next Steps (developer)？
- **F-05 SKILL.md Templates 表 + COVERAGE / GLOSSARY 介紹段**：是否實際加上？
- **F-06 (Path A) anchor 欄併入 COVERAGE.md**：COVERAGE.md 是否含 Section anchors 欄（per Path A 不另開 SECTION-ANCHORS.md）？
- **F-07 (Path A) §5 baseline 比對表**：P013 是否含 P010 既有 baseline + P013 新增 README.md 對照？
- **F-08 Anchor coexistence rule**：drift-verification.md 是否新增 Anchor coexistence with `dflow:section` 段？
- **F-11 (Path A) COVERAGE / GLOSSARY 放 repo 根**：實際路徑 ✓
- **F-12 §1.1 glossary 補 3 term + §7 加註指向 §1.1**：GLOSSARY 含 Lightweight Changes / Resume Pointer / Behavior Delta + lightweight-spec.md DOC-1 對齊？
- **F-13 ddd-modeling-guide.md 補 context-map.md 缺檔處理**：Core 唯一 ddd-modeling-guide.md 是否新增此段？
- **F-14 Git-principles 4 列 Integration Commit Message Conventions 段精化**：雙版 Git-principles-{gitflow,trunk}.md 是否含 Feature Goal / Change Scope / Related BR-IDs / Phase Count / Lightweight Changes 等 canonical labels？
- **F-15 (Path B) Anchor naming rules + Design intent**：P013 §1 是否含此兩段？
- **F-17 (Path A) git-integration verify**：雙版 git-integration.md 檔尾是否加 R8b verified traceability comment？

### Dimension 4 — 既有機制相容性 Regression Check（C-01 ~ C-08）

R8b 動到 BC layer docs / drift-verification / lightweight-spec / init-project，這些都是 P003-P012 已建立的機制。逐一 verify R8b 實施未破壞既有機制：

**C-01 · P003 rules.md / behavior.md source-of-truth**：drift-verification.md 新增 anchor coexistence 段是否明示 `dflow:section` HTML comment anchor 不取代 BR-ID + markdown auto-id？P003 BR-ID 結構是否保持？

**C-02 · P004 lightweight-spec 設計理念**：Sub-wave 4 補的 Implementation Tasks 段是否保留 lightweight 精神（含「If the fix scope starts to expand, AI should pause and ask the developer」guard）？

**C-03 · P008 drift-verification**：機械驗證演算法是否仍依 markdown auto-id？dflow:section 是否確實作為輔助而非取代？

**C-04 · P009 § H Lightweight Changes vs P013 § 7 Implementation Tasks**：兩者是否仍是不同層級協作（feature 層 vs spec 層）？lightweight-spec.md DOC-1 是否引用 _index.md Lightweight Changes（cross-reference 而非重複）？

**C-05 · P009 _index.md 設計**：6 個 _index.md sections 是否仍存在（只是 heading 改英文）？順序 / 必要性是否未變？

**C-06 · P010 mandatory baseline 一致性**：雙版 init-project-flow.md baseline 清單是否仍是 P010 既有 4 項 + 新增 README.md（per F-07）？是否未新增其他 baseline 項目？

**C-07 · P010 scaffolding 邊界**：glossary.md 是否從 scaffolding 重分類到 templates（per P013 §1）？scaffolding 是否仍維持「init 寫入後由專案持有」定位？

**C-08 · P012 路徑使用 post-shared/**：R8b 實施過程是否一致使用 `specs/shared/`（無殘留 `specs/_共用/`）？

### Dimension 5 — 4 項新概念設計決策執行品質

P013 引入 4 項新概念，逐項評估執行品質（不只「有做」，而是「做得對」）：

**D5-01 · Canonical English structural language**：
- 是否區分清楚「結構性術語」（H2/H3 / 固定 labels）vs「使用者可填寫內容」（自然語言段落）？
- 是否有過度英文化（連自然語言段落都改英文）的失誤？
- 雙版命名是否一致（無 WebForms 用 X、Core 用 Y 但語意相同的不一致）？

**D5-02 · `<!-- dflow:section X -->` anchor 機制**：
- 6 個初始 anchor 是否實際注入到對應 templates？
- anchor namespace（per P013 §1 Anchor naming rules）是否一致用？
- Anchor coexistence rule（不取代 markdown auto-id）是否實作正確？
- scaffolding 層是否**未**加 anchor（只應在 templates）？

**D5-03 · TEMPLATE-COVERAGE.md 維護契約**：
- 14 rows 涵蓋 + Section anchors 欄存在
- 矩陣描述是否與實際 template 內容對齊（無 drift）？
- 雙版差異欄（Allowed differences）是否準確？

**D5-04 · TEMPLATE-LANGUAGE-GLOSSARY.md**：
- 13 term 是否每個都有實際使用位置（不是空理論）？
- 中英對照是否準確？
- 放 repo 根而非 skill 內（per F-11 Path A）？

### Dimension 6 — 邊界守住（與 backlog / 繁中 / Sub-wave 範圍）

- 是否未踩進 backlog § 1（content language strategy 內容語言改寫）？
- 是否未踩進 backlog § 2（npm / CLI）？
- 是否未踩進 backlog § 6（根目錄改名 specs/ → dflow/）？
- 是否未動繁中版（`*-tw/`）？
- 是否未動 tutorial / proposals / reviews（除 cross-round-notes 在 R8a Implement Review Fix 加段外）？
- 各 sub-wave 之間是否守住自己的範圍邊界（Sub-wave 1 純新增 / Sub-wave 2 不動 references / Sub-wave 3 不動 templates / Sub-wave 4 不動 SKILL.md）？

## 第三步：Finding 格式

每個 Finding 採以下格式：

```markdown
### F-NN {一句話問題摘要}

**嚴重度**: Critical | Major | Minor | Question
**對照**: {擇一或混合標示}
  - 實施 vs P013：`PROPOSAL-013 § 段落名` ↔ `{實際檔案路徑}:{行號}`
  - 實施 vs R8b Decisions：`reviews/round-8b-decisions.md F-NN Path X` ↔ `{實際檔案路徑}:{行號}`
  - 跨 Sub-wave cascade：`Sub-wave A 段` ↔ `Sub-wave B 段` (CW-NN)
  - Regression：`PROPOSAL-0XX 段` ↔ `{實際檔案路徑}:{行號}` (C-NN)
  - 設計決策：`PROPOSAL-013 §1 / R8b decisions §設計決策拍板紀錄` ↔ `{實際}` (D5-NN)
**維度**: Dimension 1 / 2 / 3 / 4 / 5 / 6（單選）
**現況**: {實際讀到什麼}
**問題**: {為什麼是問題 —— 實施缺漏 / 違背 approve 決議 / cascade 漏網 / regression / 設計品質 / 邊界越界}
**建議**: {具體怎麼修；若 Question 類型則寫「需釐清：...」}
```

### 嚴重度定義（沿用 R7 / R8a 慣例）

- **Critical**：實施根本違背 P013 意圖，會破壞 P003-P012 既有機制。**嚴格使用，不濫用**。
- **Major**：影響範圍漏改、approve 決議違背、跨 sub-wave cascade 漏網、regression 風險、設計決策執行品質低。實施再修。
- **Minor**：措辭不夠精確、無關行的順帶修改、可讀性問題。
- **Question**：Reviewer 不確定實施意圖或 P013 意圖，需設計者釐清。

## 第四步：產出格式

寫入 `reviews/round-8b-implement-review-report.md`：

```markdown
# Round 8b Implement Review Report — 4 Sub-waves 實作品質審查

**審查範圍**: PROPOSAL-013 實施產物（commit chain 62640c7 → 0cd99fb → 2aaba8b → 61c0df4 → 12f552e）
**審查類型**: Implement review（意圖 vs 實作對照 + 跨 sub-wave cascade + 設計決策執行品質）
**審查日期**: YYYY-MM-DD
**審查者**: {Cursor 模型名稱}

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| {總數} | {數} | {數} | {數} | {數} |

**建議下一步**: clean / minor-fix / requires-revision

## Findings

### F-01 ...

（按嚴重度排序：Critical → Major → Minor → Question）

## Sub-wave 實施對照表（Dimension 1）

對每 sub-wave 實施範圍 verify：

### Sub-wave 1 (commit 62640c7 — 純新增 14 檔)

| 檔案 | 預期狀態 | 實際狀態 | 一致？ | Finding |
|---|---|---|---|---|
| `<repo root>/TEMPLATE-COVERAGE.md` | 14 rows + Section anchors 欄 | | ✓ / ✗ / ⚠ | |
| `<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md` | ≥10 initial term | | | |
| 雙版 `templates/glossary.md` 等 5 種 ×2 | 含 Dflow marker | | | |
| `core/templates/events.md` | 含 Dflow marker | | | |
| `core/scaffolding/architecture-decisions-README.md` | 含 Dflow marker | | | |

### Sub-wave 2 (commit 0cd99fb + 2aaba8b — 既有 templates / scaffolding canonical English + anchors + GLOSSARY +3)

| 檔案 | 預期狀態 | 實際狀態 | 一致？ | Finding |
|---|---|---|---|---|
| 雙版 `templates/{_index,phase-spec,lightweight-spec,behavior,context-definition,CLAUDE}.md` | H2/H3 全英；對應 anchor 注入 | | | |
| `core/templates/aggregate-design.md` | H2/H3 全英 | | | |
| 雙版 `scaffolding/{_overview,_conventions}.md` | 已英文化（檢查級別）| | | |
| 雙版 `scaffolding/Git-principles-{gitflow,trunk}.md` | Integration Commit labels 英文 | | | |
| 雙版 `scaffolding/CLAUDE-md-snippet.md` | H2/H3 英文化 | | | |
| `<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md` | +3 term (Feature Goal / Related BR-IDs / Phase Count) | | | |

### Sub-wave 3 (commit 61c0df4 — references cascade + SKILL.md cascade)

| 檔案 | 預期狀態 | 實際狀態 | 一致？ | Finding |
|---|---|---|---|---|
| 雙版 `references/init-project-flow.md` | baseline 加 cascade source 註記 | | | |
| 雙版 `references/new-feature-flow.md` | 缺 domain docs 用 template 建立 | | | |
| 雙版 `references/modify-existing-flow.md` | task guard cascade | | | |
| 雙版 `references/finish-feature-flow.md` | Step 5 Integration Summary canonical labels | | | |
| 雙版 `references/drift-verification.md` | Anchor coexistence 段 | | | |
| 雙版 `references/pr-review-checklist.md` | section name cascade | | | |
| 雙版 `references/git-integration.md` | F-17 traceability comment | | | |
| 雙版 `references/new-phase-flow.md` | conditional cascade | | | |
| `core/references/ddd-modeling-guide.md` | context-map.md 缺檔處理 | | | |
| 雙版 `SKILL.md` | Templates 表新增 + COVERAGE/GLOSSARY 段 | | | |

### Sub-wave 4 (commit 12f552e — lightweight Implementation Tasks + task guard 補完 + CHANGELOG)

| 檔案 | 預期狀態 | 實際狀態 | 一致？ | Finding |
|---|---|---|---|---|
| 雙版 `templates/lightweight-spec.md` | Implementation Tasks 段內容 + 對應 layer tags | | | |
| 雙版 `references/modify-existing-flow.md` | bug-fix path 補 task 確認 | | | |
| `CHANGELOG.md` | R8b 實施段（4 sub-wave 涵蓋）| | | |

## 跨 Sub-wave Cascade 對照表（Dimension 2）

| 檢查點 | 實施狀態 | 一致？ | 對應 Finding |
|---|---|---|---|
| CW-01 · Sub-wave 1 新 templates ↔ Sub-wave 3 SKILL.md cascade | | ✓ / ✗ / ⚠ | |
| CW-02 · Sub-wave 1 新 templates ↔ Sub-wave 3 references cascade | | | |
| CW-03 · Sub-wave 2 anchor ↔ Sub-wave 4 lightweight 引用緊接 | | | |
| CW-04 · Sub-wave 2 canonical English heading ↔ Sub-wave 3 references 引用 cascade | | | |
| CW-05 · Sub-wave 2 GLOSSARY +3 ↔ Sub-wave 3 finish-feature-flow Integration Summary | | | |
| CW-06 · Sub-wave 4 DOC-1 ↔ Sub-wave 2 _index.md heading | | | |
| CW-07 · TEMPLATE-COVERAGE 14 rows ↔ 實際 templates / scaffolding | | | |
| CW-08 · TEMPLATE-LANGUAGE-GLOSSARY 13 term ↔ 實際 templates 使用 | | | |

## R8b Approve 決議落實對照表（Dimension 3）

| Finding | 決議類型 | 預期效果 | 實施狀態 | 一致？ | 對應 Implement Review Finding |
|---|---|---|---|---|---|
| F-01 (Path A) | accept-with-choice | 雙版 templates/CLAUDE.md 全 H2/H3 英文化 | | ✓ / ✗ / ⚠ | |
| F-02 | accept | §4 矩陣 14 rows | | | |
| F-03 | accept | pr-review-checklist 雙版 cascade | | | |
| F-04 | accept | finish-feature-flow Step 5 canonical English | | | |
| F-05 | accept | SKILL.md cascade | | | |
| F-06 (Path A) | accept-with-choice | anchor 欄併入 COVERAGE.md | | | |
| F-07 (Path A) | accept-with-choice | §5 baseline 比對表 | | | |
| F-08 | accept | Anchor coexistence rule | | | |
| F-09 | accept | §不納入影響範圍 加註 | | | |
| F-10 | accept | backlog § 1 接管邊界明示 | | | |
| F-11 (Path A) | accept-with-choice | COVERAGE / GLOSSARY 放 repo 根 | | | |
| F-12 | accept | glossary +3 + §7 指向 §1.1 | | | |
| F-13 | accept | ddd-modeling-guide context-map 缺檔 | | | |
| F-14 | accept | Git-principles labels 精化 | | | |
| F-15 (Path B) | accept-with-choice | Anchor naming rules + Design intent | | | |
| F-16 | accept | F-07 Path A 涵蓋 | | | |
| F-17 (Path A) | accept-with-choice | git-integration traceability comment | | | |

## 既有機制相容性對照表（Dimension 4）

| C-NN | 檢查點 | 實施結論 | 對應 Finding |
|---|---|---|---|
| C-01 · P003 rules / behavior anchor | ✓ / ⚠ / ✗ | | |
| C-02 · P004 lightweight task guard | | | |
| C-03 · P008 drift-verification anchor | | | |
| C-04 · P009 § H Lightweight Changes vs § 7 Implementation Tasks 雙層紀錄 | | | |
| C-05 · P009 _index.md 6 sections 仍存在 | | | |
| C-06 · P010 mandatory baseline 一致性 | | | |
| C-07 · P010 scaffolding 邊界 | | | |
| C-08 · P012 路徑使用 specs/shared/（無 _共用 殘留） | | | |

## 設計決策執行品質對照（Dimension 5）

| 決策 | 執行品質 | 主要觀察 |
|---|---|---|
| D5-01 · Canonical English structural language | ✓ / ⚠ / ✗ | |
| D5-02 · dflow:section anchor 機制 | | |
| D5-03 · TEMPLATE-COVERAGE.md 維護契約 | | |
| D5-04 · TEMPLATE-LANGUAGE-GLOSSARY.md | | |

## 邊界守住對照（Dimension 6）

| 邊界 | 實施結果 | 對應 Finding |
|---|---|---|
| 未踩進 backlog § 1（content language）| ✓ / ✗ | |
| 未踩進 backlog § 2（npm / CLI）| | |
| 未踩進 backlog § 6（根目錄改名）| | |
| 未動繁中版（`*-tw/`）| | |
| 未動 tutorial / proposals / reviews | | |
| Sub-wave 之間範圍邊界守住 | | |

## 審查備註

- （任何需要補充的觀察、審查流程中的限制等）

---

**跨輪議題**：若審查過程順手看到其他 proposal（P001-P012）或其他議題的問題，記入 `reviews/cross-round-notes.md`，**不佔本輪 Finding 數量**。
```

## 第五步：重要規則

- **不要修改任何檔案**。只產出 `reviews/round-8b-implement-review-report.md`。
- **不要 review R8a / P012**。R8a 已完整收尾（含 Implement Review Fix），本輪不重審。
- **不要比對英文 skill vs 繁中 skill**。繁中延到 Closeout C1。
- **取樣即可**。對 4 sub-wave 共 ~55 檔次，不需精讀全文；按 Dimension 1 表格逐 sub-wave 抽樣 verify，重點檔（COVERAGE / GLOSSARY / SKILL.md / drift-verification.md / lightweight-spec.md）細讀，其他 spot-check
- **跨 Sub-wave 一致性是本輪重點**。Dimension 2 的 8 個 CW 檢查點每一項都要在對照表給出 ✓ / ✗ / ⚠ + 理由
- **設計決策若有疑慮，優先標 Question**。Dimension 5 的 4 項新概念若 reviewer 認為「執行偏離方向但不確定」，標 Question 讓設計者釐清
- **嚴重度從嚴**：Critical 保留給「破壞既有 P003-P012 機制 / 違背 P013 核心意圖」級別
- **`_共用` 全域 grep 殘留檢查**：`grep _共用 sdd-ddd-{webforms,core}-skill/` 預期 0；若有殘留列為 Critical（破壞 P012 落地）
- **預估時間**：~55 檔次取樣 verify + 3 個對照表填寫，預期 90-150 分鐘；若超過 3 小時建議分 2 段（第一段 Sub-wave 1+2 + Dimension 1+5；第二段 Sub-wave 3+4 + Dimension 2+3+4+6）

開始審查。完成後只產出 `reviews/round-8b-implement-review-report.md`。

---END PROMPT---
