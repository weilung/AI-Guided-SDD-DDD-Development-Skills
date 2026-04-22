## PROPOSAL-009: Feature 目錄化 + 多階段規格 + 新命令

**狀態**: `draft`

**提出日期**: 2026-04-22

**優先級**: `高`

**來源**: `reviews/field-reference-candidates.md` 候選 A（Feature 組織方式）+ 候選 J（`_index.md` 作為 resume 入口）的混血方案 11 項決議，Obts 實戰背書 2026-04-21。

**實施排程**: R7 Wave 2（依賴 PROPOSAL-011 先完成 `git-integration.md` 基底；本 proposal 在其上擴增「Directory Moves Must Use git mv」段）

---

### 問題描述

當前 Dflow 的 feature 單位是**單一檔案**（`specs/features/active/{SPEC-ID}-{name}.md`），在實戰中暴露以下缺口（2026-04-21 Obts 背書確認）：

1. **feature 跨多個 commit / 多次修改時沒有全貌**。Obts 實測顯示，一個 feature 達 8 個 commit / 2 週規模時，單一 spec 檔無法呈現「這個 feature 整體做了什麼、每個 commit 解決了什麼」。團隊需要 feature 級 dashboard。

2. **多階段規格無法組織**。同一 feature 跨多次循環（「Kickoff → Domain → Design → Build → Verify」多次執行）時，現況只能拆成多個獨立 spec，失去「同屬一個 feature」的脈絡。OpenSpec 風格的「一次完整流程 → 1-N commit」在 commit 數多時會失真。

3. **缺乏 feature 級 resume 入口**。新對話接續前次工作時，需要跑 `/dflow:status` 聚合系統級狀態，但沒有「讀單一檔案就理解這個 feature 到哪了」的入口。

4. **收尾步驟是隱式的**。目前 feature 完成時搬 spec 到 `completed/` 是 new-feature-flow Step 8.4 / modify-existing-flow Step 5.4 的文件步驟，沒有命令觸發，容易被忽略。也沒有「檢查所有 phase spec ✅ 再搬目錄」的統一收尾儀式。

5. **分支 / 目錄命名已綁死英文**。Branch 格式 `feature/{CONTEXT-ID}-{short-description}` 預設英文，但 Obts 實戰使用中文 slug（如 `feature/SPEC-20260421-001-報表調整`）未遇到編碼或工具鏈問題；現行 skill 未明確支援。

6. **`/dflow:status` 系統級聚合與 feature 級儀表板是兩個不同層級的需求**，目前只有前者。

Obts 團隊針對候選 A 的背書結論：**採混血方案** —— V2 的 feature 目錄化 + 多階段規格 + `_index.md`，疊加在 Dflow 現有的狀態分類（`active/` / `completed/` / `backlog/`）之上，而非年份分類。

---

### 提議的解法

本 proposal 將混血方案決議拆為 4 類、共 11 項，並整合對應的流程與產出修改。

#### A. 結構類（決議 1–6）

1. **Feature 單位由「單檔」改為「目錄」**：
   ```
   舊：specs/features/active/{SPEC-ID}-{name}.md
   新：specs/features/active/{SPEC-ID}-{slug}/
   ```

2. **保留 `active/` / `completed/` / `backlog/` 狀態分類**（拒絕 V2 的年份分類）。Dflow 已有狀態分類機制，實戰使用良好。

3. **保留 SPEC-ID 系統**（`SPEC-YYYYMMDD-NNN`）；feature 目錄名格式 = `{SPEC-ID}-{slug}`（例如 `SPEC-20260421-001-報表調整`）。

4. **每個 feature 目錄含**（範本終局詳見 § H，R7 Review F-01 定案）：
   - `_index.md`：feature 級 dashboard + Current BR Snapshot + 輕量修改紀錄 + 接續入口（六段結構見 § H 決議 14）
   - 0-N 份 phase spec：`phase-spec-YYYY-MM-DD-{slug}.md`（T1 Heavy ceremony 產出，完整 SDD 循環）
   - 0-N 份 lightweight spec：`lightweight-{YYYY-MM-DD}-{slug}.md` 或 `BUG-{NUMBER}-{slug}.md`（T2 Light ceremony 產出，含 BR Delta）

   三層 Ceremony 分層（T1 Heavy / T2 Light / T3 Trivial）由 AI 依 § H 決議 13 判準選擇；T3 最輕（顏色 / 文案 / 格式等無 BR 變動的修改）只在 `_index.md` 輕量修改紀錄段 inline 寫一列，不產獨立 spec 檔。

5. **一份 phase spec ≈ 一次完整「Kickoff → Domain → Design → Build → Verify」循環的產出**。這對齊現況 `feature-spec.md` 範本的範圍（單一完整循環），但重新命名為 phase-spec 以反映「feature 內的一個階段」而非「feature 本身」。

6. **Feature 完成後，整個目錄從 `active/` 搬到 `completed/`**（取代現況「單檔搬移」），用 `git mv`（見決議 11）。

#### B. 命名類（決議 7–8）

7. **結構詞保持英文**：`phase-spec`、`_index`、`SPEC-` 一律英文，避免國際化團隊混用時的語意歧義。

8. **Slug 跟隨使用者與 AI 的討論語言**：中文討論 → 中文 slug；英文討論 → 英文 slug；不強迫翻譯。
   - Obts 實測：中文分支、中文目錄名未遇到 Git server / CI / PR review bot 編碼問題。
   - 範例：`SPEC-20260421-001-報表調整/`（中文）、`SPEC-20260421-002-jpy-currency-support/`（英文）都合法。

#### C. 命令類（決議 9–10）

9. **新增 `/dflow:new-phase`**：在**已啟動的 active feature branch 上**啟動新階段。
   - **做什麼**：建新 phase spec（`phase-spec-YYYY-MM-DD-{slug}.md`）+ 更新 `_index.md` 進度欄
   - **不做什麼**：不建 branch、不建 feature 目錄（這些是 `/dflow:new-feature` / `/dflow:modify-existing` 的職責）
   - **觸發時機**：同 feature 內已完成 N 個 phase，要開始第 N+1 個 phase 時
   - **階段 slug 確認**：進入時要求使用者確認階段 slug（類比 Step 3.5 Slug Confirmation）

10. **新增 `/dflow:finish-feature`**：feature 收尾儀式。
    - **做什麼**：
      - 驗證目錄內所有 phase spec 的 status 為 `completed`
      - 檢查 `_index.md` 是否有未結項
      - 更新 `_index.md` 的 feature 狀態為 completed
      - **依 `_index.md` 的 Current BR Snapshot，同步 BR 變化到對應 BC 的 `rules.md` / `behavior.md`**（延續 Step 8.3 / Step 5.3 既有 sync 機制，不新設流程）
      - 用 `git mv` 將整個 feature 目錄從 `active/` 搬到 `completed/`
      - 產出 merge commit 訊息建議（給使用者 copy-paste）
    - **不做什麼**：**不自動 merge**（merge 策略由專案 / 使用者決定，Dflow 不預設）
    - **修補現況缺口**：目前收尾是 new-feature-flow Step 8.4 的文件步驟，沒有命令可觸發；新命令讓這個動作顯式化。

#### D. 流程類（附帶產出）

- **`new-feature-flow.md` 增加 Step 3.5 Slug Confirmation**：
  - 時機：Step 3（Domain Modeling，Core 版）/ Step 3（Impact Analysis，WebForms 版）完成後、Step 4（Write the Spec）之前
  - AI 提案 SPEC-ID + slug + 目錄名 + 分支名，使用者確認後才建目錄
  - 範例：
    ```
    「依我們的討論，SPEC-ID: SPEC-20260421-001，slug: 報表調整（跟隨中文討論）
    → feature 目錄：specs/features/active/SPEC-20260421-001-報表調整/
    → git branch：feature/SPEC-20260421-001-報表調整
    這樣可以嗎？或你想改 slug？」
    ```
- **`/dflow:new-phase` 進入時同樣要求階段 slug 確認**（格式類比上述）；進入時同步 refresh `_index.md` 的 Current BR Snapshot 段（見 § H 決議 14）
- **`/dflow:finish-feature` 修補 Dflow 現況缺口**（目前收尾是流程內隱式步驟，無命令觸發）；收尾時依 `_index.md` Current BR Snapshot 同步 BR 變化到對應 BC 的 `rules.md` / `behavior.md`（延續 Step 8.3 / Step 5.3，不新設流程）
- **`modify-existing-flow.md` 增加三層 Ceremony 判準表**（見 § H 決議 13）：AI 依 T1 / T2 / T3 分層執行對應產出；`new-feature-flow.md` 預設走 T1 Heavy，不需判準

#### E. Git 整合類（決議 11）

11. **所有目錄 / 檔案移動必須用 `git mv`**：
    - `/dflow:finish-feature` 搬 `active/{feature}/` → `completed/{feature}/` 必須用 `git mv`
    - Phase spec 重命名、feature slug 確認後微調，均用 `git mv`
    - 在 `references/git-integration.md` 增加「Directory Moves Must Use git mv」原則段
    - **理由**：Dflow 與 Git 高耦合（feature branch ↔ feature 目錄綁定），若用普通 `mv` 會破壞 git 歷史追蹤 —— 這是 OpenSpec 目錄重命名的已知缺點，Dflow 必須避開

#### F. 命令全景圖（混血方案後）

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

#### G. `/dflow:status` 與 `_index.md` 的互動

- `_index.md` 為 **feature 級**聚合（單一 feature 的所有 phase spec 進度、輕量修改紀錄、接續指引）
- `/dflow:status` 為 **系統級**聚合（所有 active feature 的摘要）
- 兩者不衝突：`/dflow:status` 在混血方案下改為讀各 active feature 目錄的 `_index.md` 摘要後彙整
- 開新對話接續工作時的建議動作：`/dflow:status` → 選目標 feature → 讀該 feature 的 `_index.md`

#### H. 範本與 Ceremony 終局（R7 Review F-01 決議，accept-with-choice Path A-3）

> 本段由 R7 Review F-01 定案，補足原決議 4 對 phase-spec / `_index.md` / lightweight-spec 的範本終局與使用判準。核心設計：「Delta 是局部（commit 式），Snapshot 是聚合（feature 層），系統層 State 由 P003 `rules.md` / `behavior.md` 承擔」—— 三層狀態線明確分工。

**決議 12：`phase-spec.md` 由 `feature-spec.md` 重命名；範本新增「Delta from prior phases」選用段**

- 重命名路徑：`templates/feature-spec.md` → `templates/phase-spec.md`（雙版，用 `git mv`，見決議 11）
- 範本結構沿用現況 feature-spec.md 既有章節（問題描述 / 領域概念 / 行為規格 / 業務規則 / 邊界情況 / Domain Events（Core 版）/ 實作計畫 / 資料結構變更 / 測試策略 / 實作任務）
- **新增章節：「Delta from prior phases」**（置於「業務規則」之後，「邊界情況」之前）
  - 首 phase 免填（AI 在 `/dflow:new-feature` 時留空並註明「首 phase，無前置 Delta」）
  - Phase 2+ 必填；格式沿用 P002 / P004 既有 Delta 格式（ADDED / MODIFIED / REMOVED / RENAMED / UNCHANGED）
  - 僅記**本 phase 的變化**，**不累積**歷史（累積狀態由 `_index.md` Current BR Snapshot 承擔，見決議 14）
  - 「業務規則」章節在 phase 2+ 只列**本 phase 新增 / 修改到的 BR**，不重抄未變動 BR
- `templates/feature-spec.md` 刪除（由 `git mv` 重命名而來，保留 git 歷史）

**決議 13：三層 Ceremony 分層**

| Tier | 情境 | 產出 | 命令 / 觸發 |
|---|---|---|---|
| **T1 Heavy** | 新功能、新 phase、架構變動、新增 BR | 獨立 `phase-spec-YYYY-MM-DD-{slug}.md` + `_index.md` 登記 Phase Specs 列表 + refresh BR Snapshot | `/dflow:new-feature` / `/dflow:new-phase` |
| **T2 Light** | bug fix、UI 輸入驗證調整、流程分支修改——有 BR Delta | 獨立 `lightweight-{YYYY-MM-DD}-{slug}.md`（或 `BUG-{NUMBER}-{slug}.md`）置於 feature 目錄內 + `_index.md` 輕量修改紀錄段登記一列（外連）+ refresh BR Snapshot | `/dflow:bug-fix` 或 `/dflow:modify-existing` 走 lightweight 分支 |
| **T3 Trivial** | 按鈕顏色、文案修正、typo、排版、純註解——**無 BR 變動、無 Domain 概念動、無資料結構動** | **只在 `_index.md` 輕量修改紀錄段 inline 寫一列**，不產獨立 spec 檔 | `/dflow:modify-existing` 走 `_index-only` 分支 |

**T3 判準**（AI 需**同時**滿足以下四條才能判 T3）：
1. 不涉及任何 BR-ID 變更（無 ADDED / MODIFIED / REMOVED / RENAMED 的業務規則）
2. 不涉及任何 Domain 概念新增 / 變更（Aggregate / Entity / VO / Event）
3. 不涉及資料結構變更（資料表、欄位、關聯、索引）
4. 只改 UI 表層（顏色、文字、排版）、純註解、或純格式化

任一不滿足 → 降到 T2；若同時有 Domain / BR / 資料結構動 → 升 T1。

**Dflow 完全不走的底線**（低於 T3）：純 typo 修正、commit message 打錯字、純格式化 commit（如 prettier / dotnet format 自動整理）——連 T3 的「寫一列到 `_index.md`」都可省，直接 `git commit`。SKILL.md 的 Ceremony Scaling 段應補一句對應此底線的明文。

**決議 14：`_index.md` 範本新增，含六段**

1. **Metadata（YAML front matter）**：`spec-id` / `slug` / `status`（in-progress / completed）/ `created` / `branch`
2. **目標與範圍**：散文段，描述 feature 解決什麼、為誰、邊界在哪（1-3 段）
3. **Phase Specs**（T1 列表）：表格欄位 `| Phase | Date | Slug | Status | 檔案連結 |`
4. **Current BR Snapshot**（feature 層累積狀態，本決議核心新增）：表格欄位 `| BR-ID | 現況規則 | 首次出現（phase）| 最後修訂（phase）| Status（active / removed）|`
   - AI 在每次 `/dflow:new-phase` 進入時、每次完成一份 phase-spec 時、每次 T2 lightweight spec 定稿時 regenerate
   - 是 feature 目錄內 BR 的**當前狀態**（不是歷史）；歷史由各 phase-spec 的「Delta from prior phases」段串接閱讀
   - 與 BC 層 `rules.md` / `behavior.md` 的同步關係：feature 完成時 `/dflow:finish-feature` 把 Snapshot 推進到對應 BC（延續 Step 8.3 / Step 5.3 既有 sync 機制）
5. **輕量修改紀錄**（T2 外連 + T3 inline 兩用）：表格欄位 `| Date | Tier | 描述 | commit |`
   - T2 行：描述含「見 `lightweight-{date}-{slug}.md`」外連
   - T3 行：inline 完整描述一句話 + 標籤（如 `[cosmetic]` / `[text]` / `[format]`）
6. **接續入口（Resume Pointer）**：目前進展一句話 + 下次接手建議的下一個動作

**決議 15：lightweight-spec.md 保留獨立範本，實例化檔置於 feature 目錄內**

- 範本沿用現況（問題 / 行為變更 Delta / 根因 / 修復方式 / 發現的技術債）；不變動範本內容
- 更新 SKILL.md / flow 文件的使用指引，明確對應 T2 ceremony（T1 走 phase-spec、T3 直接寫 `_index.md` inline）
- **實例化檔案位置**：置於對應 feature 目錄內（不再像現況放在 `specs/features/active/` 頂層單檔）
- **實例化檔名**：`lightweight-{YYYY-MM-DD}-{slug}.md`（通用輕量修改）或 `BUG-{NUMBER}-{slug}.md`（已分配 BUG-ID 的 bug）
- 若 feature 目錄尚未存在（例如獨立 bug 未附掛既有 feature），`/dflow:bug-fix` 需先建 feature 目錄（含最小版 `_index.md`），再放 lightweight 實例化檔進去——保持「所有規格檔都在 feature 目錄內」的結構統一

---

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| **SKILL.md / 決策樹 / 指令清單** | | |
| `sdd-ddd-webforms-skill/SKILL.md` | 修改 | Primary triggers 加入 `/dflow:new-phase`、`/dflow:finish-feature`；決策樹對應節點；Slash Commands 表；參考檔引用 |
| `sdd-ddd-core-skill/SKILL.md` | 修改 | 同上；Project Structure 內 `specs/features/active/` 範例改為目錄形式 |
| **Flow 文件** | | |
| `sdd-ddd-webforms-skill/references/new-feature-flow.md` | 修改 | Step 3.5 Slug Confirmation 新增；Step 4（spec）/Step 6（branch）改為「目錄 + `_index.md`（含初始 BR Snapshot）+ 第一份 phase-spec（首 phase，Delta 段空）」建立；Step 8.4 archival 改為「整個目錄搬 `completed/`」|
| `sdd-ddd-core-skill/references/new-feature-flow.md` | 修改 | 同上（Core 版 8 步結構、Step 8.4 archival）|
| `sdd-ddd-webforms-skill/references/modify-existing-flow.md` | 修改 | 判斷樹重寫為三層 Ceremony（見 § H 決議 13）：T1（升 `/dflow:new-phase`）/ T2（產出 lightweight spec 置於 feature 目錄內）/ T3（只在 `_index.md` inline 紀錄）；Step 5 archival 同步更新；同步 BC 層的步驟延續既有機制 |
| `sdd-ddd-core-skill/references/modify-existing-flow.md` | 修改 | 同上（Core 版 5 步結構）|
| `sdd-ddd-webforms-skill/references/new-phase-flow.md` | **新增** | `/dflow:new-phase` 完整流程（可從 new-feature-flow 衍生，去除 branch / 目錄建立步驟；保留階段 slug 確認、phase-spec 撰寫含「Delta from prior phases」段、進入時 refresh `_index.md` Current BR Snapshot）|
| `sdd-ddd-core-skill/references/new-phase-flow.md` | **新增** | 同上 |
| `sdd-ddd-webforms-skill/references/finish-feature-flow.md` | **新增** | `/dflow:finish-feature` 完整流程（驗證清單、依 `_index.md` BR Snapshot 同步到 BC 層 `rules.md` / `behavior.md`、`git mv` 搬目錄、merge commit 建議格式）|
| `sdd-ddd-core-skill/references/finish-feature-flow.md` | **新增** | 同上 |
| **Git / Review / Verify** | | |
| `sdd-ddd-webforms-skill/references/git-integration.md` | 修改 | 增加「Directory Moves Must Use git mv」原則段；Branch naming 段補 slug 語言跟隨討論的規則（補 PROPOSAL-011 留的 placeholder）|
| `sdd-ddd-core-skill/references/git-integration.md` | 修改 | 同上 |
| `sdd-ddd-webforms-skill/references/pr-review-checklist.md` | 修改 | 多份 phase spec 的檢查邏輯（「檢查所有 phase spec ✅ 而非單一 spec」）|
| `sdd-ddd-core-skill/references/pr-review-checklist.md` | 修改 | 同上 |
| `sdd-ddd-webforms-skill/references/drift-verification.md` | 修改 | 路徑假設從 `active/{SPEC-ID}-{name}.md` 改為 `active/{SPEC-ID}-{slug}/phase-spec-*.md`；BR-ID 擷取邏輯需跨多份 phase spec |
| `sdd-ddd-core-skill/references/drift-verification.md` | 修改 | 同上 |
| **Templates** | | |
| `sdd-ddd-webforms-skill/templates/_index.md` | **新增** | feature 級 dashboard 範本；含 Metadata / 目標與範圍 / Phase Specs / Current BR Snapshot / 輕量修改紀錄（T2 外連 + T3 inline）/ 接續入口 六段（見 § H 決議 14）|
| `sdd-ddd-core-skill/templates/_index.md` | **新增** | 同上 |
| `sdd-ddd-webforms-skill/templates/phase-spec.md` | **由 `feature-spec.md` `git mv` 重命名** | 沿用現況 feature-spec.md 全部章節；於「業務規則」之後新增「Delta from prior phases」選用段（首 phase 免填，phase 2+ 必填；格式沿用 P002/P004 Delta）見 § H 決議 12 |
| `sdd-ddd-core-skill/templates/phase-spec.md` | 由 `feature-spec.md` `git mv` 重命名 | 同上 |
| `sdd-ddd-webforms-skill/templates/feature-spec.md` | **刪除**（由 `git mv` 重命名而來，保留 git 歷史）| 不再獨立存在 |
| `sdd-ddd-core-skill/templates/feature-spec.md` | 刪除（由 `git mv` 重命名而來，保留 git 歷史）| 同上 |
| `sdd-ddd-webforms-skill/templates/lightweight-spec.md` | 修改（保留獨立範本）| 範本內容不變；更新使用指引使其明確對應 T2 ceremony；實例化檔置於 feature 目錄內、命名 `lightweight-{YYYY-MM-DD}-{slug}.md` 或 `BUG-{NUMBER}-{slug}.md`（見 § H 決議 15）|
| `sdd-ddd-core-skill/templates/lightweight-spec.md` | 修改（保留獨立範本）| 同上 |
| `sdd-ddd-webforms-skill/templates/CLAUDE.md` | 修改 | 專案指引範本中的目錄結構 / 流程引用同步更新 |
| `sdd-ddd-core-skill/templates/CLAUDE.md` | 修改 | 同上 |
| **繁中版同步** | | |
| `sdd-ddd-webforms-skill-tw/` 對應檔全套 | 修改 / 新增 | SKILL_tw.md、new-feature-flow_tw.md、modify-existing-flow_tw.md、new-phase-flow_tw.md（新增）、finish-feature-flow_tw.md（新增）、git-integration_tw.md、pr-review-checklist_tw.md、drift-verification_tw.md |
| `sdd-ddd-core-skill-tw/` 對應檔全套 | 修改 / 新增 | 同上 |

**影響檔案總數估計**：英文版約 20 檔（雙版）+ 繁中版約 10 檔 ≈ 30 檔。

---

### 預估工作量

大

- 結構性變動影響 10+ 個核心檔案
- 雙版（WebForms + Core）的流程文件需各自維持一致性（Step 數不同、措辭不同）
- 英文版 + 繁中版雙語同步
- 新增 2 個 flow 文件（new-phase / finish-feature）+ 2 個 template（_index.md / phase-spec.md）
- 需驗證 P003（rules.md + behavior.md）、P008（drift-verification）、P005（completion flow）等既有機制在新目錄結構下仍能運作

實施時建議切分為至少 3 個 sub-wave：
- Wave 9a：結構 + 命名（決議 1–8）+ `_index.md` / phase-spec 範本
- Wave 9b：新命令（決議 9–10）+ 對應 flow 文件
- Wave 9c：既有文件同步（Git 整合段、drift-verification、pr-review-checklist、CLAUDE.md 範本）+ 繁中同步

---

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-011 | 依賴 | 本 proposal 的決議 11（`git mv` 規範）要加在 011 重構後的 `git-integration.md` 上；檔名也需是 011 後的新名 |
| PROPOSAL-010 | 前置於 010 | 010 的 `/dflow:init-project` 要產出的目錄結構需符合本 proposal 定義的 feature 目錄化規範；`_index.md` 範本需已定義 |
| PROPOSAL-003 | 互補 | `rules.md` + `behavior.md` 為系統級 BC 聚合，不受 feature 目錄化影響；但 drift-verification 的路徑假設需同步更新 |
| PROPOSAL-005 / 005a / 005b | 互補 | Completion checklist 結構不變，但 Step 8.4 / Step 5.4 的 archival 動作由「搬單檔」改為「搬整個目錄」；新增 `/dflow:finish-feature` 可作為 checklist 完成後的收尾 trigger |
| PROPOSAL-007a | 互補 | Brownfield baseline capture 產物寫入 `domain/` 目錄（跨 feature），不受本 proposal 影響；但 Step 編號若調整需同步 |
| PROPOSAL-007c | 互補 | `CLAUDE.md` segmentation（系統脈絡 / 開發流程）保留；本 proposal 只更新「開發流程」段內的 feature 結構說明 |
| PROPOSAL-008 | 互補 | `/dflow:verify` 的 BR-ID 擷取邏輯需跨多份 phase spec；驗證流程本體不變 |

---

### 關鍵假設

- **假設 1**（來自 Obts 背書）：多階段規格在實戰確實常見——feature 達 8 commit / 2 週時，`_index.md` 是唯一「全貌位置」。
- **假設 2**：目錄化對「單 commit 快速結束的小 feature」不會顯著增加 overhead——`_index.md` 可極簡（一段摘要 + 單份 phase spec 連結）。
- **假設 3**：中文 / 英文混用 slug 在 Git server / CI / PR review bot 不會出現編碼問題（Obts 已驗證，不同 Git 平台仍需注意）。
- **假設 4**：`/dflow:finish-feature` 的「驗證所有 phase spec ✅」是 AI 可靠執行的機械檢查（不需人工判斷 spec 內容品質）；若 phase spec 的 status 欄位規範不穩，此假設需重檢。
- **假設 5**（R7 Review F-01 定案後轉為確認）：`_index.md` 的「輕量修改紀錄」段同時承擔 T2 外連（指向獨立 lightweight 檔）與 T3 inline（無獨立檔的表層修改）兩種場景；`lightweight-spec.md` 範本保留對應 T2 ceremony，不併入 `_index.md`（見 § H 決議 13-15）。三層 Ceremony 判準在長期實戰下是否需調整（例如 T2/T3 邊界）納入 R7 Implement 後的驗證點。

**驗證點（實施後）**：
- 跑一個小規模 feature（1 phase spec、2-3 commit）全流程，確認 overhead 可接受
- 跑一個大規模 feature（3+ phase spec、10+ commit）全流程，確認 `_index.md` 確實替代了原先 `/dflow:status` 需聚合的資訊
- 跑 `/dflow:verify` 在新目錄結構下，確認 BR-ID 擷取仍正常
- 中文 slug 專案的 PR review bot / CI 腳本 run 一輪，確認無 regression

---

### 預期後果（風險）

- **風險 1**：既有 Dflow 使用者的 `active/` 下有單檔 spec，遷移需手動處理。
  - 緩解：實施時提供「單檔 → 目錄」的遷移指引（`mkdir {SPEC-ID}-{slug}/` → `git mv {SPEC-ID}-{name}.md {SPEC-ID}-{slug}/phase-spec-{date}-{slug}.md` → 建 `_index.md`）；列入 CHANGELOG 的 Migration Notes 段
- **風險 2**：小 feature 引入目錄結構過度。
  - 緩解：`_index.md` 範本預設極簡（約 20 行），明確標示「這對 1-commit feature 也適用」；在 new-feature-flow 加註「若預期 feature 只有 1 個 phase，`_index.md` 可只填基本欄位」
- **風險 3**：雙版（WebForms + Core）流程步驟數不同（Core 版 modify-existing-flow 為 5 步，WebForms 版為 5 步但編號不同），實施時容易步調不一。
  - 緩解：實施時建議先改一版為標竿（例如 Core 版），驗證後複製模式到另一版；R7 審查需交叉比對
- **風險 4**：繁中版翻譯滯後，影響團隊成員理解。
  - 緩解：沿用 R1-R6 慣例，繁中同步列為 Wave 9c 的必產項；Closeout C1 可再補
- **風險 5**：`/dflow:finish-feature` 的「不自動 merge」原則若解讀為「它不做任何 Git 動作」，可能讓使用者誤以為 `git mv` 搬目錄也需手動執行。
  - 緩解：在 finish-feature-flow.md 明確區分「AI 做 `git mv` + git add」vs「使用者做 merge / push」的界線

---

### 評估紀錄（整合評估時填寫）

**評估日期**:

**結論**:

**理由**:
