# Round 4 Review Prompt — P003 + P008（給 Codex CLI 使用，建議切 GPT-5.4）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Codex CLI。
> **產出位置**：Codex 會寫入 `reviews/round-4-report.md`。
> **禁止事項**：Codex 此輪不應修改任何實作檔。
> **範圍提醒**：本輪只審查**英文 Skill**，不審繁中版（`*-tw/`）和 tutorial。
> **模型選擇**：建議續用 Codex GPT-5.4。

---BEGIN PROMPT---

你是 P003（系統 Source of Truth — behavior.md + A+C 結構）和 P008（Drift Verification — `/dflow:verify` 指令）實作的審查者。本次 review 範圍限定這兩份 proposal 的實作產物，不含其他 proposal，且**只審英文版 Skill**（繁中版延後至 Post-Review Closeout 階段）。

## 第一步：讀入以下檔案

**Proposal 來源（理解設計意圖）**
1. `proposals/PROPOSAL-003-system-source-of-truth.md`——**必讀**，特別注意：
   - A+C 結構：rules.md 是「索引」（BR-ID 宣告 + anchor 連結）、behavior.md 是「內容」（Given/When/Then 情境）
   - 與 OpenSpec 差異：按 Bounded Context 組織，不是按 capability
   - 不建 CLI 自動 merge，由 AI 引導
   - P003 實施為**簡化版**——「Phase 3 draft 段落」和「rules.md anchor last-updated 欄位」是 forward-looking（詳見 PROPOSAL-005b 實施紀錄說明）
2. `proposals/PROPOSAL-008-drift-verification.md`——**必讀**，特別注意：
   - 只做**機械層**三項檢查：BR-ID 對應 / anchor 有效性 / 反向檢查
   - **明確排除語意層**——LLM 讀描述判斷矛盾是 Wave D 候選
   - 指令 `/dflow:verify [<bc>]` 屬於 P001 的 `/dflow:` 前綴族
3. `proposals/PROPOSAL-002-delta-spec-format.md`（可選讀）——特別注意「重要澄清」：delta 標記只用於變更 spec，不累積到 behavior.md；behavior.md 讀取 delta 後 merge 成當前真相，不保留歷史軌跡
4. `proposals/evaluation-p002-p008.md`（讀「P003 區塊」「P008 區塊」+ A9 假設——確認簡化版決議脈絡和機械/語意層分工）

**實作產物（僅英文版，8 份）**

P003 相關：
5. `sdd-ddd-webforms-skill/templates/behavior.md`（新增，behavior.md 範本）
6. `sdd-ddd-core-skill/templates/behavior.md`（同上，Core 版補 Aggregate / Domain Events）
7. `sdd-ddd-webforms-skill/templates/context-definition.md`（應提及 behavior.md 存在）
8. `sdd-ddd-core-skill/templates/context-definition.md`（同上）

P008 相關：
9. `sdd-ddd-webforms-skill/references/drift-verification.md`（新增，5 步驟驗證流程）
10. `sdd-ddd-core-skill/references/drift-verification.md`（同上，Core 版含 events.md 交叉引用警告）

P003 + P008 共用：
11. `sdd-ddd-webforms-skill/SKILL.md`——**只看兩個範圍**：
    - `Behavior Source of Truth` 段落（P003 新增）
    - `/dflow:verify` 相關內容（P008 新增：description frontmatter、決策樹對應項、Slash Commands 段、Reference Files 表格）
    - 其他段落（Workflow Transparency 主段、Completion Checklist Execution 等）**不在本輪範圍**
12. `sdd-ddd-core-skill/SKILL.md`（同上）

**流程規範（本次 review 格式要求）**
13. `proposals/review-workflow.md`（第三節「Review 報告格式」）

## 第二步：審查範圍邊界

**在範圍內**：上述 8 份檔案的**指定部分**：
- templates/behavior.md（整份，新增檔案）
- templates/context-definition.md（關鍵業務規則段中 behavior.md 的提及）
- references/drift-verification.md（整份，新增檔案）
- SKILL.md 的 `Behavior Source of Truth` 段落 + `/dflow:verify` 相關內容

**不在範圍內**：
- `sdd-ddd-webforms-skill-tw/`、`sdd-ddd-core-skill-tw/`（繁中版，延後處理）
- `tutorial/` 目錄所有檔案
- `references/new-feature-flow.md` 和 `references/modify-existing-flow.md`（R3 已審 Step 8 / 最終 Step 的 behavior.md 更新項；本輪不重審）
- SKILL.md 中 `Behavior Source of Truth` 和 `/dflow:verify` 以外的段落（R1/R3 範圍）
- 其他 proposal（P001、P002、P004、P005、P007）對應的實作
- 不比對「英文版 vs 繁中版」的一致性

若審查過程順手看到繁中版、tutorial、或超出指定段落的內容有明顯錯誤，記入 `reviews/cross-round-notes.md`（格式見第七步），**不佔本輪 Finding 數量**。

## 第三步：審查重點

照以下四個角度各看一輪：

1. **Proposal 意圖對齊**：
   - **P003**：behavior.md template 是否提供初始結構（按功能區域組織、每 BR-ID 一個 section 含 Given/When/Then）
   - **P003**：A+C 分工是否在 SKILL.md 明確表達（rules.md 索引 vs behavior.md 內容）
   - **P003**：Core 版 behavior.md 是否額外包含 Aggregate 狀態轉換和 Domain Events
   - **P003**：context-definition.md 是否在適當位置提及 behavior.md
   - **P008**：drift-verification.md 是否提供**5 步驟流程**（找檔案 → 擷取 rules.md BR-ID → 擷取 behavior.md BR-ID → 交叉比對 → 回報）
   - **P008**：drift-verification.md 是否涵蓋**三項機械檢查**（正向 BR-ID 對應 / anchor 有效性 / 反向檢查）
   - **P008**：drift-verification.md 是否**明確排除語意層**（Wave D 候選）
   - **P008**：Core 版 drift-verification.md 是否包含 events.md 交叉引用警告
   - **P008**：SKILL.md 是否把 `/dflow:verify [<bc>]` 加入決策樹、Slash Commands 段、Reference Files 表格

2. **邏輯自洽**：
   - behavior.md template 的 maintenance 規則（merge/update/delete/rename）是否與 P002 的 delta 語義一致
   - behavior.md 是否明確表達「當前真相」而非歷史軌跡（與 P002 澄清一致）
   - drift-verification 的回報格式是否給出**可行動的建議**（例如「補 behavior.md」或「刪 rules.md」）
   - A+C 分工是否互相支持——rules.md 有 anchor 連結 → behavior.md 對應段落；反之亦然

3. **WebForms 版 vs Core 版一致性**：
   - A+C 結構和 5 步驟驗證流程兩版必須一致
   - 刻意差異（**不是** bug）：
     - Core 版 behavior.md template 補 Aggregate 狀態 + Domain Events
     - Core 版 drift-verification.md 含 events.md 交叉引用警告
     - WebForms 版沒有 events.md（Domain Events 是 Core 戰術模式）
   - 這些技術棧差異必須**明確標示**（加註解或獨立子章節），不是漏實作

4. **範圍邊界**：
   - 實作有沒有夾帶超出 P003/P008 範圍的改動？
   - 特別留意 SKILL.md：`/dflow:verify` 的描述是否混入其他 `/dflow:` 指令的改動（那是 R1 範圍）

## 第四步：嚴重度判斷原則（重要，請從嚴）

**Critical**：違背 proposal 意圖、讓流程無法運作。例如：
- ✓ Critical 例：drift-verification.md 完全沒有機械檢查的三項（整個驗證流程失效）
- ✓ Critical 例：SKILL.md 完全沒有 Behavior Source of Truth 段落（A+C 結構未宣告）
- ✗ **非** Critical 例：template 範例的 BR-ID 格式是 `BR-001` 還是 `BR001`（格式小差異）

**Major**：不完整或兩版不一致，影響使用。例如：
- ✓ Major 例：Core 版 behavior.md template 沒有 Aggregate 狀態轉換段（proposal 明列）
- ✓ Major 例：反向檢查（behavior.md BR-ID → rules.md）遺漏
- ✗ **非** Major 例：5 步驟流程排序是 1/2/3/4/5 還是 1→2→3→4→5 標記差異

**Minor**：格式、排版、措辭不夠精確。
- ⚠️ **節制使用**：若超過 5 項，考慮是不是在挑刺。

**Question**：你不確定意圖。

**高風險誤判類型（請警覺，不要標為 Finding）**：
1. **誤判 P008 應含語意層驗證**：P008 明確排除語意層，是 Wave D 候選
2. **誤判 P003 沒實作 B3/B4（Phase 3 draft、anchor last-updated）**：P003 是簡化版，這些是 forward-looking，不是漏實作
3. **誤判 behavior.md 應保留 Delta 歷史軌跡**：P002 澄清 behavior.md 是 consolidated 當前狀態，不累積歷史（歷史由 git 保留）
4. **誤判 Core 獨有的 events.md 應該 WebForms 也有**：WebForms 版沒 Domain Events 戰術模式是刻意差異
5. **誤判 A+C 應合併為單一檔案**：A+C 是 P003 核心設計，rules.md 和 behavior.md 分檔

**報告自我控制**：若你產出 15+ 項 Findings、其中 10+ 是 Minor，請檢討。寧可漏記 2 個爭議 Minor 讓 Approve 階段補抓。

## 第五步：關注的具體特徵（供參考，不限於此）

**P003 相關**
- `templates/behavior.md` 頂部是否說明 purpose、maintenance 規則、與 rules.md 的 A+C 關係
- 底部是否有 HTML comment 列出維護注意事項（merge/update/delete/rename 規則）
- 情境結構：每 BR-ID 一個 section，含 Given/When/Then
- Core 版 template 是否額外涵蓋：Aggregate 狀態轉換、Domain Events 列舉
- SKILL.md `Behavior Source of Truth` 段是否：
  - 解釋 rules.md（索引）+ behavior.md（內容）的 A+C 分工
  - 對比 OpenSpec 的 specs/ 目錄角色
  - Project Structure 樹狀圖加入 behavior.md
  - Templates 表格加入 behavior.md
- `templates/context-definition.md` 關鍵業務規則段是否提及 behavior.md（作為業務規則的情境補充）

**P008 相關**
- `references/drift-verification.md` 是否給完整 5 步驟：找檔案 → 擷取 rules.md BR-ID → 擷取 behavior.md BR-ID → 交叉比對 → 回報
- 三項機械檢查是否齊全：
  - 正向：rules.md 的 BR-* 在 behavior.md 中是否有對應情境
  - Anchor：rules.md 指向的 `behavior.md#段落` 是否存在
  - 反向：behavior.md 中引用的 BR-ID 是否在 rules.md 宣告
- 回報格式是否提供可行動建議（不只「發現問題」而是「建議：X 或 Y」）
- Core 版是否包含 events.md 交叉引用警告
- 明確排除語意層的說明是否保留（避免未來誤以為要做）
- SKILL.md 是否有：
  - `/dflow:verify [<bc>]` 的 description frontmatter
  - 決策樹對應項（「要驗證一致性時」分支）
  - Slash Commands 段完整指令說明
  - Reference Files 表格加入 `drift-verification.md`

## 第六步：產出 review 報告

照 `proposals/review-workflow.md` 第三節「Review 報告格式」寫入 `reviews/round-4-report.md`。

- 每個 Finding 都要有：嚴重度、位置（`path:line-range`）、對照 proposal 段落、現況、問題、建議
- **嚴重度從嚴**（見第四步詳述）
- **兩版一致性對照表**（WebForms vs Core）必須有
- **不做繁中同步對照**——不在本輪範圍
- Findings 要有明確證據（引用 line range）

## 第七步：跨輪發現如何處理

若看到的問題明顯不屬於 P003/P008 範圍、但屬於另一輪範圍（例如 R1 的 SKILL.md 其他 `/dflow:` 指令、R3 的 new-feature-flow Step 8.3 behavior.md 合併項），或屬於 Post-Review Closeout 階段（繁中版、tutorial 的問題），請寫在 `reviews/cross-round-notes.md`（若檔案不存在則建立）。格式：

```
## R4 發現、屬於 R{N} 的備忘

- 位置：`path:line-range`
- 描述：...
- 建議轉交：R{N}（{該輪主題}）或 Post-Review Closeout

## R4 發現、屬於 Post-Review Closeout 的備忘

- 位置：`path:line-range`（繁中版或 tutorial 的問題）
- 描述：...
```

這些備忘**不佔本輪 Finding 數量**。

## 禁止事項

- 不要修改任何實作檔。本輪只產出 review 報告。
- 不要審查繁中版（`*-tw/`）或 tutorial。
- 不要重審 SKILL.md 中 `Behavior Source of Truth` 和 `/dflow:verify` 以外的段落（R1/R3 範圍）。
- 不要重審 flow 檔（new-feature-flow、modify-existing-flow）的 Step 8 / 最終 Step 的 behavior.md 合併項（R3 範圍）——順手看到問題記 cross-round-notes.md。
- 不要評論 P003/P008 以外的 proposal 實作。
- 不要挑「P008 為什麼沒做語意層」——明確排除。
- 不要挑「P003 為什麼沒有 last-updated 欄位」——簡化版，forward-looking。
- 不要挑「behavior.md 為什麼不保留 Delta 歷史」——P002 澄清過。
- 不要做出假設性推論。
- Minor 數量自我節制。

## 完成後回報

報告完成後告訴使用者：「Round 4 review 報告已產出於 reviews/round-4-report.md，共 N 個 findings（Critical: X / Major: Y / Minor: Z / Question: W）。範圍限定英文 Skill（P003 + P008）。可進入 Approve 階段（另開新 Claude Code session）。」

---END PROMPT---
