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
| B | 專案級 scaffolding 檔案群（`_overview.md` / `_conventions.md` / `Git-原則.md` / `SDD-工作流程說明.md`）| 結構擴充 | 未覆蓋 | **高** | ✓ **重新定位為 `dflow init` scaffolding**（併入候選 K，2026-04-22）|
| C | Onboarding 文件組（README / 使用說明 / AI 協作介紹 / CLAUDE.md 範例片段）| 散佈性產物 | 未覆蓋 | 中（取決於散佈意圖）| ✓ 社群散佈定位（2026-04-21）|
| D | 單一「給人讀的」工作流程說明檔（集中 vs 目前分散於 SKILL.md + references）| 可讀性 | 未覆蓋 | 中-高 | ✗ 不入 skill；移至 Tutorial 重建（C2）（2026-04-22）|
| E | Hotfix 24 小時補規格時限 + 系統性問題記 tech-debt | 校準 | 部分覆蓋 | 低 | ✗ 不納入（Git Flow 解耦後 hotfix 非 Dflow 職責；實戰亦未遵守 24h，2026-04-22）|
| F | AI 協作規則三級分類表（允許 / 禁止 / 須先問）| 校準 | 未覆蓋 | 中 | ✗ 不納入 skill；✓ 併入 K scaffolding 選用範本（2026-04-22）|
| G | AI Commit 署名通用化（`Co-Authored-By: Claude`，不綁模型版本）| 校準 | 屬 Claude Code 範疇 | 低 | ✗ 不納入 Dflow；K scaffolding 附建議不強制（2026-04-22）|
| H | 中文分支命名選項（`feature/{中文名稱}` vs `feature/{SPEC-ID}`）| 選項擴充 | 未覆蓋 | 低 | ✓ 已由 A+J 決議 5+8 處理；實戰無編碼問題（2026-04-22）|
| I | Gate Checks 合併前檢查清單 | 校準 | 已覆蓋 | 低 | ✓ 已覆蓋，PROPOSAL-009 實施時順手微調措辭（2026-04-22）|
| J | `_index.md` 作為 resume 入口機制 | 結構擴充（綁 A） | 未覆蓋 | 高（綁 A）| ✓ 隨 A 納入（2026-04-21）|
| K | `dflow init` 機制（含 scaffolding 範本集）| 機制擴充（新） | 未覆蓋 | **高** | ✓（2026-04-22 新增，源自 B 重新定位 + OpenSpec init 啟發）|
| L | Git Flow Decoupling（`git-flow-integration.md` 重構、`/dflow:bug-fix` 語意澄清）| 架構解耦 | 未覆蓋 | **高** | ✓（2026-04-22 新增，源自社群中立要求）|

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

**Git 整合類（2026-04-22 補）**

11. **所有目錄 / 檔案移動必須用 `git mv`**：
    - `/dflow:finish-feature` 搬 `active/{feature}/` → `completed/{feature}/` 必須用 `git mv`
    - Phase spec 重命名、feature slug 確認後微調，均用 `git mv`
    - 在 `references/git-flow-integration.md` 增加「Directory Moves Must Use git mv」原則段
    - **理由**：Dflow 與 Git 高耦合（feature branch ↔ feature 目錄綁定），若用 `mv` 會破壞 git 歷史追蹤 —— 這是 OpenSpec 的已知缺點，Dflow 必須避開

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

### Obts 實戰背書（2026-04-22 討論結論）

**重新定位**：這 4 份檔**不直接納入 skill 本體**，而是作為 **`dflow init` 可選產生的 scaffolding 範本**（併入候選 K）。

**逐項處置**：

| V2 檔 | 處置 | 理由 |
|---|---|---|
| `_overview.md` | → K scaffolding 範本（選擇性） | DDD 有 Context Map 概念，這份是雛形；但不是 Dflow 的職責，作為專案可選起始檔 |
| `_conventions.md` | → K scaffolding 範本；PROPOSAL-009 實施時做「Dflow 現有範本 vs V2 範本」差異比較 | Ceremony Scaling 已在 Dflow SKILL.md:195 有；集中化與補強另案 |
| `Git-原則.md` | → K scaffolding 範本（選擇性） | **不是 Dflow 該決定的事**，但提供範本讓使用者起步 |
| `SDD-工作流程說明.md` | → **不進 scaffolding**，改由 Tutorial（Closeout C2）承接 | 是人讀文件，不是 AI workflow，也不是專案資產 |

**關鍵判斷**：Dflow 的職責 = AI-guided SDD/DDD workflow。專案治理文件（Git 規範、系統概觀）不該內建於 skill，但可以透過 `dflow init` 提供範本起步。這讓 skill 保持精簡，同時降低社群使用者的入門門檻。

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

### Obts 實戰背書（2026-04-22 討論結論）

**不納入 skill，改由 Tutorial 重建（Closeout C2）承接**。

**理由**：
- V2 `SDD-工作流程說明.md` 是**人讀文件**，不是 AI workflow，不屬於 skill 的職責
- Skill 裡的 `SKILL.md` + `references/` 已是 AI-facing 完整設計，再加一份人讀版會造成雙重維護
- 人讀版的定位更接近 **Tutorial**（教學／入門），而不是 skill（執行規範）

**處置**：
- Closeout C2「Tutorial 重建」時，把「單一線性敘事版的 SDD 流程」納入 Tutorial 規劃
- Tutorial 完成後，可從 repo README 導引人讀 Tutorial、導引 AI 讀 SKILL.md

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

### Obts 實戰背書（2026-04-22）

- **實戰反饋**：24 小時時限**沒有遵守**。這印證了「AI 無法追蹤時間」的規則本質上難落實。
- **架構上**：Git Flow 已被 Dflow 解耦（見候選 L），hotfix 是 Git Flow 專屬概念，Dflow 不該知道。
- **決議**：✗ **完全不納入 Dflow**（連 K scaffolding 也不必強行納入）。K scaffolding 若提供 `Git-原則.md` 範本（Git Flow 版本），範本內可保留此條，但不是 Dflow 推薦。

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

### Obts 實戰背書（2026-04-22）

- **Claude Code 預設 git safety 已覆蓋 70%**：force push、reset --hard、branch -D 需明確授權、不 skip hooks、不 amend、不自動 commit、不改 git config
- V2 第六節多出的細節屬「專案級補強」（如 `git merge` 明確列入必問），不是 Dflow skill 的職責
- **決議**：✗ 不納入 Dflow skill；✓ 併入 K scaffolding 的 `Git-原則.md` 範本（選擇性，專案自選）

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

### Obts 實戰背書（2026-04-22）

- Commit 署名格式屬 Claude Code 層級設定，不是 Dflow skill 的職責
- **決議**：✗ 不納入 Dflow（不干涉 Claude Code 預設行為）；✓ K scaffolding 的 `Git-原則.md` 範本可**附建議格式**（例如 `Co-Authored-By: Claude <noreply@anthropic.com>`），但**不強制**

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

### Obts 實戰背書（2026-04-22）

- **實戰反饋**：中文分支命名**未遇到編碼或工具鏈問題**
- **決議**：✓ **已由 A+J 決議 5+8 綜合處理** —— Branch 格式為 `feature/{SPEC-ID}-{slug}`，slug 跟隨使用者與 AI 討論語言（中文討論→中文 slug）。中文使用者的實際分支會是 `feature/SPEC-20260421-001-報表調整`，H 獨立採納已無必要。

---

## 候選 I：Gate Checks 合併前檢查清單

### V2 vs Dflow

兩邊內容幾乎重疊：
- V2 `Git-原則.md` 5.2：`_index.md` 勾選 / glossary 更新 / tech-debt 更新 / Domain 抽離評估
- Dflow `git-flow-integration.md:69-77`：spec completed / business logic in Domain / terms added to glossary / tech-debt recorded / Domain no System.Web / rules/models updated

**結論**：已覆蓋，無候選價值。納入僅需微調措辭。

### Obts 實戰背書（2026-04-22）

- **決議**：✓ 已覆蓋，不需額外動作。PROPOSAL-009 實施時若接觸到相關段落，順手微調措辭即可。

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

## 候選 K：`dflow init` 機制（含 scaffolding 範本集）

**2026-04-22 新增**，源自 B 的重新定位與 OpenSpec `openspec init` 啟發。

### 源起

- Dflow 目前沒有 init / scaffold / install 機制，使用者要手動建 `docs/specs/` 結構
- OpenSpec 有 `openspec init`，降低新專案採納門檻
- 候選 B 的 4 份 scaffolding 檔若直接內建於 skill，職責混雜；若透過 `dflow init` 選擇性產生，職責清楚

### 提議設計

**新增命令 `/dflow:init-project`**（或類似）：

1. 使用者在新（或既有）專案執行命令
2. AI 問幾個問題：
   - 專案類型：Greenfield / Brownfield
   - 是否有遷移計畫（WebForms → Core 之類）
   - 語言：中文 / 英文（影響範本文案）
   - 可選起始檔：`_overview.md`? `Git-原則.md`? `_conventions.md`?
3. AI 用 Write 工具建立 `docs/specs/` 目錄結構 + 選定的 scaffolding 檔 + `CLAUDE.md` 範例片段
4. 產出後提示使用者：「下一步請執行 `/dflow:new-feature` 開始第一個 feature」

### 內容組成

| 項目 | 來源 | 必產 vs 選擇性 |
|---|---|---|
| `docs/specs/active/` / `completed/` / `backlog/` 目錄 | Dflow 自身結構 | 必產 |
| `domain/glossary.md` 空範本 | Dflow 既有概念 | 必產 |
| `migration/tech-debt.md` 空範本 | Dflow 既有概念 | 必產（或依專案類型選擇性）|
| `behavior.md` 空範本 | R5/P007a | 必產 |
| `_overview.md` | V2 | 選擇性 |
| `_conventions.md` | V2 | 選擇性 |
| `Git-原則.md` | V2 | 選擇性（專案有自己的 Git 規範時不產）|
| `CLAUDE.md` 範例片段 | V2 + Dflow `templates/CLAUDE.md` | 必產（若專案沒 CLAUDE.md）|

### 獨立於 PROPOSAL-009 的理由

- A+J（PROPOSAL-009）是**既有 Dflow 使用者**也會受影響的結構變動
- K（PROPOSAL-010）是**新 Dflow 使用者**的導入機制
- 兩個提案可並行設計但獨立審議、獨立實施

### Obts 實戰背書（2026-04-22）

✓ **採納**。使用者明確表達「這不是 DFlow 該決定的事情（指 Git 規範），或者維持獨立檔案，但是我們目標是社群散佈，我想這可以設計為 npm install 時候建立或者多一個 DFlow init 建立一些選擇性前置」—— 直接點出 `dflow init` 的需求與定位。

**採 Claude Code 命令形式（`/dflow:init-project`）而非 npm CLI**，理由：
- Dflow 是 Claude Code Skill，使用者已在 Claude Code 環境內
- 用命令形式可直接用 Write 工具產生檔案，不需額外執行環境
- 與 Dflow 其他命令（`new-feature`、`new-phase`、`finish-feature` 等）設計風格一致

### 影響範圍（PROPOSAL-010 實施時需修改 / 新增）

- **新增** `references/init-project-flow.md`：init 流程指引（類似 new-feature-flow.md）
- **新增** `scaffolding/` 目錄：存放各 scaffolding 範本檔
- `SKILL.md`：Primary triggers 加入 `/dflow:init-project`（雙版）
- `README.md`：更新入門指引，建議新使用者從 `/dflow:init-project` 開始
- 英文版 + 繁中版同步

---

## 候選 L：Git Flow Decoupling

**2026-04-22 新增**，源自社群散佈定位下「Git Flow 非普世」的觀察。

### 源起

使用者在候選審查過程中指出：

> 「我想到 Dflow 與 SDD-Starter-Kit V2 我在設計之初都是高耦合 Git Flow，但是如果要社群散佈，git 可以高耦合但是 git flow 不可以，不過要開 feature branch 這件事，可以是在 Dflow 必須的我覺得這沒問題。也就是說有沒有用 git flow，Dflow 並不關切，只差一 feature branch 是哪一個 branch 開出去的，是 develop 還是 master/main。」

### 雙層區分

| 層次 | Dflow 的立場 | 理由 |
|---|---|---|
| **Git**（版控本身）| ✓ 高耦合 | `git mv`、commit、branch、history 是普世版控概念 |
| **Git Flow**（develop / release / hotfix 工作流）| ✗ 解耦 | Git Flow 只是一種分支策略；還有 trunk-based、GitHub Flow 等，Dflow 不應預設 |
| **Feature branch per feature** | ✓ 維持為 Dflow 強制 | SDD 需要 branch ↔ feature 目錄 ↔ slug 對應 |
| **Feature branch 從哪開、怎麼合** | ✗ Dflow 不決定 | 是專案決策（develop vs main/master、merge vs squash vs rebase）|

### Dflow 現況問題

- `references/git-flow-integration.md` 檔名即標示 Git Flow 耦合
- 內容含 develop / release / hotfix 分支結構圖與流程步驟（GitFlow 專屬）
- `/dflow:bug-fix` 命令過往可能被誤讀為「Git Flow hotfix 流程」

### 提議重構（PROPOSAL-011 內容）

**檔名調整**：

| 舊 | 新 |
|---|---|
| `references/git-flow-integration.md` | `references/git-integration.md` |

**保留內容**（SDD ↔ Git 的直接連結）：

- Feature branch per feature（強制）
- `git mv` 規範（來自 A+J 決議 11）
- SDD 產物與 git commit 的時機指引
- Branch naming（`feature/{SPEC-ID}-{slug}`，slug 語言跟隨討論）

**移除內容**（Git Flow 專屬）：

- develop / release / hotfix 分支結構圖
- release 流程步驟
- hotfix 流程步驟
- 「AI should verify all specs in active/ before release branch」等 release-specific 檢查

**命令語意澄清**：

- `/dflow:bug-fix` **重新定位為「輕量 ceremony 的修改」**，不綁任何分支策略
- 文件用語從「hotfix」改為「bug fix / 輕量修改」，避免誤導

### 影響範圍（PROPOSAL-011 實施時需修改）

- **重命名 + 重構** `references/git-flow-integration.md` → `references/git-integration.md`（**必須用 `git mv`** —— 這條原則本身的首次實踐）
- `SKILL.md`：更新 references 連結、`/dflow:bug-fix` 描述（雙版）
- `references/modify-existing-flow.md`：`/dflow:bug-fix` 的 ceremony 調整段落更新措辭（去 hotfix 關聯）
- `references/pr-review-checklist.md`：移除 release branch 專屬檢查
- `templates/CLAUDE.md`：更新 Git 相關段落（若有提及 Git Flow）
- 英文版 + 繁中版同步

### 與 PROPOSAL-009 / -010 的依賴關係

```
PROPOSAL-011（Git Flow 解耦、git-integration.md 重構）
  ↓（009 要在穩定的 git-integration.md 上加 git mv 規範段）
PROPOSAL-009（Feature 目錄化、多階段、新命令）
  ↓（010 的 scaffolding 要知道 Dflow 核心包含什麼）
PROPOSAL-010（dflow init、scaffolding）
```

建議實施順序：011 → 009 → 010。

### Obts 實戰背書（2026-04-22）

✓ **採納**。使用者明確拍板：
- Git Flow 解耦方向 OK
- 新增 PROPOSAL-011 OK
- `/dflow:bug-fix` 重新定位為輕量 ceremony 修改 OK
- `git-flow-integration.md` → `git-integration.md` OK

---

## Phase 2 你的背書方式

請對每個候選填入 Obts 實戰反饋：

- ✓ = 有用、建議納入
- ✗ = 無感 / 有摩擦，建議不納入
- ? = 需進一步討論
- N/A = 與我的實戰經驗無關（例如候選 C 散佈性產物）

可以直接編輯本檔的「Obts 實戰背書（待填）」欄，或口頭告訴我你對每項的判斷。

---

## Phase 3 實施規劃（2026-04-22 定案）

### 統計

Phase 2 審查結果：**✓ 納入類候選 = 5**（A / C / J / K / L），皆為結構性／機制性變動，不是單純校準。

依原先「納入數量 → 路徑」判準對應到 **6+ 類**，但本次採納的候選有明確邏輯分群，可整併為 **3 份獨立 proposal**，不是 6 份分散的校準。

### 三份 Proposal 規劃

| 編號 | 主題 | 源候選 | 主要產出 | 規模估計 |
|---|---|---|---|---|
| **PROPOSAL-011** | Git Flow Decoupling | L + E/F/G 的 Git Flow 衍生條目 | `git-flow-integration.md` → `git-integration.md` 重命名 + 內容剝離；`/dflow:bug-fix` 語意澄清 | Small |
| **PROPOSAL-009** | Feature 目錄化 + 多階段規格 + 新命令 | A + J（11 項決議）| Feature 目錄化、`_index.md`、phase-spec、`/dflow:new-phase`、`/dflow:finish-feature`、Slug Confirmation、git mv 規範 | Large |
| **PROPOSAL-010** | `dflow init` 機制 + scaffolding | K + B（重新定位）+ C（社群散佈 onboarding）+ F/G 的 scaffolding 用法 | `/dflow:init-project`、`scaffolding/` 目錄、`_overview.md` / `Git-原則.md` / `_conventions.md` / `CLAUDE.md` 範本 | Medium |

**依賴順序**：011 → 009 → 010（見 §L「與 PROPOSAL-009/-010 的依賴關係」）。

### 不入 proposal 的項目

- **D（單一人讀工作流程檔）**：移至 Closeout C2 Tutorial 重建承接
- **E/F/G/I 的 Dflow skill 層面**：已於各候選背書標示不納入

### R7 輪結構

採 R1–R6 既有流程模式，但**範圍擴大到 3 份 proposal**，分階段作業：

```
R7-Review
  ├─ 讀 3 份 draft proposal
  ├─ 交叉檢查一致性
  └─ 輸出統一審查報告（reviews/round-7-report.md），列出每份 proposal 的 Findings

R7-Approve（draft → approved）
  ├─ 決議 3 份 proposal 狀態
  └─ 若有回修，回 Review

R7-Implement（分 3 個 session，每個 session 單一 proposal）
  ├─ Session 1: PROPOSAL-011（small，預估 1-2 小時）
  ├─ Session 2: PROPOSAL-009（large，預估 4-6 小時，可能需切分）
  └─ Session 3: PROPOSAL-010（additive，預估 2-3 小時）

R7-Approve（實作品質審查）
  ├─ 檢查三份實作是否各自忠於 proposal
  ├─ 檢查實作後的系統整體一致性
  └─ 若有問題，回實作

Post-R7
  ├─ Global Sweep（如 R1-R6 結束後做的那次）
  ├─ Closeout C1（繁中同步）
  ├─ Closeout C2（Tutorial 重建）
  ├─ 改名（dflow-greenfield-skill / dflow-brownfield-skill）
  └─ 遷移到新 GitHub repo → Dflow V1 ships
```

### AI 分派建議

考量 Claude Code token 稀缺、Cursor (Opus 4.7 High) 模型相同、Codex 不同模型家族提供獨立視角：

| 階段 | 建議 AI | 理由 |
|---|---|---|
| 撰寫 3 份 draft proposal | **Claude Code**（首選，本 session 有完整 context）或 **Cursor (Opus 4.7 High)**（若 Claude Code token 緊張，Cursor 需餵 `reviews/field-reference-candidates.md` 完整內容 + R1-R6 proposal 作 style 參考）| 模型相同；差別在 context 延續性 |
| R7 Review（統一審查）| **Codex** | 延續 R1-R6 慣例；不同模型家族提供獨立視角 |
| R7 Approve（draft → approved）| **Claude Code**（短時間、低 token 消耗）| 對話為主，決策為主，需使用者判斷 |
| Implement PROPOSAL-011 | **Cursor (Opus 4.7 High)** | Small refactor，明確 scope，Cursor 勝任且省 Claude Code token |
| Implement PROPOSAL-009 | **Claude Code**（主力投入）| 影響 10+ 檔、需 memory + Agent 搜尋、雙版雙語同步複雜 |
| Implement PROPOSAL-010 | **Cursor (Opus 4.7 High)** | 主要為新增（不動既有流程檔），有明確 proposal 當 spec 照做 |
| R7 Approve（實作審查）| **Codex** | 再一次獨立視角，避免「誰寫誰審」盲點 |
| Global Sweep | **Codex + Cursor (Opus 4.7 High) 平行** | 仿前次 R6 後做法：兩個 AI 在不同 branch 跑 sweep，回 main 合併，發揮互補盲點優勢 |
| Closeout C1（繁中同步）| **Cursor (Opus 4.7 High)** | 機械性翻譯；Opus 中文品質與 Claude Code 同；不該佔用 Claude Code |
| Closeout C2（Tutorial 重建）| **Claude Code**（若 token 允許）或 **Cursor + outline** | 需設計判斷（誰看 / 看什麼 / 線性敘事）|
| 改名 + 遷移到新 repo | **Cursor (Opus 4.7 High)** | 大規模機械性 rename，明確 scope |

### Cursor (Opus 4.7 High) vs Claude Code — 補充說明

- **模型本身**：相同（Opus 4.7）
- **harness 差異**：
  - Claude Code 有 Skills / Agents / Memory / MCP
  - Cursor 為 IDE 整合，工具較少
  - Cursor **不原生認識 Claude Code skill 格式**，需手動引導讀檔
- **R7 大部分工作是「改 Dflow skill 檔案本身」**（而非用 Dflow 開發），所以 Cursor 可替代 Claude Code，產出品質不因模型差降低
- **但 Cursor 沒 memory**：每次 session 要手動餵 context（如 `reviews/field-reference-candidates.md` 全文、相關 proposal 範本）

### 下一步動作（新 session 執行）

**執行順序：**

1. **新 session 1（建議 Claude Code）**：撰寫 3 份 draft proposal
   - 載入：`reviews/field-reference-candidates.md`、`proposals/PROPOSAL-000-template.md`、任選 1-2 份 R1-R6 proposal 作 style 參考
   - 產出：`proposals/PROPOSAL-009-*.md`、`proposals/PROPOSAL-010-*.md`、`proposals/PROPOSAL-011-*.md`（draft 狀態）

2. **新 session 2（建議 Codex）**：R7 Review
   - 參考：`reviews/prompts/` 下既有的 review 提示詞格式（如 `round-5-review.md`）
   - 產出：`reviews/round-7-report.md`，含 3 份 proposal 的 Findings 與交叉一致性檢查

3. **Claude Code 本機 session**：R7 Approve draft
   - 依 Review Findings 決定 accept / revise
   - 更新 proposal 狀態 draft → approved（或回修）

4. **新 session 3-5**：R7 Implement（依 AI 分派表分開執行）

5. **新 session 6**：R7 Approve 實作品質（Codex）

6. **後續 Global Sweep / Closeout / 遷移**：見上表

### 建議起始提示詞（複製給新 session 用）

**撰寫 draft proposal（Session 1）提示詞：**

```
我要撰寫 3 份 Dflow proposal（PROPOSAL-009、010、011），請依順序進行。

Context 載入：
1. 讀取 reviews/field-reference-candidates.md（Phase 1-2 候選審查結果，含 A/C/J/K/L 的納入決議與詳細說明）
2. 讀取 proposals/PROPOSAL-000-template.md（格式範本）
3. 參考 proposals/PROPOSAL-007-*.md 與 PROPOSAL-008-*.md 的寫法風格

任務：
- PROPOSAL-011（Git Flow Decoupling）：依候選 L 的描述起草
- PROPOSAL-009（Feature 目錄化 + 多階段）：依候選 A+J 的 11 項決議起草
- PROPOSAL-010（dflow init + scaffolding）：依候選 K + B 重新定位起草

每份 proposal 狀態設為 draft，放在 proposals/ 目錄下。完成後告訴我。
```

**R7 Review（Session 2，Codex）提示詞：**

```
我需要對 3 份 Dflow proposal 做 R7 統一審查。

請讀取：
- proposals/PROPOSAL-009-*.md、010-*.md、011-*.md（3 份 draft）
- reviews/field-reference-candidates.md（背景脈絡）
- reviews/prompts/round-5-review.md 或類似檔（既有 review 提示詞格式參考）
- CHANGELOG.md 與 R1-R6 歷次 review 報告（了解本專案的 review 標準）

任務：
1. 對每份 proposal 逐項檢查（severity: Critical / Major / Minor / Nit）
2. 交叉檢查 3 份 proposal 的一致性（特別注意 011 與 009 對 git-integration.md 的先後假設、009 與 010 對 scaffolding 內容的劃分）
3. 輸出到 reviews/round-7-report.md，格式仿 round-5-report.md

審查時請注意：Dflow 為 WebForms 版與 Core 版雙版本，proposal 影響兩版一致性時需標出。
```

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
