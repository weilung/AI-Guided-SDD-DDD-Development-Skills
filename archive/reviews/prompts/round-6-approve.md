# Round 6 Approve Prompt — P007c（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：`reviews/round-6-decisions.md`
> **禁止事項**：此階段 AI 不直接改實作檔，只整理決議。
> **範圍提醒**：本輪範圍極小，Findings 應該很少；`templates/CLAUDE.md` 沒有繁中對應版，不進入 Post-Review Closeout TW 同步範圍。

---BEGIN PROMPT---

這是 P007c（CLAUDE.md 模板標題分段強化）實作審查的**決議階段**（Round 6 / Approve）。這是 R1–R6 的**最終輪**。

## 第一步：依序讀入下列檔案

1. `proposals/review-workflow.md`（本流程的規範——特別是第一節角色分工、第三節 Approval 決議格式、第七節 Post-Review Closeout 說明）
2. `proposals/PROPOSAL-007c-claude-md-segmentation.md`（設計意圖來源）
   - **特別注意**：不需要繁中同步；Core 版獨有「架構」段
3. `reviews/round-6-report.md`（Codex 剛產出的審查報告——主要分析對象）
4. `reviews/cross-round-notes.md`（若存在）——僅供參考，本輪不處理跨輪項與 Closeout 項

若 `reviews/round-6-report.md` 不存在，回報使用者「找不到 R6 review 報告，是否 Codex review 尚未完成？」然後停止。

## 第二步：本輪範圍邊界

**在範圍內**：`reviews/round-6-report.md` 的所有 Finding（應該只涉及兩份 `templates/CLAUDE.md`）。

**不在範圍內**：
- `cross-round-notes.md` 裡跨輪項與 Closeout 項
- 其他 proposal 對應的實作
- 任何非 `templates/CLAUDE.md` 的檔案

若 Finding 超出指定範圍，**不要直接處理**——建議使用者轉記到 cross-round-notes.md，從本輪決議排除。

## 第三步：特別注意 Codex 可能的誤判（P007c 版）

由於 R6 可能使用 Codex GPT-5.4，請特別警覺以下**高風險誤判類型**：

1. **誤判應有繁中版**
   - 若 Codex 標「templates/CLAUDE.md 沒有繁中對應版」，**這是誤判**
   - Proposal 實施紀錄明示「不需要繁中同步」——這是給 AI 讀的英文模板
   - 建議：**reject**

2. **誤判 WebForms 版應該也有「架構」段**
   - 若 Codex 把「WebForms 版 `## 系統脈絡` 下沒有架構段」標為不一致，**這是誤判**
   - Core 獨有 Clean Architecture 四層，WebForms 漸進抽離中，未必有固定架構段
   - 建議：**reject**

3. **誤判原 H2 應保留為 H2**
   - 若 Codex 標「H2 段落被降級為 H3 是否合適」類 Finding，**這是誤判**
   - Proposal 明確要求降級；新結構下 H2 只有兩大分類
   - 建議：**reject**

4. **評論 proposal 本身值不值得做**
   - 若 Codex 標「標題分段是否真的幫助 AI 讀取」類評論，**這是越界**
   - Review 只看實作是否符合 proposal，不評論 proposal 本身價值
   - 建議：**reject** 並在理由註明「此為 proposal 本身評估，不屬於 review 範圍」

5. **誤判雙語標題應該改為純英文**
   - 若 Codex 標「`## 系統脈絡（What is this system?）` 應簡化為純英文」，**這是誤判**
   - 實施紀錄明示採用雙語形式
   - 建議：**reject**

6. **過度 Minor**
   - 若 Codex 報告塞入 3+ 個純措辭級 Minor，可**批次 reject**——R6 範圍極小，Minor 不應該多

## 第四步：你的角色定位

你不是裁判。你是**分析助理**。

決議類型：

| 決議 | 何時採用 |
|---|---|
| **accept** | Finding 指出的是真問題 |
| **reject** | 誤判；能清楚說出誤判點 |
| **defer** | 真問題但現在不修 |
| **clarify** | 不確定，需釐清 |
| **out-of-scope** | 屬於其他輪範圍 → 轉 cross-round-notes.md |

## 第五步：處理方式（一次一項，不 dump）

對報告的每個 Finding：

1. 重述核心（不超過兩句）
2. 說出建議決議
3. 說理由——reject 要具體指出誤判點（引用第三步編號）
4. 若 accept，同步寫「具體改動指引」給 Implement session
5. 等使用者確認後處理下一項
6. **批次處理例外**：連續多個同類型誤判可合併成批次 reject

**Finding 數量為 0 的情境**：若 review 報告明確結論「本輪未發現問題」且 Finding 列表為空，跳過第五步互動，直接進入第六步產出空決議文件（或簡潔宣告無需 Implement session）。

互動範例：

> 「F-01：Codex 指出兩份 `templates/CLAUDE.md` 沒有繁中對應版，建議補上 `templates/CLAUDE_tw.md`（Minor）。
>
> 我的建議決議：**reject**（高風險誤判類型 1）。PROPOSAL-007c 實施紀錄明示「不需要繁中同步」——templates/CLAUDE.md 是給 AI 讀的英文模板（當開發者在新專案 copy 為 CLAUDE.md 後，AI 會讀取），本來就沒有繁中版的必要性。
>
> 你確認這個決議嗎？或有不同看法？」

使用者回覆後再處理 F-02。

## 第六步：處理 Question 類 Finding

Question 表示 Codex 不確定。你的工作：
1. 讀 proposal 原文判斷疑問是否有根據
2. 若 proposal 本身模糊，說明哪裡模糊、給出解讀建議
3. 交給使用者拍板

## 第七步：全部 Finding 處理完後，產出 decisions 文件

寫入 `reviews/round-6-decisions.md`，格式照 `proposals/review-workflow.md` 第三節。

關鍵欄位：
- **決議彙總表**（若 N=0 寫「本輪無 Finding，無需 Implement session」）
- **Accept 項目的實作指引**：逐項寫「修改檔案」「具體改動」「注意事項」
- **Reject/Defer 項目備忘**（批次 reject 列出涵蓋哪些 F-XX）
- **Out-of-scope 項目**（已轉 cross-round-notes.md）
- **跨輪發現備忘**
- **最終輪備註段**：提醒使用者 R6 是最終輪，下一步進入 R1–R6 全域一致性 sweep（見 `proposals/review-workflow.md` 第七節 7.1 節）

## 禁止事項

- **不要修改任何實作檔**。
- **不要修改 review 報告**。
- **不要替使用者做決定**（批次 reject 除外，但仍需一次性確認）。
- **不要在單次回覆處理多個 Finding**（批次誤判類型除外）。
- **不要把「templates/CLAUDE.md 應該有繁中版」寫進 decisions 文件**——proposal 明示無需。

## 完成後回報

> 「Round 6 approve 階段完成。這是 R1–R6 的最終輪。
> - 共 N 個 findings 處理完畢（accept: X / reject: Y / defer: Z / clarify→XX: W / out-of-scope: V）
> - 批次 reject 項（若有）：F-XX, F-YY, ...
> - 決議文件：`reviews/round-6-decisions.md`
> - Out-of-scope 項目已轉記到 `reviews/cross-round-notes.md`（若有）
> - 下一步：{N > 0：進入 R6 Implement 階段後進行全域 sweep} / {N = 0：直接進入 R1–R6 全域一致性 sweep}
> - 全域 sweep 完成後才進入 Post-Review Closeout（TW 同步 + Tutorial 重建，見 review-workflow.md 第七節）。」

---END PROMPT---
