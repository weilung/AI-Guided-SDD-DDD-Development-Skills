# Round 7 Implement Prompt — PROPOSAL-009（給 Claude Code 主力 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：
>   - 修改 / 新增 / 重命名 skill 檔（`sdd-ddd-*-skill/`，英文版 ~20 檔）
>   - 更新 `CHANGELOG.md`
>   - **不改繁中版檔**（延到 Closeout C1）
>   - **不改 proposal 本身**（已 approved）
> **禁止事項**：不補繁中版、不動 PROPOSAL-010 scope（scaffolding / init-project）、不自動 commit、**所有檔案/目錄移動必須用 `git mv`**（本 proposal 決議 11 的原則，本 session 需實踐）。
> **前置依賴**：PROPOSAL-011 **必須已完成實施**（`git-flow-integration.md` 已重命名為 `git-integration.md`、內容已剝離 Git Flow 專屬段）。本 session 會在其上擴增「Directory Moves Must Use git mv」完整段與 slug 語言段。
> **規模 & 時程**：Large；預估 4-6 小時，建議分 3 個 sub-wave 執行（可全在本 session 做完，也可分段）。
> **為何指派 Claude Code**：本 proposal 影響 10+ 檔、需 Agent 輔助 codebase 探索、雙版協調複雜、範本新增含設計判斷；Claude Code 的 Skills / Memory / Agent 工具對此最合。
> **下一步**：本 session 完成後，接 `round-7-implement-010.md`。

---BEGIN PROMPT---

你是 PROPOSAL-009（Feature 目錄化 + 多階段規格 + 新命令）的實施者。本 proposal 已於 R7 Review + Approve 階段完成設計評估與修訂，狀態為 `approved`，內含 11 項原始決議 + 4 項 § H 決議（範本與 Ceremony 終局，F-01 Path A-3）+ 4 項 § I 決議（Completed Feature Reopen 生命週期，F-02 Path C）+ F-03 scope 收斂 + F-04 Path B Integration Summary 中立化。你的工作是把 approved proposal 的內容落實到 skill 檔。

## Step 0：前置檢查

1. 確認 `proposals/PROPOSAL-009-feature-directory-and-phases.md` 狀態為 `approved`
2. 確認 PROPOSAL-011 **已完成實施**：`sdd-ddd-*-skill/references/git-integration.md` 存在（舊名 `git-flow-integration.md` 應已不存在；可 `git log --follow` verify 歷史）
3. 確認 `reviews/round-7-decisions.md` 存在

若前置條件不滿足，停止並回報使用者。**不要**自己跑去實施 PROPOSAL-011。

## Step 1：讀入必要檔案

### A. 權威設計來源（必讀，逐段對照）

1. `proposals/PROPOSAL-009-feature-directory-and-phases.md`（**THE 實施藍圖**；§ A-G 原決議 1-11、§ H 決議 12-15、§ I 決議 16-19、§ 影響範圍表）

### B. R7 context

2. `reviews/round-7-decisions.md`（F-01 Path A-3 / F-02 Path C / F-03 / F-04 Path B 的決議詳情）
3. `reviews/round-7-report.md`（Codex 原始 finding）

### C. 流程規範

4. `proposals/review-workflow.md`（§三 Implementation 紀錄格式）

### D. 現況檔（會實際修改）

依 proposal § 影響範圍表分類：

**Skill 本體 / 決策樹 / 指令清單**
- `sdd-ddd-webforms-skill/SKILL.md`
- `sdd-ddd-core-skill/SKILL.md`

**Flow 文件**
- `sdd-ddd-webforms-skill/references/new-feature-flow.md`
- `sdd-ddd-core-skill/references/new-feature-flow.md`
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
- `sdd-ddd-core-skill/references/modify-existing-flow.md`
- `sdd-ddd-webforms-skill/references/new-phase-flow.md`（**待新增**）
- `sdd-ddd-core-skill/references/new-phase-flow.md`（**待新增**）
- `sdd-ddd-webforms-skill/references/finish-feature-flow.md`（**待新增**）
- `sdd-ddd-core-skill/references/finish-feature-flow.md`（**待新增**）

**Git / Review / Verify**
- `sdd-ddd-webforms-skill/references/git-integration.md`（P011 已改名，本 session 擴增）
- `sdd-ddd-core-skill/references/git-integration.md`（同上）
- `sdd-ddd-webforms-skill/references/pr-review-checklist.md`
- `sdd-ddd-core-skill/references/pr-review-checklist.md`
- `sdd-ddd-webforms-skill/references/drift-verification.md`（輕量改動）
- `sdd-ddd-core-skill/references/drift-verification.md`（同上）

**Templates**
- `sdd-ddd-webforms-skill/templates/_index.md`（**待新增**）
- `sdd-ddd-core-skill/templates/_index.md`（**待新增**）
- `sdd-ddd-webforms-skill/templates/feature-spec.md`（**待 `git mv` 為 phase-spec.md**）
- `sdd-ddd-core-skill/templates/feature-spec.md`（同上）
- `sdd-ddd-webforms-skill/templates/phase-spec.md`（由 feature-spec.md `git mv` 而來 + 新增 Delta 段）
- `sdd-ddd-core-skill/templates/phase-spec.md`（同上）
- `sdd-ddd-webforms-skill/templates/lightweight-spec.md`（內容不變，使用指引調整）
- `sdd-ddd-core-skill/templates/lightweight-spec.md`（同上）
- `sdd-ddd-webforms-skill/templates/CLAUDE.md`
- `sdd-ddd-core-skill/templates/CLAUDE.md`

## Step 2：本輪範圍邊界（嚴格遵守）

**在範圍內**（PROPOSAL-009 § 影響範圍表所列，約 20 檔英文版）：
- 上述 D 段列出的所有檔

**不在範圍內**：
- **繁中版**（`*-tw/`）—— 延到 Closeout C1
- PROPOSAL-010 scope（`scaffolding/` 目錄、`references/init-project-flow.md`、`CLAUDE-md-snippet.md`、README 更新等）
- R1-R6 既有機制的核心結構調整（P003 `rules.md`/`behavior.md` 結構、P005 completion checklist 結構、P007c CLAUDE.md H2 分段、P008 drift-verification 核心對照邏輯等）—— 可**更新引用 / 路徑**，但不改核心結構
- Obts 或其他外部專案的任何改動

若實施過程發現 proposal 沒列但邏輯上牽連的檔，**停下來回報**，不要擴大 scope。

## Step 3：實施策略（建議分 3 個 Sub-wave，可全在本 session 執行）

### Sub-wave 9a：結構 + 命名 + 範本新增（決議 1-8、12-15）

目標：建立「feature 目錄化」的結構基礎 + `_index.md` / phase-spec 範本定稿。

1. **建立 `templates/_index.md` 範本（雙版）**（決議 14）
   - 六段結構：
     1. **Metadata（YAML front matter）**：`spec-id` / `slug` / `status`（in-progress / completed）/ `created` / `branch`；選用欄 `follow-up-of: {原 SPEC-ID}`（決議 18）
     2. **目標與範圍**：散文段（1-3 段）
     3. **Phase Specs**：表格 `| Phase | Date | Slug | Status | 檔案連結 |`
     4. **Current BR Snapshot**（本決議核心新增）：表格 `| BR-ID | 現況規則 | 首次出現（phase）| 最後修訂（phase）| Status（active / removed）|`
        - 範本含「AI 在 `/dflow:new-phase` 進入時、完成 phase-spec 時、T2 lightweight 定稿時 regenerate」註解
        - 註明：是 feature 目錄內 BR 的**當前狀態**，不是歷史；歷史由 phase-spec 的「Delta from prior phases」串接；feature 完成時由 `/dflow:finish-feature` 推進到 BC 層（延續 Step 8.3 / 5.3）
     5. **輕量修改紀錄**（T2 外連 + T3 inline 兩用）：表格 `| Date | Tier | 描述 | commit |`
        - T2 行：描述含「見 `lightweight-{date}-{slug}.md`」外連
        - T3 行：inline 完整描述 + 標籤（如 `[cosmetic]` / `[text]` / `[format]`）
     6. **接續入口（Resume Pointer）**：一句話目前進展 + 下次下一個動作建議
   - 選用段「**Follow-up Tracking**」（決議 19）：若有 follow-up feature 衍生才填入，表格 `| SPEC-ID | slug | Created | Status |`
   - 範本預設極簡（~30-40 行），明確標示「1-commit 快速 feature 也適用」
   - 雙版可共用範本結構（內容相同），Core 版可在註解中提醒與 P003 `rules.md`/`behavior.md` 的同步時機

2. **`templates/feature-spec.md` → `templates/phase-spec.md`（雙版，`git mv`）**（決議 12）
   ```bash
   git mv sdd-ddd-webforms-skill/templates/feature-spec.md \
          sdd-ddd-webforms-skill/templates/phase-spec.md
   git mv sdd-ddd-core-skill/templates/feature-spec.md \
          sdd-ddd-core-skill/templates/phase-spec.md
   ```
   - **不要用普通 `mv`**——破壞 git 歷史
   - 在「業務規則」之後、「邊界情況」之前新增章節「**Delta from prior phases**」：
     - 首 phase 免填（範本註明「若為首 phase，此段可註記『首 phase，無前置 Delta』」）
     - Phase 2+ 必填（格式沿用 P002 / P004 既有 Delta：`ADDED` / `MODIFIED` / `REMOVED` / `RENAMED` / `UNCHANGED`）
     - 僅記本 phase 變化，**不累積歷史**
     - 「業務規則」章節在 phase 2+ 只列本 phase 新增 / 修改到的 BR，不重抄未變動 BR
   - 原檔其他章節沿用（問題描述 / 領域概念 / 行為規格 / 業務規則 / 邊界情況 / Domain Events (Core)/ 實作計畫 / 資料結構變更 / 測試策略 / 實作任務）

3. **`templates/lightweight-spec.md` 使用指引調整（雙版）**（決議 15）
   - 範本內容**不變**（問題 / 行為變更 Delta / 根因 / 修復方式 / 發現的技術債）
   - 在範本頂部或註解處，更新使用指引：
     - 明確對應 T2 ceremony（T1 走 phase-spec、T3 直接寫 `_index.md` inline）
     - 實例化檔案位置：置於對應 feature 目錄內，命名 `lightweight-{YYYY-MM-DD}-{slug}.md` 或 `BUG-{NUMBER}-{slug}.md`
     - 若 feature 目錄尚未存在（獨立 bug），`/dflow:bug-fix` 需先建 feature 目錄（含最小版 `_index.md`），再放 lightweight 實例化檔進去

4. **SKILL.md 結構更新（雙版）**（決議 1-3、7-8）
   - **Project Structure 圖**（Core 版 `SKILL.md:228-251` 附近、WebForms 版對應段）：
     - `specs/features/active/` 的範例改為目錄形式：`active/{SPEC-ID}-{slug}/` 內含 `_index.md`、`phase-spec-YYYY-MM-DD-{slug}.md`、`lightweight-{YYYY-MM-DD}-{slug}.md`（視情境）
     - 補註：SPEC-ID 格式 `SPEC-YYYYMMDD-NNN`；slug 跟隨討論語言（中文 / 英文皆可）
   - **Ceremony Scaling 表** 更新為三層（T1 Heavy / T2 Light / T3 Trivial）（決議 13）：
     - 表格欄位照 proposal § H 決議 13
     - **底線段**：補一行明文說明純 typo / 格式化 commit（低於 T3）可直接 `git commit`，不需走 Dflow
   - **Templates 表** 更新：
     - `templates/feature-spec.md` 改為 `templates/phase-spec.md`
     - 新增 `templates/_index.md` 列
     - `templates/lightweight-spec.md` 描述更新為對應 T2 ceremony

### Sub-wave 9b：新命令 + 對應 flow 文件（決議 9-10、16-19）

目標：定義新命令的 entry point + 完整流程 + completed feature reopen lifecycle。

5. **SKILL.md 命令清單更新（雙版）**（決議 9-10）
   - **Frontmatter Primary triggers**：加入 `/dflow:new-phase`、`/dflow:finish-feature`
   - **決策樹**（`## Decision Tree` 段）：新增節點
     ```
     /dflow:new-phase       → NEW PHASE WORKFLOW (references/new-phase-flow.md)
     /dflow:finish-feature  → FINISH FEATURE WORKFLOW (references/finish-feature-flow.md)
     ```
   - **Slash Commands 表**（`### Slash Commands` 段）：
     - 啟動類：保持 `/dflow:new-feature` / `/dflow:modify-existing` / `/dflow:bug-fix`
     - **新增「階段類」子分類**：`/dflow:new-phase`（建新 phase spec + 更新 `_index.md`，active feature only）
     - **新增「收尾類」子分類**：`/dflow:finish-feature`（驗證所有 phase spec、同步 BC 層、`git mv` 搬目錄、Integration Summary 產出）
     - 控制類 / 獨立類保持不變
   - **Reference Files 表**：新增 `references/new-phase-flow.md`、`references/finish-feature-flow.md` 兩列

6. **新增 `references/new-phase-flow.md`（雙版）**（決議 9）
   - 結構類比 `new-feature-flow.md`（但去除 branch / 目錄建立步驟）
   - 主要步驟：
     - Step 1：讀取 active feature 脈絡（讀 `_index.md`、讀最新 phase-spec）
     - Step 2：確認本 phase 主題與範圍
     - Step 3：階段 slug 確認（類比 new-feature-flow 的 Step 3.5）
     - Step 4：撰寫新 phase-spec（`phase-spec-YYYY-MM-DD-{slug}.md`，Delta 段必填）
     - Step 5：refresh `_index.md` 的 Current BR Snapshot 段 + Phase Specs 表
   - **嚴格只適用於 active feature**（決議 16）：Step 1 若偵測到目標 feature 在 `completed/` 下，**拒絕執行**並提示改用 `/dflow:modify-existing` 走 follow-up 路徑
   - Phase Gate（若有）：Phase 設計 → 撰寫 phase-spec 間可設一個 gate（類比 new-feature-flow 的 Step 4 → Step 5）
   - Core 版在 Step 1 可加「讀相關 BC 的 `rules.md` / `behavior.md` 了解系統層現況」

7. **新增 `references/finish-feature-flow.md`（雙版）**（決議 10）
   - 主要步驟：
     - Step 1：驗證所有 phase-spec 的 status 為 `completed`；檢查 `_index.md` 是否有未結項（若有，停止並提示使用者）
     - Step 2：更新 `_index.md` 的 feature 狀態為 `completed`
     - Step 3：**依 `_index.md` Current BR Snapshot，同步 BR 變化到對應 BC 的 `rules.md` / `behavior.md`**（延續 Step 8.3 / 5.3 既有 sync 機制；不新設流程）
     - Step 4：用 `git mv` 將整個 feature 目錄從 `active/` 搬到 `completed/`
     - Step 5：產出 **Integration Summary**（Git-strategy-neutral）：feature 目標 / 變動範圍 / 關聯 BR-ID / phase 清單
       - 註明：具體 commit 訊息格式依專案 Git-principles 選定的 merge strategy 決定（若採用 PROPOSAL-010 scaffolding，參考 `scaffolding/Git-principles-{gitflow|trunk}.md` 的「Integration Commit 訊息慣例」段）
     - Step 6：若該 feature 是 follow-up（Metadata 有 `follow-up-of` 欄），**反向更新舊 feature 的 `_index.md` Follow-up Tracking 表**（該列 Status 欄改為 `completed`，決議 19）
   - **明確區分「AI 做」vs「使用者做」**（proposal 風險 5 的緩解）：
     - AI 做：`git mv`、`git add`（stage 改動）、產出 Integration Summary（文字）
     - 使用者做：決定 merge strategy、執行 merge / push、發 PR
   - **不自動 merge**：明文說明此原則

8. **`new-feature-flow.md` 修訂（雙版）**（決議 4、6，Step 3.5）
   - **新增 Step 3.5「Slug Confirmation」**：位於 Step 3（Domain Modeling，Core）/ Step 3（Impact Analysis，WebForms）完成後、Step 4（Write the Spec）之前
     - AI 提案 SPEC-ID + slug + 目錄名 + 分支名
     - 使用者確認後才建目錄
     - 範例含中英文 slug 各一份
   - **Step 4（Write the Spec）**：改為「建 feature 目錄 + `_index.md`（含初始 BR Snapshot）+ 第一份 phase-spec（首 phase，Delta 段可空白或註記『首 phase，無前置 Delta』）」
     - 目錄結構：`specs/features/active/{SPEC-ID}-{slug}/`
     - 初始 `_index.md`：Metadata 填入 SPEC-ID / slug / status=in-progress / created / branch；Phase Specs 表含本次首 phase 行；Current BR Snapshot 依首 phase 產出初始化；輕量修改紀錄空表；接續入口填「首 phase 進行中」
   - **Step 6（Git Branch）**：沿用現況（建 branch `feature/{SPEC-ID}-{slug}`），**確認 slug 跟隨 Step 3.5 的定案**
   - **Step 8.4（Archival）**：改為「整個 feature 目錄從 `active/` `git mv` 到 `completed/`」；原「搬單檔」不再適用
   - 其他 Step（1-3、5、7、8.1-8.3）基本沿用；Step 8.3 的 `behavior.md` merge 仍存在，與本 proposal 的 BC 層同步不衝突（實際上是同一件事的兩個層次：`/dflow:finish-feature` Step 3 呼叫既有 Step 8.3 的 sync 機制）
   - **首 phase 預設走 T1 Heavy**，不需 Ceremony 判準（判準只在 `modify-existing-flow` 用；見決議 13）

9. **`modify-existing-flow.md` 修訂（雙版）**（決議 13、17、18）
   - **判斷樹重寫為三層 Ceremony**（決議 13）：
     - AI 依 T1 / T2 / T3 判準分流：
       - T1 → 升 `/dflow:new-phase` 或 `/dflow:new-feature`（依是否為既有 feature 的延伸）
       - T2 → 產出 lightweight spec 置於 feature 目錄內
       - T3 → 只在 `_index.md` 輕量修改紀錄段 inline 寫一列
     - 判準表照 proposal § H 決議 13 逐條列出
   - **新增：completed feature 偵測與 follow-up 分流**（決議 17）：
     - 現況 `modify-existing-flow` 的「檢查 completed/ 既有 spec」步驟（原 webforms:32-34,257-260 / core:30-37,189-191）在 P009 目錄化後改為：
       - AI 讀使用者描述，比對 `specs/features/completed/*/\_index.md` 的「目標與範圍」段，偵測語意相關的 completed feature
       - 若偵測到，主動詢問：「這是延續 `{SPEC-ID}-{slug}` 的 follow-up，還是獨立新需求？」
       - 使用者選「follow-up」→ 走新建 follow-up feature 流程（決議 18）
       - 使用者選「獨立新需求」→ 走 `/dflow:new-feature` 正常流程
       - 使用者說「其實是輕量修改，不需開新 feature」→ **拒絕** + 提示「已完成 feature 不接受 T2/T3 直接寫進原目錄；仍需建新 follow-up feature（可只含 T3 inline 紀錄）」
   - **Follow-up feature 新建規則**（決議 18）：
     - 新配 SPEC-ID（依當天日期序，不沿用原 SPEC-ID）
     - 新 slug 不強制等於原 slug
     - 新 `_index.md` Metadata 必填 `follow-up-of: {原 SPEC-ID}`
     - 新 `_index.md` 目標與範圍段頂部自動生成「本 feature 為 `{原 SPEC-ID}-{原 slug}` 的 follow-up」註記
     - BR 演化繼承：新 feature 的 `_index.md` Current BR Snapshot 初始化時，AI 從 BC 層 `rules.md` 讀入涉及的 BR 作 baseline（「首次出現」欄標示「inherited from rules.md」，「最後修訂」欄暫空）
     - 同時更新**舊 `_index.md` 的 Follow-up Tracking 段**（決議 19）：新增該 follow-up 的一列（SPEC-ID / slug / Created / Status）
   - **Step 5 archival 同步更新**：若本 modify 走新 phase（T1）或 follow-up feature 路徑，archival 遵循 `/dflow:finish-feature` 的整目錄搬移
   - **同步 BC 層**：若 modify 涉及 BR 變更，完成後由 `/dflow:finish-feature`（或 completion flow Step 5.3）同步到 `rules.md` / `behavior.md`

### Sub-wave 9c：既有文件同步（決議 11、F-03、F-04）

目標：補齊 Git 整合段、drift-verification 路徑更新、pr-review-checklist 多份 phase spec 邏輯、CLAUDE.md 範本更新。

10. **`references/git-integration.md` 擴增（雙版）**（決議 11，F-04 一致性）
    - **新增段「Directory Moves Must Use git mv」**（完整段，補上 P011 留的框架）：
      - 原則：所有 feature 目錄 / 檔案的移動、重命名，必須用 `git mv`，保留 git 歷史追蹤
      - 適用情境清單：
        - `/dflow:finish-feature` 搬 `active/{feature}/` → `completed/{feature}/`
        - Feature slug 確認後的微調（極罕見，但原則一致）
        - Phase-spec 的重命名（如日期修正）
        - Template 層級的重命名（本 proposal 即：`feature-spec.md` → `phase-spec.md`）
      - 理由段：OpenSpec 目錄重命名不用 git mv 造成的歷史追蹤問題是已知缺點，Dflow 必須避開；這也是為何 Dflow 與 Git 高耦合但與 Git Flow 解耦（見 PROPOSAL-011）
    - **Branch naming 段補齊 slug 語言規則**（填 P011 留的 placeholder，同時對應 proposal 決議 8）：
      - Slug 跟隨使用者 / AI 討論語言（中文 / 英文均合法）
      - 範例：`feature/SPEC-20260421-001-報表調整`（中文）、`feature/SPEC-20260421-002-jpy-currency-support`（英文）
      - 實戰驗證：Obts 實測中文 slug 未遇到 Git server / CI / PR bot 編碼問題
    - **F-04 相關**：在 `git-integration.md` 若提及 commit 訊息或 merge 行為，確認措辭中立（不預設特定 merge strategy）；若需提到 Integration Summary，引用 `/dflow:finish-feature` 而非寫死格式

11. **`references/pr-review-checklist.md` 修訂（雙版）**（feature 目錄化帶來的新邏輯）
    - 現況 checklist 是單一 spec 的檢查；目錄化後需適配「多份 phase spec」
    - 調整方向：
      - 明示「feature 目錄內可能有多份 phase-spec + 多份 lightweight spec + `_index.md`」
      - 檢查清單擴充：「所有 phase-spec status 為 completed」「`_index.md` Current BR Snapshot 與 BC 層 `rules.md` 一致」「若為 follow-up feature，`_index.md` Metadata 有 `follow-up-of` 欄且舊 feature 有 Follow-up Tracking 更新」
      - 原單 spec 檢查項（BR 覆蓋、實作任務打勾等）改為「per phase-spec」檢查
    - **不動其他 checklist 項**（P005a / P005b 既有內容保留）

12. **`references/drift-verification.md` 路徑更新（雙版，輕量）**（F-03 決議）
    - **路徑假設更新**：將現況 `active/{SPEC-ID}-{name}.md` 的假設改為 `active/{SPEC-ID}-{slug}/`（目錄化）
    - **機械對照邏輯完全不變**：`rules.md` ↔ `behavior.md` 的 BR-ID 正向 / anchor / 反向對照保持原樣
    - **不擴張 verify scope 到跨 phase-spec 聚合**（F-03 決議）：drift-verification 不讀 feature 目錄內的 phase-spec；BR 現況聚合由 `_index.md` Current BR Snapshot 承擔；若未來要加入 `_index.md` 對照點，屬後續 P008 延伸，不在本輪 scope
    - 明確註記此 scope 邊界（避免日後 reviewer 誤以為 verify 應做更多）

13. **`templates/CLAUDE.md` 修訂（雙版）**
    - **「開發流程（How do we work?）」段**（P007c segmentation 保留）內的 feature 結構說明同步更新：
      - 目錄結構圖從單檔改為目錄
      - 新命令（`/dflow:new-phase` / `/dflow:finish-feature`）加入說明
      - 三層 Ceremony 提及
    - **「系統脈絡」段不動**（P007c 保留；本 proposal 不涉及）
    - Core 版的「架構」段（Clean Architecture 四層）**保持**不動

## Step 4：實施後自我驗收

跑過以下檢查：

### 4.1 結構驗收

- `templates/_index.md` 雙版存在 + 六段結構齊全
- `templates/phase-spec.md` 雙版存在 + 有「Delta from prior phases」段（`git log --follow` 應看到原 `feature-spec.md` 歷史）
- `templates/feature-spec.md` 雙版**不存在**
- `templates/lightweight-spec.md` 雙版存在 + 使用指引更新
- `references/new-phase-flow.md` 雙版存在
- `references/finish-feature-flow.md` 雙版存在

### 4.2 決策樹 / 命令清單 verify

Grep 以下字串在 SKILL.md 雙版應有命中：
- `/dflow:new-phase`
- `/dflow:finish-feature`
- `_index.md`
- `phase-spec`
- `Current BR Snapshot`

### 4.3 三層 Ceremony verify

Grep `T1 Heavy` / `T2 Light` / `T3 Trivial` 在 SKILL.md + modify-existing-flow.md 雙版應有命中。

### 4.4 Completed feature reopen verify

Grep `follow-up-of` / `Follow-up Tracking` / `completed feature` 在 `_index.md` 範本、`modify-existing-flow.md`、`finish-feature-flow.md` 雙版應有命中（對應決議 16-19）。

### 4.5 Integration Summary verify（F-04 Path B）

Grep `Integration Summary` 在 `finish-feature-flow.md` 雙版應有命中；不應出現「merge commit 訊息建議」（舊措辭）。

### 4.6 drift-verification 路徑更新 + scope 不擴張 verify

- Grep `active/{SPEC-ID}-{name}.md` 應無命中（舊路徑假設）
- Grep `active/{SPEC-ID}-{slug}/` 應有命中（新路徑假設）
- Grep `跨 phase-spec` / `phase-spec 聚合` 在 `drift-verification.md` 應有明確「不在本輪 scope」註記

### 4.7 git-integration.md 擴增 verify

- Grep `Directory Moves Must Use git mv` 在 `git-integration.md` 雙版應有命中（完整段）
- Grep `slug 跟隨` / `中文 slug` 應有命中

### 4.8 範圍外檔案 untouched verify

- 繁中版檔案（`*-tw/`）**未被修改**
- PROPOSAL-010 scope 檔案（`scaffolding/`、`init-project-flow.md`、README.md、`CLAUDE-md-snippet.md`）**未被修改**
- P001 / P003 / P005 / P007 / P008 的核心結構**未被修改**

若任一驗收失敗，修正後重跑。

## Step 5：CHANGELOG 更新

在 `CHANGELOG.md` 最上方新增一段：

```markdown
## {YYYY-MM-DD} — R7 Wave 2 實施：PROPOSAL-009 Feature 目錄化 + 多階段規格 + 新命令

**前置**：R7 Wave 1（PROPOSAL-011 Git Flow Decoupling）已完成（見前段 CHANGELOG）；R7 Review + Approve 處理 4 個 finding（accept-with-choice: F-01 Path A-3 / F-02 Path C / F-04 Path B；accept: F-03）
**Proposal**：`proposals/PROPOSAL-009-feature-directory-and-phases.md`（approved）
**影響範圍**：

**結構 + 命名 + 範本（Sub-wave 9a）**
- 新增：`templates/_index.md`（雙版）—— feature 級 dashboard 六段範本，含 Current BR Snapshot
- `git mv`：`templates/feature-spec.md` → `templates/phase-spec.md`（雙版）—— 新增「Delta from prior phases」段
- 修改：`templates/lightweight-spec.md`（雙版）—— 使用指引對應 T2 ceremony
- 修改：`SKILL.md`（雙版）—— Project Structure、Ceremony Scaling（三層 T1/T2/T3）、Templates 表

**新命令 + Flow（Sub-wave 9b）**
- 新增：`references/new-phase-flow.md`（雙版）—— `/dflow:new-phase` 流程
- 新增：`references/finish-feature-flow.md`（雙版）—— `/dflow:finish-feature` 流程（**Integration Summary Git-strategy-neutral**，F-04 Path B）
- 修改：`SKILL.md`（雙版）—— Primary triggers / 決策樹 / Slash Commands 表 / Reference Files 表加入兩新命令
- 修改：`references/new-feature-flow.md`（雙版）—— Step 3.5 Slug Confirmation 新增、Step 4/Step 8.4 目錄化
- 修改：`references/modify-existing-flow.md`（雙版）—— 三層 Ceremony 判斷樹、completed feature 偵測與 follow-up 分流

**既有文件同步（Sub-wave 9c）**
- 修改：`references/git-integration.md`（雙版）—— 新增「Directory Moves Must Use git mv」完整段、Branch naming 段補 slug 語言規則
- 修改：`references/pr-review-checklist.md`（雙版）—— 多份 phase-spec 的檢查邏輯
- 修改（輕量）：`references/drift-verification.md`（雙版）—— 路徑假設更新、明確不擴張 scope（F-03）
- 修改：`templates/CLAUDE.md`（雙版）—— 「開發流程」段內 feature 結構與新命令同步

**繁中版同步**：延至 Post-Review Closeout C1（依 review-workflow.md §七）

**下一步**：R7 Wave 3（PROPOSAL-010 實施）—— 本 proposal 完成後，`/dflow:init-project` 產出結構已有 `_index.md` 範本可引用
```

## Step 6：完成後回報

```
R7 Wave 2 實施完成（PROPOSAL-009 Feature 目錄化 + 多階段規格 + 新命令）。

**已新增檔案**（4 檔）：
- sdd-ddd-webforms-skill/templates/_index.md
- sdd-ddd-core-skill/templates/_index.md
- sdd-ddd-webforms-skill/references/new-phase-flow.md
- sdd-ddd-core-skill/references/new-phase-flow.md
- sdd-ddd-webforms-skill/references/finish-feature-flow.md
- sdd-ddd-core-skill/references/finish-feature-flow.md
（實際 6 檔，新增列表）

**已重命名檔案**（2 檔，`git mv`）：
- templates/feature-spec.md → templates/phase-spec.md（雙版）

**已修改檔案**：
- {列出每個修改的檔 + 一句話描述主要改動}

**CHANGELOG.md**：R7 Wave 2 實施段已新增

**驗收結果**（見 Step 4）：
- 4.1 結構驗收：✓ / ✗
- 4.2 決策樹 / 命令清單 verify：✓ / ✗
- 4.3 三層 Ceremony verify：✓ / ✗
- 4.4 Completed feature reopen verify：✓ / ✗
- 4.5 Integration Summary verify：✓ / ✗
- 4.6 drift-verification verify：✓ / ✗
- 4.7 git-integration.md 擴增 verify：✓ / ✗
- 4.8 範圍外檔案 untouched verify：✓ / ✗

**待使用者 commit 後進入 R7 Wave 3（PROPOSAL-010 實施）**。建議 commit message：
  `Implement PROPOSAL-009: Feature Directory + Multi-Phase + New Commands (R7 Wave 2)`
  含 R7 Review F-01 Path A-3 / F-02 Path C / F-03 / F-04 Path B 的 finding 修正。

**備註 / 意外發現**（如有，記入 cross-round-notes.md；若無，寫「無」）：
```

## 禁止事項

- **不要用普通 `mv`**—— 所有重命名必須用 `git mv`（`templates/feature-spec.md` → `phase-spec.md` 雙版）
- **不要自己實施 PROPOSAL-011**—— 前置檢查已要求 011 完成；若發現未完成，**停止回報**，不要插手
- **不要動繁中版檔**（`*-tw/`）—— 延到 Closeout C1
- **不要動 PROPOSAL-010 scope 的檔**：
  - `scaffolding/` 目錄（雙版）
  - `references/init-project-flow.md`（雙版）
  - `scaffolding/CLAUDE-md-snippet.md`（雙版）
  - `README.md`（repo 根，010 會動）
- **不要動 proposal 本身**（`proposals/PROPOSAL-009-*.md` 已 approved，不需改）
- **不要擴張 `/dflow:verify` scope 到跨 phase-spec 聚合**（F-03 決議）
- **不要把 Integration Summary 綁特定 merge strategy**（F-04 Path B 決議；具體格式交 PROPOSAL-010 scaffolding）
- **不要讓 completed feature 走「搬回 active/」**（F-02 Path C 決議；完成 feature 凍結，修改走 follow-up）
- **不要自動 commit**

## 若遇到歧義

- 第一優先：回到 `proposals/PROPOSAL-009-*.md` § A-I 對應段 + § 影響範圍表
- 第二優先：回到 `reviews/round-7-decisions.md` 對應 finding 段（F-01 Path A-3 / F-02 Path C / F-03 / F-04 Path B）
- 第三優先：R1-R6 既有機制（P003 / P005 / P007 / P008）的既有實施，作為「不破壞相容性」的參考
- 第四優先：若仍無法決定，**停下來回報使用者**，不要自行裁定
- **不要**試圖跑去讀 PROPOSAL-010 來「幫忙連貫」—— 010 是後續 wave

## 特別提醒

- **`/dflow:finish-feature` 的 BC 層同步動作**（決議 10 Step 3、finish-feature-flow.md Step 3）：**不新設流程**，而是**呼叫 P003/P007a 既有的 Step 8.3 / Step 5.3 sync 機制**。實施時在 `finish-feature-flow.md` 明確寫「延續既有 Step 8.3 / 5.3 的 `rules.md` / `behavior.md` 更新邏輯」，不要重新發明同步邏輯。
- **`/dflow:new-phase` 嚴格只適用於 active feature**（決議 16）：若使用者在 completed feature 上呼叫，`/dflow:new-phase` 應**拒絕執行**並導向 `/dflow:modify-existing` follow-up 路徑。實施時在 `new-phase-flow.md` Step 1 明確寫出此檢查。
- **`_index.md` 的 Current BR Snapshot 不是歷史**：這是 feature 層**當前狀態**；歷史由各 phase-spec 的「Delta from prior phases」串接。範本與 flow 文件中要明確這個區分，避免 AI 未來把 Snapshot 寫成累積日誌。

---END PROMPT---
