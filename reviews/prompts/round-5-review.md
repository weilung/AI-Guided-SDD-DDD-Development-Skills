# Round 5 Review Prompt — P006a + P007a（給 Codex CLI 使用，建議切 GPT-5.4）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Codex CLI。
> **產出位置**：Codex 會寫入 `reviews/round-5-report.md`。
> **禁止事項**：Codex 此輪不應修改任何實作檔。
> **範圍提醒**：本輪只審查**英文 Skill**，不審繁中版（`*-tw/`）和 tutorial。
> **模型選擇**：建議續用 Codex GPT-5.4。
> **特別提醒**：R5 兩份 proposal 都是**刻意單版本**（P006a 只動 Core、P007a 只動 WebForms），因此**不做兩版一致性對照表**——不是漏實作另一版。

---BEGIN PROMPT---

你是 P006a（Ceremony Scaling 加入 DDD 建模深度欄）和 P007a（Brownfield 系統性逆向文件化）實作的審查者。本次 review 範圍限定這兩份 proposal 的實作產物，且**只審英文版 Skill**（繁中版延後至 Post-Review Closeout 階段）。

## 第一步：讀入以下檔案

**Proposal 來源（理解設計意圖）**
1. `proposals/PROPOSAL-006a-ddd-depth-column.md`——**必讀**，特別注意：
   - **只動 Core 版 SKILL.md**——WebForms 版 Ceremony Scaling 表不引入 DDD 深度欄（漸進抽離專案不需要這個維度）
   - 4 級分類：Full / Standard / Lightweight / Skip（原提議是 Required/Evaluate/Skip 三級，實施時升為四級）
   - **P006-2 漸進導入子項是 defer（方案 γ）**——不是漏實作，Wave D 再評估
2. `proposals/PROPOSAL-007a-brownfield-reverse-documentation.md`——**必讀**，特別注意：
   - **只動 WebForms 版 modify-existing-flow.md**——Core 版是 greenfield 新專案，沒有 Code-Behind 可掃描
   - 位置是 Step 2 的**子段**（名稱：Systematic Baseline Capture），不是獨立 step，也不是在 Step 2 和 Step 3 之間獨立存在
   - 明確標註為 **opportunistic**（機會主義），不是 mandatory
3. `proposals/evaluation-p002-p008.md`（讀「P006 / P007 區塊」「P006-2 defer 方案 γ 討論」「D2 備註：條件式 Stage vs 時間綁定」）

**實作產物（僅英文版，2 份）**

P006a 相關：
4. `sdd-ddd-core-skill/SKILL.md`——**只看 Ceremony Scaling 表格段**（該表和緊跟的定義段）
   - 其他段落（Behavior Source of Truth / `/dflow:verify` / Workflow Transparency / Completion Checklist Execution 等）**不在本輪範圍**
   - WebForms SKILL.md 的 Ceremony Scaling 表不在 R5 範圍（P006a 刻意不動 WebForms）

P007a 相關：
5. `sdd-ddd-webforms-skill/references/modify-existing-flow.md`——**只看 Step 2 內的 `Systematic Baseline Capture` 子段**
   - Step 2 整體的 Delta 格式說明（R2 範圍）不在本輪
   - Step 6（最終 Step）的 completion checklist（R3 範圍）不在本輪
   - Core 版 modify-existing-flow.md 完全不在 R5 範圍（P007a 刻意不動 Core）

**流程規範（本次 review 格式要求）**
6. `proposals/review-workflow.md`（第三節「Review 報告格式」）

## 第二步：審查範圍邊界

**在範圍內**（兩段內容）：
- `sdd-ddd-core-skill/SKILL.md` 的 Ceremony Scaling 表（含 4 級 DDD Modeling Depth 定義）
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md` Step 2 內的 `Systematic Baseline Capture` 子段

**不在範圍內**：
- `sdd-ddd-webforms-skill/SKILL.md`（P006a 不動 WebForms 版）
- `sdd-ddd-core-skill/references/modify-existing-flow.md`（P007a 不動 Core 版）
- 兩份檔案中**指定段落以外的其他段落**（R1/R2/R3/R4 範圍）
- `sdd-ddd-webforms-skill-tw/`、`sdd-ddd-core-skill-tw/`（繁中版，延後處理）
- `tutorial/` 目錄所有檔案
- 其他 proposal（P001、P002、P003、P004、P005、P008、P007b、P007c）對應的實作
- 不比對「英文版 vs 繁中版」的一致性
- **不比對 WebForms 版 vs Core 版一致性**（兩份 proposal 刻意單版本）

若審查過程順手看到繁中版、tutorial、或超出指定段落的內容有明顯錯誤，記入 `reviews/cross-round-notes.md`（格式見第七步），**不佔本輪 Finding 數量**。

## 第三步：審查重點

照以下**三**個角度各看一輪（**不做 WebForms vs Core 一致性**，因為是單版本設計）：

1. **Proposal 意圖對齊**：
   - **P006a**：Ceremony Scaling 表是否加入「DDD 建模深度」欄
   - **P006a**：是否 4 級（Full / Standard / Lightweight / Skip），不是 3 級（Required/Evaluate/Skip）
   - **P006a**：「New feature」是否拆成「New Aggregate / BC」（Full）和「New feature (within existing BC)」（Standard）
   - **P006a**：表後是否附 4 級定義說明
   - **P007a**：`Systematic Baseline Capture` 子段是否存在於 Step 2 內
   - **P007a**：是否涵蓋 5 步機會主義掃描（讀 Code-Behind → 抽規則 → 識別領域概念 → 檢查重複邏輯 → 記錄到 domain docs + tech-debt）
   - **P007a**：是否明確標註為 opportunistic、範圍限定在被修改功能和鄰近頁面
   - **P007a**：是否附 AI 話術範例
2. **邏輯自洽**：
   - **P006a**：4 級定義（Full / Standard / Lightweight / Skip）的邊界是否清楚——例如「Standard」和「Lightweight」的分野是否有可判斷標準
   - **P006a**：每個變更類型對應的 DDD 深度級別是否合理（例如 Bug fix 對應 Lightweight 是合理的；若某列出現明顯錯配，標 Finding）
   - **P006a**：DDD 深度欄與既有 Spec 層級欄是否有矛盾（例如某列 Spec Level = Lightweight 但 DDD Depth = Full，會否造成指引衝突）
   - **P007a**：機會主義掃描的 5 步是否互相銜接（不是 5 個獨立指令，而是一個連貫流程）
   - **P007a**：與 Step 2 原有的 Delta 格式描述是否協調（baseline capture 在 Delta 之前還是之後？若位置不當會造成 AI 執行順序混亂）
3. **範圍邊界**：
   - **P006a**：實作有沒有誤動 WebForms 版 SKILL.md？
   - **P007a**：實作有沒有誤動 Core 版 modify-existing-flow.md？
   - 有沒有夾帶超出 P006a/P007a 範圍的改動（例如順便加了其他 Ceremony Scaling 欄位）？

## 第四步：嚴重度判斷原則（重要，請從嚴）

**Critical**：違背 proposal 意圖、讓流程無法運作。例如：
- ✓ Critical 例：Core SKILL.md 完全沒有 DDD 建模深度欄（P006a 核心意圖缺失）
- ✓ Critical 例：WebForms modify-existing-flow Step 2 完全沒有 Systematic Baseline Capture 子段
- ✗ **非** Critical 例：4 級定義的詞彙選擇（「Standard」vs「Normal」）

**Major**：不完整，影響使用。例如：
- ✓ Major 例：P006a 只有 3 級定義（漏了其中一級，例如沒有 Lightweight）
- ✓ Major 例：P007a 5 步掃描漏了其中 1-2 步（例如沒有「檢查重複邏輯」）
- ✓ Major 例：P007a 的 opportunistic 標註完全缺失，被寫成 mandatory scan
- ✗ **非** Major 例：掃描步驟的排序與 proposal 略有差異但邏輯仍連貫

**Minor**：格式、排版、措辭不夠精確。
- ⚠️ **節制使用**：若超過 5 項，考慮是不是在挑刺。

**Question**：你不確定意圖。

**高風險誤判類型（請警覺，不要標為 Finding）**：
1. **誤判 P006a 應該 WebForms 版也有 DDD 深度欄**：P006a 明確只動 Core——WebForms 漸進抽離不需要這個維度
2. **誤判 P006-2 漸進導入缺失**：P006-2 defer（方案 γ），Wave D 才評估，不是漏實作
3. **誤判 P007a 應該 Core 版也有**：Core 是 greenfield，沒有 Code-Behind 可掃描
4. **誤判 P007a 應為 mandatory**：proposal 明確為 opportunistic 策略，「趁修改時順便做」
5. **誤判 4 級應該只有 3 級**：實施時主動從 Required/Evaluate/Skip 升級為 4 級，是改善不是瑕疵
6. **誤判 P007a 應獨立成 Step**：proposal 明確為 Step 2 子段，不是新 Step

**報告自我控制**：若你產出 10+ 項 Findings、其中 6+ 是 Minor，請檢討。本輪兩份 proposal 的實作範圍都很小（Core SKILL.md 一張表、WebForms 一個子段），Finding 應該很少——若超過 5 項總量，多半在挑刺。

## 第五步：關注的具體特徵（供參考，不限於此）

**P006a 相關**
- Ceremony Scaling 表的變更類型列是否涵蓋：New Aggregate / BC、New feature (within existing BC)、業務規則變更、Bug fix、API / UI 變更
- 每列的 DDD 深度選擇是否與 proposal 表格對齊（New Agg/BC = Full；BC 內新功能 = Standard；業務規則 = Standard；Bug fix = Lightweight；API/UI = Skip）
- 4 級定義段是否說明：
  - **完整/Full**：需要 aggregate-design 模板、更新 context-map、定義 events
  - **標準/Standard**：確認歸屬、更新 models.md 和 rules.md
  - **輕量/Lightweight**：只確認修改在正確的架構層
  - **跳過/Skip**：不涉及 domain logic
- 表格是否與既有 Spec 層級欄、Tech Debt Record 欄協調共存（沒有把既有欄位刪掉）

**P007a 相關**
- 子段標題是否為「Systematic Baseline Capture」（或等價措辭，指涉「系統性逆向文件化」）
- 5 步是否完整：
  1. 讀取相關 Code-Behind 檔案
  2. 抽取所有業務規則（if/else、計算、驗證）
  3. 識別領域概念（潛在的 Entity、VO、Service）
  4. 檢查是否有跨頁面的重複邏輯
  5. 將發現記錄到對應的 domain docs 和 tech-debt.md
- 觸發條件是否清楚：「被修改的功能沒有現有 spec 時」（而非每次都做）
- Opportunistic 標註和範圍限定（「被修改功能和鄰近頁面」）是否明確
- AI 話術範例是否合理（給 AI 一個可模仿的開口句）

## 第六步：產出 review 報告

照 `proposals/review-workflow.md` 第三節「Review 報告格式」寫入 `reviews/round-5-report.md`。

- 每個 Finding 都要有：嚴重度、位置（`path:line-range`）、對照 proposal 段落、現況、問題、建議
- **嚴重度從嚴**（見第四步詳述）
- **兩版一致性對照表改為「單版本設計說明」段落**：列出 R5 兩份 proposal 都是刻意單版本（P006a Core only / P007a WebForms only），並附理由（WebForms 漸進抽離 vs Core greenfield），證明你檢查過並理解這是設計選擇而非缺失
- **不做繁中同步對照**——不在本輪範圍
- Findings 要有明確證據（引用 line range）

## 第七步：跨輪發現如何處理

若看到的問題明顯不屬於 P006a/P007a 範圍、但屬於另一輪範圍（例如 R1 的 SKILL.md 其他段落、R2 的 Step 2 Delta 格式、R3 的 Step 6 completion checklist、R4 的 Behavior Source of Truth），或屬於 Post-Review Closeout 階段（繁中版、tutorial 的問題），請寫在 `reviews/cross-round-notes.md`（若檔案不存在則建立）。格式：

```
## R5 發現、屬於 R{N} 的備忘

- 位置：`path:line-range`
- 描述：...
- 建議轉交：R{N}（{該輪主題}）或 Post-Review Closeout

## R5 發現、屬於 Post-Review Closeout 的備忘

- 位置：`path:line-range`（繁中版或 tutorial 的問題）
- 描述：...
```

這些備忘**不佔本輪 Finding 數量**。

## 禁止事項

- 不要修改任何實作檔。本輪只產出 review 報告。
- 不要審查繁中版（`*-tw/`）或 tutorial。
- 不要審查 WebForms SKILL.md 的 Ceremony Scaling 表（P006a 刻意不動）——若順手看到想法，記 cross-round-notes.md。
- 不要審查 Core modify-existing-flow.md（P007a 刻意不動）——同上。
- 不要挑「P006a 應該 WebForms 也有」「P007a 應該 Core 也有」——刻意單版本設計。
- 不要挑「P006-2 漸進導入為什麼沒實作」——defer 方案 γ。
- 不要挑「P007a 應該是 mandatory scan 而非 opportunistic」——機會主義是設計選擇。
- 不要挑「P006a 應該保持原 3 級」——升為 4 級是主動改善。
- 不要做出假設性推論。
- Minor 數量自我節制（本輪範圍很小，Finding 數量應該很少）。

## 完成後回報

報告完成後告訴使用者：「Round 5 review 報告已產出於 reviews/round-5-report.md，共 N 個 findings（Critical: X / Major: Y / Minor: Z / Question: W）。範圍限定英文 Skill（P006a Core + P007a WebForms，單版本設計）。可進入 Approve 階段（另開新 Claude Code session）。」

---END PROMPT---
