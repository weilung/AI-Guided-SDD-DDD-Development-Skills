# Round 7 Review Prompt — PROPOSAL-009 / 010 / 011（Draft Review，給 Codex CLI 使用）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Codex CLI。
> **產出位置**：Codex 會寫入 `reviews/round-7-report.md`。
> **禁止事項**：Codex 此輪不得修改任何檔（包含 proposal、skill 檔、V2 儲存庫皆不動），只產出 review 報告。
> **R7 與 R1–R6 的根本差異**：R1–R6 審的是**已實施**的 proposal 產物（proposal 意圖 vs 實作），R7 審的是**尚未實施**的 3 份 draft proposal 本身（proposal 本體品質 + 三份之間的交叉一致性）。不要試圖對照「實作檔」—— 本輪沒有實作檔。
> **V2 的角色**：`E:\工作區\SDD-Starter-Kit`（以下稱 V2）是 Codex 的**背景閱讀**，目的是讓你能驗證 proposal 對 V2 的描述是否準確、有無漏用 V2 有價值的元素；**V2 本身不是 review 標的**，不要產出關於 V2 品質的 Finding。
> **範圍規模**：3 份 draft proposal 合計約 43 KB，加 V2 約 30 KB 背景；Finding 預估 10–25 項。
> **模型選擇**：建議續用 Codex GPT-5.4 或更新。

---BEGIN PROMPT---

你是 Dflow Round 7 Review 的審查者。本輪範圍為 3 份 draft proposal（PROPOSAL-009 / 010 / 011）的設計品質與三份之間的交叉一致性，**不審任何實作檔**（本輪還沒實施）。

## 第一步：讀入以下檔案

### A. 審查標的（必讀，逐段細讀）

1. `proposals/PROPOSAL-011-git-flow-decoupling.md` — Git Flow 解耦（small 規模）
2. `proposals/PROPOSAL-009-feature-directory-and-phases.md` — Feature 目錄化 + 多階段規格 + 新命令（large 規模）
3. `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md` — `/dflow:init-project` + scaffolding 範本（medium 規模）

### B. 設計脈絡（必讀，理解三份 proposal 的來歷）

4. `reviews/field-reference-candidates.md` — Phase 1-2 候選審查結果
   - §候選 A（feature 組織方式）+ §候選 J（`_index.md` resume 入口）+ §A 的 11 項決議 ← 009 的源頭
   - §候選 K（`dflow init` 機制）+ §候選 B（重新定位）+ §候選 C（社群散佈 onboarding）← 010 的源頭
   - §候選 L（Git Flow Decoupling）← 011 的源頭
   - §Phase 3 實施規劃 ← 三份 proposal 的依賴順序（011 → 009 → 010）與範圍切分依據
5. `proposals/review-workflow.md` — R1-R6 流程規範的骨幹；R7 沿用其嚴重度分類、檔名結構、三階段分工，但審查維度因本輪審 draft 而有調整（本提示詞會覆蓋）

### C. 現況對照檔（必讀，檢查 proposal 影響範圍的準確性）

6. `sdd-ddd-webforms-skill/SKILL.md`
7. `sdd-ddd-core-skill/SKILL.md`
8. `sdd-ddd-webforms-skill/references/git-flow-integration.md`（011 要重命名的檔）
9. `sdd-ddd-core-skill/references/git-flow-integration.md`
10. `sdd-ddd-webforms-skill/references/new-feature-flow.md`
11. `sdd-ddd-core-skill/references/new-feature-flow.md`
12. `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
13. `sdd-ddd-core-skill/references/modify-existing-flow.md`
14. `sdd-ddd-webforms-skill/references/pr-review-checklist.md`
15. `sdd-ddd-core-skill/references/pr-review-checklist.md`
16. `sdd-ddd-webforms-skill/references/drift-verification.md`
17. `sdd-ddd-core-skill/references/drift-verification.md`
18. `sdd-ddd-webforms-skill/templates/CLAUDE.md`
19. `sdd-ddd-core-skill/templates/CLAUDE.md`

不需要全讀 —— 依 proposal 的「影響範圍」表對照檢查即可（例如 proposal 說「修改 Step 3.5」，就讀 new-feature-flow.md 確認 Step 編號與措辭是否真的能容納 Step 3.5 插入）。

### D. V2 背景閱讀（必讀，但只為理解 proposal 而讀；不產生 V2 相關的 Finding）

V2 路徑：`E:\工作區\SDD-Starter-Kit\`

| 檔案 | 對應 proposal | 讀來做什麼 |
|---|---|---|
| `docs/specs/SDD-工作流程說明.md` | 009 | 驗證 009 對「feature 目錄 + `_index.md` + 多階段規格」的描述是否忠於 V2 概念設計；**注意 V2 本身沒有實際填滿的 feature 目錄範例**（是 starter template），所以 009 的 11 項決議背後的「Obts 實戰背書」來自 `field-reference-candidates.md` 記錄，不是直接看 V2 就能驗證 |
| `docs/specs/_overview.md` | 010 | 驗證 010 的 scaffolding `_overview.md` 範本定位是否合理 |
| `docs/specs/_conventions.md` | 010 | 同上，驗證 `_conventions.md` 範本定位 |
| `docs/specs/_共用/Git-原則.md` | 010 / 011 | (010) 驗證 `Git-principles-gitflow.md` 範本設計；(011) 驗證 011 把 Git Flow 規範移出 skill 後，V2 的這份檔是否適合作為「scaffolding 範本藍本」 |
| `CLAUDE.md-範例片段.md` | 010 | 驗證 010 的 `CLAUDE-md-snippet.md` 範本定位 |
| `README.md` | 010 | 對照 V2 散佈版的 onboarding 結構，評估 010 更新 repo README 的方向 |
| `SDD-AI協作開發模式介紹.md`、`使用說明.md` | 010（了解分工）| 不納入 Dflow 本體（候選 C 決議）；但理解 V2 散佈版的 onboarding 面向可幫助判斷 010 對「repo README 更新」範圍是否合理 |

**禁止事項**：不 review V2 本身（不評論 V2 的品質、不建議 V2 改動、不產生關於 V2 的 Finding）。V2 是 context，不是 target。

### E. 不要讀的東西

- `E:\VsProject\Obts\`（Obts 實戰專案）— Obts 的 SDD 實戰結論已蒸餾到 V2 與 `field-reference-candidates.md` 的背書段。讀 Obts 會引入大量雜訊。
- 任何繁中版檔（`*-tw/`）— 繁中版延到 Post-Review Closeout C1 處理（依 review-workflow.md §七）；本輪**不做**英文 vs 繁中對照。
- `sdd-ddd-*-skill/templates/feature-spec.md`（若 009 要重命名為 phase-spec.md，此檔狀態屬「待實施」，非本輪審查範圍）— 讀它只是為了確認 009 對「演化 / 重命名」的描述是否與檔案現況相容。
- `proposals/evaluation-p002-p008.md`（R1-R6 的設計脈絡檔，對 R7 無關）

## 第二步：審查維度（4 個 lens，逐份 proposal 各看一輪）

不同於 R1-R6 的「意圖 vs 實作」對照，R7 審的是 **draft 本身的設計品質**。從以下 4 個維度切入：

### Dimension 1 — Proposal 內部完整性與具體性

對每份 proposal 檢查：

- 問題描述是否具體（能不能從描述裡看出「為什麼現狀有問題、誰受影響」）
- 提議的解法是否落到檔案層級的動作（有無殘留「TBD」、「待討論」、「之後決定」等懸置項）
- 影響範圍表是否完整（對照 §C 的現況檔，proposal 聲稱要改的段落是否真的存在於該檔；有沒有漏列應受影響的檔）
- 預估工作量的級別（小/中/大）是否合理（對照 R1-R6 的實際工作量樣本）
- 關鍵假設是否明列，有無「隱藏假設」（proposal 的解法依賴但沒寫出來的前提）
- 預期後果（風險）是否涵蓋主要實施風險

### Dimension 2 — Proposal 內部邏輯一致性

對每份 proposal 檢查：

- 解法各段落之間有無矛盾（例如 A 段說「保留」、B 段說「移除」同一個東西）
- 影響範圍表列出的變更是否與解法描述一致（有無「解法說要做，但影響範圍沒列」或相反）
- 關聯的 Proposal 表的「依賴 / 互補」關係是否與解法描述吻合
- 命令 / 檔名 / slug 規則在 proposal 內部是否前後一致使用（例如 PROPOSAL-009 在不同段提到「phase spec」命名格式是否一致）

### Dimension 3 — 三份 proposal 的交叉一致性（本輪重點）

這是 R7 的核心審查內容。請逐項檢查以下交叉點：

**C-01 · 011 ↔ 009：`git-integration.md` 的 placeholder 與填齊**
- 011 §2「git-integration.md 保留內容」宣稱留 placeholder 給 009 填（slug 語言、`git mv` 規範）
- 009 §E 決議 11 宣稱「在 references/git-integration.md 增加『Directory Moves Must Use git mv』原則段」
- 檢查：placeholder 與填齊是否對應？是否存在「011 留了但 009 沒填」或「009 以為 011 留了其實沒留」？

**C-02 · 011 ↔ 009：`git mv` 首次實踐的主張**
- 011 §1 宣稱「本 proposal 即首次實踐『目錄/檔案移動必須用 git mv』的原則，同時為 PROPOSAL-009 決議 11 建立先例」
- 009 §E 宣稱 `git mv` 是 feature 目錄化的必要原則
- 檢查：兩份對「首次實踐」與「原則來源」的歸屬一致嗎？是否 009 依賴 011 建立的先例、而 011 並未實際提供該先例的範例段？

**C-03 · 009 ↔ 010：feature 目錄化規範 ↔ init 產出**
- 009 定義 feature 單位為 `active/{SPEC-ID}-{slug}/` 目錄（含 `_index.md` + 0-N phase spec）
- 010 §1 `/dflow:init-project` 要產出 `specs/features/{active,completed,backlog}/` 目錄
- 檢查：010 init 產出的結構是否與 009 的規範對齊？010 是否說明了 init 階段**不**產出任何 feature 目錄（只產空的 active/completed/backlog）？若產空目錄，009 的 `_index.md` 範本與 phase-spec 範本由誰負責放到 skill 的 `templates/`（應在 009 範圍）？

**C-04 · 010 ↔ 011：`Git-principles-trunk.md` 存在的前提**
- 010 §2 計畫提供兩版 Git-principles 範本（gitflow / trunk）作為選擇性 scaffolding
- 011 負責解耦 Dflow 本體對 Git Flow 的預設
- 檢查：若 011 未實施、Dflow 本體仍綁 Git Flow，010 的 trunk-based 範本是否會造成「skill 本體預設 Git Flow、scaffolding 卻提供 trunk」的內部矛盾？010 §關聯的 Proposal 是否明確標示此依賴？

**C-05 · 009 ↔ 010：`_index.md` 範本歸屬**
- 009 §影響範圍 列 `templates/_index.md`（新增）由 009 負責
- 010 的 scaffolding 定位為「專案治理文件」、不重疊 skill 本體
- 檢查：`_index.md` 範本應歸 skill 本體的 `templates/`（009 範圍）還是 010 的 `scaffolding/`？兩份 proposal 的說法是否一致無重複？

**C-06 · 三份影響範圍加總覆蓋完整性**
- 把三份的「影響範圍」表加總，對照 §C 的現況檔清單
- 檢查：是否有某個現況檔**應該**被這 3 份動到、卻沒被任何一份列入？（例如 `templates/context-definition.md`、`templates/behavior.md`、`templates/aggregate-design.md` 是否會因 009 目錄化而受影響？）
- 檢查：是否有某現況檔被**兩份**都列入，但兩份的修改意圖可能互斥？

**C-07 · 依賴順序與實施順序一致性**
- field-reference-candidates.md §Phase 3 明示順序為 011 → 009 → 010
- 三份 proposal 各自的「關聯的 Proposal」表是否一致標示此順序？
- 檢查：A 宣稱「依賴 B」時，B 是否相應宣稱「前置於 A」？是否存在循環依賴或單向宣稱？

**C-08 · 命令命名與語意一致性**
- 011 重新定位 `/dflow:bug-fix`（輕量 ceremony，不綁分支策略）
- 009 新增 `/dflow:new-phase`、`/dflow:finish-feature`
- 010 新增 `/dflow:init-project`
- 檢查：三份描述的命令全景（含「啟動類 / 階段類 / 收尾類 / 輔助類」分類）是否自洽？命令描述在三份 SKILL.md 影響範圍段落的措辭是否一致？

### Dimension 4 — 假設合理性、風險辨識、實施可行性

對每份 proposal 檢查：

- 關鍵假設是否有**可驗證**的依據（實戰背書 / V2 前例 / 現況觀察），或只是一廂情願
- 預期後果（風險）是否遺漏重要面向（例如對「既有 Obts 使用者」、「社群新採用者」、「R1-R6 已建立機制」的影響）
- 實施可行性：工作量級別是否合理；若標「大」，是否有提出切分建議；若有切分，wave 切法是否合理
- 對既有 Dflow 機制（P001-P008）的相容性說明是否充分（特別：009 對 P003 的 rules.md ↔ behavior.md、P008 的 drift-verification、P005 的 completion checklist 影響）

## 第三步：Finding 格式

每個 Finding 採以下格式寫入報告：

```markdown
### F-NN {一句話問題摘要}

**嚴重度**: Critical | Major | Minor | Question
**對照**: {擇一或混合標示}
  - Proposal 內部：`PROPOSAL-00X.md § 段落名`
  - 交叉一致性：`PROPOSAL-00X ↔ PROPOSAL-00Y · C-NN`（對應第二步 §C 的檢查點編號）
  - V2 對照：`V2: docs/specs/...md` + 說明
  - 現況對照：`sdd-ddd-*-skill/...md:line-range`
**維度**: Dimension 1 / 2 / 3 / 4（單選）
**現況**: {實際讀到什麼}
**問題**: {為什麼是問題 —— 遺漏 / 矛盾 / 不可行 / 假設薄弱 / 風險未覆蓋}
**建議**: {具體怎麼改該 proposal；若 Question 類型則寫「需釐清：...」}
```

### 嚴重度定義（R7 專屬，覆蓋 review-workflow.md §三）

- **Critical**：proposal 設計根本缺陷，會導致**無法實施**或**破壞既有 skill 結構**。例如：解法自相矛盾、依賴不存在的前提、實施後會讓 R1-R8 某項機制失效。**嚴格使用，不濫用**。
- **Major**：內部邏輯矛盾、三份之間明顯不一致、影響範圍漏列關鍵檔案、關鍵假設無依據且存在反證。實施前必須修正。
- **Minor**：措辭不夠精確、範本格式細節、列表遺漏、標題不一致、可讀性問題。不影響功能。
- **Question**：Reviewer 不確定 proposal 意圖，需設計者釐清後才能判斷是否問題。**若無法確認是否為 Critical，優先標 Question，不要擅自升為 Critical**。

## 第四步：產出格式

寫入 `reviews/round-7-report.md`，結構如下：

```markdown
# Round 7 Review Report — Draft Proposal Review (P009 / P010 / P011)

**審查範圍**: PROPOSAL-009 / PROPOSAL-010 / PROPOSAL-011（draft）
**審查類型**: Draft proposal review（無實作對照；三份之間交叉一致性為核心）
**審查日期**: YYYY-MM-DD
**審查者**: Codex

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| {總數} | {數} | {數} | {數} | {數} |

## 按 Proposal 分類

| Proposal | Finding 數 | 建議下一步 |
|---|---|---|
| PROPOSAL-009 | N | approved / revise-and-resubmit / needs-clarification |
| PROPOSAL-010 | N | 同上 |
| PROPOSAL-011 | N | 同上 |

> 「建議下一步」是 Reviewer 的初判；最終 draft → approved 決定由 R7-Approve session 做，本輪不定。

## Findings

### F-01 ...

（按嚴重度排序：Critical → Major → Minor → Question；同嚴重度內按 proposal 編號 → 段落順序）

## 三份 Proposal 交叉一致性對照表

| 檢查點 | 主張方 | 對方回應 | 一致？ | 對應 Finding |
|---|---|---|---|---|
| C-01 · 011 留 placeholder ↔ 009 填齊 | 011 §2 | 009 §E 決議 11 | ✓ / ✗ / ⚠ | F-NN |
| C-02 · `git mv` 首次實踐歸屬 | 011 §1 | 009 §E | | |
| C-03 · feature 目錄規範 ↔ init 產出 | 009 §A | 010 §1 | | |
| C-04 · trunk-based 範本 ↔ 011 解耦 | 010 §2 | 011 §1 | | |
| C-05 · `_index.md` 範本歸屬 | 009 §影響範圍 | 010 §scaffolding 定位 | | |
| C-06 · 影響範圍加總 | 三份加總 | §C 現況檔 | | |
| C-07 · 依賴順序一致性 | 三份的依賴表 | field-reference-candidates.md Phase 3 | | |
| C-08 · 命令全景自洽 | 三份的命令描述 | — | | |

> ⚠ = 部分一致 / 存在歧義

## V2 對照備註（非 Finding，僅作理解記錄）

簡述 Codex 閱讀 V2 後對各 proposal 描述準確性的判斷（非 Finding，不計入 severity；若發現 proposal 對 V2 的描述有明顯錯誤，才升級為 Finding）。

## 審查備註

- （任何需要補充的觀察、審查流程中的限制等）

---

**跨輪議題**：若審查過程順手看到其他 proposal（P001-P008）或其他議題的問題，記入 `reviews/cross-round-notes.md`，**不佔本輪 Finding 數量**。
```

## 第五步：重要規則

- **不要修改任何檔案**。包括 proposal 檔、skill 檔、V2 儲存庫、review-workflow.md。只產出 `reviews/round-7-report.md`。
- **不要 review V2 本身**。V2 是背景閱讀用，不是標的。若發現 V2 有問題，**忽略**（V2 不是本 repo 的 skill 來源）。
- **不要比對英文 skill vs 繁中 skill**。繁中延到 Post-Review Closeout C1；本輪**不做**中英對照。
- **嚴重度從嚴**：Critical 保留給「無法實施 / 破壞既有結構」級別；不確定就標 Question。
- **交叉一致性是本輪重點**：Dimension 3 的 8 項檢查點（C-01 到 C-08）每一項都要在對照表給出 ✓ / ✗ / ⚠ 判斷 + 理由（若 ✗ 或 ⚠，對應 Finding）。
- **不要對 proposal 內提到但本 repo 未實施的檔案作存在性檢查**。例如 009 提到 `references/new-phase-flow.md`（新增檔），現況當然不存在——這不是 Finding，除非 proposal 聲稱檔案**已存在**但實際不存在。
- **工作量與複雜度判讀**：3 份合計 43 KB，加 V2 約 30 KB，預期審查需 60-90 分鐘；若超過 2 小時仍無法完成，可分 2 次進行（第一次聚焦 Dimension 1+2，第二次聚焦 Dimension 3+4）。

開始審查。完成後只產出 `reviews/round-7-report.md`。

---END PROMPT---
