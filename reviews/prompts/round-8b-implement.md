# Round 8b Implement Prompt — PROPOSAL-013（給 Codex CLI 使用）

> **使用方式**：開新 Codex CLI session（建議 gpt-5.4 xhigh 或更新），把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：
>   - 新增 / 修改 skill 檔（`sdd-ddd-*-skill/`，英文版約 40 檔影響）
>   - 新增 repo 根 `TEMPLATE-COVERAGE.md` 與 `TEMPLATE-LANGUAGE-GLOSSARY.md`
>   - 更新 `CHANGELOG.md`
>   - **不改繁中版檔**（延到 Closeout C1）
>   - **不改 proposal / review 報告 / decisions 檔**（已 approved）
> **禁止事項**：不補繁中版、不動其他 proposal scope（P012 路徑已由 R8a 處理）、不自動 commit、**散文 / 範例文字保留原語言**（per P013 §1 邊界：只英文化「結構性術語」，不英文化「使用者可填寫內容」）。
> **前置依賴**：
>   - **PROPOSAL-012 必須已完成實施**（路徑前提；本 prompt Step 0.2 會 verify）
>   - PROPOSAL-013 狀態為 `approved`；`reviews/round-8b-decisions.md` 存在
>   - PROPOSAL-009 / 010 / 003 / 008 已 implemented（其產物為 cascade 引用對象，本 session 不修改其機制）
> **規模 & 時程**：Large — 約 40 檔；新增 14 檔 + 修改 23 檔。預估 5-8 小時。
> **為何指派 Codex**：本輪以「結構性英文化 + anchor 注入 + cascade 同步」為主，動作機械化、跨檔重複高，Codex 一次大批包效率好；R8b 設計判斷已在 R8b Approve 全部敲定，Implement 階段不需設計決策。
>
> **⚠ Codex Token 緊縮警告**：使用者目前 Codex 週用量僅剩 ~6%。本 prompt 設計為「處理到完，跑到 limit 就斷」框架——
>   - 4 個 Sub-wave 之間是**強 checkpoint**，斷在 sub-wave 邊界 = 安全
>   - Sub-wave 2 / 3 內部按**檔案類別**再切（templates → scaffolding；references → SKILL.md），斷在類別邊界 = 安全
>   - 檔內中段斷 = 不安全；接手者需從該檔頭重做
>   - 每完成一個 sub-wave / 類別，必須 announce `### CHECKPOINT: <name> COMPLETE`（marker 字串，給接手者 grep 用）
>   - 若用盡 token 仍未跑完，輸出「中段斷掉回報」（見 Step 6.2）讓接手者（Cursor / Claude Code）能 resumption
>
> **下一步**：本 session 完成（或斷掉接手完成）後，進入 **R8b Implement Review**（仿 R7 Implement Review pattern；目標執行者另行決定）。

---BEGIN PROMPT---

你是 PROPOSAL-013（系統文件樣板覆蓋與 Template Coverage Matrix）的實施者。本 proposal 已於 R8b Review + R8b Approve 完成設計評估與修訂，狀態為 `approved`，含 17 個 finding 的修訂紀錄與 4 項設計決策拍板（per `reviews/round-8b-decisions.md`）。你的工作是把已 approved 的內容落實到 skill 檔。

## Step 0：前置檢查（critical，不可略過）

### 0.1 P013 / R8b 文件 verify

執行：
1. 讀 `proposals/PROPOSAL-013-system-document-template-coverage.md` 第一行附近，確認 `**狀態**: \`approved\``
2. 確認 `reviews/round-8b-decisions.md` 存在
3. 確認 `reviews/round-8b-report.md` 存在

任一不滿足 → **停止回報**，不要自行 approve。

### 0.2 P012 implemented verify（critical）

P013 影響範圍含「P012-after」路徑（特別是 `templates/CLAUDE.md` 內的 directory tree 路徑、`init-project-flow.md` 的 `specs/shared/` cascade）。P012 未 implemented 時 Sub-wave 2 / 3 部分檔會無法落地。

執行以下檢查：
1. `proposals/PROPOSAL-012-distribution-friendly-paths.md` 第一行附近狀態為 `implemented`（不是 `approved` 也不是 `draft`）
2. `grep -r "specs/_共用" sdd-ddd-webforms-skill/ sdd-ddd-core-skill/` → 應**無**命中（P012 已把舊路徑改完）
3. `grep -rl "specs/shared" sdd-ddd-webforms-skill/templates/CLAUDE.md sdd-ddd-core-skill/templates/CLAUDE.md sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md` → 應**有**命中

任一不滿足 → **停止回報**：「P012 未 implemented 或路徑 cascade 不完整。請先確認 R8a Implement 完成。**不要**自己跑去做 P012。」

### 0.3 R8a Approve 完成 verify

確認 `reviews/round-8a-decisions.md` 存在（R8a 處理 P012 的 approve 決議）。若不存在但 0.2 通過，可繼續（R8a decisions 是 P012 implement 的前置，0.2 通過代表 P012 已 implemented）。

## Token 緊縮策略 & Resumption（critical for Codex）

**使用者目前 Codex 週用量僅剩 ~6%**。本 prompt 設計為「處理到完，跑到 limit 就斷」：

- **每個 Sub-wave 之間**：強 checkpoint。斷在 sub-wave 邊界 = 安全（接手者從未開始的 sub-wave 接續）。
- **Sub-wave 2 / 3 內部**：按檔案類別切（Sub-wave 2: `templates/` → `scaffolding/`；Sub-wave 3: `references/` → `SKILL.md`），斷在類別邊界 = 安全。
- **檔內中段斷**：不安全。接手者需 git revert 該檔再從頭做（避免 partial state）。
- **每完成一個 sub-wave / 類別**，必須 announce 含以下 marker 字串的訊息：
  - `### CHECKPOINT: SUB-WAVE 1 COMPLETE`
  - `### CHECKPOINT: SUB-WAVE 2.TEMPLATES COMPLETE`
  - `### CHECKPOINT: SUB-WAVE 2.SCAFFOLDING COMPLETE`
  - `### CHECKPOINT: SUB-WAVE 2 COMPLETE`
  - `### CHECKPOINT: SUB-WAVE 3.REFERENCES COMPLETE`
  - `### CHECKPOINT: SUB-WAVE 3.SKILL COMPLETE`
  - `### CHECKPOINT: SUB-WAVE 3 COMPLETE`
  - `### CHECKPOINT: SUB-WAVE 4 COMPLETE`
- **接手者（Cursor / Claude Code）的 Resumption 邏輯**：
  1. `git status` 查看 dirty 檔
  2. 在前次 Codex session 的 transcript / 終端輸出中 grep `### CHECKPOINT:` 找最後一個 marker
  3. 對照本 prompt Step 3 的 sub-wave 切分，從第一個未完成的 sub-wave / 類別接續
  4. 若 transcript 不可得，依 `git status` 列出的 dirty 檔對照 §影響範圍 推斷進度

## Step 1：讀入必要檔案

### A. 權威設計來源（必讀，逐段對照）

1. `proposals/PROPOSAL-013-system-document-template-coverage.md`（**THE 實施藍圖**）
   - §1 Dflow-managed `.md` 樣板覆蓋原則 + canonical English structural language + Important Dflow-updated sections（6 個 anchor）+ **Anchor coexistence rule**（per F-08）+ **Anchor naming rules**（per F-15 Path B：namespacing only，versioning 延後）+ **Design intent**（anchor 啟用 localized heading 的長期動機）
   - §1.1 Template Language Glossary（含 R8b 補的 Lightweight Changes / Resume Pointer / Behavior Delta 三列；位置 repo 根，per F-11 Path A）
   - §2 / §3 補齊 WebForms / Core 樣板清單（含每個 template 主要章節 / 欄位定位）
   - §4 TEMPLATE-COVERAGE.md matrix（14 列 + 第 7 欄 Section anchors，per F-06 Path A；位置 repo 根 per F-11 Path A）
   - §5 init-project mandatory baseline + **Baseline 比對表**（per F-07 / F-16 Path A：明列「Core decisions/README.md 為 P013 修補 P010 漏列」）
   - §6 references 缺檔處理 cascade
   - §7 lightweight-spec Implementation Tasks 段（DOC-1 行對齊 §1.1 glossary canonical heading，per F-12）
   - §影響範圍 表（含 R8b 補列：templates/CLAUDE.md 雙版 / pr-review-checklist 雙版 / git-integration.md 雙版 / ddd-modeling-guide.md 等）
   - §不納入影響範圍 表
   - §關聯 Proposal（特別 P010 / P012 / backlog 三列）

### B. R8b context（理解 finding 修訂脈絡）

2. `reviews/round-8b-decisions.md`（17 finding 修訂紀錄；4 項設計決策拍板；既有機制相容性確認；Sub-wave 切分建議）
3. `reviews/round-8b-report.md`（reviewer 原始 finding 脈絡，遇歧義時參考）

### C. 流程規範

4. `proposals/review-workflow.md`（§三 Implementation 紀錄格式）

### D. 現況檔（會實際修改 / 新增；按 Sub-wave 列表，見 Step 3）

### E. 不要讀的東西

- 繁中版（`*-tw/`）—— 延到 Closeout C1
- `tutorial/` —— 屬 Closeout C2
- 其他 proposal 全文（P003 / P008 / P009 / P010 / P012）—— 已 implemented；本輪不修改其機制；遇引用直接信任 P013 已 verify 的相容性結論
- R1-R7 review 報告 / decisions —— 歷史檔，凍結
- `E:\工作區\SDD-Starter-Kit\`（V2）—— P013 不依賴 V2

## Step 2：本輪範圍邊界（嚴格遵守）

**在範圍內**（PROPOSAL-013 §影響範圍表所列，約 40 檔英文版）：

- 新增 12 份 templates / scaffolding（Sub-wave 1）
- 新增 2 份 repo-level 維護契約檔（Sub-wave 1）
- 修改 11 份既有 templates（Sub-wave 2）
- 修改 8 份既有 scaffolding（Sub-wave 2）
- 擴充 TEMPLATE-LANGUAGE-GLOSSARY.md（Sub-wave 2 末段）
- 修改 15 份既有 references（Sub-wave 3）
- 修改 2 份 SKILL.md（Sub-wave 3）
- 修改 lightweight-spec.md / modify-existing-flow.md（Sub-wave 4 — 同檔可能跨 wave，本輪只做 §7 Implementation Tasks 與 task guard 對應段）
- 更新 CHANGELOG.md（Sub-wave 4）

**不在範圍內**：

- **繁中版**（`*-tw/`）—— 延到 Closeout C1
- **proposal / review / decisions 檔**（已 approved，不需改）
- **tutorial/**（C2 範圍）
- **其他 proposal 機制核心**：P003 BR-ID 結構 / P008 drift-verification 演算法 / P009 _index.md 6 段結構 / P010 mandatory baseline 既有 4-5 項 / P012 路徑機制 —— 可**更新引用 / cascade 同步**，但**不改機制核心**
- 新增 V2 級別的 onboarding 文件（`SDD-AI協作開發模式介紹.md` 等，屬 C2）
- `references/*.md` WebForms / Core 流程同步矩陣（P013 §不納入影響範圍 已聲明）

若實施過程發現 proposal 沒列但邏輯上牽連的檔，**停下來回報**，不要擴大 scope。

## Step 3：實施動作（4 個 Sub-wave，按順序執行）

---

### Sub-wave 1 — 純新增（無依賴，最安全）

**目標**：建立 12 份新 templates / scaffolding + 2 份維護契約檔。**完全不觸碰任何既有檔**。

**新增 templates**（雙版共 11 檔）：

| # | 檔 | 內容定位（依 P013 §2 / §3）|
|---|---|---|
| 1 | `sdd-ddd-webforms-skill/templates/glossary.md` | Term / Definition / Bounded Context / Code Mapping / optional notes |
| 2 | `sdd-ddd-webforms-skill/templates/models.md` | Entities / Value Objects / Domain Services / Repository Interfaces / Code Mapping |
| 3 | `sdd-ddd-webforms-skill/templates/rules.md` | BR-ID / Rule summary / Behavior anchor（範例值 `[BR-001](./behavior.md#br-001-rule-name)`，per F-08）/ Status / Last updated |
| 4 | `sdd-ddd-webforms-skill/templates/context-map.md` | Context list / Relationships / Integration notes / Open questions |
| 5 | `sdd-ddd-webforms-skill/templates/tech-debt.md` | Item / Location / Description / Severity / Migration impact / Status |
| 6 | `sdd-ddd-core-skill/templates/glossary.md` | 同 webforms 版欄位 |
| 7 | `sdd-ddd-core-skill/templates/models.md` | Aggregates / Entities / VOs / Domain Services / Specifications / Repository Interfaces |
| 8 | `sdd-ddd-core-skill/templates/rules.md` | BR-ID / Rule summary / Behavior anchor / Aggregate / Status / Last updated |
| 9 | `sdd-ddd-core-skill/templates/events.md` | Event name / Producer / Trigger / Payload / Consumers / Delivery expectation |
| 10 | `sdd-ddd-core-skill/templates/context-map.md` | Contexts / Relationships / Upstream/Downstream / Published Language / ACL / Events |
| 11 | `sdd-ddd-core-skill/templates/tech-debt.md` | Item / Layer / Decision / debt / Impact / Follow-up / Status |

**新增 scaffolding**（Core only 1 檔）：

| # | 檔 | 內容定位 |
|---|---|---|
| 12 | `sdd-ddd-core-skill/scaffolding/architecture-decisions-README.md` | ADR naming convention / when to write ADR / minimal ADR fields（per P013 §3）|

**新增維護契約檔**（repo 根，雙版共用，per F-11 Path A）：

| # | 檔 | 內容 |
|---|---|---|
| 13 | `<repo root>/TEMPLATE-COVERAGE.md` | 把 P013 §4 的 14 列 matrix（含第 7 欄 Section anchors）落地為獨立檔；首段加說明：「本檔是 maintenance 契約，非 runtime 大腦；SKILL.md 只指向不複製」+「Section anchors 欄使用方式」段 per P013 §4 末段 |
| 14 | `<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md` | 把 P013 §1.1 glossary 表（含 R8b 補的 Lightweight Changes / Resume Pointer / Behavior Delta）落地為獨立檔；首段加說明：「人讀輔助 / review reference，非第二套 template」 |

**範本格式要求（每檔頂部）**：

```markdown
<!-- Template maintained by Dflow. See proposals/PROPOSAL-013 for origin. -->
```

維護檔（13、14）首行可改為：

```markdown
<!-- Maintenance contract for Dflow. See proposals/PROPOSAL-013 §4 / §1.1 for origin. -->
```

**內容語言原則（per P013 §1）**：

- 結構性 heading / 欄位 / placeholder 名 / anchor → canonical English
- 散文說明 / 範例文字 / 占位 placeholder → 可繁中（讓使用者讀範本時直觀）

**完成 announce**：`### CHECKPOINT: SUB-WAVE 1 COMPLETE`（含 14 檔列表）

---

### Sub-wave 2 — 既有 templates / scaffolding canonical English + anchor 注入

**目標**：把既有 templates / scaffolding 的結構標題英文化 + 6 個 initial anchor 注入到對應 template；同步擴充 TEMPLATE-LANGUAGE-GLOSSARY.md。

**Anchor 注入對照**（per P013 §1 & §4 matrix Section anchors 欄）：

| Template | 注入 anchor |
|---|---|
| `_index.md` | `current-br-snapshot`, `lightweight-changes` |
| `phase-spec.md` | `implementation-tasks`, `behavior-scenarios`, `open-questions` |
| `lightweight-spec.md` | `implementation-tasks` |
| `rules.md` | `business-rules` |
| `behavior.md` | `behavior-scenarios` |
| 其他 templates / scaffolding | — |

**Anchor 寫入格式**：每個 anchor 在對應 H2 / H3 之上一行 ——

```markdown
<!-- dflow:section <id> -->
## <Heading>
```

#### Sub-wave 2.templates — 既有 templates 英文化（雙版共 11 檔）

按依賴 / 重要度順序處理：

1. **`templates/_index.md`**（雙版 2 檔，per P013 §影響範圍 + F-12）
   - H2 改名：`目標與範圍` → `Goals & Scope`；`輕量修改紀錄` → `Lightweight Changes`；`接續入口（Resume Pointer）` → `Resume Pointer`
   - 保留 `Phase Specs` / `Current BR Snapshot`（P009 既有英文）
   - 注入 anchor：`current-br-snapshot`、`lightweight-changes`
2. **`templates/phase-spec.md`**（雙版 2 檔）
   - 全部 Chinese 結構標題英文化（依 §1.1 glossary：實作任務 → Implementation Tasks；行為情境 → Behavior Scenarios；業務規則 → Business Rules；領域概念 → Domain Concepts；資料結構變更 → Data Structure Changes；測試策略 → Test Strategy；遷移考量 → Migration Considerations 等）
   - Core 版的 Domain Events 段保留原英文標題
   - 注入 anchor：`implementation-tasks`、`behavior-scenarios`、`open-questions`
3. **`templates/lightweight-spec.md`**（雙版 2 檔）
   - H2 改名：`問題` → `Problem`；`行為變更（Delta）` → `Behavior Delta`；`根因` → `Root Cause`；`修復方式` → `Fix Approach`；`發現的技術債（如有）` → `Tech Debt Discovered (if any)`
   - 保留 Sub-wave 4 處理 `Implementation Tasks` 段新增（本 wave 先做改名 + anchor 注入）
   - 注入 anchor：`implementation-tasks`（為 Sub-wave 4 預留位置）
4. **`templates/behavior.md`**（雙版 2 檔，主體已英文，僅檢查 + 注入 anchor）
   - 注入 anchor：`behavior-scenarios`
5. **`templates/context-definition.md`**（雙版 2 檔）
   - 結構標題英文化（依 §1.1 / §3 欄位定位）
   - 不注入 anchor
6. **`templates/aggregate-design.md`**（Core only 1 檔）
   - 結構標題英文化
   - 不注入 anchor
7. **`templates/CLAUDE.md`**（雙版 2 檔，per F-01 Path A）
   - **H2 全英文化**：`系統脈絡` → `System Context`；`開發流程` → `Development Workflow`
   - **H3 全英文化**：`核心原則` → `Core Principles`；`目錄結構` → `Project Structure`；`Ceremony 三層` → `Three Ceremony Tiers`；`新增功能` → `New Feature`；`新增階段` → `New Phase`；`修改既有功能` → `Modify Existing`；`Bug 修復` → `Bug Fix`；`Feature 收尾` → `Feature Closeout`；`Git 整合` → `Git Integration`；`Domain 層規範` → `Domain Layer Rules`；`術語表` → `Glossary`；`AI 協作注意事項` → `AI Collaboration Notes`
   - **不改 specs/ 路徑**（已由 P012 處理）
   - **不改散文 / 範例 / 註解**（per P013 §1 邊界）

**完成 announce**：`### CHECKPOINT: SUB-WAVE 2.TEMPLATES COMPLETE`

#### Sub-wave 2.scaffolding — 既有 scaffolding 英文化（雙版共 8 檔）

8. **`scaffolding/_overview.md`**（雙版 2 檔，檢查級別）
   - grep verify 結構性中文詞，極少或無；少量殘留改英文
9. **`scaffolding/_conventions.md`**（雙版 2 檔，檢查級別）
   - 同上
10. **`scaffolding/Git-principles-gitflow.md`**（雙版 2 檔，per F-14 精化）
    - 範圍精化：**只改** Integration Commit Message Conventions 段內固定 labels（`Feature 目標` → `Feature Goal`；`變動範圍` → `Change Scope`；`關聯 BR-ID` → `Related BR-IDs`；`Phase 數` → `Phase Count`；`輕量修改` → `Lightweight Changes`）
    - 其他段落已英文，僅檢查
11. **`scaffolding/Git-principles-trunk.md`**（雙版 2 檔，per F-14 精化）
    - 同 gitflow 版（squash / rebase 相關段內固定 labels）
12. **`scaffolding/CLAUDE-md-snippet.md`**（雙版 2 檔，per F-01 Path A 同步）
    - 與 templates/CLAUDE.md 同步全英化（H2 / H3 結構標題）
    - inline 路徑引用 / directory tree 範例由 P012 處理（本 wave 不動）

**完成 announce**：`### CHECKPOINT: SUB-WAVE 2.SCAFFOLDING COMPLETE`

#### Sub-wave 2.glossary — 擴充 TEMPLATE-LANGUAGE-GLOSSARY.md

把 Sub-wave 2 過程中**實際遇到**的所有結構性中文詞補入 glossary（位置：repo 根 `TEMPLATE-LANGUAGE-GLOSSARY.md`，Sub-wave 1 已建立）：

- §1.1 已列：Implementation Tasks / Behavior Scenarios / Business Rules / Current BR Snapshot / Domain Models / Change Scope / Lightweight Change / Lightweight Changes / Resume Pointer / Behavior Delta
- 待補（依 Sub-wave 2 處理結果）：Goals & Scope / Project Structure / Three Ceremony Tiers / Domain Layer Rules / AI Collaboration Notes / Feature Goal / Related BR-IDs / Phase Count / Test Strategy / Data Structure Changes / Migration Considerations / 等等

**完成 announce**：`### CHECKPOINT: SUB-WAVE 2 COMPLETE`

---

### Sub-wave 3 — references cascade + SKILL.md

**目標**：把既有 references 與 SKILL.md 雙版同步到 Sub-wave 2 已英文化的 canonical heading。

#### Sub-wave 3.references — references cascade（雙版共 15 檔）

按依賴順序：

1. **`references/init-project-flow.md`**（雙版 2 檔，per P013 §5）
   - mandatory baseline 加 `← templates/<xxx>.md` cascade source 註記
   - WebForms：glossary.md / migration/tech-debt.md cascade
   - Core：glossary.md / context-map.md / architecture/tech-debt.md / **architecture/decisions/README.md ← scaffolding/architecture-decisions-README.md**（per F-07 / F-16，新加）
2. **`references/new-feature-flow.md`**（雙版 2 檔，per P013 §6）
   - 缺 domain docs（glossary / models / rules / behavior）時用對應 template 建立
   - Core 加 events.md / context-map.md cascade
3. **`references/modify-existing-flow.md`**（雙版 2 檔，per P013 §6 + §7）
   - baseline capture 缺 domain docs 時用 template 建立
   - tech debt 缺檔時用 templates/tech-debt.md 建立
   - **task checklist guard**：T2 lightweight 不再一律跳過 task generation；改為產生精簡 task checklist；若 task list 看似超出 short fix checklist，AI 應**主動詢問開發者**是否升級 T1（**不以項目數自動升級**）
   - completion checklist 適用範圍擴展：Implementation Tasks 完成檢查同時適用 phase-spec.md 與 lightweight-spec.md
4. **`references/finish-feature-flow.md`**（雙版 2 檔，per F-04）
   - (a) BC sync 前缺檔時用對應 template 建立（rules.md / behavior.md / events.md）
   - (b) **Step 5 Integration Summary 段**內固定 labels 改 canonical English：`Feature 目標` → `Feature Goal`；`變動範圍` → `Change Scope`；`關聯 BR-ID` → `Related BR-IDs`；`Phase 數` → `Phase Count`；`輕量修改` → `Lightweight Changes`；`Phase 清單` → `Phase List`；`下一步（developer）` → `Next Steps (developer)`
   - 引用 `_index.md` 的 section name 同步更新（`目標與範圍` → `Goals & Scope` 等）
5. **`references/drift-verification.md`**（雙版 2 檔，per F-08）
   - rules.md 缺失時指向 `templates/rules.md`
   - **新增段**「Anchor coexistence with `dflow:section`」：明示 `dflow:section` HTML comment anchor 不取代 BR-ID markdown anchor link；drift-verification 演算法**不變**，仍以 BR-ID + markdown auto-id 為主索引；`dflow:section` 僅作段落定位輔助
   - Core 版保留 events.md bonus check
6. **`references/pr-review-checklist.md`**（雙版 2 檔，per F-03）
   - 同步引用的 template section name 改 canonical English：`輕量修改紀錄` → `Lightweight Changes`；`接續入口` → `Resume Pointer`；`行為變更（Delta）` → `Behavior Delta`；`Current BR Snapshot` 維持（P009 既有英文）
7. **`references/git-integration.md`**（雙版 2 檔，per F-17 檢查級別）
   - grep verify 該檔是否含 P013 §1 範圍內 Chinese 結構性詞
   - **預期 grep 結果為 0**（R8b approve 已 verify 過）
   - 在檔尾加一行註記：`<!-- R8b verified: no Chinese structural terms in scope; per F-17 Path A. -->` 作為 implement traceability
8. **`references/ddd-modeling-guide.md`**（Core only 1 檔，per F-13）
   - Bounded Context Relationships 段補一句：「若 `specs/domain/context-map.md` 不存在，使用 `templates/context-map.md` 建立後再填寫。」
   - 與 §6 其他 reference flow 的缺檔處理一致

**完成 announce**：`### CHECKPOINT: SUB-WAVE 3.REFERENCES COMPLETE`

#### Sub-wave 3.SKILL — SKILL.md cascade（雙版共 2 檔）

**`sdd-ddd-webforms-skill/SKILL.md` + `sdd-ddd-core-skill/SKILL.md`**（per F-05）：

(1) Templates 表新增 living document templates 列：
- glossary.md / models.md / rules.md / context-map.md / tech-debt.md（雙版共有）
- events.md（Core only）
- aggregate-design.md（Core only，原已存在但確認列表完整）

(2) Templates / Scaffolding 段加段：說明 Template Coverage Matrix 與 Template Language Glossary 位置（repo 根）+ 用途（review reference / maintenance contract）+ 使用情境（不在 runtime 讀，僅在 review / maintenance / new template 新增時參考）

(3) Templates & Scaffolding 段內所有引用的 template section name 同步為 canonical English（cascade from Sub-wave 2）：
- `_index.md` 含的 section 描述：`目標與範圍` → `Goals & Scope`；`輕量修改紀錄` → `Lightweight Changes`；`接續入口` → `Resume Pointer`
- scaffolding/CLAUDE-md-snippet 描述視 F-01 Path A 同步全英化提示
- 其他引用 cascade

**完成 announce**：`### CHECKPOINT: SUB-WAVE 3.SKILL COMPLETE` 後 `### CHECKPOINT: SUB-WAVE 3 COMPLETE`

---

### Sub-wave 4 — lightweight-spec Implementation Tasks + task guard 流程細節 + CHANGELOG

**目標**：補上 P013 §7 的 lightweight-spec Implementation Tasks 段；完善 modify-existing-flow.md 的 task guard 細節（Sub-wave 3 已 cascade，本 wave 補 §7 細節）；產出 CHANGELOG.md 條目。

1. **`templates/lightweight-spec.md`**（雙版 2 檔，per P013 §7）
   - 在 Sub-wave 2 已注入的 `<!-- dflow:section implementation-tasks -->` anchor 之下，新增完整 Implementation Tasks 段：

```markdown
<!-- dflow:section implementation-tasks -->
## Implementation Tasks

> Keep T2 Light tasks concise. If the fix scope starts to expand, AI should pause and ask the developer whether to keep this as T2 or upgrade it to T1. Do not auto-upgrade based on task count alone.

- [ ] {LAYER}-1: {minimal required change}
- [ ] TEST-1: {minimal verification / regression test}
- [ ] DOC-1: Update `_index.md` Lightweight Changes and Current BR Snapshot
```

   - DOC-1 的 `Lightweight Changes` 與 `Current BR Snapshot` 必須與 _index.md 英文化後的 heading 完全一致（per F-12；Sub-wave 2 已英文化）
   - **WebForms 版 layer tags**：`DOMAIN / PAGE / DATA / TEST / DOC`
   - **Core 版 layer tags**：`DOMAIN / APP / INFRA / API / TEST / DOC`
   - 在範本下方加註：「Layer tag list above is the recommended set; the developer may extend with project-specific tags as needed.」

2. **`references/modify-existing-flow.md`**（雙版 2 檔，per P013 §7 細節擴充）
   - Sub-wave 3 已做基本 task checklist guard cascade；本 wave 確認以下細節落地：
     - 不再說 lightweight spec 一律跳過 task generation
     - T2 產生精簡 task checklist
     - 若 task list 看似超出 short fix checklist，AI **主動詢問**開發者是否升級為 T1
     - **不以固定項目數作為自動升級標準**
     - completion checklist 適用範圍：Implementation Tasks 完成檢查適用 phase-spec.md 與 lightweight-spec.md
     - 「是否 collapse / remove 實作任務」可保留為 team convention，但不應只限 full spec；T2 是否保留 task section 由開發者確認

3. **`CHANGELOG.md`**（repo 根，1 檔）

在 CHANGELOG 最上方新增段（依 review-workflow.md 第三節格式）：

```markdown
## {YYYY-MM-DD} — R8b 實施：PROPOSAL-013 系統文件樣板覆蓋與 Template Coverage Matrix

**前置**：R8a / PROPOSAL-012 已 implemented；R8b Review + Approve 處理 17 個 finding（accept: 11 / accept-with-choice: 6）；4 項設計決策拍板 per `reviews/round-8b-decisions.md`
**Proposal**：`proposals/PROPOSAL-013-system-document-template-coverage.md`（approved）

**Sub-wave 1 — 純新增**
- 新增 `templates/glossary.md` / `models.md` / `rules.md` / `context-map.md` / `tech-debt.md` 雙版共 10 檔
- 新增 `templates/events.md`（Core only）
- 新增 `scaffolding/architecture-decisions-README.md`（Core only，per F-07 / F-16 修補 P010 baseline）
- 新增 `<repo root>/TEMPLATE-COVERAGE.md`（含 Section anchors 欄，per F-06 Path A）
- 新增 `<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md`（per F-11 Path A 雙版共用）

**Sub-wave 2 — 既有 templates / scaffolding canonical English + anchor 注入**
- 修改 `templates/_index.md` / `phase-spec.md` / `lightweight-spec.md` / `behavior.md` / `context-definition.md`（雙版共 10 檔）
- 修改 `templates/aggregate-design.md`（Core only）
- 修改 `templates/CLAUDE.md`（雙版，per F-01 Path A 全 H2/H3 英文化）
- 修改 `scaffolding/_overview.md` / `_conventions.md`（雙版共 4 檔，檢查級別）
- 修改 `scaffolding/Git-principles-{gitflow,trunk}.md`（雙版共 4 檔，per F-14 精化 Integration Commit labels）
- 修改 `scaffolding/CLAUDE-md-snippet.md`（雙版，per F-01 Path A 同步全英化）
- 注入 6 個 initial `<!-- dflow:section X -->` anchor 到對應 templates
- 擴充 `<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md`

**Sub-wave 3 — references cascade + SKILL.md**
- 修改 `references/init-project-flow.md` / `new-feature-flow.md` / `modify-existing-flow.md` / `finish-feature-flow.md` / `drift-verification.md` / `pr-review-checklist.md` / `git-integration.md`（雙版共 14 檔）+ `ddd-modeling-guide.md`（Core only）
- `finish-feature-flow.md` Step 5 Integration Summary labels 英文化（per F-04）
- `drift-verification.md` 補 Anchor coexistence rule 段（per F-08）
- `pr-review-checklist.md` section name cascade 同步（per F-03）
- `git-integration.md` 檢查級別，加 R8b verified 註記（per F-17）
- `ddd-modeling-guide.md` 補 context-map.md 缺檔處理（per F-13）
- 修改 `SKILL.md`（雙版，per F-05：Templates 表 + Coverage Matrix 引用 + section name cascade）

**Sub-wave 4 — lightweight-spec Implementation Tasks + task guard**
- 修改 `templates/lightweight-spec.md`（雙版）：補 Implementation Tasks 段 per §7 + 雙版 layer tags
- 修改 `references/modify-existing-flow.md`（雙版）：完善 task guard 流程（不以項目數自動升級；AI 主動詢問）

**繁中版同步**：延至 Post-Review Closeout C1（依 review-workflow.md §七）

**設計動機補寫**（R8b approve 階段識別並落地於 P013）：
- §1 補 Anchor coexistence rule 段 + Anchor naming rules（namespacing only，versioning 延後到 backlog § 7）+ Design intent 段（明示 anchor 機制啟用 localized heading 的長期動機）
- `planning/public-distribution-backlog.md` § 1 加註 P013 接管；新增 §1.1「anchor-enabled localized heading（P013 後）」候選方向

**下一步**：R8b Implement Review（仿 R7 Implement Review pattern）
```

**完成 announce**：`### CHECKPOINT: SUB-WAVE 4 COMPLETE`

---

## Step 4：實施後自我驗收

### 4.1 Sub-wave 1 verify

- 14 個新檔存在（11 templates + 1 scaffolding + 2 repo root maintenance contract）
- `<repo root>/TEMPLATE-COVERAGE.md` 含 14 列 matrix + 第 7 欄 Section anchors（`grep "Section anchors" <repo root>/TEMPLATE-COVERAGE.md` 應命中）
- `<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md` 含完整 glossary（最少 10 列，視 Sub-wave 2 擴充而增）
- 每個新檔頂部有 `<!-- Template maintained by Dflow` 或 `<!-- Maintenance contract for Dflow` marker

### 4.2 Sub-wave 2 verify

執行：
```
grep -rE "(系統脈絡|開發流程|核心原則|目錄結構|Ceremony 三層|新增功能|新增階段|修改既有功能|Bug 修復|Feature 收尾|Git 整合|Domain 層規範|術語表|AI 協作注意事項|目標與範圍|輕量修改紀錄|接續入口|實作任務|行為情境|業務規則|Feature 目標|變動範圍|關聯 BR-ID|Phase 數)" sdd-ddd-webforms-skill/templates/ sdd-ddd-core-skill/templates/ sdd-ddd-webforms-skill/scaffolding/ sdd-ddd-core-skill/scaffolding/
```
**預期**：極少或無命中（散文 / 範例文字內的偶發詞除外，需逐項判斷是否屬「結構性術語」）

執行：
```
grep -rc "dflow:section" sdd-ddd-webforms-skill/templates/ sdd-ddd-core-skill/templates/
```
**預期**：6+ 個檔有命中（_index.md / phase-spec.md / lightweight-spec.md / behavior.md / rules.md 雙版至少有 anchor）

### 4.3 Sub-wave 3 verify

執行：
```
grep -rE "(輕量修改紀錄|目標與範圍|接續入口|行為變更（Delta）|Feature 目標|變動範圍|關聯 BR-ID|Phase 數)" sdd-ddd-webforms-skill/references/ sdd-ddd-core-skill/references/ sdd-ddd-webforms-skill/SKILL.md sdd-ddd-core-skill/SKILL.md
```
**預期**：無命中

執行：
```
grep -E "TEMPLATE-COVERAGE|Template Coverage Matrix|TEMPLATE-LANGUAGE-GLOSSARY" sdd-ddd-webforms-skill/SKILL.md sdd-ddd-core-skill/SKILL.md
```
**預期**：兩版 SKILL.md 都有命中

執行：
```
grep "Anchor coexistence" sdd-ddd-webforms-skill/references/drift-verification.md sdd-ddd-core-skill/references/drift-verification.md
```
**預期**：兩版都命中

執行：
```
grep "R8b verified" sdd-ddd-webforms-skill/references/git-integration.md sdd-ddd-core-skill/references/git-integration.md
```
**預期**：兩版都命中

### 4.4 Sub-wave 4 verify

執行：
```
grep -A 5 "dflow:section implementation-tasks" sdd-ddd-webforms-skill/templates/lightweight-spec.md sdd-ddd-core-skill/templates/lightweight-spec.md
```
**預期**：兩版都命中且其下緊接 Implementation Tasks 段含 DOC-1 行

執行：
```
grep -E "(DOMAIN|PAGE|DATA|APP|INFRA|API)" sdd-ddd-webforms-skill/templates/lightweight-spec.md sdd-ddd-core-skill/templates/lightweight-spec.md
```
**預期**：webforms 版命中 DOMAIN/PAGE/DATA；core 版命中 DOMAIN/APP/INFRA/API

執行：
```
grep -E "(主動詢問|不以.*項目數.*升級|Do not auto-upgrade based on task count)" sdd-ddd-webforms-skill/references/modify-existing-flow.md sdd-ddd-core-skill/references/modify-existing-flow.md
```
**預期**：兩版都命中

### 4.5 範圍外 untouched verify

- 繁中版檔案（`*-tw/`）**未被本 session 修改**（`git diff --name-only sdd-ddd-webforms-skill-tw sdd-ddd-core-skill-tw` 應為空）
- `proposals/PROPOSAL-013-*.md` **未被修改**（只讀）
- `reviews/round-8b-*.md` **未被修改**
- `tutorial/` **未被修改**

任一驗收失敗 → 修正後重跑該驗收。

## Step 5：CHANGELOG 更新

已在 Sub-wave 4 完成（見 Step 3 / Sub-wave 4 / 第 3 點）。

## Step 6：完成後回報 + 中段斷掉的處理

### 6.1 完成回報（全部 4 個 Sub-wave 都跑完才用此）

```
R8b 實施完成（PROPOSAL-013 系統文件樣板覆蓋與 Template Coverage Matrix）。

**Sub-wave 1 — 純新增**：✓ 完成（14 檔新增）
**Sub-wave 2 — 既有 templates / scaffolding 英文化 + anchor 注入**：
  - 2.templates：✓ 完成（11 檔，含 Core only aggregate-design.md）
  - 2.scaffolding：✓ 完成（8 檔）
  - 2.glossary 擴充：✓ 完成
**Sub-wave 3 — references cascade + SKILL.md**：
  - 3.references：✓ 完成（15 檔，含 Core only ddd-modeling-guide.md）
  - 3.SKILL：✓ 完成（2 檔）
**Sub-wave 4 — lightweight Implementation Tasks + task guard + CHANGELOG**：✓ 完成

**驗收結果**（見 Step 4）：
- 4.1 Sub-wave 1 verify：✓ / ✗
- 4.2 Sub-wave 2 verify：✓ / ✗
- 4.3 Sub-wave 3 verify：✓ / ✗
- 4.4 Sub-wave 4 verify：✓ / ✗
- 4.5 範圍外 untouched：✓ / ✗

**待使用者 commit 後進入 R8b Implement Review**。
建議 commit message：
  `Implement PROPOSAL-013: System Document Template Coverage (R8b)`
  含 R8b Review + Approve 17 個 finding 修正、4 項設計決策拍板（F-01/F-06/F-07/F-11 Path A、F-15 Path B）、anchor 機制設計動機補寫。

**備註 / 意外發現**（如有，記入 `reviews/cross-round-notes.md`；若無，寫「無」）：
```

### 6.2 中段斷掉的回報（給接手者用）

若 Codex 在中途到達 token limit / 被中斷，**最後一次 announce** 必須含以下格式：

```
### CHECKPOINT: SESSION INTERRUPTED — Resumption info follows

**最後完成的 sub-wave / 類別**：[依 transcript 中最近一個 `### CHECKPOINT: ... COMPLETE` 確定]

**正在處理但未完成（不安全狀態）**：
- [列出剛開始或部分完成的檔案路徑]
- [若有，標明「需 git revert 後重做」]

**未開始（安全）**：
- [列出剩餘 sub-wave / 類別]

**接手者建議**（給 Cursor / Claude Code）：
1. `git status` 查看 dirty 檔
2. 對照本 prompt Step 3 的 sub-wave 切分（Sub-wave 1 / 2.templates / 2.scaffolding / 2.glossary / 3.references / 3.SKILL / 4）找到斷點
3. 對「正在處理但未完成」的檔，執行 `git checkout -- <path>` 還原（避免 partial state）
4. 從第一個未開始的 sub-wave / 類別接續
5. 接續時依本 prompt 的 Step 3 執行，跳過已完成段
```

## 禁止事項

- **不要動 PROPOSAL-013 / 任何 proposal 本身**（已 approved）
- **不要動 `reviews/round-8b-*.md`**（決議檔已凍結）
- **不要動繁中版**（`*-tw/`）—— 延到 Closeout C1
- **不要改其他 implemented proposal 的機制核心**（P003 BR-ID / P008 drift-verification 演算法 / P009 _index.md 6 段結構 / P010 既有 baseline 4-5 項 / P012 路徑機制）—— 可同步 cascade 引用，但不改機制
- **不要把散文 / 範例文字英文化**（per P013 §1 邊界：只英文化「結構性術語」，不動「使用者可填寫內容」）
- **不要新增 anchor**（只用 P013 §1 列的 6 個 initial anchor：implementation-tasks / current-br-snapshot / lightweight-changes / behavior-scenarios / business-rules / open-questions；新增 anchor 屬於後續 proposal）
- **不要把 TEMPLATE-COVERAGE.md / TEMPLATE-LANGUAGE-GLOSSARY.md 放錯位置**（per F-11 Path A：repo 根，不是 proposals/，不是 references/，不是 per-skill）
- **不要改 P013 §5 既有 4-5 項 baseline**（per F-07 Path A：唯一新增是 Core decisions/README.md；既有 features/ / glossary.md / context-map.md / tech-debt.md / 空目錄 decisions/ 不變）
- **不要試圖把 anchor 與 markdown auto-id 合併**（per F-08 Anchor coexistence：兩者並存，不同維度）
- **不要在 lightweight-spec.md 的 Implementation Tasks 段做「複雜化」**（per P013 §7：保持精簡 4 行範例；不加冗餘）
- **不要自動 commit**

## 若遇到歧義

- 第一優先：`proposals/PROPOSAL-013-*.md`（特別 §1 / §1.1 / §3 / §4 / §5 / §6 / §7 / §影響範圍 表）
- 第二優先：`reviews/round-8b-decisions.md`（17 finding 修訂紀錄、4 項設計決策拍板、Sub-wave 切分建議）
- 第三優先：`reviews/round-8b-report.md`（reviewer 原始 finding 脈絡）
- 第四優先：若仍無法決定，**停下來回報使用者**，不要自行裁定

## 特別提醒

- **F-01 Path A 全英文化邊界**：templates/CLAUDE.md 雙版的 H2/H3 全英文化；散文 / 範例 / 註解 / 中文說明保留繁中（per P013 §1 邊界）。CLAUDE-md-snippet 同步處理。
- **F-06 Path A anchor 欄位**：TEMPLATE-COVERAGE.md 第 7 欄叫 `Section anchors`；**不另開 SECTION-ANCHORS.md**。新增 anchor 時 (1) 改 §1 anchor 表 (2) 改 TEMPLATE-COVERAGE.md Section anchors 欄。
- **F-07 / F-16 baseline 邊界**：Core decisions/README.md 是「修補 P010 漏列」，**不是新引入**；P010 既有 4 項 baseline 不變。實施時 init-project-flow.md cascade 註記「← scaffolding/architecture-decisions-README.md (NEW; 擴張自 P010)」。
- **F-08 Anchor coexistence**：drift-verification.md 補一段「`dflow:section` 不取代 BR-ID markdown anchor link，演算法不變，仍以 BR-ID + markdown auto-id 為主索引」；rules.md 範本內 Behavior anchor 欄位範例值用 `[BR-001](./behavior.md#br-001-rule-name)` 樣式（讓 P008 既有演算法直接套用）。
- **F-11 Path A 維護檔位置**：TEMPLATE-COVERAGE.md / TEMPLATE-LANGUAGE-GLOSSARY.md **放 repo 根**（與 CHANGELOG.md / README.md 同層）；雙版 skill 共用一份；不是 proposals/，不是 references/。
- **F-12 §7 DOC-1 行對齊**：`Lightweight Changes` 與 `Current BR Snapshot` 必須與 _index.md 英文化後的 heading 完全一致（Sub-wave 2 已英文化，Sub-wave 4 完成 DOC-1 寫入時 verify 一致）。
- **F-15 Path B namespacing**：anchor id 全 repo 共用語意（document-type-agnostic stable identifier）。`business-rules` 在 rules.md / _index.md 兩處出現代表同類段落，這是設計意圖。
- **anchor 機制 Design intent**（per P013 §1 已寫入）：anchor 不只是 stability fail-safe，更是「啟用 localized heading 的基礎契約」——本 wave 不 implement 本地化（屬 backlog § 1.1），但 anchor 注入要做到位，為將來鋪路。
- **不擅自擴張 scope**：本 wave 範圍嚴格 = §影響範圍表所列 + R8b 補的條目。發現新議題（例如某 reference 還有別的中文 cascade）→ 記 cross-round-notes，不擴張。

---END PROMPT---
