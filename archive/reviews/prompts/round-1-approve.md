# Round 1 Approve Prompt — P001 + P007b（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：`reviews/round-1-decisions.md`
> **禁止事項**：此階段 AI 不直接改實作檔，只整理決議。
> **範圍提醒**：本輪只處理英文 Skill 的 findings，繁中版和 tutorial 相關問題（若 Codex 記在 cross-round-notes.md）延後至 Post-Review Closeout 階段。

---BEGIN PROMPT---

這是 P001（Workflow Transparency）和 P007b（PR Review Spec Intent）實作審查的**決議階段**（Round 1 / Approve）。

## 第一步：依序讀入下列檔案

1. `proposals/review-workflow.md`（本流程的規範——特別是第一節角色分工、第三節 Approval 決議格式、第七節 Post-Review Closeout 說明）
2. `proposals/PROPOSAL-001-workflow-transparency.md`（設計意圖來源）
3. `proposals/PROPOSAL-007b-pr-review-spec-intent.md`(設計意圖來源)
4. `reviews/round-1-report.md`（Codex 剛產出的審查報告——主要分析對象）
5. `reviews/cross-round-notes.md`（若存在）——僅供參考，本輪不處理跨輪項與 Closeout 項

若 `reviews/round-1-report.md` 不存在，回報使用者「找不到 R1 review 報告，是否 Codex review 尚未完成？」然後停止。

## 第二步：本輪範圍邊界

**在範圍內**：`reviews/round-1-report.md` 的所有 Finding（應該只涉及英文 Skill 的 8 份檔案）。

**不在範圍內**：
- `cross-round-notes.md` 裡標為「屬於 R{N}」的項目 → 等那一輪處理
- `cross-round-notes.md` 裡標為「屬於 Post-Review Closeout」的項目 → 等六輪全部完成後處理
- 繁中版（`*-tw/`）或 tutorial 的任何問題

若你發現 review 報告裡有 Finding 指向繁中版或 tutorial 的問題（Codex 不小心越界了），**不要直接處理**——建議使用者把該 Finding 轉記到 cross-round-notes.md 的 Post-Review Closeout 區段，從本輪決議排除。

## 第三步：你的角色定位

你不是裁判。你是**分析助理**，幫使用者整理每個 Finding 的選項和你的建議，由使用者做最終決定。

每個 Finding 有四種可能決議：

| 決議 | 何時採用 |
|---|---|
| **accept** | Finding 指出的是真問題，應該修正 |
| **reject** | Finding 是誤判；你能清楚說出誤判在哪（例如 Codex 誤解 proposal 意圖、或 Codex 沒讀到某段已涵蓋的指引） |
| **defer** | 真問題，但現在不修（例如會干擾其他輪的審查、或等 Wave D 精簡化一起處理） |
| **clarify** | 你自己不確定，或需要使用者補充資訊才能判斷 |

**特殊決議 `out-of-scope`**：若 Finding 本身屬於繁中版或 tutorial 範圍，記為 `out-of-scope → post-closeout`，並建議使用者轉記到 cross-round-notes.md。

## 第四步：處理方式（一次一項，不 dump）

對報告的每個 Finding：

1. 重述 Finding 的核心（不超過兩句）
2. 說出你的**建議決議**（accept / reject / defer / clarify / out-of-scope）
3. 說**理由**——如果是 reject，要具體指出誤判點（例如「Codex 認為 X 段落缺 Y 規則，但 Y 規則其實在 Z 檔案的 W 段落有涵蓋」）
4. **如果是 accept**，同步把「具體改動指引」寫給未來的 Implement session（改哪個檔案、改什麼行、改成什麼樣）
5. 等使用者確認你的建議（accept / reject / defer / clarify / out-of-scope）或給不同決議，確認後再處理下一項
6. **不要一次列完全部 Findings 等使用者逐一回覆**——這會造成 context 爆炸且使用者難追蹤。一次一項、走過才下一項。

互動範例：

> 「F-03：Codex 指出 Core 版 SKILL.md 缺少 `/dflow:cancel` 指令說明（Major）。
>
> 我的建議決議：**accept**。我讀過 Core SKILL.md 第 120–180 行的 Workflow Transparency 章節，確認只列出六個指令，`/dflow:cancel` 遺漏。WebForms 版有完整七個指令。
>
> 若 accept，具體改動指引會是：`sdd-ddd-core-skill/SKILL.md` 的指令清單段落加入 `/dflow:cancel` 一行，格式參照 WebForms 版第 NN 行。
>
> 你確認這個決議嗎？或有不同看法？」

使用者回覆後再處理 F-04。

## 第五步：處理 Question 類 Finding

Question 表示 Codex 不確定。你的工作：

1. 讀 proposal 原文，判斷 Codex 的疑問是否有根據
2. 若 proposal 本身有模糊，說明哪裡模糊、給出你的解讀建議
3. 交給使用者拍板（接受你的解讀、或給不同解讀、或回去改 proposal）

Question 決議後可能進一步變成 accept / reject / defer。用 `clarify → accept` 的標記形式，保留審議軌跡。

## 第六步：全部 Finding 處理完後，產出 decisions 文件

寫入 `reviews/round-1-decisions.md`，格式照 `proposals/review-workflow.md` 第三節「Approval 決議格式」。

關鍵欄位：

- **決議彙總表**：Finding、嚴重度、決議、一句話說明
- **Accept 項目的實作指引**：逐項寫「修改檔案」「具體改動」「注意事項」——寫得要**讓 Implement session 只讀這份 decisions 就能動手，不用再回去翻 review 報告**
- **Reject/Defer 項目備忘**：寫清楚理由（誤判在哪、為何延後），這是審議軌跡
- **Out-of-scope 項目**：列出並註明已轉記到 `cross-round-notes.md` 的 Post-Review Closeout 區段
- **跨輪發現備忘**：若審議過程中你發現任何可能影響其他輪的事項，記入 `reviews/cross-round-notes.md`（不放在 decisions 文件內）

## 禁止事項

- **不要修改任何實作檔**。決議階段只產出 decisions 文件。
- **不要修改 review 報告**。即使 Codex 寫錯了，在 decisions 文件裡標 reject 並說明，不動原報告。
- **不要替使用者做決定**。每個 Finding 你都應該等使用者確認——即使是你信心很高的 accept，也要問一句。
- **不要在單次回覆處理多個 Finding**。慢就是快。
- **不要把繁中版或 tutorial 的修改指引寫進 decisions 文件**。這類項目標為 out-of-scope，轉交 Closeout 階段。

## 完成後回報

全部 Findings 處理完、decisions 文件寫好後，告訴使用者：

> 「Round 1 approve 階段完成。
> - 共 N 個 findings 處理完畢（accept: X / reject: Y / defer: Z / clarify→XX: W / out-of-scope: V）
> - 決議文件：`reviews/round-1-decisions.md`
> - Out-of-scope 項目已轉記到 `reviews/cross-round-notes.md`（若有）
> - 可進入 Implement 階段（另開新 Claude Code session）。」

---END PROMPT---
