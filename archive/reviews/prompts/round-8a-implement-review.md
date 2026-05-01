# Round 8a Implement Review Prompt — PROPOSAL-012 實作品質審查（給 Cursor 使用）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Cursor。
> **產出位置**：Reviewer 寫入 `reviews/round-8a-implement-review-report.md`。
> **禁止事項**：本輪**不修改任何檔**（proposal、skill 檔、CHANGELOG、planning 檔皆不動），只產出 review 報告。
> **R8a Implement Review 的本質**：審**實作產物 vs approved P012 意圖**的忠誠度（仿 R1-R6 「意圖 vs 實作」模式）。
> **與 R7 Implement Review 的差異**：R7 審 3 wave；R8a 只審 P012 單一 wave，scope 小（10 檔），預期 finding 2-8 項。
> **R8a Implement Review 與 R8b 的關係**：R8b 路徑依賴 P012；本輪是 R8b implement 啟動的最後 sanity check。R8a 通過後，R8b implement 可放心使用 `specs/shared/` 路徑前提。
> **模型選擇**：使用者於 Cursor 自選；任何 frontier 模型皆適用。
> **預估時程**：30-60 分鐘。

---BEGIN PROMPT---

你是 Dflow Round 8a Implement Review 的審查者。本輪範圍為 **PROPOSAL-012 實施產物 vs approved proposal 意圖** 的對照審查（仿 R1-R6 模式）。

## 第一步：讀入必要檔案

### A. 實施權威來源（必讀）

1. `proposals/PROPOSAL-012-distribution-friendly-paths.md`（approved；藍圖。狀態欄應為 `approved`，§ 影響範圍表是核對基準）

### B. R8a context（finding 解析追溯）

2. `reviews/round-8a-decisions.md`（10 個 finding 的 accept path 定案；特別 F-01 Path A、F-04 Path B、F-10 歷史保留原則）
3. `reviews/round-8a-report.md`（原始 finding，作為背景）

### C. 實施紀錄（了解改動邊界）

4. `CHANGELOG.md` 最上方一段：`## 2026-04-27 — R8a Implement：PROPOSAL-012 ...` —— 這是實施者（Claude Opus 4.7 主 session）自報的改動摘要，需 Reviewer 用實際檔案 verify

### D. 流程規範

5. `proposals/review-workflow.md`（§三 Review 報告格式 + §五 輪次執行順序）

### E. 實施產物（依 PROPOSAL-012 §影響範圍 表逐檔核對）

對照 P012 §影響範圍 列的 11 個檔案項目（README.md 為「可能只需確認」），實際 read：

**雙版 SKILL.md（Templates & Scaffolding 段路徑）**：
- `sdd-ddd-webforms-skill/SKILL.md`（line 384-386 區域）
- `sdd-ddd-core-skill/SKILL.md`（line 453-455 區域）

**雙版 init-project-flow.md（多處 `_共用` → `shared`）**：
- `sdd-ddd-webforms-skill/references/init-project-flow.md`
- `sdd-ddd-core-skill/references/init-project-flow.md`

**雙版 scaffolding/CLAUDE-md-snippet.md（inline + tree restructure）**：
- `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md`（5 處 inline + tree 重構）
- `sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md`（1 處 inline；無 tree 重構）

**雙版 templates/CLAUDE.md（tree restructure + Core 補檔）**：
- `sdd-ddd-webforms-skill/templates/CLAUDE.md`（tree 移 _overview/_conventions 到 shared/）
- `sdd-ddd-core-skill/templates/CLAUDE.md`（**tree 補上漏列的 _overview/_conventions**，per F-04 Path B）

**其他**：
- `README.md`（verify 無 `_共用` 引用即可；P012 §影響範圍 標「可能只需確認」）
- `CHANGELOG.md`（新增 R8a 段；歷史條目 line 17、line 61 應**保持原樣**，per F-10）
- `planning/project-review-brief.md`（line 51, 59, 111, 367 區域更新；per F-01 Path A）

### F. 不要讀的東西

- 任何繁中版檔（`sdd-ddd-*-skill-tw/`）— 繁中版同步延到 Closeout C1
- `tutorial/` 目錄 — 屬 Closeout C2
- R1-R7 的歷史 review 報告 — 凍結不動
- `E:\工作區\SDD-Starter-Kit\`（V2）— 與本輪實施品質無關
- PROPOSAL-013 / `reviews/round-8b-*.md` / `reviews/prompts/round-8b-*.md` — 本輪不審 R8b

## 第二步：審查維度（5 個 lens）

### Dimension 1 — 實施對 PROPOSAL-012 §影響範圍 的忠誠度（每檔核對）

對 P012 §影響範圍 表的每一列，verify：

- **該檔是否真的被修改**？（git diff 應顯示變動）
- **修改範圍是否符合 P012 §影響範圍 描述**？（例如 P012 說 WebForms scaffolding/CLAUDE-md-snippet.md 應「inline 5 處 + tree 範例 line 48-69 重構」，verify 兩件事都做了）
- **是否有未列入 §影響範圍 的檔案被改動**？若有，可能是合理連帶（例如 typo 修正）也可能是越界，需 reviewer 判斷

### Dimension 2 — 對 R8a Approve 決議的忠誠度

對 R8a Decisions 中的 accept / accept-with-choice 項目逐一 verify：

- **F-01 Path A**：`planning/project-review-brief.md` 的 line 51, 59, 111 前瞻性註解是否實際更新？§10 P012 候選議題段是否加上「（已由 P012 處理）」？
- **F-02**：scaffolding/CLAUDE-md-snippet.md 兩版的描述精準化（WebForms = inline + tree 雙層；Core = 僅 inline）是否反映在 P012 §影響範圍 + 實際實施都符合？
- **F-04 Path B**：Core templates/CLAUDE.md tree 是否確實**補上**漏列的 _overview.md / _conventions.md（不是「移動」，是「新增」，因為原本就沒有）？WebForms 版是否同步「移動」？
- **F-05**：WebForms init-project-flow.md 的章節名是否與 P012 §3 一致（Step 2 Q5 / Step 3 file-list preview / Step 4 CLAUDE.md special handling / Step 5 results report）？
- **F-06 Path A**：P012 §影響範圍 是否已**移除** `planning/public-distribution-backlog.md` 列（決議要求把該檔從影響範圍移到「關聯的 Proposal」表）？
- **F-07**：P012 §不納入影響範圍 row 3 是否明示歸屬 P013 + backlog § 1, § 4？
- **F-08**：P012 §1 政策表 row 4 是否補上 ASCII slug 選項出口？
- **F-10**：CHANGELOG 新段是否**只新增、不改動歷史 line 17/61**？

### Dimension 3 — 跨版（WebForms / Core）一致性

對應雙版的修改，檢查：

- 雙版 SKILL.md `Templates & Scaffolding` 段措辭是否一致（除預期的版本特異字外）
- 雙版 init-project-flow.md 的 `_共用/` → `shared/` 替換是否完整且對稱（兩版各自的 17 處左右都改完）
- 雙版 scaffolding/CLAUDE-md-snippet.md 的 inline 引用是否都用 `shared/`
- 雙版 templates/CLAUDE.md tree 內 `shared/` 子節點下的檔案順序與註解風格是否一致

### Dimension 4 — Regression check（P001-P011 機制相容性）

P012 動到 init-project（P010）路徑、scaffolding 範本（P010）、SKILL.md（多 proposal 影響）。檢查：

- **P010 兼容**：`/dflow:init-project` flow 描述全部用 `shared/` 後，是否仍然語意連貫？mandatory baseline 清單是否未受影響（P010 baseline 不在 P012 範圍內）？
- **P011 兼容**：P011 的 git-integration.md / Git Flow Decoupling 是否未受 P012 改動影響？scaffolding 的 Git-principles-{gitflow,trunk}.md 路徑由 specs/_共用/ 改為 specs/shared/ 是否與 P011 既有設計一致？
- **P007c CLAUDE.md H2 navigation**：templates/CLAUDE.md 的 H2 結構（系統脈絡 / 開發流程）是否未因 tree 重構而被破壞？（P012 範圍只動 directory tree 圖，不動 H2 標題）
- 其他既有 references 檔（`new-feature-flow.md` / `modify-existing-flow.md` 等）若引用 `specs/_共用/`，是否被遺漏未更新？
  - **檢查方式**：對 `sdd-ddd-{webforms,core}-skill/` 兩個目錄全域 grep `_共用`，期望結果為 0；若有殘留，定位後判斷是否該修

### Dimension 5 — 邊界守住（與 P013 / backlog 的職責切分）

- 實施過程是否**沒有踩進 P013 範圍**？特別檢查：
  - 是否有任何 template heading 被改成英文（P013 範圍）？
  - 是否有任何新增 template / TEMPLATE-COVERAGE.md / TEMPLATE-LANGUAGE-GLOSSARY.md（P013 範圍）？
  - 是否有任何 anchor `<!-- dflow:section X -->` 被加入 templates（P013 範圍）？
- 實施過程是否**沒有踩進 backlog 範圍**？特別檢查：
  - 是否有任何文件根目錄改名（`specs/` → `dflow/` 等，backlog § 6）？
  - 是否有任何 npm / package.json / CLI 相關產出（backlog § 2）？
  - 是否有任何雙語 template set / `templates/en/` / `templates/zh-TW/` 建立（backlog § 1）？

## 第三步：Finding 格式

每個 Finding 採以下格式：

```markdown
### F-NN {一句話問題摘要}

**嚴重度**: Critical | Major | Minor | Question
**對照**: {擇一或混合標示}
  - 實施 vs P012：`PROPOSAL-012 § 影響範圍 列 N` ↔ `{實際檔案路徑}:{行號}`
  - 實施 vs R8a Decisions：`reviews/round-8a-decisions.md F-NN Path X` ↔ `{實際檔案路徑}:{行號}`
  - 跨版一致性：`{WebForms 檔案}:{行號}` ↔ `{Core 檔案}:{行號}`
  - Regression：`PROPOSAL-0XX 段` ↔ `{實際檔案路徑}:{行號}`
**維度**: Dimension 1 / 2 / 3 / 4 / 5（單選）
**現況**: {實際讀到什麼}
**問題**: {為什麼是問題 —— 實施缺漏 / 違背 approve 決議 / 跨版不一致 / regression / 邊界越界}
**建議**: {具體怎麼修；若 Question 類型則寫「需釐清：...」}
```

### 嚴重度定義（沿用 R7 / R8a 慣例）

- **Critical**：實施根本違背 P012 意圖，會破壞 P010 init flow / P011 / 其他既有機制。**嚴格使用，不濫用**。
- **Major**：影響範圍漏改、approve 決議違背、跨版不一致、regression 風險。實施再修。
- **Minor**：措辭不夠精確、無關行的順帶修改、可讀性問題。
- **Question**：Reviewer 不確定實施意圖或 P012 意圖，需設計者釐清。

## 第四步：產出格式

寫入 `reviews/round-8a-implement-review-report.md`：

```markdown
# Round 8a Implement Review Report — PROPOSAL-012 實作品質審查

**審查範圍**: PROPOSAL-012 實施產物（commit `2803f27` Round 8a Implement）
**審查類型**: Implement review（意圖 vs 實作對照）
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

## 影響範圍實施對照表（Dimension 1）

| P012 §影響範圍 列 | 檔案 | P012 描述 | 實施狀態 | 一致？ | 對應 Finding |
|---|---|---|---|---|---|
| 1 | `sdd-ddd-webforms-skill/SKILL.md` | Templates & Scaffolding 段路徑 | {modified / unchanged / partial} | ✓ / ✗ / ⚠ | F-NN |
| 2 | `sdd-ddd-core-skill/SKILL.md` | 同 WebForms | | | |
| 3 | `sdd-ddd-webforms-skill/references/init-project-flow.md` | Step 2 Q5、Step 3、Step 4、Step 5 | | | |
| 4 | `sdd-ddd-core-skill/references/init-project-flow.md` | 同 WebForms | | | |
| 5 | `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md` | inline 5 處 + tree 重構 | | | |
| 6 | `sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md` | inline 1 處 | | | |
| 7 | `sdd-ddd-webforms-skill/templates/CLAUDE.md` | tree 移 _overview/_conventions | | | |
| 8 | `sdd-ddd-core-skill/templates/CLAUDE.md` | tree 補檔 + 重構 | | | |
| 9 | `README.md` | verify 無引用 | | | |
| 10 | `CHANGELOG.md` | 新增段 + 歷史保留 | | | |
| 11 | `planning/project-review-brief.md` | line 51, 59, 111, 367 更新 | | | |

## R8a Approve 決議落實對照表（Dimension 2）

| Finding | 決議類型 | 預期效果 | 實施狀態 | 一致？ | 對應 Implement Review Finding |
|---|---|---|---|---|---|
| F-01 (Path A) | accept-with-choice | project-review-brief 4 處更新 | | ✓ / ✗ / ⚠ | |
| F-02 | accept | scaffolding/CLAUDE-md-snippet.md 描述精準化 | | | |
| F-04 (Path B) | accept-with-choice | Core templates/CLAUDE.md 補檔 | | | |
| F-05 | accept | init-project-flow Step 名稱對齊 | | | |
| F-06 (Path A) | accept-with-choice | backlog 從影響範圍移除 | | | |
| F-07 | accept | row 3 明示歸屬 | | | |
| F-08 | accept | 政策表 row 4 ASCII slug 出口 | | | |
| F-10 | accept | CHANGELOG 新增 + 歷史保留 | | | |

## 跨版一致性對照表（Dimension 3）

| 配對 | 檢查點 | 一致？ | 對應 Finding |
|---|---|---|---|
| 雙版 SKILL.md | Templates & Scaffolding 段措辭 | ✓ / ✗ / ⚠ | |
| 雙版 init-project-flow.md | 全檔 `shared/` 替換完整性 | | |
| 雙版 scaffolding/CLAUDE-md-snippet.md | inline 引用 | | |
| 雙版 templates/CLAUDE.md | tree shared/ 子節點 | | |

## Regression check（Dimension 4）

| 既有機制 | 檢查結果 | 對應 Finding |
|---|---|---|
| P010 `/dflow:init-project` flow 連貫性 | ✓ / ✗ | |
| P011 Git Flow Decoupling 不受影響 | | |
| P007c CLAUDE.md H2 navigation 結構 | | |
| `_共用` 全域 grep 殘留檢查 | {結果摘要} | |

## 邊界守住對照（Dimension 5）

| 邊界 | 檢查結果 | 對應 Finding |
|---|---|---|
| 未踩進 P013 範圍（template heading 英文化 / anchor / coverage / glossary）| ✓ / ✗ | |
| 未踩進 backlog 範圍（npm / 根目錄改名 / 雙語 template）| | |

## 審查備註

- （任何需要補充的觀察、審查流程中的限制等）

---

**跨輪議題**：若審查過程順手看到其他 proposal（P001-P011 或 P013）的問題，記入 `reviews/cross-round-notes.md`，**不佔本輪 Finding 數量**。
```

## 第五步：重要規則

- **不要修改任何檔案**。只產出 `reviews/round-8a-implement-review-report.md`。
- **不要 review P013 / R8b**。R8b 是平行 wave，不在本輪範圍。若發現 R8b 相關問題，記到 `reviews/cross-round-notes.md` 而非本輪 Finding。
- **不要比對英文 skill vs 繁中 skill**。繁中延到 Closeout C1。
- **影響範圍對照表必填**。第四步的「影響範圍實施對照表」每一列都要附**實際檔案 grep / read 證據**，不能只回 ✓ 而不附行號。
- **嚴重度從嚴**：Critical 保留給「破壞既有機制 / 違背 P012 核心意圖」級別。
- **「`_共用` 全域 grep 殘留檢查」是 Dimension 4 的硬性要求**。對 `sdd-ddd-{webforms,core}-skill/` 兩個目錄執行 `grep _共用`，預期結果為 0；若有殘留，列為 finding。
- **CHANGELOG 歷史保留 verify 是 F-10 的硬性要求**。對 CHANGELOG.md 行 17（R7 F-03 紀錄）和行 61（P010 init-project 紀錄）特別 verify 是否未被改動；若改動則 Critical。

開始審查。完成後只產出 `reviews/round-8a-implement-review-report.md`。

---END PROMPT---
