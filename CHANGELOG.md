# Changelog

本文件記錄 SDD/DDD Skill 的版本變更。變更依 Proposal 編號分段，對應 `proposals/PROPOSAL-NNN-*.md`。

格式：每個版本標註日期、關聯 Proposal、影響範圍。新版本加在最上方。

---

## 2026-04-20 — Round 2 審查修正：變更描述格式（P002 + P004）

**關聯 Proposal**: P002, P004
**審查輪次**: R2
**對應決議文件**: `reviews/round-2-decisions.md`

### 修正項目

- **F-01 (Major)**: 兩版 `references/new-feature-flow.md` Step 8.1 的「實作任務」驗證項未依 PROPOSAL-004 實施紀錄自述的「加入第一項」位置語放在清單首位（WebForms 版落第 5 項、Core 版落第 8 項） → 將該驗證項移到 Step 8.1 清單第 1 項，其餘項相對順序不動。

### 影響檔案

- `sdd-ddd-webforms-skill/references/new-feature-flow.md`
- `sdd-ddd-core-skill/references/new-feature-flow.md`

### 繁中版同步狀態

延後至 Post-Review Closeout 階段統一處理（見 `proposals/review-workflow.md` 第七節）。

---

## 2026-04-20 — Round 1 審查修正：流程入口與透明度（P001 + P007b）

**關聯 Proposal**: P001, P007b
**審查輪次**: R1
**對應決議文件**: `reviews/round-1-decisions.md`

### 修正項目

- **F-01 (Major)**: Core `SKILL.md` 的 `/dflow:status` Response Format 範例 Step 3 沿用 WebForms 版 `Domain Concept Discovery`，與 Core `references/new-feature-flow.md` 正式名稱 `Domain Modeling` 不一致 → 將範例中 Step 3 兩處（line 179 status line、line 186 in-progress checklist）改為 `Domain Modeling`，與 Core flow 檔對齊。

### 影響檔案

- `sdd-ddd-core-skill/SKILL.md`

### 繁中版同步狀態

延後至 Post-Review Closeout 階段統一處理（見 `proposals/review-workflow.md` 第七節）。

---

## 2026-04-17 — Tutorial 同步 P001–P008：兩份 hybrid 教學文件更新

**關聯 Proposal**: 無（非 Proposal，為 P001 Hybrid 教學的 P002–P008 累積同步）

**目的**：`TUTORIAL_WEBFORMS_tw_hybrid.md` / `TUTORIAL_CORE_tw_hybrid.md` 兩份「情境帶領、身歷其境」的教學文件原本只反映 P001 Hybrid，P002–P008 完成後需同步，否則新人照著教學體驗不到新行為。

### 新增

- **WebForms 教學新增情境五：Brownfield 基準線補寫（P007a）**——以「請假單退回規則要改，但沒寫過 spec」的場景展示 B1–B4 四步（定位 → 抽取 → 結構化 → 確認），補完後接回 modify-existing-flow 用 Delta 記錄變更。
- **兩份教學在 Scenario 1 / 2 加入四層完成 checklist narrative**：5.1 / 8.1 AI 獨立跑、5.2 / 8.2 一題一題問、5.3 / 8.3 動文件（含 behavior.md B3/B4 子步驟）、5.4 / 8.4 歸檔（P005a / P005b）。
- **兩份教學 Scenario 2 spec 輸出加入 Phase markers 與 `實作任務` 段**（P004），Step 5 規劃後展示 `[LAYER]-[NUMBER]` 任務清單。
- **Core 教學 Scenario 2 spec frontmatter 加入 `ddd-modeling-depth`**（P006a），narrative 解釋輕 / 中 / 重三級與 PR Review 嚴格度的對應。
- **兩份教學 Scenario 2 結尾加入 `/dflow:verify` 執行示範**（P008），展示 drift 機械檢查回報格式。
- **WebForms 教學 Scenario 3 / Core 教學 Scenario 4（PR Review）加入 A+C 結構對齊檢查與 DDD Depth 嚴格度調整**（P003 / P006a / P007b）。

### 變更

- **兩份教學的 title 與 intro**：從「P001 實作後」改為「P001–P008 實作後」，並明示本文涵蓋的所有新特性。
- **兩份教學「原版 vs 新版」對照表**：加入 Delta 格式、A+C 結構、DDD Depth（Core 限定）、四層完成、verify、Brownfield（WebForms 限定）等新列。
- **兩份教學指令速查表**：加入 `/dflow:verify`；其餘指令的用途欄位補上新特性標註（Phase markers、Delta、Aggregate 邊界複檢、Step 0 Spec Intent）。
- **兩份教學 flow 圖**：Step 5 / Step 8 展開為四層子結構；Scenario 2 的 Step 4 標示含 Phase markers + 實作任務段。
- **Core 教學 Ceremony Scaling 表新增 `DDD Modeling Depth` 欄**（P006a），行數擴展到涵蓋「新 Aggregate」「跨 / 內 Aggregate 規則變更」「Domain 層 bug 修復」等更細的情境。
- **WebForms 教學 Ceremony Scaling 表新增 `格式` 欄**（feature-spec / Delta / Brownfield 基準線 / lightweight），與「要抽 Domain？」欄互補。
- **WebForms 教學目錄加入情境五的連結**。

### 設計判斷

- **教學文件不重寫 scenario，只添加新特性敘述**：為了保留原 P001 教學的情境故事性（使用者身歷其境），採「在既有情境裡插入新特性 narrative」而非另開 P002–P008 專屬情境。例外是 Brownfield（P007a）——因它改變整個進入流程，必須獨立成一個情境。
- **Core vs WebForms 差異保留**：DDD Modeling Depth 欄位僅 Core 教學呈現（P006a 本為 Core-only）；Brownfield 基準線補寫僅 WebForms 教學呈現（P007a 本為 WebForms-only）。

### 影響檔案

- `tutorial/TUTORIAL_WEBFORMS_tw_hybrid.md`
- `tutorial/TUTORIAL_CORE_tw_hybrid.md`

---

## 2026-04-17 — P005b 實施：完成流程 Checklist 補完 behavior.md + Tasks 分層項（Wave B 子集）

**關聯 Proposal**: `PROPOSAL-005b-completion-checklist-wave-b.md`（approved → implemented）

**Wave**: B（依賴 P003 + P004，兩者已於 Wave A / Wave B 先行實施）

### 新增

- **X.1 AI 可獨立驗證** 加入兩項標 `*(post-X.3)*` 的 post-merge 項（兩版 new-feature-flow Step 8、WebForms modify Step 6、Core modify Step 5）：
  - `behavior.md` 對本 spec 的每個 `BR-*` 有對應 section anchor（`/dflow:verify` 機械輸入）
  - `behavior.md` `last-updated` 晚於 spec `created` 日期（機械 drift 防線）
  - modify-existing 版特別提 REMOVED delta 的 anchor 刪除
- **X.2 需開發者確認** 加入兩題：
  - 合併進 `behavior.md` 的情境是否忠實表達預期行為（Core 版補 Aggregate 轉換 + Events）
  - 完成後 spec 的 `實作任務` 段要不要摺疊 / 移除（團隊慣例）
- **X.3 文件更新** 的 `behavior.md` bullet 擴充含兩個子步驟：
  - 把 Phase 3 draft 段落（B3 中途同步）轉為正式段落
  - 更新對應 `rules.md` anchor 的 `last-updated` 日期（B4）
- **X.3 新增 `behavior.md` 草稿清理 bullet**：中途放棄的 spec 要保留 `## 提案中變更` 段歷史或明確 REMOVE

### 變更

- **兩版 SKILL.md `Completion Checklist Execution` / `完成流程 Checklist 的執行方式` 子節**：
  - AI-independent 段落拆成「合併前」/「X.3 之後」兩類時機說明，讓 post-merge 項目的執行順序清晰。
  - Developer-confirmation 段落加入 P005b 的兩題。
  - 文件更新段落加入 `behavior.md` 合併的兩個子步驟（B3 / B4）與草稿清理指引。

### 設計判斷

- **8.1 放 post-merge 項還是另闢新段？** 選擇放回 8.1 用 `*(post-X.3)*` 標籤區分，避免 checklist 多一層結構。SKILL.md 描述段明確區分兩類時機。
- **B3 / B4 的前瞻性**：P003 實作時採精簡版，尚未正式建立「Phase 3 draft 段落」與「rules.md anchor last-updated」的機械結構。本次 checklist 引導語為前瞻性——後續補強這些結構時，文字無需再改。

### 影響檔案

EN（6 個）：兩版 new-feature-flow.md、兩版 modify-existing-flow.md、兩版 SKILL.md
TW（6 個）：兩版 new-feature-flow_tw.md、兩版 modify-existing-flow_tw.md、兩版 SKILL_tw.md

另：`proposals/PROPOSAL-005b-completion-checklist-wave-b.md`（status + 實施紀錄）

---

## 2026-04-17 — P008 實施：Drift Verification 指令（/dflow:verify）

**關聯 Proposal**: `PROPOSAL-008-drift-verification.md`（approved → implemented）

**前置依賴**: P003（A+C 結構），同日提前實施後解除封鎖。

### 新增

- **`references/drift-verification.md`（兩版）**：5 步驟驗證流程——找檔案 → 擷取 rules.md BR-ID → 擷取 behavior.md BR-ID → 交叉比對 → 回報。三項機械檢查：正向（rules→behavior）、anchor 有效性、反向（behavior→rules）。Core 版額外含 events.md 交叉引用警告。語意層明確排除（Wave D）。
- **`drift-verification_tw.md`（兩版）**：繁體中文翻譯。

### 變更

- **兩版 SKILL.md**：`/dflow:verify [<bc>]` 加入 description frontmatter、決策樹、Slash Commands 段、Reference Files 表格
- **兩版 SKILL_tw.md**：決策樹、Slash 指令段、參考文件表格同步新增 `/dflow:verify`

### 影響檔案

EN（4 個）：兩版 references/drift-verification.md（新增）、兩版 SKILL.md
TW（4 個）：兩版 drift-verification_tw.md（新增）、兩版 SKILL_tw.md

---

## 2026-04-17 — P003 實施：系統現狀 Source of Truth（behavior.md）

**關聯 Proposal**: `PROPOSAL-003-system-source-of-truth.md`（draft → implemented）

**原排程**: Wave B（等 Checkpoint A）。提前實施理由：用戶實際使用 OpenSpec 後確認「一覽行為全貌」是核心需求，不需等團隊回饋。

### 新增

- **`templates/behavior.md`（兩版）**：行為規格彙整模板。按功能區域組織、每個 BR-ID 一個 section 含 Given/When/Then。Core 版額外強調 Aggregate 狀態轉換和 Domain Events。
- **「Behavior Source of Truth」段（兩版 SKILL.md + SKILL_tw.md）**：說明 A+C 結構——`rules.md`（宣告式索引）+ `behavior.md`（情境式內容）——的分工與維護方式。對比 OpenSpec 的 `specs/` 目錄角色。
- **完成流程文件更新段加入 behavior.md**（四個 EN flow + 四個 TW flow）：new-feature-flow 完成時合併 spec 情境；modify-existing-flow 完成時依 Delta 結果更新（合併最終狀態，不保留 Delta 標記）。

### 變更

- **兩版 SKILL.md Project Structure**：`rules.md` 註解改為「BR-ID index」，新增 `behavior.md` 行
- **兩版 `templates/context-definition.md`**：關鍵業務規則段提及 rules.md + behavior.md 分工
- **兩版 SKILL.md Templates 表格**：新增 `behavior.md` 條目

### 影響檔案

EN（12 個）：兩版 templates/behavior.md（新增）、兩版 SKILL.md、四個 flow 檔案、兩版 templates/context-definition.md

TW（6 個）：兩版 SKILL_tw.md、四個 flow TW 檔案

---

## 2026-04-17 — Wave C 實施：P006a / P007a / P007c

**關聯 Proposal**: `PROPOSAL-006a-ddd-depth-column.md`、`PROPOSAL-007a-brownfield-reverse-documentation.md`、`PROPOSAL-007c-claude-md-segmentation.md`（全部 approved → implemented）

**Wave**: C（獨立微調，無前置依賴）

**備註**: P008（`/dflow:verify` drift 驗證）排程為 Wave C 但依賴 P003（A+C 結構，Wave B），本次未實施。

### P006a — Ceremony Scaling 加入 DDD 建模深度欄

- Core 版 `SKILL.md` Ceremony Scaling 表格欄位 `DDD Modeling` → `DDD Modeling Depth`
- 四級深度定義：Full（Aggregate 設計 + Events + Context Map）/ Standard（確認歸屬 + 更新 models/rules）/ Lightweight（確認正確的層）/ Skip
- 「New feature」拆為「New Aggregate / BC」（Full）和「New feature (within existing BC)」（Standard）
- 繁中版同步更新

### P007a — Brownfield 系統性逆向文件化

- WebForms 版 `modify-existing-flow.md` Step 2 新增「Systematic Baseline Capture」子段
- 5 步機會主義掃描指引：讀相關 Code-Behind → 提取業務規則 → 識別領域概念 → 檢查重複邏輯 → 記錄到 domain docs + tech-debt
- 範圍限定在被修改功能和鄰近頁面，不做完整 codebase 掃描
- 繁中版同步更新

### P007c — CLAUDE.md 標題分段強化

- 兩版 `templates/CLAUDE.md` 重構為兩大區塊：
  - `## 系統脈絡（What is this system?）` — 背景、架構、目錄結構
  - `## 開發流程（How do we work?）` — SDD 流程、Git Flow、Domain 規範、AI 協作
- 模板為 AI 使用（英文），無需繁中同步

### 影響檔案

- `sdd-ddd-core-skill/SKILL.md`（P006a）
- `sdd-ddd-core-skill-tw/SKILL_tw.md`（P006a 繁中）
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`（P007a）
- `sdd-ddd-webforms-skill-tw/modify-existing-flow_tw.md`（P007a 繁中）
- `sdd-ddd-webforms-skill/templates/CLAUDE.md`（P007c）
- `sdd-ddd-core-skill/templates/CLAUDE.md`（P007c）
- `proposals/PROPOSAL-006a-ddd-depth-column.md`（status + 實施紀錄）
- `proposals/PROPOSAL-007a-brownfield-reverse-documentation.md`（status + 實施紀錄）
- `proposals/PROPOSAL-007c-claude-md-segmentation.md`（status + 實施紀錄）

---

## 2026-04-17 — 繁體中文版同步 Wave A 全部變更（P001 / P002 / P004 / P005a / P007b）

**關聯 Proposal**: Wave A 全部（P001、P002、P004、P005a、P007b）

### 同步內容

將 Wave A 實施的所有英文版變更同步到繁體中文版（`sdd-ddd-webforms-skill-tw/` 和 `sdd-ddd-core-skill-tw/`），包括：

- **P001**：決策樹改為雙軌（/dflow: 指令 + NL 安全網）；新增完整「工作流程透明度」章節（三層透明度、Phase Gate 位置、確認訊號、/dflow:status 格式）
- **P002**：modify-existing-flow 的 Step 2 改用 Delta 格式（ADDED / MODIFIED / REMOVED / RENAMED + 可選 UNCHANGED）
- **P004**：SKILL 引導問題加 Phase 標記說明；new-feature-flow / modify-existing-flow 加「產生實作任務清單」子段
- **P005a**：new-feature-flow Step 8 / modify-existing-flow 最終 Step 改為四段分層 Completion Checklist（X.1 AI 驗證 / X.2 開發者確認 / X.3 文件更新 / X.4 歸檔）；SKILL 加「完成流程 Checklist 的執行方式」子段
- **P007b**：pr-review-checklist 開頭加 Step 0（理解變更意圖）
- **所有 flow 檔案**加 Phase Gate / step-internal 轉換標記

### 影響檔案（8 個）

- `sdd-ddd-webforms-skill-tw/SKILL_tw.md`
- `sdd-ddd-webforms-skill-tw/new-feature-flow_tw.md`
- `sdd-ddd-webforms-skill-tw/modify-existing-flow_tw.md`
- `sdd-ddd-webforms-skill-tw/pr-review-checklist_tw.md`
- `sdd-ddd-core-skill-tw/SKILL_tw.md`
- `sdd-ddd-core-skill-tw/new-feature-flow_tw.md`
- `sdd-ddd-core-skill-tw/modify-existing-flow_tw.md`
- `sdd-ddd-core-skill-tw/pr-review-checklist_tw.md`

---

## 2026-04-16 — P007b 實施：PR Review Step 0（Spec Intent）

**關聯 Proposal**: `PROPOSAL-007b-pr-review-spec-intent.md`（approved → implemented）

**Wave**: A（D1 優先級從 Wave C 提前至 Wave A，理由：完成 SDD 回饋迴路）

### 新增

- **PR Review Step 0**（兩版 pr-review-checklist.md 開頭）：`/dflow:pr-review` 進入時第一步。四項 checkbox：讀 spec / 讀 Delta（含 RENAMED、可選 UNCHANGED）/ 用一句話陳述 PR 意圖 / 然後才進入 code review。
- **無 spec 的引導話術**：要求 PR author 提供 spec 位置或先開 lightweight-spec。
- **Core 版額外檢查**：若 `behavior.md` 存在，交叉確認 Delta 是否已反映（B3 中途同步 review 閘門）；特別留意 Delta 中的 Aggregate 狀態轉換與 Domain Events。

### 為什麼 D1 提前

原排程放 Wave C 是工作量導向判斷失誤。真正價值在於：沒有 Step 0，reviewer 不會先看 spec，前面所有規格工作（Delta、behavior.md、tasks）就失去驗證機制。SDD 流程的回饋迴路必須由 Step 0 收合。

### 影響檔案

- `sdd-ddd-webforms-skill/references/pr-review-checklist.md`
- `sdd-ddd-core-skill/references/pr-review-checklist.md`
- `proposals/PROPOSAL-007b-pr-review-spec-intent.md`（status + 實施紀錄）

### 繁中版同步狀態

- 已同步（2026-04-17）— 見「繁體中文版同步 Wave A 全部變更」entry。

---

## 2026-04-16 — P005a 實施：完成流程分層 Checklist（Wave A 子集）

**關聯 Proposal**: `PROPOSAL-005a-completion-checklist-wave-a.md`（approved → implemented）；`PROPOSAL-005-completion-flow.md` 同步改為 `superseded`，新增 `PROPOSAL-005b-completion-checklist-wave-b.md`（approved，Wave B，暫不實施）。

**Wave**: A

### 拆分決定

原 P005 依賴 P003 / P004，整體排到 Wave B。評估後發現其中「Domain 純淨度、Given/When/Then 覆蓋、文件更新、歸檔」這些項目不依賴 P003/P004，先行實施可建立驗證文化。拆為 P005a（Wave A，本次實施）+ P005b（Wave B，等 P003/P004 穩定後補上 behavior.md 轉正式 + Tasks 勾選整併）。

### 新增

- **四段分層 Completion Checklist**（兩版 new-feature-flow.md Step 8、modify-existing-flow WebForms Step 6 / Core Step 5）：
  - `X.1 AI-independent verification`：BR/EC/Given-When-Then 覆蓋、Domain 純淨度（無 `System.Web` / 無外部 NuGet）、Tasks 狀態；Core 版補 Aggregate invariants、Domain Events raised、EF Fluent API only。
  - `X.2 Developer-confirmation verification`：AI 一次問一項，不 dump；涵蓋意圖一致性、Aggregate boundary、Domain Event placements、tech-debt 補漏。
  - `X.3 Documentation updates`：glossary / models / rules / events / context-map / tech-debt。
  - `X.4 Archival`：`status` → `completed`、檔案移到 `completed/`。
- **Completion Checklist Execution 子節**（兩版 SKILL.md）：明定 Phase Gate 是唯一觸發點、執行順序嚴格為 X.1 → X.4、AI-independent 一次報完 ✓/✗、Developer-confirmation 一次問一項；若開發者跳過 Phase Gate 直接 commit，auto-trigger 安全網須攔截並引導跑 checklist。

### 重要簡化

取消原提案的「自然語言完成信號偵測」。直接用 P001 `/dflow:next` Phase Gate 作為觸發點，理由：精準、開發者控制感強、免維護信號詞表。

### 新增 Proposal 檔

- `PROPOSAL-005a-completion-checklist-wave-a.md`（本次實施）
- `PROPOSAL-005b-completion-checklist-wave-b.md`（Wave B，尚未實施）
- `PROPOSAL-005-completion-flow.md` 狀態改為 `superseded`，`superseded-by` 指向 P005a + P005b

### 影響檔案

- `sdd-ddd-webforms-skill/references/new-feature-flow.md`
- `sdd-ddd-core-skill/references/new-feature-flow.md`
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
- `sdd-ddd-core-skill/references/modify-existing-flow.md`
- `sdd-ddd-webforms-skill/SKILL.md`
- `sdd-ddd-core-skill/SKILL.md`
- `proposals/PROPOSAL-005-completion-flow.md`
- `proposals/PROPOSAL-005a-completion-checklist-wave-a.md`（新檔）
- `proposals/PROPOSAL-005b-completion-checklist-wave-b.md`（新檔）

### 繁中版同步狀態

- 已同步（2026-04-17）— 見「繁體中文版同步 Wave A 全部變更」entry。

---

## 2026-04-16 — P002 實施：Delta Spec 變更描述格式（含 RENAMED + UNCHANGED）

**關聯 Proposal**: `PROPOSAL-002-delta-spec-format.md`（approved → implemented）

**Wave**: A

### 新增

- **Delta Spec 格式**（兩版 modify-existing-flow.md Step 2）：ADDED / MODIFIED / REMOVED / RENAMED 四種變更操作 + 可選的 UNCHANGED 段落，取代原本自由文字的 Current Behavior / Changed Behavior 對照。
  - `MODIFIED` 必填「原本 / 改為」對照。
  - `RENAMED`（B1 追加）：brownfield 常見的業務概念改名（「簽核」→「審批」等）。
  - `UNCHANGED`（B2 追加）：建議但非必填；regression 風險高時主動填。
  - Core 版的 Delta 格式包含 Aggregate 狀態轉換與 Domain Events，讓 `/dflow:pr-review` Step 0 能讀出語義。
- **lightweight-spec Delta 精簡版**（兩版）：原「現有行為 / 預期行為」改為單一 MODIFIED 條目，保留「原本 / 改為 / 原因」結構；若需要其他 delta 類型指引開發者參考 modify-existing-flow.md。

### 重要澄清（寫入 PROPOSAL-002）

Delta 標記**僅用於變更 spec**，不累積到 P003 的 `behavior.md`（consolidated source of truth）。behavior.md 讀取 delta 後 merge 成當前真相，不保留歷史軌跡（git 已覆蓋）。

### 影響檔案

- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
- `sdd-ddd-core-skill/references/modify-existing-flow.md`
- `sdd-ddd-webforms-skill/templates/lightweight-spec.md`
- `sdd-ddd-core-skill/templates/lightweight-spec.md`
- `proposals/PROPOSAL-002-delta-spec-format.md`（status + B1/B2 追加 + 評估紀錄）

### 繁中版同步狀態

- 已同步（2026-04-17）— 見「繁體中文版同步 Wave A 全部變更」entry。

---

## 2026-04-16 — P004 實施：Spec 模板 Phase 標記 + 實作任務清單（精簡版）

**關聯 Proposal**: `PROPOSAL-004-spec-template-restructure.md`（approved → implemented，精簡版）

**Wave**: A

### 精簡化決定

原方案要求將 feature-spec 模板重構為顯式五段式章節。評估後採精簡版：保留既有段落結構不動，改用 HTML comment phase 標記 + 新增實作任務段落。理由：既有 spec 不需要破壞性遷移；Phase 歸屬可以機讀但對人不干擾。

### 新增

- **HTML comment phase 標記**（兩版 feature-spec.md）：每個段落 heading 行尾加 `<!-- 填寫時機：Phase N -->`，對應 SKILL.md § Guiding Questions by Phase。模板頂部加 template note 區塊說明用途。
- **實作任務段落**（兩版 feature-spec.md）：
  - WebForms 分類 `DOMAIN / PAGE / DATA / TEST`
  - Core 分類對應 Clean Architecture `DOMAIN / APP / INFRA / API / TEST`，標註建議產出順序 `DOMAIN → APP → INFRA → API`
- **Tasks 產生指引**（new-feature-flow Step 5、modify-existing-flow WebForms Step 4 / Core Step 3）：規定 AI 在計畫定稿後把 task 清單寫入 spec，提供格式與範例。
- **Tasks 驗證項**（new-feature-flow Step 8、modify-existing-flow WebForms Step 6 / Core Step 5）：完成時驗證實作任務段全部勾選，或未勾選項明確標註 follow-up。

### 變更

- **SKILL.md § Guiding Questions by Phase**（兩版）：段首補上 HTML comment 標記約定說明，指向「實作任務」段由哪個 flow step 產生。
- modify-existing-flow 中的 tasks 產生限定為「使用完整 feature-spec 時才產生」，bug-fix lightweight-spec 模式跳過。

### 影響檔案

- `sdd-ddd-webforms-skill/templates/feature-spec.md`
- `sdd-ddd-core-skill/templates/feature-spec.md`
- `sdd-ddd-webforms-skill/references/new-feature-flow.md`
- `sdd-ddd-core-skill/references/new-feature-flow.md`
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
- `sdd-ddd-core-skill/references/modify-existing-flow.md`
- `sdd-ddd-webforms-skill/SKILL.md`
- `sdd-ddd-core-skill/SKILL.md`

### 繁中版同步狀態

- 已同步（2026-04-17）— 見「繁體中文版同步 Wave A 全部變更」entry。

---

## 2026-04-16 — P001 實施：指令化入口 + 流程透明化（Hybrid）

**關聯 Proposal**: `PROPOSAL-001-workflow-transparency.md`（approved → implemented）

**Wave**: A

### 新增

- **`/dflow:` slash command 族**：`/dflow:new-feature`、`/dflow:modify-existing`、`/dflow:bug-fix`、`/dflow:pr-review`、`/dflow:status`、`/dflow:next`、`/dflow:cancel`
- **Workflow Transparency 章節**（兩版 SKILL.md）：涵蓋 Slash Commands 定義、Auto-Trigger Safety Net 規則、Three-Tier Transparency（Flow entry / Phase gate / Step-internal）、Confirmation Signals（NL ↔ command 等價）、`/dflow:status` 回應格式
- **Phase Gate markers**（兩版 new-feature-flow.md + modify-existing-flow.md）：每個 step 邊界標註為 Phase Gate 或 step-internal，Phase Gate 處有具體發話內容 + wait for confirmation 規則

### 變更

- **description 欄位**（兩版 SKILL.md）：從單純 NL 觸發改為 PRIMARY（/dflow: commands）+ SECONDARY（NL 安全網，判斷後等確認、不自動前進）
- **Decision Tree**（兩版 SKILL.md）：重寫為「/dflow: 命令 → 直接路由；NL → 判斷後等確認」雙軌結構
- **modify-existing-flow 的 `/dflow:bug-fix` 說明**：兩版在頂部明確寫出 bug-fix 模式下的 ceremony 調整（用 lightweight-spec、預設 defer 抽離/DDD 影響判斷）

### 實施期發現的結構差異

- Core 版 modify-existing-flow 實際為 5 步（非原 P001 假設的 6 步），Phase Gate 位置對應調整為 2→3 / 3→4 / 4→5。兩版 SKILL.md 的 Workflow Transparency 段落分別列出各自的 Phase Gate 位置。

### 影響檔案

- `sdd-ddd-webforms-skill/SKILL.md`
- `sdd-ddd-webforms-skill/references/new-feature-flow.md`
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
- `sdd-ddd-core-skill/SKILL.md`
- `sdd-ddd-core-skill/references/new-feature-flow.md`
- `sdd-ddd-core-skill/references/modify-existing-flow.md`

### 繁中版同步狀態

- 已同步（2026-04-17）— 見「繁體中文版同步 Wave A 全部變更」entry。
