# Round 7 Decisions — Draft Proposal Review (P009 / P010 / P011)

**對應 review 報告**: `reviews/round-7-report.md`
**決議日期**: 2026-04-22（R7 Approve session）
**決議者**: 使用者（Claude 整理選項 + 當場修訂 proposal）
**檔案狀態**: **完成**（7 個 finding 處理完畢；三份 proposal 狀態推進完成）

---

## 決議彙總（陸續更新）

| Finding | Proposal | 嚴重度 | 維度 | 決議 | 說明 |
|---|---|---|---|---|---|
| F-01 | 009 | Major | D1 | **accept-with-choice（Path A-3）** | 選定：phase-spec 由 feature-spec `git mv` 重命名 + 新增 Delta 段；lightweight-spec 保留獨立檔並置於 feature 目錄內；`_index.md` 六段範本（含 Current BR Snapshot）；三層 Ceremony T1/T2/T3 |
| F-02 | 009 | Major | D1 | **accept-with-choice（Path C）** | 選定：completed/ = 凍結歷史不搬回；reopen 走新建 follow-up feature（新 SPEC-ID + `follow-up-of` 欄位 + 反向 Follow-up Tracking 段）；`/dflow:new-phase` 嚴格只適用於 active feature |
| F-03 | 009 | Major | D1 | **accept** | `/dflow:verify` scope 收斂：不擴張到跨 phase-spec 聚合；drift-verification 維持 `rules.md` ↔ `behavior.md` 現況對照；BR 現況聚合由 `_index.md` Current BR Snapshot 承擔（F-01 A-3 間接處理） |
| F-04 | 009/010/011 | Major | D3 | **accept-with-choice（Path B）** | P009 `/dflow:finish-feature` 產出改為 Integration Summary（Git-strategy-neutral）；P010 兩版 Git-principles 各自補「Integration Commit 訊息慣例」段；P011 關聯表註記與解耦目標一致 |
| F-05 | 010 | Major | D4 | **accept** | /dflow:init-project 必產清單拆為 WebForms 版（migration/tech-debt.md）/ Core 版（architecture/tech-debt.md + architecture/decisions/ + domain/context-map.md）；`behavior.md` 從必產清單移除，由 P003/P007a 既有機制產生 |
| F-06 | 011 | Major | D1 | **accept** | P011 scope 精準化：補入 drift-verification.md 雙版（移除「Before a release branch cut」觸發點）；移除 modify-existing-flow.md × 2 + pr-review-checklist.md × 2（Grep verify 現況無 Git Flow 字樣）|
| F-07 | 010 | Minor | D2 | **accept** | P010 § 3 README 範例步驟 1 改為「Install the appropriate skill using the current Claude Code skill mechanism (see Claude Code official docs)」，不寫死 `.claude/skills/` 路徑，與風險 6 緩解說明一致 |

---

## Accept / Accept-with-choice 項目的修訂紀錄

### F-01（accept-with-choice, Path A-3：phase-spec 重命名 + Delta 段 + _index.md 含 BR Snapshot + 三層 Ceremony）

**背景脈絡**：
- Codex 指出 P009 § 影響範圍的 Templates 區塊對 phase-spec / feature-spec / lightweight-spec 的終局策略仍寫三種可能（新增/重命名、刪除/重命名、修改或保留），把「是否保留 lightweight 獨立模板」留到「實施時評估」。這不是可延後的彈性，會直接決定下游 flow / SKILL.md 的修訂無法一致落稿。
- R7 Approve session 討論中，使用者延伸發現兩個關聯 gap：(a) phase-spec 範本沒有 Delta 機制，phase 2+ 的 BR-ID 會撞 / 看不出演化 / 讓 drift-verification 誤報；(b) 現況 ceremony 只有兩層（new-feature heavy / bug-fix lightweight），沒有涵蓋「按鈕顏色、文案修正」這類無 BR 變動的表層修改。
- 討論中也確認 Dflow 本來就是「workflow 按 feature 切、system state 按 BC 切」的雙軸設計，BC 層系統狀態由 P003 `rules.md` / `behavior.md` 承擔（Step 8.3 / Step 5.3 的既有 sync 機制），F-01 只需把 phase 層 Delta + feature 層 Snapshot 補起來，不需新設系統層機制。

**選定 Path A-3 的終局決定**：

1. **phase-spec.md**：由 `templates/feature-spec.md` 用 `git mv` 重命名；沿用現有全部章節；於「業務規則」之後新增「Delta from prior phases」選用段（首 phase 免填，phase 2+ 必填；格式沿用 P002/P004 的 ADDED / MODIFIED / REMOVED / RENAMED / UNCHANGED）；Delta 僅記本 phase 變化，不累積歷史
2. **`_index.md`**（新增範本）：六段結構——Metadata / 目標與範圍 / Phase Specs / **Current BR Snapshot**（feature 層累積狀態，AI 在 new-phase 進入 / 完成 phase-spec / T2 lightweight 定稿時 regenerate）/ 輕量修改紀錄（T2 外連 + T3 inline 雙用）/ 接續入口
3. **lightweight-spec.md**：保留獨立範本（內容不變）；實例化檔置於 feature 目錄內，命名 `lightweight-{YYYY-MM-DD}-{slug}.md` 或 `BUG-{NUMBER}-{slug}.md`；對應 T2 ceremony
4. **三層 Ceremony（T1 Heavy / T2 Light / T3 Trivial）**：T3 判準四條同時成立才算（無 BR 變動、無 Domain 概念動、無資料結構動、只改 UI 表層/註解/格式化）；低於 T3 的純 typo / 格式化 commit 直接 `git commit` 不走 Dflow
5. **`/dflow:finish-feature`**：收尾時依 `_index.md` BR Snapshot 同步 BR 變化到對應 BC 的 `rules.md` / `behavior.md`，延續 Step 8.3 / Step 5.3 既有機制，不新設流程

**修訂檔案**: `proposals/PROPOSAL-009-feature-directory-and-phases.md`

**具體修訂段落**（7 處）：
- § A 結構類 / 決議 4（重寫）：明列 feature 目錄含三類檔案（_index / phase-spec / lightweight-spec）+ 指向 § H
- § C 命令類 / 決議 10（finish-feature 產出補一項）：新增「依 Current BR Snapshot 同步到 BC 層」動作
- § D 流程類（新增三項 bullet）：`/dflow:new-phase` 進入時 refresh BR Snapshot；`/dflow:finish-feature` 同步 BC 層（延續既有機制）；`modify-existing-flow` 加 Ceremony 判準表
- **§ H（全新段）範本與 Ceremony 終局**：決議 12（phase-spec 重命名 + Delta 段）/ 決議 13（三層 Ceremony + 判準 + Dflow 不走的底線）/ 決議 14（`_index.md` 六段結構）/ 決議 15（lightweight-spec 實例化位置與命名）
- § 影響範圍 Flow 段（四行描述更新）：new-feature-flow、modify-existing-flow、new-phase-flow（新增）、finish-feature-flow（新增）
- § 影響範圍 Templates 段（六行描述定稿）：_index.md / phase-spec.md / feature-spec.md / lightweight-spec.md 終局全部寫死
- § 關鍵假設 5：從「兩者關係由實施時決定」改為「T2/T3 定案 + 長期測試點」

**交叉影響**: 無跨 proposal 修訂（僅動 P009）。但 F-01 的 A-3 終局同時**預先解決了 F-03 的大部分問題**（drift-verification 未來對照的對象從「跨多份 phase spec」縮回「_index.md 的 BR Snapshot 一張表」，scope 不再混入 feature spec 聚合器），F-03 處理時再確認。

**Cross-round-notes 延後項目**（2 項）：
- `lightweight-spec.md` 缺 OpenSpec `tasks.md` 對應的「實作任務」段（P002/P004 當初未納入；本輪不擴大範圍處理）
- 三層 Ceremony T2/T3 邊界在長期實戰的調整（R7 Implement 後蹲點觀察）

### F-02（accept-with-choice, Path C：新建 follow-up feature + 雙向連結）

**背景脈絡**：
- Codex 指出現況 `modify-existing-flow` 會先檢查 `completed/` 裡是否已有既有 spec（webforms:32-34,257-260 / core:30-37,189-191），但 P009 draft 同時規定 feature 完成後整個目錄搬到 `completed/`，而 `/dflow:new-phase` 只適用於已啟動的 active feature branch。draft 完全沒處理「已完成的 feature 日後又要改」的情境，這正好落在 `/dflow:modify-existing` 的主場景，屬可實施性問題。
- R7 Approve session 比較了四種 path：A（搬回 active/）、B（原地加 phase）、C（新建 follow-up feature）、D（混合判準）。A 破壞 completed 凍結語意 + git dir rename 追蹤問題；B 讓狀態分類語意混亂；D 判準模糊。
- 選定 Path C 理由：狀態分類最乾淨；實戰中 90% reopen 其實是「延伸需求」；BR 歷史跨 feature 的成本可控（BC 層 `rules.md` 有當前值，Snapshot 繼承機制可補齊）。

**選定 Path C 的終局決定**：

1. **`/dflow:new-phase` 嚴格只適用於 active feature**（§ I 決議 16）：拒絕「搬回 active/」策略
2. **`/dflow:modify-existing` 的 completed feature 偵測與分流**（§ I 決議 17）：AI 主動詢問「follow-up vs 獨立新需求」；禁止把 T2/T3 輕量修改寫回 completed 原目錄
3. **新建 follow-up feature**（§ I 決議 18）：新 SPEC-ID；`_index.md` Metadata 加 `follow-up-of: {原 SPEC-ID}` 必填欄；目標與範圍段頂部自動標註；BR Snapshot 從 BC 層 `rules.md` 繼承 baseline
4. **反向連結維護**（§ I 決議 19）：舊 `_index.md` 加「Follow-up Tracking」段，雙向索引；以新 feature 的 `follow-up-of` 為權威；follow-up 完成時反向更新舊表 Status

**修訂檔案**: `proposals/PROPOSAL-009-feature-directory-and-phases.md`

**具體修訂段落**（4 處）：
- § C 命令類 / 決議 9：`/dflow:new-phase` 加「嚴格只適用於 active feature；若在 completed/ 拒絕執行並導向 follow-up 路徑」條目
- **§ I（全新段）Completed Feature Reopen 生命週期**：決議 16-19 四項（new-phase 限制、modify-existing 分流、follow-up 建立、反向連結維護）
- § 影響範圍 Flow 段 modify-existing-flow.md 描述：加 completed feature 偵測與 follow-up 分流說明（指向 § I 決議 17-18）
- § 影響範圍 Templates 段 _index.md 描述：加 Metadata 選用 `follow-up-of` 欄 + 末尾選用「Follow-up Tracking」段

**交叉影響**: 本 finding 僅動 P009。但「F-02 禁止把 T2/T3 寫回 completed 原目錄」的規則與 F-01 的三層 Ceremony 配合——若使用者選「這其實是極輕量修改」，系統仍強制建新 follow-up feature（可只含 T3 inline 紀錄）。`/dflow:verify` 校正新/舊 `_index.md` 連結一致性的機制屬後續 P008 延伸，不列本輪 scope。

### F-03（accept：縮回 `/dflow:verify` scope，drift-verification 保持現況機械對照）

**背景脈絡**：
- Codex 指出 P009 draft § 影響範圍把 `drift-verification.md` 的變更寫成「路徑假設改為 `active/{SPEC-ID}-{slug}/phase-spec-*.md`；BR-ID 擷取邏輯需跨多份 phase spec」，但現況 `drift-verification.md` 只做 `rules.md` ↔ `behavior.md` 的機械對照，不讀 feature 目錄、不從 feature spec 擷取 BR-ID。P009 的描述等於在不存在的前提上堆新需求。
- R7 Approve 過程中發現：F-01 A-3 定下 `_index.md` Current BR Snapshot 段後，BR 現況聚合的職責已由 `_index.md` 承擔（AI 主動 refresh），`/dflow:verify` 不需要也不應該讀 phase-spec 做聚合。F-03 因此變成「縮回 scope + 措辭修正」的精準度修訂，無設計決策。

**決定**：
1. `drift-verification.md` 僅更新路徑假設（目錄化）；現有 `rules.md` ↔ `behavior.md` 機械對照邏輯**不變**
2. 明確表態不擴張 `/dflow:verify` scope 到跨 phase-spec 聚合；若未來要加入 `_index.md` Snapshot 對照點，屬後續 P008 延伸，不列本輪 scope
3. 驗證點同步修改：verify 在新目錄結構下確認「不受 feature 目錄化影響」，而非「BR-ID 擷取仍正常」

**修訂檔案**: `proposals/PROPOSAL-009-feature-directory-and-phases.md`

**具體修訂段落**（3 處）：
- § 影響範圍 Git/Review/Verify 段 drift-verification.md 兩列描述：改寫為「輕量修改 + 不擴張 scope + 延伸屬 P008」，明確標示來源「R7 Review F-03 決議」
- § 關聯的 Proposal 表 PROPOSAL-008 那列：改為「本輪不擴張 scope + 跨 phase-spec 聚合屬後續 P008 延伸」
- § 驗證點（實施後）：verify 的驗證目標改為「不受 feature 目錄化影響」

**交叉影響**: 無跨 proposal 修訂。但 F-03 的 accept **確認了 F-01 A-3 的 BR Snapshot 設計已足夠處理 BR 現況聚合需求**，不需要 verify 做替代聚合。這也讓 P009 scope 更純粹（回歸 feature 目錄化 + 多階段規格 + 新命令的主軸），不混入 P008 的職責領域。

### F-04（accept-with-choice, Path B：P009 中立化 + P010 scaffolding 補 Integration 格式）

**背景脈絡**：
- Codex 指出 P009 § C 決議 10 把 `/dflow:finish-feature` 的產出寫成「產出 merge commit 訊息建議」，但 P011 明確把「怎麼合」交回專案決策，P010 也明寫 trunk-based 範本會提供 squash / rebase 選項。這等於把剛解耦掉的 Git strategy 又從另一個命令偷偷綁回來。對採 squash merge / rebase merge / fast-forward 的專案來說，「merge commit 訊息」不是通用產物。
- 本 finding 是 R7 唯一跨三份 proposal 的 finding（C-08 檢查點）。R7 Approve session 考量三種 path：A（純中立化，三份都不綁）、B（P009 中立 + P010 scaffolding 補格式）、C（/dflow:finish-feature 條件產出）。
- 選定 Path B 理由：P009 / P011 保持絕對中立；P010 的 scaffolding 本就是裝 Git-specific 內容的地方；與候選 K/L 的職責分工相容（skill 本體中立 + scaffolding 選擇）；避免 Path C 的互動額外負擔。

**決定**：
1. **P009 `/dflow:finish-feature` 產出中立化**：從「merge commit 訊息建議」改為「Integration Summary（feature 目標 / 變動範圍 / 關聯 BR-ID / phase 清單）」，Git-strategy-neutral
2. **P010 Git-principles 兩版本各自補「Integration Commit 訊息慣例」段**：gitflow 版建議 merge commit 格式；trunk 版提供 squash / rebase commit 格式範例
3. **P011 關聯表明示與本 finding 一致**：確認 P009 的新設計符合 011 解耦目標

**修訂檔案**（3 份 proposal）：

**`proposals/PROPOSAL-009-feature-directory-and-phases.md`**（2 處）：
- § C 命令類 / 決議 10 「做什麼」清單：「產出 merge commit 訊息建議」改為「產出 Integration Summary（Git-strategy-neutral）」，並註明格式依專案 Git-principles 決定
- § 影響範圍 Flow 段 finish-feature-flow.md 描述：同步改為「Integration Summary 產出（Git-strategy-neutral）」

**`proposals/PROPOSAL-010-dflow-init-and-scaffolding.md`**（2 處）：
- § 提議的解法 / 2. Git-principles 二版本段：新增 bullet「兩版本各自新增『Integration Commit 訊息慣例』段」，說明 gitflow / trunk 兩版的格式差異
- § 影響範圍 scaffolding 段：Git-principles-gitflow.md 和 Git-principles-trunk.md 描述追加「Integration Commit 訊息慣例段」mention

**`proposals/PROPOSAL-011-git-flow-decoupling.md`**（1 處）：
- § 關聯的 Proposal 表 PROPOSAL-009 那列：追加「R7 Review F-04 定案：009 的 `/dflow:finish-feature` 產出 Git-strategy-neutral 的 Integration Summary，與本 proposal 的解耦目標一致」

**交叉影響**: 本 finding 是跨三份 proposal 的唯一 finding，處理上三份同時修訂。P011 的修訂最輕（僅關聯表補一句）；P009 的改動是「措辭中立化」；P010 的改動是「scaffolding 範本 scope 輕微擴張」（+2 段「Integration Commit 訊息慣例」）。Codex 點的 Codex F-04 三個行號（P009:77-85 / P011:49-54 / P010:106-110）全部 verify 過並處理。

### F-05（accept：/dflow:init-project 必產清單拆分 + `behavior.md` 時機修正）

**背景脈絡**：
- Codex 指出 P010 § 1 把 `specs/architecture/tech-debt.md`、`architecture/decisions/`、`domain/context-map.md`、`behavior.md` 都列成 init 必產，但現況：
  - WebForms 使用 `specs/migration/tech-debt.md`（無 `architecture/`）
  - Core 才使用 `specs/architecture/tech-debt.md` + `domain/context-map.md`
  - `behavior.md` 是 BC 級檔案（`specs/domain/{context}/behavior.md`），需先有 context 才能安放；由 P003 / P007a 機制於首個 BC 建立時產生
- 本 finding 是純精準度修訂，無設計決策；直接採用 Codex 建議的拆分方案。

**決定**：
1. init 必產清單拆為 skill-version-specific（WebForms / Core 各一份）
2. `behavior.md` 從必產清單移除；明確說明由 P003 completion flow 或 P007a baseline capture 機制產生
3. 驗證點追加雙版分別確認條目

**修訂檔案**: `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md`

**具體修訂段落**（3 處）：
- § 1. 新增命令 `/dflow:init-project` 執行流程 step 4：必產清單拆為 WebForms / Core 兩版；`behavior.md` 移除並說明由 P003 / P007a 既有機制產生
- § 關聯的 Proposal 表 PROPOSAL-007a 那列：追加「`behavior.md` 由此機制在首個 bounded context 建立時產生，不在 init 階段必產」
- § 關鍵假設 / 驗證點段：追加 WebForms / Core 雙版產出結構的驗證條目

**交叉影響**: 無跨 proposal 修訂。但本 finding 的正確處理讓 P010 的 `/dflow:init-project` 產出結構與現行 WebForms / Core skill 的 SKILL.md / templates/CLAUDE.md 對齊，避免實施後 scaffold 與既有 flow 脫節。

### F-06（accept：P011 scope 精準化，補入 drift-verification.md 並移除誤列檔）

**背景脈絡**：
- Codex 指出 P011 scope 表同時有「漏列真實受影響檔」與「列入缺乏對應文本的檔」兩個精準度問題：
  - **漏列**：`drift-verification.md` 兩版都保留「Before a release branch cut」觸發點，是 Git Flow 語境（P011 自己想移除的對象）
  - **誤列**：`modify-existing-flow.md` 雙版 + `pr-review-checklist.md` 雙版，現況並沒有 release / hotfix 流程段可刪，只是一般性的 bug-fix / spec review 指引
- R7 Approve session verify 過（用 Grep + 讀檔確認）：
  - `drift-verification.md` 現況 `webforms:122` / `core:132` 確實有 "Before a release branch cut" 字樣 ✓
  - `modify-existing-flow.md` 完全無 `release` / `hotfix` / `develop` / `Git Flow` 字樣 ✓
  - `pr-review-checklist.md` 完全無對應字樣 ✓

**決定**：
1. scope 表**補入** `drift-verification.md` 雙版（移除「Before a release branch cut」觸發點；其他觸發點維持）
2. scope 表**移除** `modify-existing-flow.md` 雙版 + `pr-review-checklist.md` 雙版（現況無 Git Flow 殘餘語句，列入會導致 reviewer 誤以為有實質改動）
3. 繁中版同步修正同樣的 scope 變化

**修訂檔案**: `proposals/PROPOSAL-011-git-flow-decoupling.md`

**具體修訂段落**（4 處）：
- § 4 `/dflow:bug-fix` 語意澄清段第 3 bullet：「modify-existing-flow 若有 hotfix 一律改」改為「現況已無 Git Flow 字樣（Grep verify 過），不列入必改 scope，實施時發現遺漏當場處理」
- § 5 交叉影響的檔案段：新增 `drift-verification.md`（雙版）bullet；修改 `pr-review-checklist.md` bullet（改為「現況無 release branch 檢查項，不列必改」）
- § 影響範圍表：移除 `modify-existing-flow.md` 雙版 + `pr-review-checklist.md` 雙版共 4 行；新增 `drift-verification.md` 雙版 2 行
- § 影響範圍表繁中版段：從「modify-existing-flow_tw.md / pr-review-checklist_tw.md 同步措辭」改為「drift-verification_tw.md 同步措辭」並註明調整理由

**交叉影響**: 無跨 proposal 修訂（僅動 P011）。但需注意 P009 § 影響範圍也列入 `drift-verification.md` 雙版修訂（路徑假設更新，F-03 縮回 scope 後）；兩份 proposal 動同一檔的**不同段落**（P011 動 When to Run 段、P009 動 Input 段），實施時按 Phase 3 順序（011 → 009 → 010）自然分段處理，不衝突。`pr-review-checklist.md` 雙版仍在 P009 scope 中（F-01 的「多份 phase spec 的檢查邏輯」），與本 finding 的移除不矛盾——P011 移除是「無 Git Flow 語句可動」，P009 保留是「feature 目錄化帶來的新邏輯需求」，兩者維度不同。

### F-07（accept：P010 README 範例不寫死安裝路徑）

**背景脈絡**：
- Codex 指出 P010 § 3 更新 README.md 的範例段（現況 `proposals/PROPOSAL-010:114-121`）步驟 1 寫死 `copy ... to your .claude/skills/`，但同一份 proposal § 預期後果 / 風險 6（`:237-238`）明確說 README 安裝段「保持高層步驟 + link to Claude Code official docs，不寫死路徑細節」。proposal 內部自相矛盾。
- 屬 Minor 精準度修訂，改動極輕。

**決定**：範例步驟 1 改為中立措辭「Install the appropriate skill using the current Claude Code skill mechanism (see Claude Code official docs)」，與風險 6 緩解說明一致。

**修訂檔案**: `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md`

**具體修訂段落**（1 處）：
- § 3 更新 README.md 的範例段步驟 1：改為中立措辭並註明對應 R7 F-07 決議

**交叉影響**: 無跨 proposal 修訂。

**附帶討論（不在本 finding scope 內，已轉 cross-round-notes）**：使用者在 R7 Approve F-07 討論中提出「最終可考慮 npm install 作為多 AI 工具（Codex / Gemini / GitHub Copilot）通用的散佈機制，同時 npm 可作為 scaffolding 選擇介面（取代 `/dflow:init-project` Q5）」的延伸構想。此為長期散佈策略議題，明確超出 R7 scope，已記入 `reviews/cross-round-notes.md`（議題 3）。

---

## Reject / Defer 項目備忘

本輪無 reject / defer 決議。7 個 finding 全部為 accept（4 項）或 accept-with-choice（3 項，F-01 Path A-3 / F-02 Path C / F-04 Path B）。

---

## Out-of-scope 項目

本輪無在範圍內但轉 out-of-scope 的 finding。R7 Approve 過程中延伸出 3 個 cross-round-notes 議題（非 finding 本身），已記入 `reviews/cross-round-notes.md`：
- 議題 1：`lightweight-spec.md` 缺 OpenSpec `tasks.md` 對應「實作任務」段（F-01 附帶討論）
- 議題 2：三層 Ceremony（T1/T2/T3）判準在長期實戰的調整（F-01 附帶討論）
- 議題 3：Dflow 長期散佈策略——npm install + 多 AI 工具相容（F-07 附帶討論）

---

## Proposal 狀態推進紀錄

| Proposal | 推進前 | 推進後 | 推進理由 |
|---|---|---|---|
| **PROPOSAL-011** | draft | **approved** | F-04 / F-06 兩項 finding 全部 accept 或 accept-with-choice，全部修訂完成；無 reject / defer / clarify；滿足推進條件 (a)(b)(c)(d)(e) |
| **PROPOSAL-009** | draft | **approved** | F-01 / F-02 / F-03 / F-04 四項 finding 全部 accept 或 accept-with-choice，全部修訂完成（含 § H 新增 4 項決議 + § I 新增 4 項決議）；無 reject / defer / clarify；滿足推進條件 |
| **PROPOSAL-010** | draft | **approved** | F-04 / F-05 / F-07 三項 finding 全部 accept 或 accept-with-choice，全部修訂完成；無 reject / defer / clarify；滿足推進條件 |

三份 proposal 的「評估紀錄」段已同步填入（評估日期 2026-04-22，結論 approved，理由詳列各段修訂對應 finding）。

---

## 下一步

### R7 Implement 階段（分 3 個 session）

依 `field-reference-candidates.md` Phase 3 實施規劃的依賴順序與 AI 分派表執行：

| Session | Proposal | 規模 | 建議模型 / 工具 | 依賴 |
|---|---|---|---|---|
| **Session 1** | **PROPOSAL-011** | small | Cursor Opus 4.7 High（見 Phase 3 AI 分派表）| 無前置 |
| **Session 2** | **PROPOSAL-009** | large | Claude Code 主力 | 依賴 PROPOSAL-011 先完成 |
| **Session 3** | **PROPOSAL-010** | medium | Cursor Opus 4.7 High | 依賴 PROPOSAL-011 / PROPOSAL-009 先完成 |

### 各 session 啟動提示詞

- 待 R7 Approve 完成（即本 session 結束）後另起：
  - `reviews/prompts/round-7-implement-011.md`
  - `reviews/prompts/round-7-implement-009.md`
  - `reviews/prompts/round-7-implement-010.md`
- 各 session 的實施輸入是 approved 後的 proposal + 本 decisions 文件 + F-01 / F-02 附帶討論的 context

### R7 Implement 完成後

仿 R1–R6 pattern 進入 **R7 實作品質審查（Codex）**：
- Codex review 實作產物 vs proposal 意圖
- Claude Approve session 處理 Codex 的 findings
- Claude Implement session 修正

### Closeout 階段（R7 全部完成後）

- 繁中版同步（`*-tw/`）：本輪 proposal 修訂未納入繁中處理（依 review-workflow.md §七 C1 延後至 Post-Review Closeout）
- Tutorial 重建（若相關）
- 評估 cross-round-notes 三項議題的處理時機（議題 1-2 可納入 Wave D 精簡化審查；議題 3 可能需獨立 wave）

---

## 跨輪發現備忘

R7 Approve 過程中延後處理的議題已寫入 `reviews/cross-round-notes.md`（見 § R7 Approve 發現、延後處理的議題，共 3 項）。
