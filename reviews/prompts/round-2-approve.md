# Round 2 Approve Prompt — P002 + P004（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：`reviews/round-2-decisions.md`
> **禁止事項**：此階段 AI 不直接改實作檔，只整理決議。
> **範圍提醒**：本輪只處理英文 Skill 的 findings，繁中版和 tutorial 相關問題（若記在 cross-round-notes.md）延後至 Post-Review Closeout。

---BEGIN PROMPT---

這是 P002（Delta Spec 變更描述格式）和 P004（Spec 模板重構 — 精簡版）實作審查的**決議階段**（Round 2 / Approve）。

## 第一步：依序讀入下列檔案

1. `proposals/review-workflow.md`（本流程的規範——特別是第一節角色分工、第三節 Approval 決議格式、第七節 Post-Review Closeout 說明）
2. `proposals/PROPOSAL-002-delta-spec-format.md`（設計意圖來源）
   - **特別注意**：B1 RENAMED、B2 UNCHANGED（可選）是 evaluation 階段追加，不是原始提議內容
3. `proposals/PROPOSAL-004-spec-template-restructure.md`（設計意圖來源）
   - **特別注意**：「評估紀錄」欄明確標記採用**精簡版**——不是原始 proposal body 的五段式重構
4. `proposals/evaluation-p002-p008.md`（P002/P004 區塊 + 第二之二節 B1/B2 追加——確認精簡版和追加項決議脈絡）
5. `reviews/round-2-report.md`（Codex 剛產出的審查報告——主要分析對象）
6. `reviews/cross-round-notes.md`（若存在）——僅供參考，本輪不處理跨輪項與 Closeout 項

若 `reviews/round-2-report.md` 不存在，回報使用者「找不到 R2 review 報告，是否 Codex review 尚未完成？」然後停止。

## 第二步：本輪範圍邊界

**在範圍內**：`reviews/round-2-report.md` 的所有 Finding（應該只涉及 8 份英文 Skill 檔）。

**不在範圍內**：
- `cross-round-notes.md` 裡標為「屬於 R{N}」的項目 → 等那一輪處理
- `cross-round-notes.md` 裡標為「屬於 Post-Review Closeout」的項目 → 等六輪全部完成後處理
- 繁中版（`*-tw/`）或 tutorial 的任何問題

若發現 Finding 指向繁中版或 tutorial 的問題（Codex 不小心越界了），**不要直接處理**——建議使用者把該 Finding 轉記到 cross-round-notes.md 的 Post-Review Closeout 區段，從本輪決議排除。

## 第三步：特別注意 Codex 可能的誤判（P002/P004 版）

由於 R2 可能使用 Codex GPT-5.4（不同模型家族），請特別警覺以下**高風險誤判類型**：

1. **誤讀 P004 為原始五段式要求**
   - 若 Codex 標「feature-spec.md 沒有重構為一、Proposal / 二、領域分析 / ... 五段式」類 Finding，**這是誤判**
   - 精簡版（HTML comment + 實作任務段）才是 approved 版本
   - 建議：**reject**，理由指向 PROPOSAL-004 評估紀錄欄

2. **誤判 Gherkin 風格為不完整**
   - 若 Codex 挑「MODIFIED 範例沒有用 RFC 2119 的 SHALL/MUST」，**這是誤判**
   - P002 刻意維持 Given/When/Then，與 OpenSpec 偏 RFC 2119 的選擇不同
   - 建議：**reject**

3. **誤判「原本/改為」對照為贅餘**
   - 若 Codex 挑「MODIFIED 段落只需寫新版本，不需寫舊版」，**這是誤判**
   - 「原本/改為」對照是 P002 相對 OpenSpec 的刻意差異
   - 建議：**reject**

4. **WebForms 版 vs Core 版實作任務分類不同 = 誤判**
   - 若 Codex 把「WebForms 的 PAGE vs Core 的 API + INFRA」標為不一致，**這是誤判**
   - 這是刻意差異（技術棧本來不同），proposal 已明示
   - 建議：**reject**

5. **過度 Minor**
   - 若 Codex 報告塞入 10+ 個純措辭級 Minor（例如「HTML comment 後多空格」），可**批次 reject**，並在 decisions 文件說明「Minor 過度挑刺類批次駁回」

## 第四步：你的角色定位

你不是裁判。你是**分析助理**，幫使用者整理每個 Finding 的選項和你的建議，由使用者做最終決定。

每個 Finding 有四種可能決議：

| 決議 | 何時採用 |
|---|---|
| **accept** | Finding 指出的是真問題，應該修正 |
| **reject** | Finding 是誤判；你能清楚說出誤判在哪（例如 Codex 誤解 P004 精簡版決議、誤讀 P002 風格設計） |
| **defer** | 真問題，但現在不修（例如會干擾其他輪的審查、或等 Wave D 精簡化一起處理） |
| **clarify** | 你自己不確定，或需要使用者補充資訊才能判斷 |

**特殊決議 `out-of-scope`**：若 Finding 屬於繁中版、tutorial、或 SKILL.md 的範圍，記為 `out-of-scope → post-closeout / cross-round`，並建議使用者轉記到 cross-round-notes.md。

## 第五步：處理方式（一次一項，不 dump）

對報告的每個 Finding：

1. 重述 Finding 的核心（不超過兩句）
2. 說出你的**建議決議**（accept / reject / defer / clarify / out-of-scope）
3. 說**理由**——如果是 reject，要具體指出誤判點（引用 proposal 對應段落或第三步的高風險誤判類型編號）
4. **如果是 accept**，同步把「具體改動指引」寫給未來的 Implement session（改哪個檔案、改什麼行、改成什麼樣）
5. 等使用者確認你的建議（accept / reject / defer / clarify / out-of-scope）或給不同決議，確認後再處理下一項
6. **不要一次列完全部 Findings 等使用者逐一回覆**——這會造成 context 爆炸且使用者難追蹤。一次一項、走過才下一項。

**批次處理例外**：若連續多個 Minor 是同一類型的誤判（例如 Codex 連續挑 5 個「HTML comment 格式」），可以合併成一個「批次 reject」決議，省去逐項確認，但要明確列出涵蓋哪些 F-XX。

互動範例：

> 「F-02：Codex 指出 feature-spec.md 沒有重構為「一、Proposal / 二、領域分析 / ...」五段式（Critical）。
>
> 我的建議決議：**reject**（高風險誤判類型 1）。PROPOSAL-004 評估紀錄欄明確標記「採用精簡版 — 不改動現有段落結構」；精簡版實施了 HTML comment phase 標記 + 新增「實作任務」段。原始 proposal body 的五段式是被評估後主動捨棄的方案。
>
> 你確認這個決議嗎？或有不同看法？」

使用者回覆後再處理 F-03。

## 第六步：處理 Question 類 Finding

Question 表示 Codex 不確定。你的工作：

1. 讀 proposal 原文，判斷 Codex 的疑問是否有根據
2. 若 proposal 本身有模糊，說明哪裡模糊、給出你的解讀建議
3. 交給使用者拍板

Question 決議後可能進一步變成 accept / reject / defer。用 `clarify → accept` 的標記形式，保留審議軌跡。

## 第七步：全部 Finding 處理完後，產出 decisions 文件

寫入 `reviews/round-2-decisions.md`，格式照 `proposals/review-workflow.md` 第三節「Approval 決議格式」。

關鍵欄位：

- **決議彙總表**：Finding、嚴重度、決議、一句話說明
- **Accept 項目的實作指引**：逐項寫「修改檔案」「具體改動」「注意事項」——寫得要讓 Implement session 只讀這份 decisions 就能動手
- **Reject/Defer 項目備忘**：寫清楚理由（誤判在哪、為何延後）；若是批次 reject，列出涵蓋哪些 F-XX
- **Out-of-scope 項目**：列出並註明已轉記到 `cross-round-notes.md`
- **跨輪發現備忘**：若審議過程中發現影響其他輪的事項，記入 `reviews/cross-round-notes.md`

## 禁止事項

- **不要修改任何實作檔**。
- **不要修改 review 報告**。即使 Codex 寫錯了，在 decisions 文件裡標 reject 並說明。
- **不要替使用者做決定**。每個 Finding 你都應該等使用者確認（批次 reject 除外，但仍需一次性確認）。
- **不要在單次回覆處理多個 Finding**（批次誤判類型除外）。
- **不要把繁中版或 tutorial 的修改指引寫進 decisions 文件**。

## 完成後回報

全部 Findings 處理完、decisions 文件寫好後，告訴使用者：

> 「Round 2 approve 階段完成。
> - 共 N 個 findings 處理完畢（accept: X / reject: Y / defer: Z / clarify→XX: W / out-of-scope: V）
> - 批次 reject 項（若有）：F-XX, F-YY, ...
> - 決議文件：`reviews/round-2-decisions.md`
> - Out-of-scope 項目已轉記到 `reviews/cross-round-notes.md`（若有）
> - 可進入 Implement 階段（另開新 Claude Code session）。」

---END PROMPT---
