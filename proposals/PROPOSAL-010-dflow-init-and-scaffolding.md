## PROPOSAL-010: `dflow init` 機制 + Scaffolding 範本集

**狀態**: `approved`

**提出日期**: 2026-04-22

**優先級**: `高`

**來源**:
- `reviews/field-reference-candidates.md` 候選 K（`dflow init` 機制，2026-04-22 新增，源自候選 B 重新定位 + OpenSpec `openspec init` 啟發）
- 候選 B（專案級 scaffolding 檔案群）重新定位——4 份 V2 scaffolding 檔不直接納入 skill 本體，改為 `dflow init` 可選產生的範本
- 候選 C（Onboarding 文件組）社群散佈定位採納

**實施排程**: R7 Wave 3（依賴 011 與 009 完成，`dflow init` 產出的結構需符合新的 Git 耦合原則與 feature 目錄化規範）

---

### 問題描述

1. **Dflow 沒有 init / scaffold / install 機制**。新使用者（個人 / 團隊 / 社群採用者）要：
   - 手動在專案內建 `specs/features/active/` / `completed/` / `backlog/`、`specs/domain/`、`specs/architecture/` 等目錄
   - 手動撰寫 `CLAUDE.md`（或從 `templates/CLAUDE.md` 複製後刪改）
   - 自行決定是否需要、如何準備「專案級入口文件」（系統概觀、Git 規範、SDD 流程說明等）
   
   比較：OpenSpec 提供 `openspec init`，降低新專案採納門檻。Dflow 目前缺對應機制。

2. **候選 B 的 4 份 V2 scaffolding 檔定位尷尬**：
   - `_overview.md`（系統現況、技術棧、遷移策略、原則）
   - `_conventions.md`（規格撰寫慣例、Ceremony Scaling、檔案命名規則）
   - `Git-原則.md`（GitFlow 規範、commit 格式、Gate Checks、AI 協作規則）
   - `SDD-工作流程說明.md`（人讀版 SDD 流程概覽）
   
   這些**不是 Dflow skill 的職責**（不是 AI workflow），但對新專案導入有價值。若內建於 skill 會職責混雜；若完全不管，社群使用者入門成本高。

3. **社群散佈 onboarding 缺口**（候選 C）。repo 根目錄的 `README.md` 目前偏「這個 repo 是什麼」，沒有「如何把 Dflow 套到我的專案」的引導。V2（`E:\工作區\SDD-Starter-Kit`）已經是散佈版，但 Dflow 作為 skill 本體缺乏對應 onboarding 文件。

4. **Claude Code 環境天然適合 slash command 形式的 init**。使用者已在 Claude Code 內，若用 `/dflow:init-project` 形式，AI 可直接用 Write 工具建立檔案，不需額外 CLI / npm 執行環境，與 Dflow 其他命令（`new-feature`、`new-phase`、`finish-feature`）設計風格一致。

使用者在候選審查中的原話：

> 「這不是 DFlow 該決定的事情（指 Git 規範），或者維持獨立檔案，但是我們目標是社群散佈，我想這可以設計為 npm install 時候建立或者多一個 DFlow init 建立一些選擇性前置。」

候選 K 因此定案：採 Claude Code 命令形式而非 npm CLI。

---

### 提議的解法

#### 1. 新增命令 `/dflow:init-project`

**定位**：專案級 init，在新或既有專案根目錄執行，建立 Dflow 所需的最小目錄結構 + 選擇性 scaffolding 範本 + `CLAUDE.md` 片段。

**執行流程**：

```
1. 使用者在專案目錄執行 /dflow:init-project
2. AI 讀取當前目錄狀態：是否已有 specs/ 目錄？是否已有 CLAUDE.md？
3. AI 透過對話收集：
   Q1. 專案類型：Greenfield（全新）/ Brownfield（既有專案）？
   Q2. 技術棧：ASP.NET WebForms / ASP.NET Core / 其他（影響引用哪版 skill）
   Q3. 是否有遷移計畫（WebForms → Core 之類）？
   Q4. 文件語言：中文 / 英文（影響 scaffolding 範本文案）
   Q5. 可選起始檔（多選）：
       [ ] _overview.md（系統概觀範本）
       [ ] _conventions.md（規格撰寫慣例範本）
       [ ] Git-原則.md（專案 Git 規範範本，含 Git Flow 版本與 trunk-based 版本可選）
       [ ] CLAUDE.md 片段（若專案沒 CLAUDE.md 則必產；若已有，僅提供建議片段供使用者合併）
4. AI 使用 Write 工具建立（**必產清單依 skill 版本分拆**，R7 Review F-05 決議）：
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
     specs/architecture/decisions/（空目錄）
     ```
   - **`behavior.md` 不在 init 階段產生**（R7 Review F-05 決議）：`behavior.md` 是 bounded-context 級檔案（`specs/domain/{context}/behavior.md`），需先有 context 名稱才能正確安放；由 P003 / P007a 既有機制於首個 bounded context 建立時（Step 8.3 / Step 5.3 completion flow 或 brownfield baseline capture）自動產生
   - 選擇性：依 Q5 勾選產生
5. AI 提示下一步：
   「結構已建立。下一步建議：
    - 新專案：執行 /dflow:new-feature 開始第一個 feature
    - 既有專案：執行 /dflow:modify-existing 從現有變更需求切入
    （Brownfield 可先讀 _overview.md 了解系統現況）」
```

#### 2. 新增 `scaffolding/` 目錄（雙版）

```
sdd-ddd-webforms-skill/
├── SKILL.md
├── references/
├── templates/
└── scaffolding/                     ← 新增
    ├── _overview.md                  ← V2 重定位範本
    ├── _conventions.md               ← V2 重定位範本
    ├── Git-principles-gitflow.md     ← Git Flow 版本範本（給採 Git Flow 的專案）
    ├── Git-principles-trunk.md       ← Trunk-based / GitHub Flow 範本
    └── CLAUDE-md-snippet.md          ← 要加入專案 CLAUDE.md 的建議片段

sdd-ddd-core-skill/scaffolding/       ← 對應結構
```

**Scaffolding 檔內容定位**：
- `{日期}` / `{系統名稱}` / `{技術棧}` / `{team-size}` 等 placeholder
- 帶最小起始內容，專案照抄後可逐步填實
- **不重疊 skill 本體**：不重述 SKILL.md 的 AI 決策邏輯、不重複 references/ 的 flow 文件；專注於「專案治理」層級內容

**Git-principles 二版本**：
- `Git-principles-gitflow.md`：含 develop / release / hotfix 分支結構、Git Flow 慣例、24 小時補 spec（對人的承諾）等——Obts 風格
- `Git-principles-trunk.md`：含 main 單分支 + feature/*、squash vs rebase 選項、commit message 規範——主流開源風格
- **兩版本各自新增「Integration Commit 訊息慣例」段**（R7 Review F-04 決議 Path B）：PROPOSAL-009 的 `/dflow:finish-feature` 只產出 Git-strategy-neutral 的 Integration Summary；兩版 Git-principles 在此段提供把 Integration Summary 格式化為具體 commit 訊息的範例：
  - gitflow 版：建議 merge commit 格式（例：`Merge feature/SPEC-{id}-{slug} into main` + Summary body）
  - trunk 版：提供 squash commit / rebase commit 格式範例（含 Co-Authored-By 等選用項）
- 使用者在 `/dflow:init-project` Q5 可選其一（或都不選，自行撰寫）
- 對應候選 L（PROPOSAL-011）解耦原則：Dflow 本體中立，Git Flow 規範只存在於選擇性 scaffolding 範本

#### 3. 更新 README.md（repo 根目錄）

- 現況：偏「這個 repo 是什麼」
- 新增：「How to adopt Dflow in your project」段
  ```
  1. Install the appropriate skill using the current Claude Code skill mechanism (see Claude Code official docs)
  2. In your project root, run: /dflow:init-project
  3. Follow the prompts to set up specs/ structure and optional scaffolding
  4. Start your first feature: /dflow:new-feature
  ```
  （R7 Review F-07 決議：步驟 1 不寫死 `.claude/skills/` 路徑，與 § 預期後果 / 風險 6 的緩解說明內部一致）
- 對照 V2（`E:\工作區\SDD-Starter-Kit`）的分工說明：V2 是完整散佈 starter kit（含 onboarding 文件組），Dflow 是 AI-guided skill 本體；兩者可並用
- **注意**：本 proposal 僅更新 repo 根 README，不新增 `SDD-AI協作開發模式介紹.md` / `使用說明.md` 等 V2 級別的完整 onboarding 文件——那屬 Closeout C2（Tutorial 重建）範圍

#### 4. 新增 `references/init-project-flow.md`（雙版）

- 結構類比 `new-feature-flow.md`：Step-by-step 執行指引
- 主要內容：
  - Step 1：現況盤點（讀目錄、檢查 `CLAUDE.md` / `specs/` 是否已存在）
  - Step 2：收集專案資訊（5 題問答）
  - Step 3：計算要產出的檔案清單（必產 + 選擇性）
  - Step 4：逐檔 Write（placeholder 填入使用者提供的資訊）
  - Step 5：報告產出結果 + 建議下一步
- 特別處理：若專案已有 `specs/` 目錄，AI 應 diff 現況、不覆寫、僅補缺失部分

#### 5. SKILL.md 更新（雙版）

- **Primary triggers** 加入 `/dflow:init-project`
- **決策樹** 新節點：
  ```
  /dflow:init-project → INIT PROJECT FLOW (references/init-project-flow.md)
  ```
- **Slash Commands** 表：加入新命令分類（建議置於「啟動類」之上，作為獨立「專案級」類別）
- **Reference Files** 表：加入 `references/init-project-flow.md`
- **Templates / Scaffolding** 表：新增 `scaffolding/` 目錄的說明段

#### 6. 與候選 D / F / G 的處置同步

- 候選 D（單一人讀工作流程檔）：**不**放入 scaffolding，改由 Closeout C2（Tutorial 重建）承接
- 候選 F（AI 協作規則三級分類表）：**不**納入 skill 本體，納入 `Git-principles-*.md` scaffolding 範本（選擇性）
- 候選 G（AI Commit 署名格式）：scaffolding 的 `Git-principles-*.md` 可附**建議**格式（如 `Co-Authored-By: Claude <noreply@anthropic.com>`），**不強制**

---

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| **Skill 本體** | | |
| `sdd-ddd-webforms-skill/SKILL.md` | 修改 | Primary triggers、決策樹、Slash Commands 表、參考檔表加入 `/dflow:init-project` |
| `sdd-ddd-core-skill/SKILL.md` | 修改 | 同上 |
| **新增 Flow 文件** | | |
| `sdd-ddd-webforms-skill/references/init-project-flow.md` | **新增** | `/dflow:init-project` 完整流程 |
| `sdd-ddd-core-skill/references/init-project-flow.md` | **新增** | 同上（Core 版 scaffolding 略有差異：context-map.md 必產）|
| **Scaffolding 目錄（新增）** | | |
| `sdd-ddd-webforms-skill/scaffolding/_overview.md` | **新增** | 系統概觀範本（placeholder 化的 V2 `_overview.md`）|
| `sdd-ddd-webforms-skill/scaffolding/_conventions.md` | **新增** | 規格撰寫慣例範本（含 Ceremony Scaling 對照，引用 SKILL.md）|
| `sdd-ddd-webforms-skill/scaffolding/Git-principles-gitflow.md` | **新增** | Git Flow 版專案 Git 規範範本（含 develop/release/hotfix、24h 補 spec、**Integration Commit 訊息慣例段**：merge commit 格式—— R7 Review F-04）|
| `sdd-ddd-webforms-skill/scaffolding/Git-principles-trunk.md` | **新增** | Trunk-based / GitHub Flow 版 Git 規範範本（含 **Integration Commit 訊息慣例段**：squash / rebase commit 格式—— R7 Review F-04）|
| `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md` | **新增** | `CLAUDE.md` 建議片段（類比 V2 `CLAUDE.md-範例片段.md`）|
| `sdd-ddd-core-skill/scaffolding/` 對應 5 檔 | **新增** | 同上（Core 版的 scaffolding，內容根據 Clean Architecture 調整）|
| **Repo 根文件** | | |
| `README.md` | 修改 | 加入「How to adopt Dflow」段；補 V2 / Dflow 分工說明 |
| **繁中版同步** | | |
| `sdd-ddd-webforms-skill-tw/init-project-flow_tw.md` | **新增** | Flow 文件繁中版 |
| `sdd-ddd-core-skill-tw/init-project-flow_tw.md` | **新增** | 同上 |
| 繁中 scaffolding 範本 | **新增**（選擇性）| 若使用者選中文語言，scaffolding 範本本身可直接用繁中版——範本屬人讀內容，繁中優先 |
| `sdd-ddd-webforms-skill-tw/SKILL_tw.md` | 修改 | 同步英文版 SKILL.md 更新 |
| `sdd-ddd-core-skill-tw/SKILL_tw.md` | 修改 | 同上 |

**影響檔案總數估計**：英文版約 15 檔（2 skill × 6 scaffolding + 2 flow + 2 SKILL 更新 + 1 README ≈ 15）+ 繁中版約 8 檔 ≈ 23 檔。

---

### 預估工作量

中

- 主要為**新增**（不動既有流程文件核心）
- Scaffolding 範本可大量參考 V2 既有 4 份檔（placeholder 化 + 雙版適配），非純創作
- 新增 flow 文件（init-project-flow.md）結構簡單（5 步對話 + Write 產出）
- 雙版（WebForms + Core）scaffolding 內容差異在：Core 版強調 Clean Architecture + DDD 完整、WebForms 版強調漸進式 Domain 抽離
- 英文版 + 繁中版雙語同步

---

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-011 | 前置 | `Git-principles-trunk.md` scaffolding 範本的存在需先有 011 解耦作前提；若 011 未實施，Dflow 本體仍綁 Git Flow，scaffolding 提供 trunk 範本會自相矛盾 |
| PROPOSAL-009 | 前置 | `/dflow:init-project` 產出的 `specs/features/active/` 必須符合 009 的 feature 目錄化規範；若 009 未實施，init 產出的結構會與 skill 本體不一致 |
| PROPOSAL-007c | 互補 | `CLAUDE-md-snippet.md` 需沿用 007c 的標題分段（系統脈絡 / 開發流程）|
| PROPOSAL-007a | 互補 | Brownfield scaffolding（`_overview.md`）引導使用者記錄現況，與 007a 的 baseline capture 形成對應（init 階段設 scaffold，baseline capture 階段填實）；**`behavior.md` 由此機制（P007a baseline capture 或 P003 completion flow）在首個 bounded context 建立時產生，不在 init 階段必產**（R7 Review F-05 決議）|
| Closeout C2 | 互補 | C2 Tutorial 重建會納入「單一線性敘事版的 SDD 流程」；本 proposal 的 scaffolding 不重疊 C2 Tutorial 內容 |

---

### 關鍵假設

- **假設 1**：新使用者傾向先 init、再開始第一個 feature——這是「從無到有」的自然路徑；若實戰顯示多數人傾向手動建結構，此 proposal 價值需重估。
- **假設 2**：Scaffolding 範本的「部分選擇性產出」（多選介面）對使用者友善，不會造成決策疲勞——若 Q5 的 5 項選擇令使用者困惑，應簡化為「Minimal / Standard / Full」預設集。
- **假設 3**：Git Flow vs Trunk-based 的 Git-principles 二版本足以覆蓋多數社群使用情境；若出現第三種（如 GitLab Flow、release trains）需求，作為 Wave D 延伸處理。
- **假設 4**：既有 Dflow 使用者（Obts）不因本 proposal 受影響——因為他們的 specs/ 結構已經存在；`/dflow:init-project` 偵測到已有結構時僅補缺失、不覆寫。
- **假設 5**（來自候選 K）：社群散佈採 Claude Code 命令形式（而非 npm CLI）能降低門檻——使用者已在 Claude Code 環境內，不需切換 shell / 安裝 Node.js。

**驗證點（實施後）**：
- 在乾淨目錄執行 `/dflow:init-project`，確認產出結構可直接承接 `/dflow:new-feature`
- **WebForms / Core 兩版各自執行一次 `/dflow:init-project`**，確認（R7 Review F-05 決議）：
  - WebForms 版產出 `specs/migration/tech-debt.md`（非 `architecture/`），不產 `specs/domain/context-map.md`
  - Core 版產出 `specs/architecture/tech-debt.md` + `specs/architecture/decisions/` + `specs/domain/context-map.md`
  - 兩版**都不**產 `specs/domain/{context}/behavior.md`（等首個 bounded context 於 completion flow 建立時才生成）
- 在已有 `specs/` 目錄的專案執行，確認 AI 不覆寫、只補缺失
- 用 `_overview.md` / `Git-principles-*.md` scaffolding 範本填實一次，確認 placeholder 完整、無殘留範本用語
- README.md 的「How to adopt」能引導一個全新使用者在 30 分鐘內跑起第一個 feature

---

### 預期後果（風險）

- **風險 1**：Scaffolding 範本內容過時（專案採 Dflow 後，範本用語 / 慣例若與 skill 本體演化脫節，會有歧義）。
  - 緩解：在 `scaffolding/` 內的每份檔頂部註明「This file is a scaffolding template, maintained alongside SKILL.md version X.Y」；skill 版本變動時納入 review 範圍
- **風險 2**：`/dflow:init-project` 對既有專案誤覆寫。
  - 緩解：init flow 的 Step 1 強制「讀取現況」；任何已存在檔案預設跳過，除非使用者明確要求 overwrite；AI 在產出前列出將要 create / skip 的檔案清單給使用者確認
- **風險 3**：多選 Q5 造成決策疲勞（特別是新使用者對 `_overview.md` / `_conventions.md` / Git-principles 不熟）。
  - 緩解：提供預設建議（「第一次使用建議勾選全部，之後可刪」）；Q5 附每個選項的一行說明
- **風險 4**：scaffolding 範本與 V2 散佈版（`E:\工作區\SDD-Starter-Kit`）內容重疊，未來雙處維護成本高。
  - 緩解：實施時建立「V2 / Dflow 分工清單」——V2 為完整 starter kit（含行銷 / onboarding 文件），Dflow scaffolding 為 skill 內建的最小範本集；重疊檔案以 Dflow scaffolding 為主，V2 引用 Dflow
- **風險 5**：Git-principles 二版本選擇讓使用者困惑「我該選哪個」。
  - 緩解：Q5 問 Git-principles 時附決策輔助：「如果你不確定，選 trunk-based——這是 GitHub / GitLab 預設風格。Git Flow 適合有正式 release cycle 的大型團隊。」
- **風險 6**：README.md 的「How to adopt」指向 `.claude/skills/` 的安裝方式可能隨 Claude Code skill 分發機制演化而失效。
  - 緩解：README 的安裝段保持「高層步驟 + link to Claude Code official docs」形式，不寫死路徑細節

---

### 評估紀錄（整合評估時填寫）

**評估日期**: 2026-04-22

**結論**: approved

**理由**: 經 R7 review（2026-04-22，Codex 產出 `reviews/round-7-report.md`）+ R7 approve（2026-04-22，本 session）處理 3 個 finding（accept-with-choice: F-04 Path B；accept: F-05、F-07；無 reject / defer / clarify）。所有需修訂部分已在 R7 approve session 內完成：
- § 1 `/dflow:init-project` 執行流程 step 4：必產清單拆為 WebForms / Core 雙版，`behavior.md` 移除（F-05）
- § 2 Git-principles 二版本段：新增「Integration Commit 訊息慣例」段說明（F-04）
- § 3 更新 README.md：步驟 1 改為中立措辭，不寫死 `.claude/skills/` 路徑（F-07）
- § 影響範圍 scaffolding 段：Git-principles-gitflow / trunk 描述加「Integration Commit 訊息慣例」段（F-04）
- § 關聯的 Proposal PROPOSAL-007a 那列：補「behavior.md 由此機制產生」說明（F-05）
- § 關鍵假設 / 驗證點段：新增 WebForms / Core 雙版產出驗證條目（F-05）

下一步進入 R7 Implement 階段（見 `reviews/round-7-decisions.md` § 下一步 + `field-reference-candidates.md` Phase 3）。
