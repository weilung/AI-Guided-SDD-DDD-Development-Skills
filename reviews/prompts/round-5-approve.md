# Round 5 Approve Prompt — P006a + P007a（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：`reviews/round-5-decisions.md`
> **禁止事項**：此階段 AI 不直接改實作檔，只整理決議。
> **範圍提醒**：本輪只處理英文 Skill 的 findings。
> **特別提醒**：R5 兩份 proposal 是**刻意單版本**（P006a Core only / P007a WebForms only），Codex 若挑「另一版也該有」是誤判。

---BEGIN PROMPT---

這是 P006a（Ceremony Scaling 加入 DDD 建模深度欄）和 P007a（Brownfield 系統性逆向文件化）實作審查的**決議階段**（Round 5 / Approve）。

## 第一步：依序讀入下列檔案

1. `proposals/review-workflow.md`（本流程的規範——特別是第一節角色分工、第三節 Approval 決議格式、第七節 Post-Review Closeout 說明）
2. `proposals/PROPOSAL-006a-ddd-depth-column.md`（設計意圖來源）
   - **特別注意**：只動 Core；4 級分類；P006-2 defer（方案 γ）
3. `proposals/PROPOSAL-007a-brownfield-reverse-documentation.md`（設計意圖來源）
   - **特別注意**：只動 WebForms；opportunistic 策略；Step 2 子段位置
4. `proposals/evaluation-p002-p008.md`（P006/P007 區塊 + P006-2 defer 方案 γ 討論）
5. `reviews/round-5-report.md`（Codex 剛產出的審查報告——主要分析對象）
6. `reviews/cross-round-notes.md`（若存在）——僅供參考

若 `reviews/round-5-report.md` 不存在，回報使用者「找不到 R5 review 報告，是否 Codex review 尚未完成？」然後停止。

## 第二步：本輪範圍邊界

**在範圍內**：`reviews/round-5-report.md` 的所有 Finding（應該只涉及兩段內容）：
- `sdd-ddd-core-skill/SKILL.md` 的 Ceremony Scaling 表（含 4 級 DDD Modeling Depth 定義）
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md` Step 2 內的 `Systematic Baseline Capture` 子段

**不在範圍內**：
- `cross-round-notes.md` 裡跨輪項與 Closeout 項
- 繁中版、tutorial
- WebForms SKILL.md（P006a 刻意不動）
- Core modify-existing-flow.md（P007a 刻意不動）
- 兩份檔案中指定段落以外的其他段落（R1/R2/R3/R4 範圍）

若 Finding 超出指定範圍，**不要直接處理**——建議使用者轉記到 cross-round-notes.md，從本輪決議排除。

## 第三步：特別注意 Codex 可能的誤判（P006a/P007a 版）

由於 R5 可能使用 Codex GPT-5.4，請特別警覺以下**高風險誤判類型**：

1. **誤判 P006a 應該 WebForms 版也有 DDD 深度欄**
   - 若 Codex 標「WebForms 版 SKILL.md 的 Ceremony Scaling 表沒有 DDD 深度欄，與 Core 版不一致」，**這是誤判**
   - P006a 明確只動 Core——WebForms 漸進抽離不引入這個維度
   - 建議：**reject**，理由指向 PROPOSAL-006a 影響範圍表

2. **誤判 P007a 應該 Core 版也有**
   - 若 Codex 標「Core 版 modify-existing-flow.md 沒有 Systematic Baseline Capture」，**這是誤判**
   - P007a 明確只動 WebForms——Core 是 greenfield 新專案，沒有 Code-Behind 可掃描
   - 建議：**reject**

3. **誤判 P006-2 漸進導入缺失**
   - 若 Codex 標「漸進導入指引（Stage 1-4）缺失」，**這是誤判**
   - P006-2 defer 為方案 γ，Wave D 才評估；P006a 只含 DDD 深度欄
   - 建議：**reject**

4. **誤判 P007a 應為 mandatory scan**
   - 若 Codex 標「baseline capture 應強制執行而非 opportunistic」，**這是誤判**
   - Proposal 明確為機會主義策略（「趁修改時順便做」）；mandatory 會增加必要工作量
   - 建議：**reject**

5. **誤判 P006a 應保持原 3 級分類**
   - 若 Codex 標「Required/Evaluate/Skip 改為 Full/Standard/Lightweight/Skip 是未經授權的改動」，**這是誤判**
   - 實施紀錄明確說明主動升級為 4 級，是改善
   - 建議：**reject**

6. **誤判 P007a 應獨立成 Step**
   - 若 Codex 標「Systematic Baseline Capture 應獨立成 Step 2.5 或 Step 3」，**這是誤判**
   - Proposal 明確為 Step 2 子段
   - 建議：**reject**

7. **過度 Minor**
   - 若 Codex 報告塞入 5+ 個純措辭級 Minor，可**批次 reject**（R5 範圍極小，Minor 不應該多）

## 第四步：你的角色定位

你不是裁判。你是**分析助理**。

決議類型：

| 決議 | 何時採用 |
|---|---|
| **accept** | Finding 指出的是真問題 |
| **reject** | 誤判；能清楚說出誤判點 |
| **defer** | 真問題但現在不修 |
| **clarify** | 不確定，需釐清 |
| **out-of-scope** | 屬於繁中版、tutorial、或其他輪範圍 → 轉 cross-round-notes.md |

## 第五步：處理方式（一次一項，不 dump）

對報告的每個 Finding：

1. 重述核心（不超過兩句）
2. 說出建議決議
3. 說理由——reject 要具體指出誤判點（引用第三步編號）
4. 若 accept，同步寫「具體改動指引」給 Implement session
5. 等使用者確認後處理下一項
6. **批次處理例外**：連續多個同類型誤判可合併成批次 reject

互動範例：

> 「F-02：Codex 指出 WebForms 版 SKILL.md 的 Ceremony Scaling 表沒有 DDD 建模深度欄，與 Core 版不一致（Major）。
>
> 我的建議決議：**reject**（高風險誤判類型 1）。PROPOSAL-006a 影響範圍表明確只列 `sdd-ddd-core-skill/SKILL.md`，WebForms 版不在範圍內。設計理由：WebForms 是漸進抽離專案，Ceremony Scaling 的主要維度是 spec 層級，DDD 建模深度對尚未抽離完整 Domain 模型的 WebForms 程式碼不是有意義的指引。
>
> 你確認這個決議嗎？或有不同看法？」

使用者回覆後再處理 F-03。

## 第六步：處理 Question 類 Finding

Question 表示 Codex 不確定。你的工作：
1. 讀 proposal 原文判斷疑問是否有根據
2. 若 proposal 本身模糊，說明哪裡模糊、給出解讀建議
3. 交給使用者拍板

## 第七步：全部 Finding 處理完後，產出 decisions 文件

寫入 `reviews/round-5-decisions.md`，格式照 `proposals/review-workflow.md` 第三節。

關鍵欄位：
- **決議彙總表**
- **單版本設計備註段**：說明 R5 兩份 proposal 的刻意單版本性質，讓 Implement session 不會誤以為要「同步另一版」
- **Accept 項目的實作指引**：逐項寫「修改檔案」「具體改動」「注意事項」
- **Reject/Defer 項目備忘**（批次 reject 列出涵蓋哪些 F-XX）
- **Out-of-scope 項目**（已轉 cross-round-notes.md）
- **跨輪發現備忘**

## 禁止事項

- **不要修改任何實作檔**。
- **不要修改 review 報告**。
- **不要替使用者做決定**（批次 reject 除外，但仍需一次性確認）。
- **不要在單次回覆處理多個 Finding**（批次誤判類型除外）。
- **不要把繁中版、tutorial、或 R5 指定範圍以外的修改指引寫進 decisions 文件**。
- **不要把「WebForms/Core 版本的另一邊也要同步改」寫進 decisions 文件**——單版本是刻意設計。

## 完成後回報

> 「Round 5 approve 階段完成。
> - 共 N 個 findings 處理完畢（accept: X / reject: Y / defer: Z / clarify→XX: W / out-of-scope: V）
> - 批次 reject 項（若有）：F-XX, F-YY, ...
> - 決議文件：`reviews/round-5-decisions.md`
> - 單版本設計已在 decisions 文件中註明，Implement session 不會同步另一版
> - Out-of-scope 項目已轉記到 `reviews/cross-round-notes.md`（若有）
> - 可進入 Implement 階段（另開新 Claude Code session）。」

---END PROMPT---
