# Round 8b Implement Review Decisions — 12 findings 處理 + Fix

**對應 review 報告**: `reviews/round-8b-implement-review-report.md`
**決議日期**: 2026-04-28
**決議者**: 使用者（Claude Opus 4.7 整理 + 當場修訂）
**Reviewer 模型**: Cursor Claude Opus 4.7
**Session 模式**: Approve + Fix 合併 session（仿 R8a Implement Review Fix pattern）

---

## 決議彙總

| Finding | 嚴重度 | 維度 | 決議 | 說明 |
|---|---|---|---|---|
| F-01 | Major | D2 | accept | references new-phase-flow / modify-existing-flow 雙版對 templates 已英文化 column / heading 的中文殘留引用 cascade 補完 |
| F-02 | Major | D2 | accept | Core SKILL.md Scaffolding 表補列 `architecture-decisions-README.md` |
| F-03 | Minor | D1 | accept | CHANGELOG L25 自述 `_overview.md` 措辭與實際 git diff 對齊（雙版實際未動 verify pass） |
| F-04 | Question | D5 | accept-with-choice (DD-A Path A) | inline bold labels (`**目前進展** / **下一個動作**`) 一律英化為 `**Current Progress** / **Next Action**`；補入 GLOSSARY |
| F-05 | Question | D5 | accept-with-choice (DD-B Path A) | GLOSSARY 開頭補 Inclusion Criteria 段（4 條收錄條件 + 3 條排除條件），不擴 term |
| F-06 | Minor | D5 | accept-with-choice (Path A) | 修 P013 §1 anchor 表 line 100（`business-rules` 去掉 `_index.md`）+ 加 Same-section single-anchor design note；同步調整 §1 Namespacing 範例避免內部矛盾 |
| F-07 | Minor | D5 | accept | `templates/lightweight-spec.md` 雙版 H3/H4 改純英；同步 cascade `references/modify-existing-flow.md` 雙版 Behavior Delta 5 段 H3/H4 |
| F-08 | Question | D5 | accept-with-choice (DD-A Path A) | inline bold labels (`**原本** / **改為** / **原因**`) 一律英化為 `**Before** / **After** / **Reason**`；補入 GLOSSARY |
| F-09 | Minor | D2 | accept | `templates/context-definition.md` 雙版 prose 引用 `behavior.md` heading 改為 canonical `Behavior Scenarios`（保留中文 prose 容器）|
| F-10 | Minor | D3 | accept-with-choice (DD-A Path A) | finish-feature-flow Step 5 list items + Core Domain Events 段一律英化（連帶 DD-A Path A 處理）|
| F-11 | Minor | D3 | accept | `templates/CLAUDE.md` 雙版 directory tree comment + Three Ceremony Tiers 段對 `_index.md` H2 中文殘留 cascade（保留中文 prose 容器，只 cascade canonical English term）|
| F-12 | Question | D5 | accept-with-choice (DD-B Path A) | GLOSSARY「使用位置」欄語意釐清併入 DD-B Path A 的 Inclusion Criteria 段末段（檔案級 vs section 級）|

**統計**：accept 6 / accept-with-choice 6 / reject 0 / defer 0 / clarify 0 / out-of-scope 0

---

## 設計議題拍板紀錄

### DD-A · Inline bold labels 歸類（影響 F-04 / F-08 / F-10）

- **拍板 path**: A（一律英化）
- **使用者拍板理由**: 「目前全版英文，主要是要國際化，等這個可以國際化的版本確定後，未來會演化為 npm 安裝的版本，到時候會評估在可以的地方做中文（本地語系）。因此中文要不要改英文的問題，其實是一律要翻譯。」
- **Path 比較**:
  | Path | 描述 | 採用？ |
  |---|---|---|
  | A | 全結構性，inline bold labels 視為 fields，一律英化；GLOSSARY +5 term | ✓ 採用 |
  | B | 全散文，保留中文 + 補 template note 解釋 | 不採用（與國際化路線不符）|
  | C | 混合：bold labels 結構 / list items 範例 | 不採用 |
- **連帶決議**:
  - F-04 → accept-with-choice (DD-A Path A) — `**目前進展** / **下一個動作**` → `**Current Progress** / **Next Action**`
  - F-08 → accept-with-choice (DD-A Path A) — `**原本** / **改為** / **原因**` → `**Before** / **After** / **Reason**`
  - F-10 → accept-with-choice (DD-A Path A) — finish-feature-flow Step 5 list items + Core Domain Events 段一律英化
- **Path A 落實範圍**:
  - **範圍 (1)（嚴格 R8b 範圍內）**: inline bold labels 文字 + finish-feature Step 5 list items + Resume Pointer 範例描述（references 內）
  - **範圍 (2) 不擴**（屬 backlog § 1）: 完整 prose / 註解 / 說明段落（如 templates 內 `> 1-3 段：本 feature 解決什麼問題？`）
- **實施範圍延伸（記錄）**: Path A 實施過程中，Resume Pointer / Behavior Delta inline labels 之後的 placeholder 內容（如 `{一句話}` / `{舊結果}` / `{為什麼改}` 等）也一併英化為 `{one-line summary}` / `{old result}` / `{why this change}` 等。此延伸與「一律翻譯」精神一致，且 templates 內既有英文 placeholder（如 `{YYYY-MM-DD}` / `{phase-slug}`）風格相近，但嚴格說超出 reviewer F-04 / F-08 標的「inline bold labels 本身」邊界；屬 placeholder content（範圍 (2) 邊緣）的低風險擴張。完整 prose 段落仍保留中文（範圍 (2) 不進入）。
- **GLOSSARY 同步擴充**: +5 term（Current Progress / Next Action / Before / After / Reason），全部標註 `per F-04 / F-08 / DD-A Path A`

### DD-B · GLOSSARY 收錄門檻 / 使用位置語意（影響 F-05 / F-12）

- **拍板 path**: A（補 Inclusion Criteria，不擴 term）
- **使用者拍板理由**: 採推薦的 Path A
- **Path 比較**:
  | Path | 描述 | 採用？ |
  |---|---|---|
  | A | 補 Inclusion criteria 段（4 條收錄 + 3 條排除）+ 末段「使用位置」欄語意釐清；GLOSSARY 維持 18 term（含 DD-A 新增的 5 term）| ✓ 採用 |
  | B | 同 A，並補 Open Questions / Edge Cases / Goals & Scope / Phase Specs 等高頻 H2 約 25-30 term | 不採用 |
- **連帶決議**:
  - F-05 → accept-with-choice (DD-B Path A) — Inclusion Criteria 段釐清「跨檔同名 / 翻譯難度高 / 流程關鍵 / commit message label」收錄；排除「直譯即懂的 H2 / 單檔出現的 heading / 純 placeholder 範例」
  - F-12 → accept-with-choice (DD-B Path A) — 「使用位置」欄語意說明併入 Inclusion Criteria 段末段（指 file path 而非 specific section；以 `Domain Models` H1 為 `models.md` 中央概念為例釐清）

---

## Accept / Accept-with-choice 項目的修訂紀錄

### F-01 (accept)
- **修訂檔案**:
  - `sdd-ddd-webforms-skill/references/new-phase-flow.md`
  - `sdd-ddd-core-skill/references/new-phase-flow.md`
  - `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
  - `sdd-ddd-core-skill/references/modify-existing-flow.md`
- **改動摘要**:
  - `new-phase-flow.md` 雙版 L57 `業務規則` → `Business Rules`（H2 引用）
  - `new-phase-flow.md` 雙版 L134/L143 `業務規則` → `Business Rules`
  - `new-phase-flow.md` 雙版 ADDED/MODIFIED/REMOVED/RENAMED 段：`首次出現 / 最後修訂 / 現況規則` → `First Seen / Last Updated / Current Rule`（_index.md Snapshot column labels）
  - `modify-existing-flow.md` 雙版 `首次出現 / 最後修訂` → `First Seen / Last Updated`（inherited row 描述）
- **驗證**: `grep '業務規則|現況規則|首次出現|最後修訂' sdd-ddd-{webforms,core}-skill/references/` → 0 殘留 ✓
- **F-01 reviewer 列的 templates/CLAUDE.md 部分**: 由 F-11 統一處理（避免重複改同檔）

### F-02 (accept)
- **修訂檔案**: `sdd-ddd-core-skill/SKILL.md`
- **改動摘要**: Scaffolding 表（L477 之後）補一列：
  ```markdown
  | `scaffolding/architecture-decisions-README.md` | ADR directory README seeded into `specs/architecture/decisions/README.md` (mandatory baseline; per PROPOSAL-013 §5 baseline 比對表) |
  ```
- **WebForms 版**: 不修（無 ADR scaffolding）

### F-03 (accept)
- **修訂檔案**: `CHANGELOG.md`
- **改動摘要**: L25 從「修改 `scaffolding/_overview.md` / `_conventions.md`（雙版共 4 檔，檢查級別）」改為：
  ```markdown
  - 修改 `scaffolding/_conventions.md`（雙版，per F-04 cascade 將「輕量修改紀錄」改為 `Lightweight Changes`）；`scaffolding/_overview.md` 雙版檢查 verify 為已英文化結構，無需修改
  ```
- **跨輪議題追蹤**: 「CHANGELOG 自述精準度」議題已 inline 於本 finding 處理，不再轉記 cross-round-notes（符合 review prompt 第三步指引）

### F-04 (accept-with-choice, DD-A Path A)
- **修訂檔案**:
  - `sdd-ddd-webforms-skill/templates/_index.md` L95/97
  - `sdd-ddd-core-skill/templates/_index.md` L103/105
  - `sdd-ddd-webforms-skill/references/finish-feature-flow.md` L85-86
  - `sdd-ddd-core-skill/references/finish-feature-flow.md` L86-87
  - `sdd-ddd-webforms-skill/references/new-phase-flow.md` L173-175
  - `sdd-ddd-core-skill/references/new-phase-flow.md` L184-186
  - `sdd-ddd-webforms-skill/references/new-feature-flow.md` L134
  - `sdd-ddd-core-skill/references/new-feature-flow.md` L160
  - `TEMPLATE-LANGUAGE-GLOSSARY.md`（+2 term）
- **改動摘要**: `**目前進展**：{一句話}` → `**Current Progress**: {one-line summary}`；`**下一個動作**：{建議的下一個動作}` → `**Next Action**: {suggested next action}`；finish-feature-flow Step 5 範例 inline labels 同步；new-phase-flow / new-feature-flow Resume Pointer 範例描述同步英化；GLOSSARY +2 term（Current Progress / Next Action）

### F-05 (accept-with-choice, DD-B Path A)
- **修訂檔案**: `TEMPLATE-LANGUAGE-GLOSSARY.md`
- **改動摘要**: 開頭補 `## Inclusion Criteria` 段：4 條收錄條件（cross-file structural term / translation-sensitive concept / workflow-critical inline label / commit message convention label）+ 3 條排除條件（self-explanatory English heading / single-template-only heading / placeholder example）+ 「使用位置」欄語意釐清

### F-06 (accept-with-choice, Path A)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **改動摘要**:
  - §1 anchor 表 line 100: `business-rules` 使用位置從 `rules.md, _index.md` 改為 `rules.md`
  - §1 anchor 表後補 Same-section single-anchor rule design note，明示「`_index.md` 的 Business Rules 概念由 `current-br-snapshot` anchor 涵蓋；同段不重複注入」
  - §1 Anchor naming rules Namespacing 範例同步調整：`business-rules` 跨檔 → `implementation-tasks` 跨檔（避免 P013 §1 內部矛盾）
- **選定 path 理由**: 實作端「同段單一 stable id」原則更穩當，COVERAGE 矩陣已與實作對齊；只需修 P013 §1 表 + 加 design note 即可

### F-07 (accept)
- **修訂檔案**:
  - `sdd-ddd-webforms-skill/templates/lightweight-spec.md` L50-51（H3/H4）
  - `sdd-ddd-core-skill/templates/lightweight-spec.md` L50-51
  - `sdd-ddd-webforms-skill/references/modify-existing-flow.md` Behavior Delta 段 5×H3+H4
  - `sdd-ddd-core-skill/references/modify-existing-flow.md` Behavior Delta 段 5×H3+H4
- **改動摘要**:
  - `lightweight-spec.md` 雙版 `### MODIFIED — 修改的行為` / `#### 規則：BR-NN {規則名稱}` → `### MODIFIED - behavior modified in this fix` / `#### Rule: BR-NN {規則名稱}`（與 phase-spec.md 風格一致）
  - `modify-existing-flow.md` 雙版 ADDED / MODIFIED / REMOVED / RENAMED / UNCHANGED 5 段 H3 + H4 全部改純英；RENAMED H4 placeholder `{舊名稱} → {新名稱}` → `{old name} -> {new name}`（同 phase-spec.md）
- **延伸範圍說明**: reviewer F-07 原本只標 `lightweight-spec.md`，但同步 cascade `modify-existing-flow.md` 是必要的內部一致性維護（DD-A Path A 拍板後，inline bold labels 已英化；H3/H4 是同類結構性元素，不同步處理會留下「lightweight-spec.md 純英 vs modify-existing-flow.md 中英混合」內部不一致）；使用者明確同意此延伸

### F-08 (accept-with-choice, DD-A Path A)
- **修訂檔案**:
  - `sdd-ddd-webforms-skill/templates/lightweight-spec.md` L52-54
  - `sdd-ddd-core-skill/templates/lightweight-spec.md` L52-54
  - `sdd-ddd-webforms-skill/templates/phase-spec.md` L106-108 / L112 / L116
  - `sdd-ddd-core-skill/templates/phase-spec.md` L109-111 / L115 / L119
  - `sdd-ddd-webforms-skill/references/modify-existing-flow.md` MODIFIED / REMOVED / RENAMED 段
  - `sdd-ddd-core-skill/references/modify-existing-flow.md` 同上
  - `TEMPLATE-LANGUAGE-GLOSSARY.md`（+3 term）
- **改動摘要**: `**原本** / **改為** / **原因**` → `**Before** / **After** / **Reason**` 雙版同步；GLOSSARY +3 term（Before / After / Reason）

### F-09 (accept)
- **修訂檔案**:
  - `sdd-ddd-webforms-skill/templates/context-definition.md` L45
  - `sdd-ddd-core-skill/templates/context-definition.md` L51
- **改動摘要**:
  ```markdown
  > 規則索引在 `rules.md`（BR-ID + 一行摘要），完整 Behavior Scenarios 在 `behavior.md`（Given/When/Then）。這裡只列最重要的幾條。
  ```
  保留中文 prose 容器，只 cascade canonical English term `Behavior Scenarios`

### F-10 (accept-with-choice, DD-A Path A)
- **修訂檔案**:
  - `sdd-ddd-webforms-skill/references/finish-feature-flow.md` Step 5 Next Steps list
  - `sdd-ddd-core-skill/references/finish-feature-flow.md` Step 5 Domain Events 段 + Next Steps list
- **改動摘要**:
  - `Next Steps (developer):` 段下 list items：「依專案 Git-principles 決定 merge strategy ...」/「推送到遠端 / 開 PR」→ 純英版本
  - Core 版額外 Step 5 `Domain Events 變動：` / `新增：` / `修改：` / `移除：` → `Domain Events Changes:` / `ADDED:` / `MODIFIED:` / `REMOVED:`

### F-11 (accept)
- **修訂檔案**:
  - `sdd-ddd-webforms-skill/templates/CLAUDE.md` L32 / L38 / L75
  - `sdd-ddd-core-skill/templates/CLAUDE.md` L51 / L57 / L97
- **改動摘要**:
  - directory tree inline comment：`目標 / BR Snapshot / 接續入口` → `Goals & Scope / Current BR Snapshot / Lightweight Changes / Resume Pointer`
  - 「`_index.md` 輕量修改紀錄」prose 引用 → 「`_index.md` Lightweight Changes」
- **方案選擇說明**: 採選項 B（保留中文 prose 容器，只 cascade canonical English term；與 F-09 一致，與 F-01 Path A 邊界「H2/H3 結構性 vs 散文」一致），而非 reviewer 在 F-11 建議的「全英 directory tree comment」方案
- **連帶清除 F-01 templates/CLAUDE.md 部分**: F-01 reviewer 列的 templates/CLAUDE.md `接續入口 / 輕量修改紀錄` cascade 由本 finding 一次處理；雙版 skill 內 0 殘留 ✓

### F-12 (accept-with-choice, DD-B Path A)
- **修訂檔案**: `TEMPLATE-LANGUAGE-GLOSSARY.md`（與 F-05 同 commit）
- **改動摘要**: Inclusion Criteria 段末段補：「The "使用位置" column refers to file paths where the term appears structurally (as heading / column / label), not necessarily a specific section. For example, `Domain Models` appears as the H1 of `models.md`, representing the file's central concept; `Implementation Tasks` appears as an H2 in two different templates.」釐清 `Domain Models` 列「使用位置 = `models.md`」的語意（檔案中心概念，非特定 section）

---

## Reject / Defer 項目備忘

無（本輪無 reject / defer / clarify / out-of-scope 項目）。

---

## 修改檔案彙總

本輪 fix 共修改 **20 檔**（雙版 skill 各檔分開計算）：

| 檔案 | 對應 Finding | 修改性質 |
|---|---|---|
| `proposals/PROPOSAL-013-system-document-template-coverage.md` | F-06 | §1 anchor 表 + Namespacing 範例（2 處）|
| `CHANGELOG.md` | F-03 | L25 措辭精準化（1 處）|
| `TEMPLATE-LANGUAGE-GLOSSARY.md` | F-04 / F-05 / F-08 / F-12 | 開頭 +Inclusion Criteria 段 + 末段「使用位置」欄釐清；Glossary 表 +5 term |
| `sdd-ddd-webforms-skill/SKILL.md` | — | 不動 |
| `sdd-ddd-core-skill/SKILL.md` | F-02 | Scaffolding 表 +1 列（1 處）|
| `sdd-ddd-webforms-skill/templates/_index.md` | F-04 | Resume Pointer inline labels（2 處）|
| `sdd-ddd-core-skill/templates/_index.md` | F-04 | 同（2 處）|
| `sdd-ddd-webforms-skill/templates/lightweight-spec.md` | F-07 / F-08 | H3/H4 + Behavior Delta inline labels（4 處）|
| `sdd-ddd-core-skill/templates/lightweight-spec.md` | F-07 / F-08 | 同（4 處）|
| `sdd-ddd-webforms-skill/templates/phase-spec.md` | F-08 | Delta MODIFIED/REMOVED/RENAMED inline labels + RENAMED placeholder（5 處）|
| `sdd-ddd-core-skill/templates/phase-spec.md` | F-08 | 同（5 處）|
| `sdd-ddd-webforms-skill/templates/context-definition.md` | F-09 | prose 引用 cascade（1 處）|
| `sdd-ddd-core-skill/templates/context-definition.md` | F-09 | 同（1 處）|
| `sdd-ddd-webforms-skill/templates/CLAUDE.md` | F-11（連帶 F-01 templates/CLAUDE.md 部分）| directory tree comment + Three Ceremony Tiers prose（3 處）|
| `sdd-ddd-core-skill/templates/CLAUDE.md` | F-11 | 同（3 處）|
| `sdd-ddd-webforms-skill/references/new-phase-flow.md` | F-01 / F-04 | Snapshot column / Business Rules 引用 + Resume Pointer 範例（4 處）|
| `sdd-ddd-core-skill/references/new-phase-flow.md` | F-01 / F-04 | 同（4 處）|
| `sdd-ddd-webforms-skill/references/new-feature-flow.md` | F-04 | Resume Pointer 範例（1 處）|
| `sdd-ddd-core-skill/references/new-feature-flow.md` | F-04 | 同（1 處）|
| `sdd-ddd-webforms-skill/references/modify-existing-flow.md` | F-01 / F-07 / F-08 | inherited row column + Behavior Delta 5×H3+H4 + inline labels（11 處）|
| `sdd-ddd-core-skill/references/modify-existing-flow.md` | F-01 / F-07 / F-08 | 同（11 處）|
| `sdd-ddd-webforms-skill/references/finish-feature-flow.md` | F-04 / F-10 | Step 5 inline labels + Next Steps list（3 處）|
| `sdd-ddd-core-skill/references/finish-feature-flow.md` | F-04 / F-10 | 同 + Domain Events 段（4 處）|

**總計**：約 75 處修訂 / 22 檔次（雙版各算 1 檔，含 PROPOSAL-013 / CHANGELOG / GLOSSARY / Core SKILL.md / Core architecture-decisions-README 不存在不算）

新增 1 檔：
- `reviews/round-8b-implement-review-decisions.md`（本文件）

---

## R8b 整體收尾狀態

| 項目 | 狀態 |
|---|---|
| PROPOSAL-013 狀態 | **approved**（本輪不變動，僅微調 §1 anchor 表 + Namespacing 範例 per F-06）|
| R8b Implement | 完成（4 sub-waves + 9 implement commits）|
| R8b Implement Review | 完成（Cursor Claude Opus 4.7，12 findings）|
| R8b Implement Review Fix | 完成（本 session）|
| 雙版 skill / scaffolding / templates / references / SKILL.md cascade 一致性 | ✓ |
| 4 項設計決策（canonical English / anchors / COVERAGE / GLOSSARY）執行品質 | ✓ |
| 結構性術語英文化 cascade 完整性 | ✓（業務規則 / 現況規則 / 首次出現 / 最後修訂 / 接續入口 / 輕量修改紀錄 / 行為情境 / inline bold labels / Delta H3/H4 全部 0 殘留）|

**R8b 全程結束。**

---

## 下一步

1. **R8b 收尾 commit**：將本輪 fix + 本決議文件打包 commit。
2. **R8 全程結束** → 進入 Post-R8：
   - **Closeout C1**（繁中版同步）：依英文 Skill 定稿做單向重譯 / 對齊（依 review-workflow.md §七）
   - **Closeout C2**（Tutorial 重建）：以英文 Skill 為基礎重寫 tutorial 對應段落
   - **改名遷移新 repo**（`dflow-greenfield-skill` / `dflow-brownfield-skill`）→ Dflow V1 ships
3. **跨輪議題追蹤**:
   - **CHANGELOG 自述精準度議題**: F-03 已 inline 處理；建議 Closeout 統一 verify CHANGELOG 描述與實際 git diff 一致性，避免後續 review 重複質疑
   - **R8a 期 cross-round-notes 議題 4**（README 引用 git-flow-integration.md 殘留）: 仍待 Closeout 處理

---

## 跨輪發現備忘

本輪 R8b Implement Review Fix session 過程中**未發現**新跨輪議題（無新轉記到 `reviews/cross-round-notes.md`）。

**反向觀察（DD-A Path A 實施範圍延伸）**：

- DD-A Path A 拍板「結構性術語一律英化」後，實施過程把 inline bold labels 之後的 placeholder 內容（如 `{舊結果}` / `{為什麼改}` 等）也一併英化。此舉雖嚴格說超出 reviewer F-04 / F-08 標的「inline bold labels 本身」邊界，但與使用者「目前全版英文，主要是要國際化」的拍板精神一致，且 templates 內既有英文 placeholder 風格相近。
- **完整 prose / 註解 / 說明段落**（如 templates 內 `> 1-3 段：本 feature 解決什麼問題？` / `> 開新對話接續工作時，從這裡讀起。`）**仍保留中文**，未進入範圍 (2)。
- 若 npm 散佈前的 backlog § 1「文件內容語言策略」階段啟動，可從本輪實施狀態（structural English + placeholder English + prose Chinese）為起點，做最終 prose 英化決策。

---

## 附錄：本輪檔案修訂前後狀態驗證指令

```bash
# 1. 結構性術語英文化 cascade 完整性（針對引用 templates H2 / column 的位置）
grep -rn '業務規則\|現況規則\|首次出現\|最後修訂\|接續入口\|輕量修改紀錄' sdd-ddd-{webforms,core}-skill/
# 預期：6 命中，全部為 prose / placeholder 範圍 (2) 殘留（合理保留）：
#   - templates/CLAUDE.md 雙版 L28：Clean Architecture 層職責 / directory tree comment 中文 prose
#   - scaffolding/CLAUDE-md-snippet.md Core L61：同 Core CLAUDE.md
#   - templates/phase-spec.md Core L161：Implementation Tasks 段 `{業務規則測試}` placeholder
#   - templates/rules.md 雙版 L12：表格 `{業務規則摘要}` placeholder
# 對 templates 內 Business Rules / Current Rule / First Seen / Last Updated /
# Resume Pointer / Lightweight Changes H2 的結構性引用：0 殘留 ✓

# 2. inline bold labels 英文化
grep -rn '\*\*目前進展\*\*\|\*\*下一個動作\*\*\|\*\*原本\*\*\|\*\*改為\*\*\|\*\*原因\*\*' sdd-ddd-{webforms,core}-skill/
# 預期：0 命中

# 3. Delta H3/H4 中英混合
grep -rn '### \(ADDED\|MODIFIED\|REMOVED\|RENAMED\|UNCHANGED\) — \|#### 規則：' sdd-ddd-{webforms,core}-skill/
# 預期：0 命中

# 4. PROPOSAL-013 §1 anchor 表 business-rules 與 _index.md 解耦
grep -n 'business-rules.*_index.md' proposals/PROPOSAL-013-system-document-template-coverage.md
# 預期：0 命中（line 100 已修，line 112 範例改 implementation-tasks）

# 5. Core SKILL.md Scaffolding 表含 architecture-decisions-README.md
grep -n 'architecture-decisions-README' sdd-ddd-core-skill/SKILL.md
# 預期：>= 1 命中

# 6. GLOSSARY Inclusion Criteria 段存在
grep -n '^## Inclusion Criteria' TEMPLATE-LANGUAGE-GLOSSARY.md
# 預期：1 命中
```
