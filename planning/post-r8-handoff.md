# Post-R8 Handoff Notes — 給 Debian 連假 session 接續用

> **目的**：讓 Debian 筆電開的新 Claude Code session 從 cold start 也能接續 Post-R8 工作
> **建立日期**：2026-04-28（主 Windows session 收尾前）
> **接續者**：Debian 筆電的 Claude Code 新 session（連假期間）

---

## 第一句話（給新 session 看的開場）

> 「我接續 Post-R8 階段。先讀 `planning/r8-overview.md` + `planning/tutorial-and-post-r8-plan.md` + 本檔（`planning/post-r8-handoff.md`）建立 baseline，再依本檔『下一步』段執行。」

---

## 當前進度截圖（commit `e86b2f0`）

### R8 全程已收尾
- R8a (PROPOSAL-012 路徑英文化) ✅ implemented + reviewed + fixed
- R8b (PROPOSAL-013 樣板覆蓋 + canonical English + anchor 機制) ✅ implemented + reviewed + fixed
- 18 個 R8 commits，從 `2803f27` 到 `ef0bac2`，全部乾淨

### Post-R8 階段 1 已完成（本 session）
- ✅ `planning/r8-overview.md` — R8 全程總覽
- ✅ `planning/tutorial-and-post-r8-plan.md` — Tutorial 規劃 + Subagent 計畫 + Post-R8 時程
- ✅ Tutorial Subagent 試水溫成功：`tutorial/01-greenfield-core/01-init-project.md` + outputs/ 11 檔
- ✅ 本檔 handoff notes

### Post-R8 階段 2 待辦（你的工作）
- ⏳ Tutorial 劇情 1 剩餘段（02-new-feature → 06-finish-feature，約 5 段）
- ⏳ Tutorial 劇情 2 全部段（00-setup → 06-finish-feature，約 7 段）
- ⏳ `tutorial/README.md` 索引文件
- ⏳ Tutorial 完成後的全程 review（人工或派 subagent）

### Post-R8 階段 3（連假後）
- ⏳ Closeout C1：繁中版 skill 同步（範圍視 Tutorial 中文版產出後重新評估）
- ⏳ Closeout C2：Tutorial 收尾調整
- ⏳ Global Sweep（可選）
- ⏳ 改名遷移（dev repo + dist repo 切割）
- ⏳ Dflow V1 ships

---

## 你的下一步（Tutorial 劇情 1 段 2）

### 派 Subagent 做 02-new-feature

複製以下整段給 subagent（依 `planning/tutorial-and-post-r8-plan.md` § Subagent prompt 範本調整 + 用 01-init-project.md 的 outputs 為起點）：

```
你的任務是為 Dflow tutorial 生產**劇情 1（Core greenfield）/ 第 2 段：/dflow:new-feature**。

接續自第 1 段（init-project）。Alice 剛跑完 init-project，repo 已有 specs/ + CLAUDE.md
（詳見 tutorial/01-greenfield-core/outputs/）。現在 Alice 要開第一個 feature：
「員工提交費用單」。

## 你扮演兩個角色

**Alice — 沿用第 1 段角色設定**：Core 新專案 ExpenseTracker 的 PO+工程師，3-5 人團隊，
DDD 經驗中等，trunk-based，已決定第一個 feature 是「員工提交費用單」。

**Dflow guardian — 讀以下 skill 檔為權威**：
1. sdd-ddd-core-skill/SKILL.md
2. sdd-ddd-core-skill/references/new-feature-flow.md（Step 1-9 全程）
3. sdd-ddd-core-skill/templates/_index.md（Goals & Scope / Phase Specs /
   Current BR Snapshot / Lightweight Changes / Resume Pointer 等 6 sections）
4. sdd-ddd-core-skill/templates/phase-spec.md
5. sdd-ddd-core-skill/templates/glossary.md
6. sdd-ddd-core-skill/templates/aggregate-design.md（Core 特有）
7. tutorial/01-greenfield-core/outputs/ 全部檔（你的起點 baseline）

## Alice 的預定選擇 / 行為（請依此 roleplay）

- SPEC-ID：SPEC-20260428-001（與第 1 段「Alice 在同一天下午」連貫）
- slug：employee-submit-expense（英文 slug，符合 P012 後 ASCII 友善）
- 第一個 BC：Expense
- 第一個 Aggregate：ExpenseReport（含 ExpenseItem entities）
- 第一個 phase：MVP（最小可運作版本）
- 預期 Domain Events：ExpenseReportSubmitted
- Behavior 範例：
  - BR-001: 員工提交費用單必須含至少 1 個費用項
  - BR-002: 提交後狀態變為 Submitted，可被主管看見
  - 1-2 個 Edge Case（例如「金額為 0 的費用項」）
- 不要過度擴張第 1 個 feature scope（避免 phase 太大）

## 兩個產出（寫到 main repo，不用 worktree）

### 產出 1：對話 markdown

寫到 `tutorial/01-greenfield-core/02-new-feature.md`，依 5 段結構：
劇情背景 / 完整對話 / 本段產出的檔案 / 觀察重點 / 下一個劇情段

完整對話必須涵蓋 new-feature-flow.md 全部 Steps（Step 1 SPEC ID + slug 確認 →
Step 2 Goals & Scope → Step 3 Domain Concepts 識別 → Step 4 Domain modeling
+ Aggregate design → Step 5 Behavior Scenarios + BR generation → Step 6
Implementation Tasks 生成 → Step 7 _index.md 建立 + Phase Specs 表 → Step 8
完成後 update Snapshot + glossary 等 → Step 9 results report）。

對話風格與 01-init-project.md 一致（工程師語氣 + AI 引導語氣）。

### 產出 2：實際生成的 spec 檔

寫到 `tutorial/01-greenfield-core/outputs/specs/`（**只放新增 / 修改的檔**，不複製
01 的 11 檔；per 第 1 段試水溫建議「outputs/ 跨段共用」）：

- `specs/features/active/SPEC-20260428-001-employee-submit-expense/_index.md`
- `specs/features/active/SPEC-20260428-001-employee-submit-expense/phase-spec-2026-04-28-mvp.md`
- `specs/features/active/SPEC-20260428-001-employee-submit-expense/aggregate-design.md`
  （Core 特有；ExpenseReport 設計）
- `specs/domain/glossary.md`（更新：補 ExpenseReport / ExpenseItem / Approver /
  Reimbursement 4 個 term；保留 templates 結構）
- `specs/domain/context-map.md`（更新：補 Expense BC 第一個 entry）
- `specs/domain/Expense/context.md`（新建 BC）
- `specs/domain/Expense/models.md`（新建：ExpenseReport Aggregate 結構）
- `specs/domain/Expense/rules.md`（新建：BR-001 / BR-002）
- 其他：依 new-feature-flow.md Step 8 應建立的檔

## 品質要求 + 不要做的事

- 對話自然（非「AI: Hello」風格）
- 忠於 new-feature-flow.md 各 Step 措辭與 Phase Gate 機制
- 雙重 H2 結構英文（System Context / Development Workflow 等已 R8b cascade）
- placeholder 替換合理
- specs/features/active/SPEC-... 路徑一致使用 P009 後的目錄化結構
- BR-001 / BR-002 用 P003 + P008 的 source-of-truth 結構（rules.md 列 ID + 一行；
  behavior.md 留 Phase 1 Step 8.3 才建）
- **不要動 main repo skill 任何檔**
- **不要動 proposals/ / reviews/ / planning/ / CHANGELOG.md / TEMPLATE-COVERAGE.md /
  TEMPLATE-LANGUAGE-GLOSSARY.md**
- **不要 commit**（使用者會 review 後 commit）

完成後回報：對話字數 / outputs/ 內新增/修改檔清單 / 設計選擇 / 自我品質評估 /
給後續段（03-new-phase）的建議。
```

### Subagent 跑完後

1. **Spot-check 對話品質**：開 02-new-feature.md，看頭 100 行是否自然
2. **Spot-check 生成 spec 品質**：
   - `_index.md` 是否含 6 個 sections + canonical English headings + dflow:section anchors
   - `phase-spec.md` 是否含 P013 §1 anchor + Implementation Tasks 段
   - `aggregate-design.md` 是否合理表達 ExpenseReport 不變條件
3. **若品質高**：commit + 派下一段（03-new-phase）
4. **若品質低**：分析原因（prompt 範本 / Alice 設定 / Dflow flow 描述？），調整後重派

### Commit message 範本

```
Tutorial 劇情 1 段 2：/dflow:new-feature (Subagent 產出)

對話 markdown ~XXXX 字 + outputs/ N 檔
（描述具體生成內容，仿 e86b2f0 commit message 風格）

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
```

---

## 接續策略（Tutorial 全劇本）

| 段 | 範圍 | Subagent 預估 token | 建議派工 |
|---|---|---|---|
| 01 init-project | ✅ 已完成 | — | — |
| 02 new-feature | 範圍中（含 Aggregate design）| 60-80k | 1 次 subagent |
| 03 new-phase | 範圍中（Phase 2 + Delta）| 50-70k | 1 次 subagent |
| 04 modify-existing | 範圍中（含 T2 lightweight）| 50-60k | 1 次 subagent |
| 05 bug-fix | 範圍小（含 lightweight task guard）| 30-50k | 1 次 subagent |
| 06 finish-feature | 範圍中（含 Integration Summary）| 50-70k | 1 次 subagent |
| **劇情 1 小計** | 6 段 | ~250-380k | ~5 次 subagent（不含 01）|
| 劇情 2 全 7 段（00-06）| brownfield WebForms | ~280-400k | ~7 次 subagent |
| README + 索引 | 小 | — | 主 session 直接寫 |
| 全程 review | 中 | ~50k | 1 次 subagent (general-purpose) |

**估總工作量**：~13 次 subagent + 1 次 review subagent + 主 session commit / handoff，約 4-6 hr 散工。

### 中途斷點建議

每完成 1 段就 commit，這樣中途斷不會丟工。Commit message 含：
- 段編號（01 / 02 / ...）
- 對話字數
- outputs 新增/修改檔數
- 是否有遺留 TODO 給下一段

---

## 給新 session 的注意事項

1. **不要重做 R8 已完成的工作**：commit chain 從 `2803f27` 到 `e86b2f0` 全部已完成且乾淨；任何「我覺得這裡可以改」的衝動先記到 `reviews/cross-round-notes.md` 而非直接動

2. **保持 Tutorial outputs/ 結構連貫**：Alice / ExpenseTracker 是固定設定；每段 outputs 只放新增/修改檔；段間有「上一段在何處留下 TODO」的接續

3. **Subagent 試水溫經驗**：第 1 段 Subagent 表現出色，但**每段都要 spot-check**（不只看自我評估）。重點看：
   - 對話自然度（不要僵硬問答）
   - skill flow 忠實度（Step transition / Phase Gate / Confirmation Signals）
   - 雙版 / Core / WebForms 區隔正確（劇情 1 用 Core；劇情 2 用 WebForms）
   - canonical English heading 無中文殘留（per R8b）
   - dflow:section anchor 注入到對應位置

4. **遇到設計問題先停下來諮詢使用者**：例如 Alice 的某個選擇影響整劇本走向、或 Subagent 產出與 skill flow 有微衝突；不要憑主觀拍板

5. **連假期間使用者可能不在**：如果有「需使用者拍板」的設計問題，記到本檔末尾的「待使用者回應」段，繼續做不需拍板的部分

---

## 待使用者回應的問題（連假期間累積）

（連假期間若有需使用者拍板的議題，列在這裡，使用者回主 session 時一次處理）

- （無）

---

## 連假期間若需要派 Codex（weekly reset 過了）

Codex 適合：
- 跑 Tutorial Subagent 各段（取代 Cursor 的 general-purpose subagent，但需注意 Codex 在 Cursor 內介面 vs Codex CLI 差異）
- C1 中文翻譯（Tutorial 中文版 / skill 中文版）
- Global Sweep（grep + 一致性確認）

不適合派 Codex：
- 設計判斷類（Tutorial 整體規劃調整、改名遷移策略決定）

---

## 連假後恢復主線

連假後使用者回 Windows 筆電，建議流程：

1. 開新 Claude Code session（不要繼續用本主 session — 那時 context 會超長）
2. 第一句話：「我從連假回來，請讀 `planning/post-r8-handoff.md` 與最新 commit log 建立 baseline」
3. 按 Post-R8 階段 3 順序處理（Closeout C1 / C2 / Sweep / 改名遷移 / V1 ships）

---

## 本主 session（Windows）的 context 狀態

- 收尾時 context 約 50%（本主 session 已跑完 R8 全程 + Post-R8 階段 1）
- 在這裡乾淨收尾合理；新 session（Debian 或連假後 Windows）從 cold start 接續
