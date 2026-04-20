# Round 3 Approve Prompt — P005a + P005b（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：`reviews/round-3-decisions.md`
> **禁止事項**：此階段 AI 不直接改實作檔，只整理決議。
> **範圍提醒**：本輪只處理英文 Skill 的 findings，繁中版和 tutorial 相關問題延後至 Post-Review Closeout。

---BEGIN PROMPT---

這是 P005a（完成流程 Checklist — Wave A）和 P005b（完成流程 Checklist — Wave B）實作審查的**決議階段**（Round 3 / Approve）。

## 第一步：依序讀入下列檔案

1. `proposals/review-workflow.md`（本流程的規範——特別是第一節角色分工、第三節 Approval 決議格式、第七節 Post-Review Closeout 說明）
2. `proposals/PROPOSAL-005a-completion-checklist-wave-a.md`（設計意圖來源）
3. `proposals/PROPOSAL-005b-completion-checklist-wave-b.md`（設計意圖來源）
   - **特別注意**：「實施期判斷」一欄說明 `*(post-8.3)*` 設計選擇 + B3/B4 前瞻性
4. `proposals/PROPOSAL-003-behavior-md.md`（可選）——理解 P005b 依賴的 behavior.md 簡化版狀況
5. `proposals/evaluation-p002-p008.md`（B5 拆分 + B6 驗證分層）
6. `reviews/round-3-report.md`（Codex 剛產出的審查報告——主要分析對象）
7. `reviews/cross-round-notes.md`（若存在）——僅供參考，本輪不處理跨輪項與 Closeout 項

若 `reviews/round-3-report.md` 不存在，回報使用者「找不到 R3 review 報告，是否 Codex review 尚未完成？」然後停止。

## 第二步：本輪範圍邊界

**在範圍內**：`reviews/round-3-report.md` 的所有 Finding（應該只涉及 6 份檔案的指定段落）：
- new-feature-flow.md 的 Step 8 全段
- modify-existing-flow.md 的最終 Step 全段（WebForms Step 6、Core Step 5）
- SKILL.md 的 `Completion Checklist Execution` 子節

**不在範圍內**：
- `cross-round-notes.md` 裡標為「屬於 R{N}」的項目 → 等那一輪處理
- `cross-round-notes.md` 裡標為「屬於 Post-Review Closeout」的項目 → 等六輪全部完成後處理
- 繁中版（`*-tw/`）或 tutorial 的任何問題
- 6 份檔案中 R3 指定段落以外的內容（R1/R2 範圍）

若發現 Finding 超出指定段落（例如 Codex 順手評論了 SKILL.md 的 Workflow Transparency 主段落），**不要直接處理**——建議使用者把該 Finding 轉記到 cross-round-notes.md，從本輪決議排除。

## 第三步：特別注意 Codex 可能的誤判（P005a/P005b 版）

由於 R3 可能使用 Codex GPT-5.4，請特別警覺以下**高風險誤判類型**：

1. **誤判「post-8.3 應另開 8.5 段」**
   - 若 Codex 標「為什麼 post-merge 項放在 8.1 而不是新開一段」類 Finding，**這是誤判**
   - P005b「實施期判斷」欄明確說明：選擇用 `*(post-8.3)*` 標籤嵌入 8.1，避免多出 8.5 段使 checklist 結構變胖
   - 建議：**reject**，理由指向 PROPOSAL-005b 實施紀錄

2. **誤判「behavior.md 機械結構不存在」是 bug**
   - 若 Codex 標「behavior.md 的 last-updated 欄位在 P003 實作中不存在，8.1 的項目無法驗證」類 Finding，**這是誤判**
   - P005b 明確是前瞻性設計——P003 簡化版先到位，等後續 B3/B4 補強時 checklist 文字無需再改
   - 建議：**reject**

3. **誤判兩版驗證項技術棧差異為不一致**
   - 若 Codex 把「WebForms 8.1 沒有 Aggregate invariants 而 Core 有」標為不一致，**這是誤判**
   - 這是刻意差異（WebForms 專案未必有 Aggregate 戰術模式）
   - 建議：**reject**

4. **誤讀 bug-fix 跳過項為不完整**
   - 若 Codex 挑「modify-existing-flow bug-fix 模式的 X.1 沒有列完整驗證項」，**這是誤判**
   - bug-fix 搭配 lightweight-spec 時 `(full spec only)` 項正確跳過是 P005a 明列設計
   - 建議：**reject**

5. **誤把執行順序嚴格性當成程式碼限制要求**
   - 若 Codex 挑「為什麼沒有機制強制 8.1 執行完才能到 8.2」，**這是誤判**
   - 這是 Skill 指引（AI 行為規範），不是程式碼分支約束
   - 建議：**reject**

6. **過度 Minor**
   - 若 Codex 報告塞入 10+ 個純措辭級 Minor，可**批次 reject**，並在 decisions 文件說明

## 第四步：你的角色定位

你不是裁判。你是**分析助理**。

決議類型：

| 決議 | 何時採用 |
|---|---|
| **accept** | Finding 指出的是真問題 |
| **reject** | 誤判；能清楚說出誤判點（引用第三步編號） |
| **defer** | 真問題但現在不修 |
| **clarify** | 不確定，需釐清 |
| **out-of-scope** | 屬於繁中版、tutorial、或其他輪範圍 → 轉 cross-round-notes.md |

## 第五步：處理方式（一次一項，不 dump）

對報告的每個 Finding：

1. 重述核心（不超過兩句）
2. 說出建議決議
3. 說理由——reject 要具體指出誤判點
4. 若 accept，同步寫「具體改動指引」給 Implement session
5. 等使用者確認後處理下一項
6. **批次處理例外**：若連續多個 Minor 是同類型的誤判，可合併成一個「批次 reject」決議

互動範例：

> 「F-04：Codex 指出 `*(post-8.3)*` 標籤在 8.1 項目中使結構混亂，建議把兩項獨立為新的 8.5 段（Major）。
>
> 我的建議決議：**reject**（高風險誤判類型 1）。PROPOSAL-005b 實施紀錄「實施期判斷」欄明確說明：選擇用 `*(post-8.3)*` 標籤嵌入 8.1 是刻意設計，避免多出一個 8.5 段使 checklist 結構變胖。SKILL.md 的描述段也明確標示兩類時機（合併前 / 8.3 後）。
>
> 你確認這個決議嗎？或有不同看法？」

使用者回覆後再處理 F-05。

## 第六步：處理 Question 類 Finding

Question 表示 Codex 不確定。你的工作：
1. 讀 proposal 原文判斷疑問是否有根據
2. 若 proposal 本身模糊，說明哪裡模糊、給出解讀建議
3. 交給使用者拍板

Question 決議後可能變 accept / reject / defer（`clarify → accept` 形式保留審議軌跡）。

## 第七步：全部 Finding 處理完後，產出 decisions 文件

寫入 `reviews/round-3-decisions.md`，格式照 `proposals/review-workflow.md` 第三節「Approval 決議格式」。

關鍵欄位：
- **決議彙總表**：Finding、嚴重度、決議、一句話說明
- **Accept 項目的實作指引**：逐項寫「修改檔案」「具體改動」「注意事項」
- **Reject/Defer 項目備忘**：寫清楚理由（誤判在哪、為何延後）；批次 reject 列出涵蓋哪些 F-XX
- **Out-of-scope 項目**：列出並註明已轉記到 `cross-round-notes.md`
- **跨輪發現備忘**：若審議過程中發現影響其他輪的事項，記入 `reviews/cross-round-notes.md`

## 禁止事項

- **不要修改任何實作檔**。
- **不要修改 review 報告**。
- **不要替使用者做決定**（批次 reject 除外，但仍需一次性確認）。
- **不要在單次回覆處理多個 Finding**（批次誤判類型除外）。
- **不要把繁中版、tutorial、或 R3 指定段落以外的修改指引寫進 decisions 文件**。

## 完成後回報

> 「Round 3 approve 階段完成。
> - 共 N 個 findings 處理完畢（accept: X / reject: Y / defer: Z / clarify→XX: W / out-of-scope: V）
> - 批次 reject 項（若有）：F-XX, F-YY, ...
> - 決議文件：`reviews/round-3-decisions.md`
> - Out-of-scope 項目已轉記到 `reviews/cross-round-notes.md`（若有）
> - 可進入 Implement 階段（另開新 Claude Code session）。」

---END PROMPT---
