# Round 7 Implement Review Prompt — Wave 1-3 實作品質審查（給 Codex CLI 使用）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Codex CLI。
> **產出位置**：Codex 寫入 `reviews/round-7-implement-review-report.md`。
> **禁止事項**：Codex 此輪**不修改任何檔**（proposal、skill 檔、CHANGELOG、V2 儲存庫皆不動），只產出 review 報告。
> **R7 Implement Review 的本質**：審**實作產物 vs approved proposal 的忠誠度**（回到 R1-R6 的「意圖 vs 實作」模式；不同於 R7 draft review 的「proposal 本身品質」審查）。三 wave 已完成：Wave 1 = PROPOSAL-011（Git Flow Decoupling），Wave 2 = PROPOSAL-009（Feature 目錄化 + 多階段規格 + 新命令），Wave 3 = PROPOSAL-010（`dflow init` + Scaffolding）。
> **模型選擇**：建議 **gpt-5.4 xhigh**（與 R1-R6 precedent 一致，整體推理能力勝過 5.3-codex 特化）。
> **範圍規模**：3 份 proposal × 20-30 skill 檔 + 3 段 CHANGELOG + R7 decisions + R7 draft review report；預估 Finding 10-30 項（若產出 >50 項需反思是否 severity 過嚴或 scope 越界）。
> **預估時程**：90-120 分鐘（若 Codex session time limit 小於此，建議分兩段：第一段跑 Wave 1+2 Dimension 1 + 2；第二段跑 Wave 3 + Dimension 3+4）。

---BEGIN PROMPT---

你是 Dflow Round 7 Implement Review 的審查者。本輪範圍為 **R7 三 Wave 實施產物 vs approved proposal 意圖** 的對照審查，回到 R1-R6 的「意圖 vs 實作」審查模式。

## R7 Implement Review 與 R7 Draft Review 的差異

| 面向 | R7 Draft Review（已完成，`reviews/round-7-report.md`） | R7 Implement Review（本輪） |
|---|---|---|
| 審查對象 | 3 份 draft proposal 本身 | 3 份 approved proposal 的**實施產物**（skill 檔） |
| 審查軸 | Proposal 內部品質 + 三份之間交叉一致性 | 實施 vs proposal 意圖對照 + 跨 wave 一致性 |
| Severity 重心 | Proposal 設計缺陷 | 實施違背 proposal 意圖 |
| V2 角色 | 背景閱讀（理解 proposal 描述是否忠於 V2）| **不讀**（V2 無關實施品質）|
| Cross-round-notes | R7 範圍內延伸議題 | 同（但本輪發現的新議題）|

## 第一步：讀入必要檔案

### A. 實施權威來源（必讀）

1. `proposals/PROPOSAL-011-git-flow-decoupling.md`（approved；Wave 1 的藍圖）
2. `proposals/PROPOSAL-009-feature-directory-and-phases.md`（approved；Wave 2 的藍圖，§ A-I 決議 1-19 + F-01/F-02/F-03/F-04 finding 對應段）
3. `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md`（approved；Wave 3 的藍圖）

### B. R7 context（finding 解析追溯）

4. `reviews/round-7-decisions.md`（7 個 finding 的 accept path 定案 + 具體修訂段落）
5. `reviews/round-7-report.md`（Codex 原始 finding）

### C. 實施紀錄（了解 wave 邊界）

6. `CHANGELOG.md` 最上方三段：
   - `## {Date} — R7 Wave 3 實施：PROPOSAL-010 ...`
   - `## {Date} — R7 Wave 2 實施：PROPOSAL-009 ...`
   - `## {Date} — R7 Wave 1 實施：PROPOSAL-011 ...`
   
   這三段是實施者（Cursor Opus 4.7 High + Claude Code 主力）自報的改動摘要 —— 是**自述**，需要 Codex 用實際檔案 verify。

### D. 流程規範

7. `proposals/review-workflow.md`（§三 Review 報告格式 + §五 輪次執行順序）

### E. 實施產物（依各 proposal § 影響範圍表核對；不需全讀，選讀關鍵段）

**Wave 1 / PROPOSAL-011 相關**：
- `sdd-ddd-webforms-skill/references/git-integration.md`（原 git-flow-integration.md `git mv` 重命名 + 重構）
- `sdd-ddd-core-skill/references/git-integration.md`
- `sdd-ddd-webforms-skill/SKILL.md`（references 表 / `/dflow:bug-fix` 描述 / 決策樹）
- `sdd-ddd-core-skill/SKILL.md`
- `sdd-ddd-webforms-skill/references/drift-verification.md`（移除 "Before a release branch cut" 觸發點）
- `sdd-ddd-core-skill/references/drift-verification.md`
- `sdd-ddd-webforms-skill/templates/CLAUDE.md`（Git Flow 措辭中立化）
- `sdd-ddd-core-skill/templates/CLAUDE.md`

**Wave 2 / PROPOSAL-009 相關**：
- `sdd-ddd-webforms-skill/templates/_index.md`（新增）
- `sdd-ddd-core-skill/templates/_index.md`
- `sdd-ddd-webforms-skill/templates/phase-spec.md`（`git mv` from feature-spec.md + 新增「Delta from prior phases」段）
- `sdd-ddd-core-skill/templates/phase-spec.md`
- `sdd-ddd-webforms-skill/templates/lightweight-spec.md`（使用指引更新）
- `sdd-ddd-core-skill/templates/lightweight-spec.md`
- `sdd-ddd-webforms-skill/references/new-phase-flow.md`（新增）
- `sdd-ddd-core-skill/references/new-phase-flow.md`
- `sdd-ddd-webforms-skill/references/finish-feature-flow.md`（新增）
- `sdd-ddd-core-skill/references/finish-feature-flow.md`
- `sdd-ddd-webforms-skill/references/new-feature-flow.md`（Step 3.5 Slug Confirmation 新增、Step 4/Step 8.4 目錄化）
- `sdd-ddd-core-skill/references/new-feature-flow.md`
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`（三層 Ceremony 判斷樹 + completed feature reopen § I）
- `sdd-ddd-core-skill/references/modify-existing-flow.md`
- `sdd-ddd-webforms-skill/references/git-integration.md`（Directory Moves Must Use git mv 完整段 + slug 語言規則）
- `sdd-ddd-core-skill/references/git-integration.md`
- `sdd-ddd-webforms-skill/references/pr-review-checklist.md`（多份 phase-spec 檢查邏輯）
- `sdd-ddd-core-skill/references/pr-review-checklist.md`
- `sdd-ddd-webforms-skill/references/drift-verification.md`（路徑更新 + F-03 scope 不擴張註記）
- `sdd-ddd-core-skill/references/drift-verification.md`
- `sdd-ddd-webforms-skill/SKILL.md`（三層 Ceremony / 新命令 / Templates 段）
- `sdd-ddd-core-skill/SKILL.md`
- `sdd-ddd-webforms-skill/templates/CLAUDE.md`（feature 結構同步）
- `sdd-ddd-core-skill/templates/CLAUDE.md`

**Wave 3 / PROPOSAL-010 相關**：
- `sdd-ddd-webforms-skill/scaffolding/`（5 檔：`_overview.md` / `_conventions.md` / `Git-principles-gitflow.md` / `Git-principles-trunk.md` / `CLAUDE-md-snippet.md`）
- `sdd-ddd-core-skill/scaffolding/`（5 檔）
- `sdd-ddd-webforms-skill/references/init-project-flow.md`（新增）
- `sdd-ddd-core-skill/references/init-project-flow.md`
- `sdd-ddd-webforms-skill/SKILL.md`（`/dflow:init-project` 加入）
- `sdd-ddd-core-skill/SKILL.md`
- `README.md`（repo 根；「How to adopt Dflow」段）

### F. 不在範圍內（明確排除）

- **繁中版**（`*-tw/` 所有檔）—— 延至 Closeout C1
- **V2 儲存庫**（`E:\工作區\SDD-Starter-Kit`）—— 本輪**不讀不審**，實施已完成，V2 比對已由 draft review 處理完
- **Obts**（`E:\VsProject\Obts`）—— 同上
- **Proposal 本身的設計品質**（已 approved；若發現 proposal 有遺漏問題應標 `out-of-scope` → cross-round-notes）
- **Tutorial 相關**（屬 Closeout C2 範圍）
- **P001-P007 既有 proposal 的既有產物本體**（只檢查是否被 R7 三 wave **意外破壞**——regression check，不評論它們自身品質）

## 第二步：審查維度（4 個 lens，逐 wave 各看一輪，再做 cross-wave）

### Dimension 1 — 各 Wave 的實施忠誠度（proposal 意圖 vs 實作對照）

對每個 wave，逐項比對 proposal § 影響範圍表 × 實施產物：

**Wave 1 / PROPOSAL-011 驗收清單**：

- [ ] **檔名重命名**：`references/git-flow-integration.md` → `git-integration.md`（雙版）；舊檔名**不存在**；新檔名**存在**
  - 可用 `git log --follow sdd-ddd-webforms-skill/references/git-integration.md` verify `git mv` 保留歷史（若沒保留，標 Major）
- [ ] **移除 Git Flow 專屬內容**（proposal § 3）：
  - `git-integration.md` 不應含 `develop` / `release branch` / `hotfix` / `24.*hour` 等字樣（或僅在「若專案採 Git Flow，可參考 `scaffolding/Git-principles-gitflow.md`」類中立註解內出現）
  - develop/release/hotfix 分支結構圖已被簡化版（`main ← feature/{SPEC-ID}-{slug}`）取代
- [ ] **保留 SDD ↔ Git 核心耦合**（proposal § 2）：
  - Branch 命名規範、`git mv` 規範總則、SDD 產物與 commit 時機、Gate Checks（SDD 相關）、Commit Message Convention、CI/CD Future Enhancement 段應存在
- [ ] **`/dflow:bug-fix` 語意澄清**（proposal § 4）：
  - SKILL.md（雙版）的 Slash Commands 表 + 決策樹 + Frontmatter 中，`/dflow:bug-fix` 描述含「Lightweight-ceremony modification of existing functionality」+「Not tied to any branch strategy (not Git Flow's hotfix)」類措辭
- [ ] **drift-verification.md 輕量改動**（proposal § 5，F-06 決議）：
  - 「Before a release branch cut」觸發點**已移除**（雙版 `webforms:122` / `core:132` 原位）
  - 其他觸發點（pre-PR / after refactor / onboarding）**未動**
- [ ] **未動的檔 verify**（F-06 決議 scope 收斂）：
  - `references/modify-existing-flow.md`（雙版）：現況應無 Git Flow 字樣；若本 wave 有修改，應只限 hotfix 語句移除
  - `references/pr-review-checklist.md`（雙版）：同上

**Wave 2 / PROPOSAL-009 驗收清單**（規模最大，分 A-F 子清單）：

**A. 結構類（決議 1-6）**
- [ ] SKILL.md（雙版）Project Structure 範例已從單檔改為目錄（`active/{SPEC-ID}-{slug}/` 含 `_index.md` / `phase-spec-*.md` / 可選 `lightweight-*.md`）
- [ ] `active/` / `completed/` / `backlog/` 三狀態分類保留（拒絕年份分類）

**B. 命名類（決議 7-8）**
- [ ] SKILL.md（雙版）或 `git-integration.md` 明示結構詞英文（`phase-spec` / `_index` / `SPEC-`）+ slug 跟隨討論語言

**C. 命令類（決議 9-10）**
- [ ] SKILL.md（雙版）Primary triggers 加入 `/dflow:new-phase`、`/dflow:finish-feature`
- [ ] 決策樹新增兩節點，指向 new-phase-flow.md / finish-feature-flow.md
- [ ] Slash Commands 表新增「階段類」+「收尾類」分類
- [ ] `references/new-phase-flow.md`（雙版）新增：含階段 slug 確認、phase-spec 撰寫、`_index.md` BR Snapshot refresh；Step 1 明示「嚴格只適用於 active feature；若偵測到 completed/，拒絕執行並導向 `/dflow:modify-existing` follow-up」
- [ ] `references/finish-feature-flow.md`（雙版）新增：含驗證所有 phase-spec status + `_index.md` 未結項檢查 + BC 層同步（延續 P003/P007a Step 8.3/5.3）+ `git mv` 搬目錄 + **Integration Summary 產出（Git-strategy-neutral）**
  - **Integration Summary** verify：Grep `Integration Summary` 應有命中；Grep `merge commit 訊息建議`（舊措辭）應**無**命中（F-04 Path B）
  - AI vs 使用者動作邊界明確（AI 做 `git mv` + `git add` + 產出 Summary 文字；使用者做 merge/push）

**D. 流程類（附帶產出）**
- [ ] `references/new-feature-flow.md`（雙版）含 Step 3.5 Slug Confirmation（AI 提案 SPEC-ID + slug + 目錄名 + 分支名，使用者確認後才建）
- [ ] Step 4（建 spec）改為「建目錄 + `_index.md` + 第一份 phase-spec（首 phase，Delta 段可空或註『首 phase，無前置 Delta』）」
- [ ] Step 8.4 archival 改為「整個 feature 目錄 `git mv` 到 `completed/`」
- [ ] `references/modify-existing-flow.md`（雙版）含三層 Ceremony 判斷樹（T1/T2/T3 + T3 四項判準 + Dflow 不走底線）
- [ ] `modify-existing-flow.md` 含 completed feature 偵測與 follow-up 分流（§ I 決議 17-18）：AI 主動詢問、follow-up feature 新配 SPEC-ID、Metadata `follow-up-of` 必填、BR 從 BC 層繼承 baseline
- [ ] **禁止 T2/T3 寫回 completed 原目錄**（§ I 決議 17）有明確表述

**E. Git 整合類（決議 11）**
- [ ] `references/git-integration.md`（雙版）新增「Directory Moves Must Use git mv」**完整段**（不只是 P011 留的框架）
- [ ] Branch naming 段補 slug 語言規則（對應 P011 留的 placeholder）

**F. § H 範本與 Ceremony 終局（F-01 Path A-3）**
- [ ] `templates/_index.md`（雙版）**新增**，含六段結構：Metadata / 目標與範圍 / Phase Specs / **Current BR Snapshot** / 輕量修改紀錄 / 接續入口
- [ ] Metadata 段含選用 `follow-up-of` 欄（§ I 決議 18）
- [ ] 末段含選用 **Follow-up Tracking** 段（§ I 決議 19）
- [ ] `templates/phase-spec.md`（雙版）**存在**（用 `git log --follow` verify 是從 `feature-spec.md` `git mv` 而來）
- [ ] `templates/feature-spec.md`（雙版）**不存在**
- [ ] `templates/phase-spec.md` 含「Delta from prior phases」段（置於「業務規則」之後、「邊界情況」之前；首 phase 免填 / phase 2+ 必填；格式沿用 P002/P004）
- [ ] `templates/lightweight-spec.md` 使用指引更新為 T2 ceremony + 實例化位置（feature 目錄內 + `lightweight-{YYYY-MM-DD}-{slug}.md` / `BUG-{NUMBER}-{slug}.md`）

**G. § I Completed Feature Reopen 生命週期（F-02 Path C）**
- [ ] 決議 16：`/dflow:new-phase` 嚴格 active-only（new-phase-flow.md Step 1 明示）
- [ ] 決議 17：`modify-existing-flow` 的 completed feature 偵測邏輯
- [ ] 決議 18：Follow-up feature 新建規則（新 SPEC-ID、Metadata `follow-up-of` 必填、頂部註記、BR baseline 繼承）
- [ ] 決議 19：舊 `_index.md` Follow-up Tracking 段反向更新；新→舊為權威連結

**H. drift-verification 路徑 + scope（F-03 決議）**
- [ ] `references/drift-verification.md`（雙版）路徑假設更新為 `active/{SPEC-ID}-{slug}/`（目錄化）
- [ ] `rules.md` ↔ `behavior.md` 機械對照邏輯**不變**（只更新路徑，不改演算法）
- [ ] 明示**不擴張** scope 到跨 phase-spec 聚合（BR 現況由 `_index.md` Current BR Snapshot 承擔）

**I. pr-review-checklist（多份 phase-spec 檢查邏輯）**
- [ ] `references/pr-review-checklist.md`（雙版）調整為「per phase-spec 檢查」+ 「`_index.md` Current BR Snapshot 與 BC 層一致性」+ 「follow-up feature 的 `follow-up-of` 欄位 verify」

**J. templates/CLAUDE.md 同步**
- [ ] 雙版「開發流程」段內 feature 結構說明更新（目錄化 + 新命令 + 三層 Ceremony）
- [ ] 「系統脈絡」段**不動**（P007c 保留）
- [ ] Core 版「架構」段**不動**

**Wave 3 / PROPOSAL-010 驗收清單**：

- [ ] **`scaffolding/` 目錄**（雙版，共 10 檔）**新增**：
  - `_overview.md` / `_conventions.md` / `Git-principles-gitflow.md` / `Git-principles-trunk.md` / `CLAUDE-md-snippet.md` 各雙版
  - 每份頂部有版本註記 `<!-- Scaffolding template ... -->`
- [ ] **雙版差異**：WebForms 版強調漸進式 Domain 抽離 / 遷移策略；Core 版強調 Clean Architecture + DDD
- [ ] **`_conventions.md` 引用（不重抄）** SKILL.md 的三層 Ceremony T1/T2/T3
- [ ] **`CLAUDE-md-snippet.md`** 沿用 P007c H2 分段（系統脈絡 / 開發流程）
- [ ] **`Git-principles-gitflow.md`** 含：
  - 分支結構（develop / release / feature / hotfix）
  - Commit 格式 + Gate Checks（Obts 風格：release 前、hotfix 24h 補 spec）
  - **Integration Commit 訊息慣例段（F-04 Path B）**：Merge commit 格式範例（`--no-ff`）對應 P009 `/dflow:finish-feature` 的 Integration Summary
  - 24h 補 spec 說明為「對人的承諾」
- [ ] **`Git-principles-trunk.md`** 含：
  - 單一 main + feature branch + Merge strategy 選項（squash / rebase / ff）
  - **Integration Commit 訊息慣例段（F-04 Path B）**：squash / rebase / ff commit 格式範例
  - 不含 hotfix / release branch 規範
- [ ] **`references/init-project-flow.md`**（雙版）**新增**，含 5 Step：現況盤點 / 5 題問答 / 檔案清單預覽 / 逐檔 Write / 結果報告
- [ ] **Step 3 必產清單依 skill 版本分拆（F-05 決議）**：
  - WebForms 版：`specs/features/{active,completed,backlog}/` + `specs/domain/glossary.md` + **`specs/migration/tech-debt.md`**（**非** `architecture/`）
  - Core 版：上列 + **`specs/domain/context-map.md`** + `specs/architecture/tech-debt.md` + `specs/architecture/decisions/`
- [ ] **`behavior.md` 明確不在 init 必產清單**（F-05 決議）：由 P003 completion flow 或 P007a baseline capture 於首個 BC 建立時產生
- [ ] **既有檔不覆寫原則**：Step 4 邏輯含「skip if exists, unless explicitly requested」+ Step 3 產出前 list preview
- [ ] **SKILL.md（雙版）更新**：
  - Primary triggers 加入 `/dflow:init-project`
  - 決策樹節點：`/dflow:init-project → INIT PROJECT FLOW (references/init-project-flow.md)`
  - Slash Commands 表新增「專案級命令」子分類**置於「啟動類」之上**
  - Reference Files 表加 `init-project-flow.md`
  - Templates 段改為「Templates & Scaffolding」，Scaffolding 子段含 5 檔清單
- [ ] **README.md（repo 根）**：
  - 新增「How to adopt Dflow in your project」四步段
  - **步驟 1 不寫死 `.claude/skills/` 路徑**（F-07 決議；Grep `.claude/skills/` 應無命中）
  - 新增 V2 / Dflow 分工說明
  - **既有段落不被改動**（只插入新段）

### Dimension 2 — 跨 Wave 一致性（本輪核心）

**Cross-wave 檢查點**（逐項 verify）：

**CW-01 · 011 留的 placeholder ↔ 009 填齊**
- Wave 1 的 `git-integration.md` 保留 branch naming placeholder（「slug 語言段落細節由 PROPOSAL-009 決議 8 正式納入」）
- Wave 2 應在此檔擴增完整 slug 語言規則 + 完整「Directory Moves Must Use git mv」段
- 檢查：完整段是否確實寫入？框架是否從「標記待填」演化為「填好內容」？

**CW-02 · 009 `_index.md` 範本 ↔ 010 init-project-flow 引用**
- Wave 2 新增 `templates/_index.md`（六段結構）
- Wave 3 的 init-project-flow 若引用 `_index.md` 範本，引用應準確（路徑、範本結構描述）
- 檢查：init-project-flow Step 4 產出的 `_index.md` 是否與 P009 範本一致？

**CW-03 · 009 Integration Summary ↔ 010 Git-principles Integration Commit 慣例段**（F-04 Path B）
- Wave 2 的 `/dflow:finish-feature` 產出 Git-strategy-neutral Integration Summary
- Wave 3 的 Git-principles-gitflow.md / trunk.md 各補 Integration Commit 訊息慣例段，把 Summary 格式化為具體 commit 訊息
- 檢查：兩份檔的搭配是否明確？009 的 Summary 字段（feature 目標 / 變動範圍 / 關聯 BR-ID / phase 清單）是否與 010 的 commit 格式範例 compatible？
- 反面：010 的 Git-principles 是否不小心把「Integration Commit 訊息」寫成 P009 的責任（職責錯位）？

**CW-04 · 009 三層 Ceremony ↔ 010 `_conventions.md` 引用**
- Wave 2 的 SKILL.md（雙版）定義三層 Ceremony T1/T2/T3
- Wave 3 的 `_conventions.md` scaffolding 應**引用而非重寫**此分類
- 檢查：`_conventions.md` 是否誤把 T1/T2/T3 判準重抄？若重抄，未來 SKILL.md 更新會造成雙處維護負擔（職責混雜）

**CW-05 · 011 Git Flow 解耦 ↔ 010 Git-principles-trunk.md 中立前提**
- Wave 1 解耦後，Dflow 本體對 Git Flow 中立
- Wave 3 的 trunk-based 範本在此中立前提下才合理
- 檢查：trunk-based 範本是否不小心引用被 011 移除的內容（例如 "release branch" / "hotfix branch"）？

**CW-06 · 009 完整 `Directory Moves Must Use git mv` 段 ↔ 011 總則**
- Wave 1 建框架（一段總則）
- Wave 2 擴增完整段（適用情境清單 + 理由）
- 檢查：完整段是否存在 + 覆蓋 P009 決議 11 列出的所有情境（finish-feature 搬目錄 / phase-spec 重命名 / feature slug 微調 / template 層級重命名）

**CW-07 · Wave 1/2/3 CHANGELOG 完整性**
- CHANGELOG 應有三段 R7 Wave 1/2/3 條目
- 每段的「影響範圍」應與對應 proposal 的 § 影響範圍表吻合（可有措辭差異，不該有大幅遺漏）
- 檢查：CHANGELOG 自述 vs 實際改動是否有 drift？

**CW-08 · SKILL.md 雙版對三 wave 的吸收一致性**
- Wave 1：`/dflow:bug-fix` 語意澄清 + `git-integration.md` 檔名更新
- Wave 2：Primary triggers + 決策樹 + Slash Commands 表 + Templates 段 + Project Structure + Ceremony Scaling
- Wave 3：Primary triggers + 決策樹 + Slash Commands 表「專案級命令」子分類 + Templates & Scaffolding 段
- 檢查：三 wave 的累積改動是否在 SKILL.md 雙版內部保持結構連貫（不重複、不自相矛盾）？

### Dimension 3 — R7 Approve Decisions 的 Accept 項是否落實

對照 `reviews/round-7-decisions.md` 的 7 個 finding（F-01 ~ F-07），逐項 verify：

**F-01（accept-with-choice Path A-3）— phase-spec 重命名 + Delta 段 + `_index.md` 六段 + 三層 Ceremony**
- [ ] 5 項 Path A-3 定案全部落實（見 Wave 2 F-J 子清單）

**F-02（accept-with-choice Path C）— completed feature reopen 走 follow-up**
- [ ] 4 項決議（16-19）全部落實（見 Wave 2 G 子清單）

**F-03（accept）— `/dflow:verify` scope 收斂**
- [ ] 3 項決議全部落實（見 Wave 2 H 子清單）

**F-04（accept-with-choice Path B）— Integration Summary 中立化 + Git-principles 補慣例段**
- [ ] P009：`/dflow:finish-feature` 產出改為 Integration Summary（Git-strategy-neutral）
- [ ] P010：`Git-principles-gitflow.md` + `Git-principles-trunk.md` 各新增 Integration Commit 訊息慣例段
- [ ] P011：關聯表 PROPOSAL-009 列追加一致性備註（可 verify `proposals/PROPOSAL-011:133` 附近是否保留此備註，但 proposal 本身不該被 wave 實施修改）

**F-05（accept）— init 必產清單分拆 + `behavior.md` 時機修正**
- [ ] init-project-flow Step 3 WebForms / Core 分拆必產清單正確
- [ ] `behavior.md` 不在 init 必產 + 說明來自 P003 / P007a

**F-06（accept）— P011 scope 精準化**
- [ ] `drift-verification.md` 雙版「Before a release branch cut」移除
- [ ] `modify-existing-flow.md` / `pr-review-checklist.md` 雙版未被 Wave 1 修改（Grep verify 本來就無 Git Flow 字樣）

**F-07（accept）— README 中立措辭**
- [ ] README.md 步驟 1 中立；無 `.claude/skills/` 路徑

### Dimension 4 — P001-P008 Regression Check

三 wave 改動應只**擴充**，不**破壞** R1-R6 既有機制。逐項 verify：

- [ ] **P003**：`rules.md` / `behavior.md` 的 BR-ID anchor 結構、Step 8.3 / 5.3 sync 機制**未被破壞**（P009 finish-feature 呼叫既有機制，不重新發明）
- [ ] **P005 / 005a / 005b**：Completion checklist 結構（8.1-8.4 / 5.1-5.4）**保留**；Step 8.4 / 5.4 archival 改為「搬目錄」是**擴充**非**破壞**；實作任務段、AI-independent verification、developer-confirmation verification、documentation updates 全部保留
- [ ] **P007a**：Brownfield baseline capture 機制**未被動**（只補了「behavior.md 由此機制產生」的說明）
- [ ] **P007c**：CLAUDE.md H2 分段（系統脈絡 / 開發流程）**保留**；Core 版「架構」段保留為 Core 獨有；WebForms 版**沒有**「架構」段（刻意設計，不補）
- [ ] **P008**：`/dflow:verify` 的 `rules.md` ↔ `behavior.md` 機械對照邏輯**未被改**（只更新路徑 + 移除 release branch 觸發點）；不擴張 scope 到跨 phase-spec 聚合
- [ ] **P001**：`/dflow:` slash command 前綴 + 自然語言 auto-trigger safety net 設計保留；新命令（`/dflow:init-project` / `new-phase` / `finish-feature`）遵循同樣 pattern
- [ ] **P002 / P004**：Delta 格式（ADDED / MODIFIED / REMOVED / RENAMED / UNCHANGED）**沿用**於 phase-spec 新增的「Delta from prior phases」段（P009 決議 12）
- [ ] **P006 / 006a**：Ceremony Scaling 表被**重寫**為三層（T1/T2/T3），但 DDD Depth 欄位（Core 版）保留 —— verify DDD Depth 不因三層改寫而消失

## 第三步：Finding 格式

每個 Finding 採以下格式寫入報告：

```markdown
### F-NN {一句話問題摘要}

**嚴重度**: Critical | Major | Minor | Question
**對照**:
  - Wave：W1 / W2 / W3 / cross-wave
  - Proposal：`PROPOSAL-0XX § 段落`
  - Decision（若對應 R7 Approve finding）：`round-7-decisions.md F-XX`
  - 實施產物：`path/to/file.md:line-range`
**維度**: Dimension 1 / 2 / 3 / 4
**現況**: {實際讀到什麼}
**問題**: {為什麼有問題——遺漏 / 矛盾 / 違背 proposal 意圖 / regression / 兩版不一致 / 跨 wave 不同步}
**建議**: {具體怎麼改；Question 類寫「需釐清：...」}
```

### 嚴重度定義（R7 Implement Review 專屬，回歸 R1-R6 標準）

- **Critical**：實施**違背** approved proposal 意圖，或導致 flow / 命令**無法運作**、或破壞 R1-R6 既有機制導致 regression。嚴格使用。
- **Major**：實施不完整（影響範圍表明列但未落實）、兩版（WebForms / Core）不一致且無正當理由、跨 wave 明顯不同步、R7 Approve decisions 的 accept 項未落實（且涉及核心設計）。
- **Minor**：措辭不夠精確、範本格式細節、列表遺漏、標題不一致、可讀性、accept 項未落實但影響次要。
- **Question**：Reviewer 不確定實施意圖與 proposal 描述是否一致，需設計者釐清。

**Severity 判讀原則**：
- 若 proposal 明列但實施缺失 → 至少 Major
- 若 proposal 明列某路徑（path A-3 / Path C / Path B）但實施走了不同 path → Critical
- 若是措辭 / 格式 / 細節差異 → Minor
- 若無法確定 proposal 意圖 → Question
- 三 wave 都無法實施某決議的情境 → 可能 Critical（需升級 flag）

## 第四步：產出格式

寫入 `reviews/round-7-implement-review-report.md`，結構如下：

```markdown
# Round 7 Implement Review Report — Wave 1-3 實作品質審查

**審查範圍**: PROPOSAL-011 / PROPOSAL-009 / PROPOSAL-010 的 Wave 1-3 實施產物
**審查類型**: Implement review（實施忠誠度 + 跨 wave 一致性 + P001-P008 regression）
**審查日期**: YYYY-MM-DD
**審查者**: Codex（gpt-5.4 xhigh）

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| {總數} | {數} | {數} | {數} | {數} |

## 按 Wave 分類

| Wave | Proposal | Finding 數 | 建議下一步 |
|---|---|---|---|
| Wave 1 | PROPOSAL-011 | N | clean / needs-fix |
| Wave 2 | PROPOSAL-009 | N | 同上 |
| Wave 3 | PROPOSAL-010 | N | 同上 |
| Cross-wave | — | N | 同上 |

> 「建議下一步」是 Reviewer 初判；最終修正決定由 R7 Implement Approve session 處理。

## Findings

### F-01 ...

（排序：Critical → Major → Minor → Question；同嚴重度內按 wave → finding 位置）

## 跨 Wave 一致性對照表

| 檢查點 | 主張方 | 回應方 | 一致？ | 對應 Finding |
|---|---|---|---|---|
| CW-01 · 011 placeholder ↔ 009 填齊 | W1 `git-integration.md` § branch naming | W2 `git-integration.md` § 擴增 | ✓ / ✗ / ⚠ | F-NN |
| CW-02 · 009 `_index.md` ↔ 010 init-flow 引用 | W2 `templates/_index.md` | W3 `init-project-flow.md` Step 4 | | |
| CW-03 · 009 Integration Summary ↔ 010 Git-principles 格式段 | W2 `finish-feature-flow.md` | W3 `Git-principles-{gitflow,trunk}.md` | | |
| CW-04 · 009 三層 Ceremony ↔ 010 `_conventions.md` 引用 | W2 SKILL.md | W3 `_conventions.md` | | |
| CW-05 · 011 Git Flow 解耦 ↔ 010 trunk.md 中立前提 | W1 `git-integration.md` | W3 `Git-principles-trunk.md` | | |
| CW-06 · 009 完整 `git mv` 段 ↔ 011 總則 | W1 `git-integration.md` 總則 | W2 `git-integration.md` 擴增 | | |
| CW-07 · Wave 1/2/3 CHANGELOG 完整性 | CHANGELOG.md | 實施檔 | | |
| CW-08 · SKILL.md 雙版吸收三 wave 一致性 | W1/W2/W3 累積 | SKILL.md 雙版結構 | | |

## R7 Approve Decisions 落實對照

| Finding | Path / 決議 | 落實狀態 | 對應實施 Finding |
|---|---|---|---|
| F-01 | accept-with-choice Path A-3 | ✓ / ✗ / ⚠ | F-NN |
| F-02 | accept-with-choice Path C | | |
| F-03 | accept | | |
| F-04 | accept-with-choice Path B（跨 3 proposal）| | |
| F-05 | accept | | |
| F-06 | accept | | |
| F-07 | accept | | |

## P001-P008 Regression Check

逐項 verify（見 Dimension 4 清單）：

| Proposal | 核心機制 | 受 R7 三 wave 影響狀態 | 對應 Finding（若有） |
|---|---|---|---|
| P001 | /dflow: 前綴 + NL safety net | 擴充 / 未動 / **破壞** | — |
| P002 / P004 | Delta 格式 | | |
| P003 | rules.md / behavior.md / Step 8.3-5.3 sync | | |
| P005 / 005a / 005b | Completion checklist 結構 | | |
| P006 / 006a | Ceremony Scaling + DDD Depth | | |
| P007a | Brownfield baseline capture | | |
| P007c | CLAUDE.md H2 分段 | | |
| P008 | /dflow:verify 機械對照 | | |

## 雙版一致性備註（WebForms / Core）

| 檢查項 | WebForms | Core | 一致？ |
|---|---|---|---|
| 三層 Ceremony 表 | `sdd-ddd-webforms-skill/SKILL.md:XXX` | `sdd-ddd-core-skill/SKILL.md:XXX` | ✓ / ✗ |
| `_index.md` 範本 | | | |
| `phase-spec.md` Delta 段 | | | |
| `/dflow:new-phase` / `finish-feature` 定義 | | | |
| `/dflow:init-project` 定義 | | | |
| Scaffolding 5 檔存在 | | | |
| 刻意差異說明（Core 版 DDD Depth / 架構段 / Aggregate 等）| | | |

## 審查備註

- （任何需要補充的觀察、審查過程中的限制、無法 verify 的項目等）

---

**跨輪議題**：若審查過程順手看到其他議題（proposal 本身設計、Closeout 議題、未來 wave 構想等），記入 `reviews/cross-round-notes.md`，**不佔本輪 Finding 數量**。
```

## 第五步：重要規則

- **不要修改任何檔案**。只產出 `reviews/round-7-implement-review-report.md`。
- **不要 review V2 / Obts**。兩者在本輪**完全不讀**。
- **不要比對英文 skill vs 繁中 skill**。繁中延至 Closeout C1。
- **不要評論 proposal 本身的設計品質**（已 approved；若發現 proposal 遺漏或矛盾，標 `out-of-scope` → cross-round-notes）。
- **不要執行功能測試**（AI 無法實際跑 skill）。只做**文件對照**審查。
- **嚴重度從嚴**：Critical 保留給「違背 proposal 意圖 / 導致不可運作 / regression」；不確定就標 Question。
- **跨 wave 一致性是本輪重點**：Dimension 2 的 8 項 CW 檢查每項都要在對照表給 ✓ / ✗ / ⚠ 判斷 + 理由（✗ / ⚠ 對應 Finding）。
- **P001-P008 regression 是安全網**：即使沒 Finding，regression check 表也要填（表達「檢查過且 clean」也是有價值的結論）。
- **CHANGELOG 自述不是絕對真相**：實施者自報的影響範圍可能有遺漏 / 誇大；以實際檔案內容為準。若 CHANGELOG 與實際 drift，也算 Finding（通常 Minor）。
- **proposal 內部行號引用可能過時**：若 proposal § 影響範圍表說「現況 `webforms:122`」但實際行號不同，**先懷疑現況檔已更新（R7 Implement 前後差異）**，不自動歸 Finding；若實施正確但行號差異，不標 Finding。
- **F-04 是唯一跨三 proposal finding**：檢查時需同時看 P009 finish-feature-flow.md（Integration Summary 中立化）+ P010 Git-principles-*.md（Integration Commit 訊息慣例段）+ P011 § 關聯表（附 F-04 一致性備註）。三處有任一缺失都算 Finding。

## 審查節奏建議

若 Codex session time 有限制，建議切兩段：

**Segment A（~60 分鐘）**：
- Dimension 1（逐 wave 驗收）
- Dimension 4（P001-P008 regression check）
- 輸出：初步 Finding list

**Segment B（~30 分鐘）**：
- Dimension 2（cross-wave 8 項 CW 檢查）
- Dimension 3（R7 Approve decisions 落實對照）
- 最終 report 產出

若 session time 充足，單次跑完即可（順序：Dimension 1 → 2 → 3 → 4 → 寫 report）。

## 若發現系統性錯誤

如發現**整個 wave 未實施**或**核心決議完全缺失**（例如 Wave 2 的 `templates/_index.md` 雙版都不存在、或 Wave 3 的 scaffolding 目錄根本沒建立），這屬於**系統性錯誤**：

1. 報告中標 Critical
2. 建議「整個 wave 重新實施」而非逐項 fix
3. 在審查備註段說明此判斷

但若只是部分遺漏（例如 `_index.md` 存在但少一兩個段），屬 Major；逐項 fix 即可。

開始審查。完成後只產出 `reviews/round-7-implement-review-report.md`。

---END PROMPT---
