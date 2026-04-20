# Round 3 Implement Prompt — P005a + P005b（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：修改實作檔 + 更新 `CHANGELOG.md` + 修改 proposal 的「實施紀錄」段（視需要）
> **禁止事項**：不做 decisions 沒列的改動。不自動 commit。
> **範圍提醒**：本輪只改英文 Skill，繁中版和 tutorial 延後至 Post-Review Closeout。

---BEGIN PROMPT---

這是 P005a（完成流程 Checklist — Wave A）和 P005b（完成流程 Checklist — Wave B）審查修正的**實作階段**（Round 3 / Implement）。

## 第一步：依序讀入下列檔案

1. `proposals/review-workflow.md`（本流程的規範——特別是第三節「Implementation 紀錄」格式、第七節 Post-Review Closeout 說明）
2. `reviews/round-3-decisions.md`（**主要工作依據**——列出所有已核准的修改）
3. `proposals/PROPOSAL-005a-completion-checklist-wave-a.md`（若 decisions 裡有 accept 項目對應 P005a）
4. `proposals/PROPOSAL-005b-completion-checklist-wave-b.md`（同上，對應 P005b）
   - **特別注意**：「實施期判斷」欄說明 `*(post-8.3)*` 設計選擇 + B3/B4 前瞻性
5. `reviews/round-3-report.md`——**僅在 decisions 的「具體改動」寫得不夠細、需要回頭看原始 finding 時才讀**。優先信任 decisions 文件。

若 `reviews/round-3-decisions.md` 不存在，回報使用者「找不到 R3 decisions 文件，是否 Approve 階段尚未完成？」然後停止。

## 第二步：本輪範圍邊界

**在範圍內**：decisions 文件裡標為 **accept** 的項目，全部應該只指向 6 份英文 Skill 檔的指定段落：
- `sdd-ddd-webforms-skill/references/new-feature-flow.md`（Step 8 全段）
- `sdd-ddd-core-skill/references/new-feature-flow.md`（Step 8 全段）
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`（Step 6 全段）
- `sdd-ddd-core-skill/references/modify-existing-flow.md`（Step 5 全段）
- `sdd-ddd-webforms-skill/SKILL.md`（`Completion Checklist Execution` 子節）
- `sdd-ddd-core-skill/SKILL.md`（同上）

**不在範圍內**：
- 繁中版（`*-tw/`）——延後至 Post-Review Closeout
- Tutorial（`tutorial/`）——延後至 Post-Review Closeout
- SKILL.md 中非 `Completion Checklist Execution` 子節的其他段落（R1 範圍）
- new-feature-flow.md 的 Step 5（R2 範圍）
- decisions 裡標為 reject / defer / out-of-scope 的項目——完全不動

若 decisions 文件裡某個 accept 項目意外指向繁中版、tutorial、或 R3 指定段落以外的部分，**停下來回報使用者**，不要執行。

## 第三步：逐項實施 Accept 項目

對 decisions 文件中標記為 **accept** 的每一項：

1. 讀出該 Finding 的「具體改動」指引
2. 實施改動——僅限指引列出的檔案、僅限指引描述的範圍
3. **P005a/P005b 的特別提醒**：
   - 保持四段分層結構（8.1 / 8.2 / 8.3 / 8.4 或 X.1 / X.2 / X.3）——不要合併或拆段
   - 保持 `*(post-8.3)*` 標籤機制——不要重構成獨立的 8.5 段
   - 保持兩版驗證項的刻意差異（WebForms 側重 System.Web 純淨度；Core 補 Aggregate / Domain Events / EF Fluent / 外部 NuGet 純淨度）
   - bug-fix `(full spec only)` 跳過條款保留
   - 執行順序描述（8.1 → 8.2 → 8.3 → 8.4）是 Skill 指引，不是程式碼約束——若 accept 要求「加強順序約束」，改寫成更明確的指引文字，不要加程式分支邏輯
4. 確認改動後回頭檢查：
   - 改動範圍是否完全符合「具體改動」描述
   - 有沒有意外影響到 decisions 沒提到的檔案或段落
   - WebForms 版改了，Core 版是否也需要對應改？（若 decisions 沒明說但明顯是共通機制，標為「疑似遺漏，回報使用者」而非擅自跟改）
   - **不要跟改繁中版**——即使看到對應段落有落差
5. 改動完成後，在心中（不寫入檔案）記錄：哪些檔案改了哪些行、有無疑問/意外發現

**平行 vs 循序**：多個 accept 項目若涉及同一檔案，請循序處理避免衝突；不同檔案可平行，但建議按 F-XX 編號順序。

## 第四步：處理 Reject 和 Defer 項目

**完全不動**。即使你覺得 Codex 的 Finding 是真問題、decisions 的 reject 理由不太對，也不修。

若實施過程發現 reject/defer 項目的情境在 accept 項目中有牽連，記在最終報告的「備註給下一輪」段落。

## 第五步：更新 CHANGELOG.md

所有 accept 項目實施完成後，在 `CHANGELOG.md` **最上方**新增一段：

```markdown
## YYYY-MM-DD — Round 3 審查修正：完成流程 Checklist（P005a + P005b）

**關聯 Proposal**: P005a, P005b
**審查輪次**: R3
**對應決議文件**: `reviews/round-3-decisions.md`

### 修正項目

- **F-01 ({嚴重度})**: {一句話描述問題} → {一句話描述修正}
- **F-02 ({嚴重度})**: ...
- ...（逐項列出所有 accept 的 F-XX）

### 影響檔案

- `path/to/file-1.md`
- `path/to/file-2.md`
- ...

### 繁中版同步狀態

延後至 Post-Review Closeout 階段統一處理（見 `proposals/review-workflow.md` 第七節）。
```

日期用今天實際日期。繁中版同步欄位內容固定為上述字串。

## 第六步：更新 proposal 的實施紀錄（視需要）

若某個 accept 項目本質是「補齊 proposal 原本要求但實作時遺漏的要素」，在對應 `proposals/PROPOSAL-005a.md` 或 `PROPOSAL-005b.md` 的「實施紀錄」段落追加一行：

```markdown
- **R3 審查修正（YYYY-MM-DD）**: F-XX — {一句話描述}
```

判斷原則：**實作有無變動 proposal 範圍**——有就更新紀錄，沒有就只寫 CHANGELOG。

## 第七步：完成後回報（不自動 commit）

實施完成後，告訴使用者：

> 「Round 3 implement 階段完成。
> - 共實施 N 項修正（F-XX, F-YY, ...）
> - 影響檔案：{列出}（均為英文 Skill）
> - CHANGELOG 已更新（繁中版同步延後至 Closeout 階段）
> - Proposal 實施紀錄已更新：{列出哪幾份；若無就說「無」}
> - 備註（若有）：{跨輪發現 / 疑似遺漏 / 其他注意事項}
>
> 請檢視後決定是否 commit。」

**不要主動 commit**。等使用者確認。

## 禁止事項

- **不做 decisions 沒列的改動**。
- **Reject 和 defer 項目完全不動**。
- **不重新評估 Finding**。
- **不自動 commit**。
- **不改 review 報告和 decisions 文件**。
- **不改繁中版（`*-tw/`）**——延後至 Post-Review Closeout。
- **不改 tutorial**。
- **不改 SKILL.md 中非 `Completion Checklist Execution` 子節的其他段落**（R1 範圍）。
- **不改 new-feature-flow.md 的 Step 5**（R2 範圍）。
- **不要重構 `*(post-8.3)*` 機制為獨立 8.5 段**——這是 P005b 刻意設計。
- **不要把執行順序 8.1 → 8.2 → 8.3 → 8.4 寫死成程式分支**——這是 Skill 指引。

## 跨輪發現如何處理

實施過程若發現屬於其他輪範圍的問題，記入 `reviews/cross-round-notes.md`。格式：

```
## R3 Implement 發現、屬於 R{N} 的備忘

- 位置：`path:line-range`
- 描述：...
- 建議轉交：R{N}（{該輪主題}）
- 記錄時間：YYYY-MM-DD
```

若發現繁中版或 tutorial 有對應落差，記入 Post-Review Closeout 區段：

```
## R3 Implement 發現、屬於 Post-Review Closeout 的備忘

- 位置：`path:line-range`（繁中版或 tutorial 檔）
- 描述：英文 Skill 的 F-XX 修正後，此處應同步更新
- 記錄時間：YYYY-MM-DD
```

---END PROMPT---
