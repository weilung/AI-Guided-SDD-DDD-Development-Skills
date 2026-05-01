# Round 4 Approve Prompt — P003 + P008（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：`reviews/round-4-decisions.md`
> **禁止事項**：此階段 AI 不直接改實作檔，只整理決議。
> **範圍提醒**：本輪只處理英文 Skill 的 findings。

---BEGIN PROMPT---

這是 P003（系統 Source of Truth — behavior.md + A+C 結構）和 P008（Drift Verification — `/dflow:verify`）實作審查的**決議階段**（Round 4 / Approve）。

## 第一步：依序讀入下列檔案

1. `proposals/review-workflow.md`（本流程的規範——特別是第一節角色分工、第三節 Approval 決議格式、第七節 Post-Review Closeout 說明）
2. `proposals/PROPOSAL-003-system-source-of-truth.md`（設計意圖來源）
   - **特別注意**：P003 採簡化版，Phase 3 draft / anchor last-updated 是 forward-looking
3. `proposals/PROPOSAL-008-drift-verification.md`（設計意圖來源）
   - **特別注意**：明確排除語意層
4. `proposals/PROPOSAL-002-delta-spec-format.md`（可選讀）——behavior.md 是 consolidated 當前狀態，不累積歷史
5. `proposals/evaluation-p002-p008.md`（P003/P008 區塊 + A9 假設）
6. `reviews/round-4-report.md`（Codex 剛產出的審查報告——主要分析對象）
7. `reviews/cross-round-notes.md`（若存在）——僅供參考

若 `reviews/round-4-report.md` 不存在，回報使用者「找不到 R4 review 報告，是否 Codex review 尚未完成？」然後停止。

## 第二步：本輪範圍邊界

**在範圍內**：`reviews/round-4-report.md` 的所有 Finding（應該只涉及 8 份檔案的指定部分）：
- templates/behavior.md（整份，新增檔案）
- templates/context-definition.md（behavior.md 提及段）
- references/drift-verification.md（整份，新增檔案）
- SKILL.md 的 `Behavior Source of Truth` 段 + `/dflow:verify` 相關內容

**不在範圍內**：
- `cross-round-notes.md` 裡跨輪項與 Closeout 項
- 繁中版、tutorial
- SKILL.md 中其他段落（R1/R3 範圍）
- flow 檔 Step 8 / 最終 Step 的 behavior.md 合併項（R3 已審）

若 Finding 超出指定範圍，**不要直接處理**——建議使用者轉記到 cross-round-notes.md，從本輪決議排除。

## 第三步：特別注意 Codex 可能的誤判（P003/P008 版）

由於 R4 可能使用 Codex GPT-5.4，請特別警覺以下**高風險誤判類型**：

1. **誤判 P008 應含語意層驗證**
   - 若 Codex 標「drift-verification 應該判斷 rules.md 和 behavior.md 描述是否矛盾」，**這是誤判**
   - P008 明確排除語意層，是 Wave D 候選
   - 建議：**reject**，理由指向 PROPOSAL-008「明確排除：語意層驗證」段

2. **誤判 P003 沒實作 B3/B4（Phase 3 draft、anchor last-updated）**
   - 若 Codex 挑「behavior.md template 沒有 Phase 3 draft 段落結構」或「rules.md anchor 沒有 last-updated 欄位」，**這是誤判**
   - P003 是簡化版，這些是 forward-looking——PROPOSAL-005b 實施紀錄「實施期判斷」欄明確說明
   - 建議：**reject**

3. **誤判 behavior.md 應保留 Delta 歷史軌跡**
   - 若 Codex 挑「behavior.md 為什麼沒記錄歷次 MODIFIED 對照」，**這是誤判**
   - P002 重要澄清：delta 標記僅用於變更 spec，不累積到 behavior.md；behavior.md 讀取 delta 後 merge 成當前真相
   - 建議：**reject**

4. **誤判 Core 獨有的 events.md 應該 WebForms 也有**
   - 若 Codex 把「WebForms 版 behavior.md template 沒有 Domain Events 段」標為不一致，**這是誤判**
   - WebForms 專案漸進式抽離業務邏輯，未必引入 Domain Events 戰術模式
   - 建議：**reject**

5. **誤判 A+C 結構應合併為單一檔案**
   - 若 Codex 建議「rules.md 和 behavior.md 合併以避免 drift 風險」，**這是誤判**
   - A+C 分檔是 P003 核心設計；drift 風險正是 P008 要解的問題
   - 建議：**reject**

6. **誤判 `/dflow:verify` 指令歸屬**
   - 若 Codex 標「`/dflow:verify` 應該在 Slash Commands 主段落列出而非獨立段」這類格式性 Finding，需視實際 SKILL.md 結構判斷
   - 若實作已在 Slash Commands 段列出，那就對了；若實作獨立成段且完整，也可接受——不是必要修正
   - 建議：**視情況 reject 或 Minor accept**

7. **過度 Minor**
   - 若 Codex 報告塞入 10+ 個純措辭級 Minor，可**批次 reject**

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
3. 說理由——reject 要具體指出誤判點
4. 若 accept，同步寫「具體改動指引」給 Implement session
5. 等使用者確認後處理下一項
6. **批次處理例外**：連續多個 Minor 同類型誤判可合併成批次 reject

互動範例：

> 「F-03：Codex 指出 drift-verification.md 沒有判斷 rules.md 描述 vs behavior.md 情境是否語意一致的步驟（Major）。
>
> 我的建議決議：**reject**（高風險誤判類型 1）。PROPOSAL-008「明確排除：語意層驗證」段明示語意層不在本 proposal 範圍，是 Wave D 候選。理由：機械層已能抓大部分 drift；語意層耗 token 且需要人類複核。
>
> 你確認這個決議嗎？或有不同看法？」

使用者回覆後再處理 F-04。

## 第六步：處理 Question 類 Finding

Question 表示 Codex 不確定。你的工作：
1. 讀 proposal 原文判斷疑問是否有根據
2. 若 proposal 本身模糊，說明哪裡模糊、給出解讀建議
3. 交給使用者拍板

## 第七步：全部 Finding 處理完後，產出 decisions 文件

寫入 `reviews/round-4-decisions.md`，格式照 `proposals/review-workflow.md` 第三節。

關鍵欄位：
- **決議彙總表**
- **Accept 項目的實作指引**：逐項寫「修改檔案」「具體改動」「注意事項」
- **Reject/Defer 項目備忘**（批次 reject 列出涵蓋哪些 F-XX）
- **Out-of-scope 項目**（已轉 cross-round-notes.md）
- **跨輪發現備忘**

## 禁止事項

- **不要修改任何實作檔**。
- **不要修改 review 報告**。
- **不要替使用者做決定**（批次 reject 除外，但仍需一次性確認）。
- **不要在單次回覆處理多個 Finding**（批次誤判類型除外）。
- **不要把繁中版、tutorial、或 R4 指定範圍以外的修改指引寫進 decisions 文件**。

## 完成後回報

> 「Round 4 approve 階段完成。
> - 共 N 個 findings 處理完畢（accept: X / reject: Y / defer: Z / clarify→XX: W / out-of-scope: V）
> - 批次 reject 項（若有）：F-XX, F-YY, ...
> - 決議文件：`reviews/round-4-decisions.md`
> - Out-of-scope 項目已轉記到 `reviews/cross-round-notes.md`（若有）
> - 可進入 Implement 階段（另開新 Claude Code session）。」

---END PROMPT---
