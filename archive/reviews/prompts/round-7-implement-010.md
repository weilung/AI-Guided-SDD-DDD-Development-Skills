# Round 7 Implement Prompt — PROPOSAL-010（給 Cursor Opus 4.7 High 使用）

> **使用方式**：開新 Cursor session（建議模型 Opus 4.7 High），把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。也可用 Claude Code 執行，但依 Phase 3 AI 分派，本 proposal 屬中規模且以新增為主，指派 Cursor 以節省 Claude Code token。
> **產出位置**：
>   - 新增 scaffolding / flow 檔（`sdd-ddd-*-skill/`）
>   - 修改 `SKILL.md`（雙版）、repo 根 `README.md`
>   - 更新 `CHANGELOG.md`
>   - **不改繁中版檔**（延到 Closeout C1，除 scaffolding 若使用者選中文語言可直接中文寫）
>   - **不改 proposal 本身**（已 approved）
> **禁止事項**：不補繁中版、不動其他 proposal 範圍（011 / 009）、不自動 commit、**不覆寫既有既有檔（README.md 應該小心 merge 而非重寫）**。
> **前置依賴**：
>   - **PROPOSAL-011 必須已完成**（`git-integration.md` 已重命名並剝離 Git Flow；`Git-principles-trunk.md` scaffolding 範本的中立前提）
>   - **PROPOSAL-009 必須已完成**（`_index.md` 範本已存在；`/dflow:init-project` 產出的 `specs/features/active/` 結構必須符合 009 的 feature 目錄化規範）
> **預估工作量**：中（~2-3 小時）。主要為新增 scaffolding 範本 + init-project-flow + SKILL.md 增補 + README 更新。
> **下一步**：本 session 完成後，R7 Implement 三 wave 全部結束，進入 **R7 實作品質審查**（Codex，仿 R1-R6 pattern）。

---BEGIN PROMPT---

你是 PROPOSAL-010（`dflow init` 機制 + Scaffolding 範本集）的實施者。本 proposal 已於 R7 Review + Approve 階段完成設計評估與修訂，狀態為 `approved`。你的工作是把 approved proposal 的內容落實到 skill 檔。

## Step 0：前置檢查

1. 確認 `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md` 狀態為 `approved`
2. 確認 **PROPOSAL-011 已完成實施**：
   - `sdd-ddd-*-skill/references/git-integration.md` 存在（舊名 `git-flow-integration.md` 應已不存在）
   - `git-integration.md` 已剝離 Git Flow 專屬段
3. 確認 **PROPOSAL-009 已完成實施**：
   - `sdd-ddd-*-skill/templates/_index.md` 存在（feature 級 dashboard 範本）
   - `sdd-ddd-*-skill/templates/phase-spec.md` 存在（`templates/feature-spec.md` 已不存在）
   - `sdd-ddd-*-skill/references/new-phase-flow.md` + `finish-feature-flow.md` 存在
   - SKILL.md 雙版已含三層 Ceremony（T1/T2/T3）+ `/dflow:new-phase` / `/dflow:finish-feature` 命令
4. 確認 `reviews/round-7-decisions.md` 存在

若前置條件不滿足（特別是 011 / 009 未完成），停止並回報使用者。**不要**自己去實施 011 / 009。

## Step 1：讀入必要檔案

### A. 權威設計來源（必讀，逐段對照）

1. `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md`（**THE 實施藍圖**；§ 1-6 提議的解法、§ 影響範圍表）

### B. R7 context

2. `reviews/round-7-decisions.md`（F-04 Path B / F-05 / F-07 的決議詳情）
3. `reviews/round-7-report.md`（Codex 原始 finding）

### C. 流程規範

4. `proposals/review-workflow.md`（§三 Implementation 紀錄格式）

### D. V2 Reference（範本藍本來源，非 review 標的）

5. `E:\工作區\SDD-Starter-Kit\docs\specs\_overview.md`（V2 原型）
6. `E:\工作區\SDD-Starter-Kit\docs\specs\_conventions.md`（V2 原型）
7. `E:\工作區\SDD-Starter-Kit\docs\specs\_共用\Git-原則.md`（V2 Git 規範原型，同時供 gitflow / trunk 二版參考）
8. `E:\工作區\SDD-Starter-Kit\CLAUDE.md-範例片段.md`（V2 CLAUDE.md snippet 原型）
9. `E:\工作區\SDD-Starter-Kit\README.md`（V2 散佈版 README 對照）

**V2 是藍本**：將 V2 的實際檔案內容 placeholder 化（日期、系統名稱、技術棧等變數），適配 Dflow 雙版（WebForms / Core）的風格差異。**不要**逐字 copy V2；**不要** review V2 品質；V2 只是起點。

### E. 現況檔（會實際修改或新增）

**需要修改的既有檔**：
- `sdd-ddd-webforms-skill/SKILL.md`
- `sdd-ddd-core-skill/SKILL.md`
- `README.md`（repo 根目錄）

**需要新增的檔**（雙版 × 2 + flow × 2 + repo 根調整）：
- `sdd-ddd-webforms-skill/references/init-project-flow.md`
- `sdd-ddd-core-skill/references/init-project-flow.md`
- `sdd-ddd-webforms-skill/scaffolding/_overview.md`
- `sdd-ddd-webforms-skill/scaffolding/_conventions.md`
- `sdd-ddd-webforms-skill/scaffolding/Git-principles-gitflow.md`
- `sdd-ddd-webforms-skill/scaffolding/Git-principles-trunk.md`
- `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md`
- `sdd-ddd-core-skill/scaffolding/` 對應 5 檔

### F. 依賴檔（讀取理解，不修改）

- `sdd-ddd-*-skill/templates/_index.md`（P009 新增，init flow 會引用）
- `sdd-ddd-*-skill/references/git-integration.md`（P011 剝離後，`Git-principles-trunk.md` 的中立前提）
- `sdd-ddd-*-skill/references/new-feature-flow.md`（init flow Step 5 的下一步）
- `sdd-ddd-*-skill/templates/CLAUDE.md`（`CLAUDE-md-snippet.md` 需對齊 P007c 的 H2 分段）

## Step 2：本輪範圍邊界（嚴格遵守）

**在範圍內**（PROPOSAL-010 § 影響範圍表所列）：
- 新增 `references/init-project-flow.md`（雙版）
- 新增 `scaffolding/` 目錄 + 5 個範本檔（雙版）
- 修改 `SKILL.md`（雙版）加入 `/dflow:init-project`
- 修改 `README.md`（repo 根）加入「How to adopt Dflow」段

**不在範圍內**：
- **繁中版**（`*-tw/`）—— 延到 Closeout C1
  - **例外**：若 scaffolding 範本本身（`_overview.md` / `_conventions.md` / `Git-principles-*.md` / `CLAUDE-md-snippet.md`）語言選擇**中文優先**（範本屬人讀內容，非 AI-facing skill 檔），可直接用繁中撰寫—— proposal § 影響範圍表「繁中 scaffolding 範本」bullet 註記「選擇性新增」。本 session 建議**先寫英文版 scaffolding**（統一性）；若使用者後續指定要中文 scaffolding，可另行補 `-tw` 版本
- **PROPOSAL-009 scope** 的 flow / 範本調整（`_index.md` / `phase-spec.md` / `new-phase-flow.md` 等）—— 已由 Wave 2 完成
- **PROPOSAL-011 scope** 的 Git Flow 解耦 —— 已由 Wave 1 完成
- R1-R6 既有機制的核心調整
- 新增 V2 級別的完整 onboarding 文件（`SDD-AI協作開發模式介紹.md` / `使用說明.md` 等）—— 屬 Closeout C2 Tutorial 重建範圍

若實施過程發現 proposal 沒列但邏輯上牽連的檔，**停下來回報**，不要擴大 scope。

## Step 3：實施動作

### 3.1 新增 `scaffolding/` 目錄與 5 份範本（雙版，共 10 檔）

**目錄結構**：
```
sdd-ddd-webforms-skill/scaffolding/
├── _overview.md
├── _conventions.md
├── Git-principles-gitflow.md
├── Git-principles-trunk.md
└── CLAUDE-md-snippet.md

sdd-ddd-core-skill/scaffolding/  （對應 5 檔）
```

**共通定位**（proposal § 2 Scaffolding 檔內容定位）：
- `{日期}` / `{系統名稱}` / `{技術棧}` / `{team-size}` 等 placeholder
- 帶最小起始內容，專案照抄後可逐步填實
- **不重疊 skill 本體**：不重述 SKILL.md 的 AI 決策邏輯、不重複 `references/` 的 flow 文件；專注「專案治理」
- **頂部加一行版本註記**（proposal 風險 1 緩解）：`<!-- Scaffolding template maintained alongside Dflow skill. See proposals/PROPOSAL-010 for origin. -->`

**雙版差異**：
- WebForms 版強調漸進式 Domain 抽離（`_overview.md` 提「遷移策略」段；`_conventions.md` 提 WebForms 特有術語）
- Core 版強調 Clean Architecture + DDD 完整（`_overview.md` 提「Clean Architecture 四層」；`_conventions.md` 提 Aggregate / VO / Domain Event）
- Git-principles 兩版（gitflow / trunk）在 WebForms / Core 兩 skill 下的內容**幾乎相同**（Git 規範與技術棧無關），可維持一致；僅範例中的分支名或 commit message 格式微調

#### 3.1.1 `_overview.md`（雙版）

內容參考 V2 `docs/specs/_overview.md`，placeholder 化：
- 系統現況段（`{系統名稱}` / `{業務領域}` / `{使用者規模}`）
- 技術架構段（WebForms 版：`{ASP.NET WebForms + EF}` 等；Core 版：`{ASP.NET Core 8 / EF Core / MediatR}`）
- 現有問題段（可選填寫；Brownfield 特別有用）
- 遷移策略段（WebForms 版必有，Core 版選填）
- 原則段（可選：Migration Awareness / Domain Extraction / Dual-Track Parallel / Pragmatic First——對應 V2 四原則）

#### 3.1.2 `_conventions.md`（雙版）

內容參考 V2 `docs/specs/_conventions.md`：
- 規格撰寫慣例段（**引用 skill 本體的 `templates/phase-spec.md` / `lightweight-spec.md`**，不重抄範本；只講專案特有 placeholder 填法）
- Ceremony Scaling 對照表（**引用 SKILL.md 的三層 Ceremony T1/T2/T3**；不重寫決議 13 的判準）
- 檔案命名規則段（專案特有約定；`SPEC-ID` 格式、`_index.md` / phase-spec / lightweight 命名沿用 P009 決議 7-8）

#### 3.1.3 `Git-principles-gitflow.md`（雙版）

Git Flow 版專案 Git 規範範本，內容參考 V2 `Git-原則.md`：
- 分支結構段（develop / release / feature / hotfix）
- Commit 格式段
- Gate Checks 段（Obts 風格：release 前檢查、hotfix 24h 補 spec）
- **Integration Commit 訊息慣例段**（F-04 Path B 決議新增）：
  - 與 P009 `/dflow:finish-feature` 的 Integration Summary 搭配使用
  - 建議 merge commit 格式範例：
    ```
    Merge feature/SPEC-{id}-{slug} into main

    {Integration Summary body, 從 /dflow:finish-feature 產出的文字}
    
    Related BRs: BR-XXX, BR-YYY
    ```
  - 說明 `--no-ff` / `-m` 等 Git Flow 慣用參數
- AI 協作規則段（可選；候選 F 併入，三級分類表）
- Tag & Release 段（Git Flow 專屬）
- **24h 補 spec 時限**（候選 E 納入此範本，非 Dflow 本體）—— 說明是「對人的承諾」，AI 無法追蹤

#### 3.1.4 `Git-principles-trunk.md`（雙版）

Trunk-based / GitHub Flow 版：
- 單一 main + feature branch 結構
- Merge strategy 選項段：squash / rebase / fast-forward（讓專案自選）
- Commit 格式段（conventional commits 風格建議，非強制）
- **Integration Commit 訊息慣例段**（F-04 Path B 決議新增）：
  - 與 P009 `/dflow:finish-feature` 的 Integration Summary 搭配使用
  - Squash commit 格式範例：
    ```
    feat({scope}): {title from Integration Summary}

    {body from Integration Summary}
    
    Related BRs: BR-XXX
    Co-Authored-By: Claude <noreply@anthropic.com>
    ```
  - Rebase commit 格式範例（每個 phase-spec 一個 commit，逐一 squash 或保留）
  - Fast-forward 情境（feature 只有 1 commit 時）
- AI 協作規則段（可選，較寬鬆，非 Git Flow 風格）
- **不含** hotfix / release branch 規範（trunk-based 不用）

#### 3.1.5 `CLAUDE-md-snippet.md`（雙版）

建議加入專案 `CLAUDE.md` 的片段，參考 V2 `CLAUDE.md-範例片段.md`：
- **沿用 P007c 的 H2 分段**（系統脈絡 / 開發流程）——**critical**：與 `templates/CLAUDE.md` 結構一致
- 系統脈絡段：提示使用者填入 `{業務領域}` / `{團隊角色}` / `{Skill 採用情境}`
- 開發流程段：說明 Dflow 核心原則（spec before code、domain-first）+ 引用 skill SKILL.md 的完整決策邏輯
- 末段：建議「此片段可合併到既有 CLAUDE.md；若專案無 CLAUDE.md，則 init flow 會建立新 CLAUDE.md 並 include 此片段」

### 3.2 新增 `references/init-project-flow.md`（雙版）

結構類比 `new-feature-flow.md`，5 個 Step（proposal § 4）：

**Step 1：現況盤點**
- 讀目錄：是否已有 `specs/` 目錄、`CLAUDE.md`、`.git`
- 若已有 `specs/`，列出其現況結構（AI 使用 Glob / LS 列出清單）
- 決策分支：全新 Greenfield / 既有 Brownfield（若既有，init 會**僅補缺失，不覆寫**）

**Step 2：收集專案資訊（5 題問答）**
- Q1. 專案類型：Greenfield / Brownfield
- Q2. 技術棧：ASP.NET WebForms / ASP.NET Core / 其他（影響引用哪版 skill 或雙版）
- Q3. 是否有遷移計畫（WebForms → Core）
- Q4. 文件語言：中文 / 英文（影響 scaffolding 範本文案，若選中文且 Dflow 已有繁中 scaffolding，使用繁中版；若無，英文版 + 後續可自行翻譯）
- Q5. 可選起始檔（多選，附決策輔助）：
  - [ ] `_overview.md`（系統概觀範本）
  - [ ] `_conventions.md`（規格撰寫慣例）
  - [ ] Git-principles（決策輔助：「如果你不確定，選 trunk-based——這是 GitHub / GitLab 預設風格。Git Flow 適合有正式 release cycle 的大型團隊。」）：
    - [ ] `Git-principles-gitflow.md`
    - [ ] `Git-principles-trunk.md`
  - [ ] `CLAUDE.md` 片段（若專案沒 CLAUDE.md 則**必產**；若已有，僅提供建議片段供使用者合併）

**Step 3：計算要產出的檔案清單**
- **必產清單**（F-05 決議，**依 skill 版本分拆**）：
  - **WebForms 版必產**：
    ```
    specs/features/{active,completed,backlog}/
    specs/domain/glossary.md（空範本）
    specs/migration/tech-debt.md（空範本——WebForms 版使用 migration/ 目錄，非 architecture/）
    ```
  - **Core 版必產**：
    ```
    specs/features/{active,completed,backlog}/
    specs/domain/glossary.md（空範本）
    specs/domain/context-map.md（空範本——Core 版必產，WebForms 版不產）
    specs/architecture/tech-debt.md（空範本）
    specs/architecture/decisions/（空目錄，可含 README.md 佔位）
    ```
  - **`behavior.md` 不在 init 階段產生**（F-05 決議）：明確說明由 P003 completion flow（Step 8.3 / 5.3）或 P007a baseline capture 於首個 bounded context 建立時產生
- 選擇性清單：依 Step 2 Q5 勾選
- AI 在產出前**列出將要 create / skip 的檔案清單給使用者確認**（proposal 風險 2 緩解）

**Step 4：逐檔 Write（placeholder 填入）**
- 必產檔逐一 Write（使用 Write 工具）
- 選擇性檔從 `sdd-ddd-*-skill/scaffolding/` 讀取範本，填入 Step 2 的資訊後 Write 到專案目錄
- **既有檔保護**：若檔已存在，**skip**（除非使用者明確要求 overwrite）
- 特別處理：
  - `CLAUDE.md` 若已存在，僅提供 `CLAUDE-md-snippet.md` 片段檔 + 提示使用者合併
  - 若 `CLAUDE.md` 不存在且 Q5 勾選 CLAUDE.md 片段，init 會建立最小版 CLAUDE.md（include scaffolding/CLAUDE-md-snippet.md 內容）

**Step 5：報告產出結果 + 建議下一步**
- 列出實際產出的檔案清單（含 skip 的檔說明）
- 建議下一步：
  ```
  「結構已建立。下一步建議：
   - 新專案：執行 /dflow:new-feature 開始第一個 feature
   - 既有專案：執行 /dflow:modify-existing 從現有變更需求切入
   （Brownfield 可先讀 _overview.md 了解系統現況）」
  ```

**雙版差異**：
- WebForms 版的 init flow 強調 `migration/tech-debt.md` 的重要性（漸進抽離脈絡）
- Core 版強調 `domain/context-map.md` 與 `architecture/decisions/` 是長期資產

### 3.3 更新 SKILL.md（雙版）

- **Frontmatter Primary triggers**：加入 `/dflow:init-project`
- **決策樹**：新增節點
  ```
  /dflow:init-project → INIT PROJECT FLOW (references/init-project-flow.md)
  ```
- **Slash Commands 表**：新增「專案級」子分類，**置於「啟動類」之上**（proposal § 5）：
  ```
  專案級命令：
    /dflow:init-project    專案初始化（建立 specs/ 結構 + 選擇性 scaffolding）
  ```
- **Reference Files 表**：新增 `references/init-project-flow.md` 列
- **新增「Templates / Scaffolding」段**（若現有結構只有「Templates」）：
  - Scaffolding 目錄說明段：說明 `scaffolding/` 的定位（「給 `/dflow:init-project` 用的可選範本集，非 AI 直接讀取」）
  - Scaffolding 檔案清單：列出 5 個範本檔名與一句話說明

### 3.4 更新 repo 根 `README.md`（F-07 決議）

**讀取現況**，找到合適插入點（不要重寫整份 README），新增「How to adopt Dflow in your project」段：

```markdown
## How to adopt Dflow in your project

1. Install the appropriate skill using the current Claude Code skill mechanism (see Claude Code official docs)
2. In your project root, run: `/dflow:init-project`
3. Follow the prompts to set up `specs/` structure and optional scaffolding
4. Start your first feature: `/dflow:new-feature`
```

**F-07 關鍵**：步驟 1 用中立措辭，**不**寫死 `.claude/skills/` 路徑（與 proposal § 預期後果風險 6 的緩解說明一致）。

同時，新增「V2 / Dflow 分工說明」（可放在此段之後或 README 末段）：
- V2（`SDD-Starter-Kit`）是完整散佈 starter kit（含 onboarding 文件組：`SDD-AI協作開發模式介紹.md` / `使用說明.md` 等）
- Dflow 是 AI-guided skill 本體（`sdd-ddd-*-skill/`）
- 兩者可並用；Dflow 的 scaffolding 是內建的最小範本集，V2 是完整 onboarding 包
- 簡短連結 V2 GitHub / 位置（若已公開）

**不要**新增 V2 級別的完整 onboarding 文件（`SDD-AI協作開發模式介紹.md` / `使用說明.md` 等）—— 屬 Closeout C2 範圍。

## Step 4：實施後自我驗收

### 4.1 Scaffolding 目錄結構驗收

- `sdd-ddd-webforms-skill/scaffolding/` 目錄存在，含 5 檔
- `sdd-ddd-core-skill/scaffolding/` 目錄存在，含 5 檔
- 每份 scaffolding 檔頂部有版本註記（`<!-- Scaffolding template ... -->`）

### 4.2 Integration Commit 訊息慣例段 verify（F-04 Path B）

Grep `Integration Commit` / `Integration Summary` 在以下檔應有命中：
- `sdd-ddd-webforms-skill/scaffolding/Git-principles-gitflow.md`
- `sdd-ddd-webforms-skill/scaffolding/Git-principles-trunk.md`
- Core 版對應 2 檔

gitflow 版應含 merge commit 格式範例；trunk 版應含 squash / rebase commit 格式範例。

### 4.3 init-project-flow verify

- `references/init-project-flow.md` 雙版存在，含 5 Step 結構
- Step 3「必產清單」雙版各自正確（WebForms 版用 `migration/tech-debt.md`、Core 版用 `architecture/tech-debt.md` + `domain/context-map.md`）
- Step 3 明確註記 `behavior.md` 不在 init 階段產生（F-05 決議）

### 4.4 SKILL.md 更新 verify

Grep `/dflow:init-project` 在 SKILL.md 雙版應有命中（Primary triggers + 決策樹 + Slash Commands 表 + Reference Files 表共 4+ 處）。
Grep `scaffolding` 應有命中（Templates / Scaffolding 段）。

### 4.5 README verify（F-07）

- Grep `.claude/skills/` 在 README.md 應**無命中**（不寫死路徑）
- Grep `/dflow:init-project` 應有命中
- Grep `How to adopt Dflow` 應有命中

### 4.6 前置依賴未被破壞 verify

- `sdd-ddd-*-skill/templates/_index.md` 存在且**未被本 session 修改**（P009 的產物）
- `sdd-ddd-*-skill/references/git-integration.md` 存在且**未被本 session 修改**（P011 的產物）
- `sdd-ddd-*-skill/references/new-phase-flow.md` / `finish-feature-flow.md` 未被本 session 修改（P009 的產物）

### 4.7 範圍外檔案 untouched verify

- 繁中版檔案（`*-tw/`）**未被修改**
- 既有 `templates/` / `references/` 的 P001-P008 產物未被本 session 修改
- Proposal 本身（`proposals/PROPOSAL-010-*.md`）未被修改

若任一驗收失敗，修正後重跑。

## Step 5：CHANGELOG 更新

在 `CHANGELOG.md` 最上方新增一段：

```markdown
## {YYYY-MM-DD} — R7 Wave 3 實施：PROPOSAL-010 `dflow init` 機制 + Scaffolding 範本集

**前置**：R7 Wave 1（PROPOSAL-011）+ Wave 2（PROPOSAL-009）已完成（見前兩段 CHANGELOG）；R7 Review + Approve 處理 3 個 finding（accept-with-choice: F-04 Path B；accept: F-05、F-07）
**Proposal**：`proposals/PROPOSAL-010-dflow-init-and-scaffolding.md`（approved）
**影響範圍**：

**新增 Scaffolding 目錄**（雙版共 10 檔）
- `sdd-ddd-{webforms|core}-skill/scaffolding/_overview.md`
- `sdd-ddd-{webforms|core}-skill/scaffolding/_conventions.md`
- `sdd-ddd-{webforms|core}-skill/scaffolding/Git-principles-gitflow.md`（**含 Integration Commit 訊息慣例段**，F-04 Path B）
- `sdd-ddd-{webforms|core}-skill/scaffolding/Git-principles-trunk.md`（同上，trunk 風格）
- `sdd-ddd-{webforms|core}-skill/scaffolding/CLAUDE-md-snippet.md`

**新增 Flow 文件**（雙版）
- `sdd-ddd-{webforms|core}-skill/references/init-project-flow.md` —— `/dflow:init-project` 流程
  - Step 3「必產清單」依 skill 版本分拆：WebForms 版用 `migration/tech-debt.md`、Core 版用 `architecture/tech-debt.md` + `domain/context-map.md`（F-05 決議）
  - `behavior.md` 不在 init 階段產生（F-05 決議；由 P003 / P007a 既有機制產生）

**修改**
- `sdd-ddd-{webforms|core}-skill/SKILL.md` —— 加入 `/dflow:init-project`（Primary triggers / 決策樹 / Slash Commands 表 / Reference Files 表 / Templates/Scaffolding 段）
- `README.md`（repo 根）—— 加入「How to adopt Dflow in your project」段；步驟 1 用中立措辭不寫死 `.claude/skills/`（F-07 決議）；補 V2 / Dflow 分工說明

**繁中版同步**：延至 Post-Review Closeout C1（依 review-workflow.md §七）；scaffolding 範本若使用者選中文語言，可選擇性新增繁中版（屬人讀內容，本 wave 先以英文版為主，繁中版選擇性）

**下一步**：R7 Implement 三 wave 全部完成；進入 **R7 實作品質審查**（Codex review，仿 R1-R6 pattern）
```

## Step 6：完成後回報

```
R7 Wave 3 實施完成（PROPOSAL-010 `dflow init` 機制 + Scaffolding 範本集）。

**已新增檔案**（12 檔）：
- sdd-ddd-webforms-skill/scaffolding/（5 檔）
- sdd-ddd-core-skill/scaffolding/（5 檔）
- sdd-ddd-webforms-skill/references/init-project-flow.md
- sdd-ddd-core-skill/references/init-project-flow.md

**已修改檔案**：
- sdd-ddd-webforms-skill/SKILL.md（/dflow:init-project 加入）
- sdd-ddd-core-skill/SKILL.md（同上）
- README.md（How to adopt Dflow 段）

**CHANGELOG.md**：R7 Wave 3 實施段已新增

**驗收結果**（見 Step 4）：
- 4.1 Scaffolding 目錄結構：✓ / ✗
- 4.2 Integration Commit 訊息慣例段 verify：✓ / ✗
- 4.3 init-project-flow verify：✓ / ✗
- 4.4 SKILL.md 更新 verify：✓ / ✗
- 4.5 README verify：✓ / ✗
- 4.6 前置依賴未被破壞：✓ / ✗
- 4.7 範圍外檔案 untouched：✓ / ✗

**R7 Implement 三 wave 全部完成**。建議 commit message：
  `Implement PROPOSAL-010: dflow init + Scaffolding (R7 Wave 3)`
  含 R7 Review F-04 Path B / F-05 / F-07 的 finding 修正。

**下一步**：進入 **R7 實作品質審查**（Codex review），仿 R1-R6 pattern。建議提示詞：仿 `reviews/prompts/round-N-review.md` 格式，標的為三 wave 的實施產物（vs 對應 proposal 意圖）；可由使用者另起 session 撰寫 R7-implement-review 提示詞。

**備註 / 意外發現**（如有，記入 cross-round-notes.md；若無，寫「無」）：
```

## 禁止事項

- **不要自己實施 PROPOSAL-011 / 009**—— 前置檢查已要求兩者完成；若發現未完成，**停止回報**
- **不要動繁中版檔**（`*-tw/`）—— 延到 Closeout C1
  - **例外允許**：scaffolding 範本本身若明確採中文撰寫（proposal § 影響範圍表允許），但本 session 建議先全用英文；中文版留待 C1 或使用者明確指示
- **不要重寫既有 README.md**—— 讀取現況找合適插入點，**新增**段落；不要破壞既有內容
- **不要動 PROPOSAL-009 / 011 的產物**：
  - `templates/_index.md` / `templates/phase-spec.md`（P009）
  - `references/new-phase-flow.md` / `finish-feature-flow.md`（P009）
  - `references/git-integration.md`（P011）
  - SKILL.md 的三層 Ceremony / `/dflow:new-phase` / `/dflow:finish-feature` 段（P009 已加）
- **不要新增 V2 級別的完整 onboarding 文件**（`SDD-AI協作開發模式介紹.md` / `使用說明.md` 等）—— 屬 Closeout C2 範圍
- **不要在 scaffolding 範本內重抄 skill 本體的內容**（不重述 SKILL.md 決策邏輯、不重複 references/ 的 flow）—— 只做「引用 + 專案級補充」
- **不要自動 commit**
- **Scaffolding 的 `Git-principles-*.md` 不要強制 commit 署名格式**（候選 G 決議：可附**建議**如 `Co-Authored-By: Claude <noreply@anthropic.com>`，但不強制）

## 若遇到歧義

- 第一優先：回到 `proposals/PROPOSAL-010-*.md` § 1-6 對應段 + § 影響範圍表
- 第二優先：回到 `reviews/round-7-decisions.md` 對應 finding 段（F-04 Path B / F-05 / F-07）
- 第三優先：V2 檔案作為範本藍本（**不**為照抄、不為 review V2）
- 第四優先：若仍無法決定，**停下來回報使用者**，不要自行裁定

## 特別提醒

- **F-05 必產清單分拆是關鍵**：WebForms 版產 `migration/tech-debt.md`、Core 版產 `architecture/tech-debt.md` + `domain/context-map.md` + `architecture/decisions/`。**`behavior.md` 不產**（由 P003 / P007a 產）。這 3 條 verify 務必通過。
- **F-07 README 中立措辭是關鍵**：步驟 1**不**寫死 `.claude/skills/` 路徑。Grep `.claude/skills/` 在 README.md 應無命中。
- **F-04 Path B Integration Commit 訊息慣例段**：gitflow 版和 trunk 版的 scaffolding 都要有此段，且與 P009 `/dflow:finish-feature` 產出的 Integration Summary 搭配。不要重寫 Integration Summary 本身（P009 範圍），只提供把 Summary 格式化為 commit 訊息的範例。
- **Scaffolding 範本引用（不重抄）**：`_conventions.md` 引用 SKILL.md 的三層 Ceremony，不重抄；`CLAUDE-md-snippet.md` 沿用 P007c 的 H2 分段（系統脈絡 / 開發流程），不重寫。
- **init flow 「既有檔不覆寫」原則**：Step 4 的邏輯要寫清楚「skip if exists, unless explicitly requested to overwrite」；**AI 產出前列出清單給使用者確認**（proposal 風險 2 緩解）。

---END PROMPT---
