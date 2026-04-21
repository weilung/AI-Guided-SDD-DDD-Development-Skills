# 實戰比照候選清單 — Dflow vs SDD-Starter-Kit V2

**建立日期**：2026-04-21
**來源**：
- 當前 Dflow（post-R1-R6，`sdd-ddd-webforms-skill/` + `sdd-ddd-core-skill/`）
- SDD-Starter-Kit V2（`E:\工作區\SDD-Starter-Kit`）
- V2 當初取捨紀錄（`E:\VsProject\Obts\docs\specs\_共用\SDD-v2-決策紀錄.md`）
- Obts 實戰專案（`E:\VsProject\Obts`）

**目的**：以當前 Dflow 為基準，逆向檢視 V2 是否有值得納入的元素。本檔為 Phase 1 產出，供 Phase 2（使用者加 Obts 實戰背書）與 Phase 3（決定走 hotfix / R7 / 完整 proposal 流程）使用。

**方法學說明**：不做全文 diff，只鎖定「V2 有、Dflow 沒有（或形式不同）」的候選項，逐條對照 R1–R6 是否已獨立解決。

---

## 候選總覽

| # | 候選 | 類型 | R1–R6 覆蓋狀態 | 初判優先 | Obts 實戰背書（待填） |
|---|---|---|---|---|---|
| A | Feature 組織方式：年份 × 目錄 × `_index.md` 儀表板 | 結構擴充 | 未覆蓋 | **高** | ✓ **混血方案**（2026-04-21，詳見 §A 背書）|
| B | 專案級 scaffolding 檔案群（`_overview.md` / `_conventions.md` / `Git-原則.md` / `SDD-工作流程說明.md`）| 結構擴充 | 未覆蓋 | **高** | ？ |
| C | Onboarding 文件組（README / 使用說明 / AI 協作介紹 / CLAUDE.md 範例片段）| 散佈性產物 | 未覆蓋 | 中（取決於散佈意圖）| ✓ 社群散佈定位（2026-04-21）|
| D | 單一「給人讀的」工作流程說明檔（集中 vs 目前分散於 SKILL.md + references）| 可讀性 | 未覆蓋 | 中-高 | ？ |
| E | Hotfix 24 小時補規格時限 + 系統性問題記 tech-debt | 校準 | 部分覆蓋 | 低 | ？ |
| F | AI 協作規則三級分類表（允許 / 禁止 / 須先問）| 校準 | 未覆蓋 | 中 | ？ |
| G | AI Commit 署名通用化（`Co-Authored-By: Claude`，不綁模型版本）| 校準 | 屬 Claude Code 範疇 | 低 | ？ |
| H | 中文分支命名選項（`feature/{中文名稱}` vs `feature/{SPEC-ID}`）| 選項擴充 | 未覆蓋 | 低 | ？ |
| I | Gate Checks 合併前檢查清單 | 校準 | 已覆蓋 | 低 | ？ |
| J | `_index.md` 作為 resume 入口機制 | 結構擴充（綁 A） | 未覆蓋 | 高（綁 A）| ✓ 隨 A 納入（2026-04-21）|

---

## 候選 A：Feature 組織方式（年份 × 目錄 × `_index.md`）

### 現況對照

| 維度 | Dflow（當前） | V2 |
|---|---|---|
| 分類軸 | 狀態（`active/` / `completed/` / `backlog/`）| 時間（年份目錄）|
| 單位 | 單一檔案（`{SPEC-ID}-{name}.md`） | 目錄（`{feature名稱}/`） |
| 多階段支援 | 無（若需多階段→拆多個 spec）| 有（目錄內多份「階段規格 YYYY-MM-DD-主題.md」）|
| 進度儀表板 | 無 feature 級；靠 `/dflow:status` + `behavior.md` 聚合系統級 | `_index.md` feature 級儀表板 |
| 接續入口 | 讀 `active/{SPEC-ID}.md` + `/dflow:status` | 讀 `_index.md`（單一入口點）|

### V2 決策紀錄依據

- A2（年份分類）：明確取 V1，理由「專案規模不需要 SPEC-ID 系統」
- B1（`_index.md`）：明確取 V1，理由「集中管理多個階段規格的進度，比新版每個 spec 自己追蹤更適合多階段開發」
- B2（日期命名）：明確取 V1，理由「日期天然帶時序資訊」

### R1–R6 是否已解決？

- R5/P007a 的 `behavior.md` 解決了**系統級** current-state 聚合
- 但 V2 的 `_index.md` 是**feature 級** dashboard（多階段規格進度 + 接續入口），層級不同
- **未被 R1–R6 覆蓋**

### 潛在衝擊

- 「目錄 per feature」取代「單檔 per feature」是結構性變動，影響：
  - `new-feature-flow.md` Step 6（建立 spec）
  - `modify-existing-flow.md`（目前假設單一 spec 檔可編輯）
  - `git-flow-integration.md`（SPEC-ID ↔ branch 對應改為 feature-name ↔ branch）
  - `/dflow:status`、`/dflow:verify` 的路徑假設
- 可行的折衷：讓 Dflow 支援兩種組織方式，專案 CLAUDE.md 宣告採用哪種

### 待你背書

- Obts 實戰上，`_index.md` 是否真的有在用？開新對話時是否從 `_index.md` 切入？
- 多階段規格（同一 feature 跨多次 commit / 多份階段規格）是否是常見情境，還是罕見？
- Dflow 的 `active/` / `completed/` 狀態流動機制，Obts 經驗上有感還是無感？

### Obts 實戰背書（2026-04-21 討論結論）

- ✓ **`_index.md` 有在用**：feature 達到 8 個 commit / 2 週規模時，`_index.md` 是展現「feature 全貌 + 每個 commit 做什麼」的唯一位置。若 feature 只有 1 個 commit 很快結束，`_index.md` 用處不大。
- ✓ **多階段規格是常見情境**：OpenSpec 式「一次完整流程 → 1-N commit」邏輯正確，但 commit 數多時單靠階段規格會失去全貌。且有些修改不需階段規格，直接在 `_index.md` 記錄即可。
- ✗ **年份分類**：選 `active/completed/backlog` 狀態分類（拒絕 V2 年份分類）

**結論：採「混血方案」** — V2 的 feature 目錄化 + 多階段規格 + `_index.md`，疊加在 Dflow 現有的狀態分類之上。

### 混血方案決議（10 項，為 PROPOSAL-009 骨幹）

**結構類**

1. Feature 單位由「單檔」改為「目錄」：`active/{SPEC-ID}-{slug}/`
2. 保留 `active/` / `completed/` / `backlog/` 狀態分類（拒絕 V2 年份分類）
3. 保留 SPEC-ID 系統（`SPEC-YYYYMMDD-NNN`）；feature 目錄名 = `{SPEC-ID}-{slug}`
4. 每個 feature 目錄含：
   - `_index.md`：feature 級 dashboard + 輕量修改紀錄 + 接續入口
   - 0-N 份 phase spec：`phase-spec-YYYY-MM-DD-{slug}.md`
5. 一份 phase spec ≈ 一次完整「Kickoff → Domain → Design → Build → Verify」循環的產出
6. Feature 完成後，整個目錄從 `active/` 搬到 `completed/`

**命名類**

7. 結構詞保持英文：`phase-spec`、`_index`、`SPEC-`
8. Slug 跟隨使用者與 AI 的討論語言（中文討論→中文 slug；英文→英文；不強迫翻譯）

**命令類**

9. 新增 `/dflow:new-phase`：在 active feature branch 上啟動新階段（建新 phase spec + 更新 `_index.md`，**不**建 branch/目錄）
10. 新增 `/dflow:finish-feature`：feature 收尾（驗證所有 phase spec ✅、更新 `_index.md`、搬目錄到 `completed/`、產出 merge commit 訊息建議；**不**自動 merge）

**流程類（附帶產出）**

- `new-feature-flow.md` 增加 **Step 3.5 Slug Confirmation**：AI 提案 SPEC-ID + slug + 目錄名 + 分支名，使用者確認後才建目錄
- `/dflow:new-phase` 進入時同樣要求階段 slug 確認
- `/dflow:finish-feature` 修補 Dflow 現況缺口（目前收尾是流程內隱式步驟，無命令觸發）

### 命令全景圖（混血方案後）

```
啟動類（建 branch + 目錄 + _index.md + 第一份 phase spec）:
  /dflow:new-feature        新功能
  /dflow:modify-existing    修改既有（≈ 啟動工作 + 首次 new-phase）
  /dflow:bug-fix            bug 修復（輕量 ceremony）

階段類（同 feature 下，建新 phase spec）:
  /dflow:new-phase          ← 新增

收尾類:
  /dflow:finish-feature     ← 新增

輔助類（不變）:
  /dflow:pr-review  /dflow:verify  /dflow:status  /dflow:next  /dflow:cancel
```

### 影響範圍（PROPOSAL-009 實施時需修改）

- `SKILL.md`：Primary triggers、架構規範節、指令決策樹（雙版）
- `references/new-feature-flow.md`：Step 3.5 新增、Step 4/6 改為目錄建立
- `references/modify-existing-flow.md`：判斷樹重寫（單檔 → 目錄；輕量修改 vs 新 phase）
- `references/new-phase-flow.md`：**新檔**（可從 new-feature-flow 衍生，去除 branch/目錄建立步驟）
- `references/git-flow-integration.md`：Branch ↔ feature 目錄對應、完成檢查清單
- `references/drift-verification.md`：路徑假設從單檔改為目錄
- `references/pr-review-checklist.md`：多份 phase spec 的檢查邏輯
- `templates/`：新增 `_index.md` 範本；`feature-spec.md` 演化為 phase-spec 範本；保留 `lightweight-spec.md`（或併入 `_index.md` 的輕量段落）
- `templates/CLAUDE.md`：專案指引範本更新
- 英文版 + 繁中版同步

---

## 候選 B：專案級 scaffolding 檔案群

### V2 提供、Dflow 沒有的檔

| V2 檔 | 內容要點 | Dflow 目前對應 |
|---|---|---|
| `docs/specs/_overview.md` | 系統現況、技術架構表、現有問題、遷移策略、原則（Migration Awareness / Domain Extraction / 雙軌並行 / 務實優先）| 無單一檔；部分散在 `templates/CLAUDE.md` 背景段 |
| `docs/specs/_conventions.md` | 規格撰寫慣例（完整規格 / 輕量規格範本）、Ceremony Scaling 對照表、檔案命名規則 | 散佈於 SKILL.md + `modify-existing-flow.md` 頂部註 + templates/*.md |
| `docs/specs/_共用/Git-原則.md` | GitFlow 規範、commit 格式、Gate Checks、AI 協作規則、Tag & Release | `references/git-flow-integration.md` 覆蓋**部分**（AI-facing 為主），V2 版更完整（human-facing、含命令範例、禁止操作清單）|
| `docs/specs/SDD-工作流程說明.md` | 五階段流程、Ceremony Scaling、目錄結構、工作步驟、接續工作指引、Feature 類型參考 | 無單一檔；分散在 SKILL.md + new-feature-flow.md |

### V2 定位差異

V2 的 scaffolding 檔**同時是範本（供專案複製使用）和說明（供團隊成員閱讀）**。每個檔都有 `{日期}` / `{系統名稱}` / `{framework}` 等 placeholder，帶最小起始內容，專案照抄後可逐步填實。

Dflow 的 `templates/` 只有 spec 類範本（feature-spec、lightweight-spec、context-definition、behavior、aggregate-design、CLAUDE.md），沒有「專案級入口檔」範本。

### R1–R6 是否已解決？

- R7/P007c（CLAUDE.md segmentation）擴充了 CLAUDE.md 範本內容
- 但「專案擁有者要先在 `docs/specs/` 根目錄準備哪些入口文件」沒被 R1–R6 觸及
- **未被覆蓋**

### 候選可行性

- 可作為 Dflow 的新目錄：`sdd-ddd-webforms-skill/scaffolding/`（與 `references/` / `templates/` 並列）
- 或延伸 `templates/`：`templates/project-scaffolding/{_overview.md, _conventions.md, Git-原則.md, SDD-工作流程說明.md}`
- 需決定：是否維持雙版本（WebForms / Core）差異化，還是共用？

### 待你背書

- Obts 上實際有用到 `_overview.md` 嗎？它是否幫助新成員快速理解系統現況？
- `_conventions.md` 是否讓「該用完整規格還是輕量規格」這個判斷變容易？
- `Git-原則.md` 對非 AI 的團隊成員有沒有實際幫助？

---

## 候選 C：Onboarding 文件組（散佈用）

### V2 在 repo 根目錄提供

- `README.md` — V2 是什麼、套件內容、快速開始提示詞、了解更多
- `SDD-AI協作開發模式介紹.md`（9.2KB）— 完整概念 + 實際案例
- `使用說明.md`（4.0KB）— 導入步驟、手動導入、導入後下一步
- `CLAUDE.md-範例片段.md`（2.5KB）— 要加入專案 CLAUDE.md 的段落

### Dflow 現況

- repo 根目錄有 `README.md`，但內容偏「這個 repo 是什麼」，不是「如何採用這套 Skill」
- 沒有等同 `SDD-AI協作開發模式介紹.md` / `使用說明.md` / `CLAUDE.md-範例片段.md` 的文件
- 目前 Dflow CLAUDE.md 明確說「本 Repo 維護一套 AI 引導的 SDD/DDD 工作流程規範」——已經是可被引用的 Skill pattern，但缺乏讓外部採用者入門的引導

### 候選可行性

- 可獨立存在，不影響 Skill 本體
- 但需先決定定位：
  - **選項 1**：Dflow 留在團隊內用，不做散佈 → 候選 C 不採納
  - **選項 2**：Dflow 走向社群散佈 → 候選 C 為必要（甚至該增加 license、貢獻指引等）
  - **選項 3**：保留彈性，先把 onboarding 文件準備好，但不特別行銷 → 候選 C 部分採納

### 待你背書 / 決策

- Dflow 的定位意圖：團隊內用 / 內部 Starter Kit / 社群散佈？
- V2 已經是散佈版（`E:\工作區\SDD-Starter-Kit`），是否規劃兩者分工（V2 繼續是散佈版、Dflow 作為開發實驗場）？

---

## 候選 D：單一「給人讀的」工作流程說明檔

### V2 的 `SDD-工作流程說明.md`

單一檔集合：五階段概念 + Ceremony Scaling + 目錄結構 + 工作流程步驟（7 步）+ 修改既有功能 + Bug 修復 + **如何接續工作** + Feature 類型參考 + 參考資源。

### Dflow 現況

等同內容分散於：
- `SKILL.md`（AI 讀的核心引導）
- `references/new-feature-flow.md`（新功能詳細流程）
- `references/modify-existing-flow.md`（修改既有詳細流程）
- `templates/CLAUDE.md`（專案級指引範本）

### 本質分析

Dflow 是 AI-facing 設計，每份檔都精確、彼此交叉引用。這對 Claude Code 運作很有效，但**對人不友善**——
- 人讀時要在四份檔間跳躍
- 沒有一份可以「一次讀完就大致理解整個流程」的文件
- 新成員 onboarding 時缺乏單一入口

V2 的 `SDD-工作流程說明.md` 等於「人版 SKILL.md」——同等資訊，但線性敘事、範例豐富、少跳躍。

### 候選可行性

- 不取代 SKILL.md（AI 仍讀 SKILL.md）
- 以 `templates/project-scaffolding/SDD-工作流程說明.md` 或類似位置提供範本
- 或在本 repo 新增 `docs/SDD-工作流程說明-人讀版.md` 作為專案層級的理解入口
- 與候選 B 可合併處理

### 待你背書

- Obts 實戰上團隊成員是否讀過 V2 的 `SDD-工作流程說明.md`？有沒有發揮過「新人入門」效果？
- 如果 Dflow 引入這份，跟 SKILL.md 的分工邊界如何定？（一個給 AI 讀、一個給人讀？）

---

## 候選 E：Hotfix 24 小時補規格時限

### V2 `Git-原則.md` 2.3 節末段

> Hotfix 規格要求：修復後 24 小時內補建輕量規格，記錄問題原因與修復方式。如果 hotfix 揭露了系統性問題，記到 `migration/tech-debt.md`。

### Dflow 現況

`git-flow-integration.md:92-102` 已有：
- 「Don't block the developer」
- 「Create a retroactive spec after the fix is merged」
- 「Record in tech-debt.md if this hotfix reveals a systemic issue」

**差異**：Dflow 沒有「24 小時內」這個具體時限。

### 本質分析

AI 無法追蹤時間，24 小時是**給人的承諾時限**，不是給 AI 的行為指引。放在 AI-facing 的 skill 效果有限；放在人讀的 `Git-原則.md`（候選 B）或 CLAUDE.md 更合適。

### 待你背書

- Obts 實戰上「24 小時內補規格」這條有沒有真的被遵守？還是變成空文？
- 若候選 B 採納 `Git-原則.md`，此條是否納入？

---

## 候選 F：AI 協作規則三級分類表

### V2 `Git-原則.md` 第六節

| 操作層級 | 內容 |
|---|---|
| **必須先詢問** | commit / push / merge |
| **禁止** | force push 到 master/develop、hard reset、修改已推送 commit、刪主分支 |
| **允許（不需詢問）** | status / diff / log / fetch / stash |

### Dflow 現況

`git-flow-integration.md` 的「Gate Checks by Branch Type」偏向**檢查清單**（「AI should verify...」），沒有明確的「AI 操作權限分級」。

Claude Code 本身的 CLAUDE.md 預設 git safety protocol 有類似規定，但並非 Dflow Skill 內的規則。

### 本質分析

V2 的分類是**專案級規則**（由專案決定 AI 能做什麼），獨立於 Claude Code 預設安全規則。這讓 Dflow 的 Skill 可以表達「在 SDD 脈絡下 AI 應該如何協作」的具體態度。

### 候選可行性

- 可加進 `references/git-flow-integration.md` 的新一節
- 或併入候選 B 的 `Git-原則.md`

### 待你背書

- Obts 上這個三級表是否有實際效果？有沒有因此避免過失誤？
- Claude Code 預設 git safety 是否已足夠？這層規則是否真的必要？

---

## 候選 G：AI Commit 署名通用化

### V2 決策

`Co-Authored-By: Claude <noreply@anthropic.com>`（不寫 Opus/Sonnet 版本）

### Dflow 現況

Dflow Skill 本身不規定 commit 署名格式。本 repo 的 CLAUDE.md 範本也沒規定。Claude Code 預設格式是 `Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>`（含模型版本）。

### 本質分析

此為 Claude Code 層級設定，不屬於 Dflow Skill 範疇。V2 把它寫進 `Git-原則.md` 是因為 V2 的 Git-原則涵蓋所有 Git 相關規則（包含 Claude Code 的預設行為）。

### 待你背書

- 若候選 B 採納 `Git-原則.md`，是否沿用 V2 的簡化格式？
- 或尊重 Claude Code 預設、不干涉？

---

## 候選 H：中文分支命名選項

### V2 決策

`feature/{中文功能名稱}`（C1），理由「與 SDD Feature 目錄名稱一致」

### Dflow 現況

`git-flow-integration.md` 用 `feature/{CONTEXT-ID}-{short-description}`（英文）。

### 本質分析

Dflow 的命名綁定 SPEC-ID 系統。若採用候選 A（改用年份 + 目錄），分支命名自然轉向 `feature/{feature-name}`，中文/英文則成為風格選擇。

### 待你背書

- Obts 的 Git server 支援中文分支沒問題嗎？有沒有工具鏈（CI、PR review bot 等）對中文分支名出過問題？
- 跟候選 A 的決定綁定處理。

---

## 候選 I：Gate Checks 合併前檢查清單

### V2 vs Dflow

兩邊內容幾乎重疊：
- V2 `Git-原則.md` 5.2：`_index.md` 勾選 / glossary 更新 / tech-debt 更新 / Domain 抽離評估
- Dflow `git-flow-integration.md:69-77`：spec completed / business logic in Domain / terms added to glossary / tech-debt recorded / Domain no System.Web / rules/models updated

**結論**：已覆蓋，無候選價值。納入僅需微調措辭。

---

## 候選 J：`_index.md` 作為 resume 入口

### V2 `SDD-工作流程說明.md` 第 178-184 行

> 下次進入 Claude Code 時，告訴 AI：「請讀取 `{專案路徑}/docs/specs/{年份}/{feature名稱}/_index.md`，我們要接續之前的工作。」AI 會讀取該文件，了解目前的進度和待辦事項。

### Dflow 現況

Dflow 的 resume 機制是 `/dflow:status`（Response Format 範例 + Phase Gate 判斷當前階段）。沒有 feature-level 的 single-file resume entry。

### 綁定候選 A

若採納候選 A（feature 目錄化 + `_index.md`），此機制自然伴隨。若未採納 A，J 無法獨立存在。

### Obts 實戰背書（2026-04-21）

✓ 隨 A 納入。`_index.md` 同時扮演 feature dashboard（見 A 背書第 1 點）與 resume 入口。`/dflow:status`（系統級聚合）與 `_index.md`（feature 級聚合）為兩個不同層級，不衝突：`/dflow:status` 在混血方案下可讀各 active feature 目錄的 `_index.md` 摘要後彙整。

---

## Phase 2 你的背書方式

請對每個候選填入 Obts 實戰反饋：

- ✓ = 有用、建議納入
- ✗ = 無感 / 有摩擦，建議不納入
- ? = 需進一步討論
- N/A = 與我的實戰經驗無關（例如候選 C 散佈性產物）

可以直接編輯本檔的「Obts 實戰背書（待填）」欄，或口頭告訴我你對每項的判斷。

---

## Phase 3 決策依據（待 Phase 2 完成後判斷）

依「✓ 納入」的候選數量：

| 數量 | 路徑 |
|---|---|
| 0-1 | 直接 hotfix 一輪、寫 CHANGELOG、完成 Dflow 定版 |
| 2-5 | R7「實戰校準輪」——照 R1-R6 流程跑 Review → Approve → Implement |
| 6+ | 可能是升級非校準，考慮：先 R7 處理校準類，結構擴充類（A / B）獨立 proposal（如 PROPOSAL-009）|

**特別提醒**：候選 A 和 B 是結構擴充性質，若採納會實質改變 Dflow 形態（從「AI workflow skill」擴充為「AI workflow skill + project scaffolding」）。這條邊界是否越過，建議在 Phase 3 時優先判斷。

---

## 附錄：已排除項目

比對過程中確認以下 V2 設計**已被 R1–R6 覆蓋或不構成候選**，為可追溯性記錄於此：

- V2 決策 A1（`docs/specs/` vs 頂層 `specs/`）— Dflow 用 `specs/`，V2 用 `docs/specs/`。專案路徑選擇差異，非 Skill 內容。
- V2 決策 A3-A6（`domain/` + `migration/` 目錄）— Dflow 已有同樣的目錄概念（透過 SKILL.md 引用 + CLAUDE.md template）。
- V2 決策 C2（類型前綴 commit）— 業界慣例，非 Dflow 範疇。
- V2 決策 C3（Commit 前置條件）— Dflow `git-flow-integration.md` Gate Checks 已覆蓋。
- V2 決策 C4-C5（Gate Checks 調整）— 同上。
- V2 決策 D1-D5（五階段流程）— Dflow 結構相近（Phase Gate + 多步 flow），名稱不同但本質對齊。
- V2 決策 E1-E2（glossary / tech-debt）— Dflow 已廣泛引用。
- V2 決策 E3（Domain 層漸進式）— Dflow 的 WebForms 版核心設計即是此模式。
- V2 決策 E5（雙觀點記錄）— Dflow 的 backlog/ + tech-debt.md 機制等效。
