# Round 1 Implement Prompt — P001 + P007b（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：修改實作檔 + 更新 `CHANGELOG.md` + 修改 proposal 的「實施紀錄」段（視需要）
> **禁止事項**：不做 decisions 沒列的改動。不自動 commit。

---BEGIN PROMPT---

這是 P001（Workflow Transparency）和 P007b（PR Review Spec Intent）審查修正的**實作階段**（Round 1 / Implement）。

## 第一步：依序讀入下列檔案

1. `proposals/review-workflow.md`（本流程的規範——特別是第三節「Implementation 紀錄」格式）
2. `reviews/round-1-decisions.md`（**主要工作依據**——列出所有已核准的修改）
3. `proposals/PROPOSAL-001-workflow-transparency.md`（若 decisions 裡有 accept 項目對應 P001，用於確認 proposal 意圖）
4. `proposals/PROPOSAL-007b-pr-review-spec-intent.md`（同上，對應 P007b）
5. `reviews/round-1-report.md`——**僅在 decisions 的「具體改動」寫得不夠細、需要回頭看原始 finding 時才讀**。優先信任 decisions 文件。

若 `reviews/round-1-decisions.md` 不存在，回報使用者「找不到 R1 decisions 文件，是否 Approve 階段尚未完成？」然後停止。

## 第二步：逐項實施 Accept 項目

對 decisions 文件中標記為 **accept** 的每一項：

1. 讀出該 Finding 的「具體改動」指引
2. 實施改動——僅限指引列出的檔案、僅限指引描述的範圍
3. 確認改動後回頭檢查：
   - 改動範圍是否完全符合「具體改動」描述（沒多改、沒少改）
   - 有沒有意外影響到 decisions 沒提到的檔案（例如兩版共通段落跟進）
   - WebForms 版改了，Core 版是否也需要對應改？（若 decisions 沒明說但明顯是共通機制，標為「疑似遺漏，回報使用者」而非擅自跟改）
4. 改動完成後，在心中（不寫入檔案）記錄：
   - 哪些檔案改了哪些行
   - 若有疑問、意外發現，累積下來

**平行 vs 循序**：多個 accept 項目若涉及同一檔案，請循序處理避免衝突；涉及不同檔案可平行處理，但建議仍按 F-XX 編號順序（F-01、F-02...）方便使用者追蹤。

## 第三步：處理 Reject 和 Defer 項目

**完全不動**。即使你覺得 Codex 的 Finding 是真問題、decisions 的 reject 理由不太對，也不修。你的工作是執行核可清單，不是重新審議。

若實施過程發現 reject/defer 項目的情境在 accept 項目中有牽連（例如修 F-01 時發現 F-03 [rejected] 指出的檔案段落確實會受影響），記在最終報告的「備註給下一輪」段落，不動原檔。

## 第四步：更新 CHANGELOG.md

所有 accept 項目實施完成後，在 `CHANGELOG.md` **最上方**（# Changelog 標題之後、最新版本之前）新增一段：

```markdown
## YYYY-MM-DD — Round 1 審查修正：流程入口與透明度（P001 + P007b）

**關聯 Proposal**: P001, P007b
**審查輪次**: R1
**對應決議文件**: `reviews/round-1-decisions.md`

### 修正項目

- **F-01 ({嚴重度})**: {一句話描述問題} → {一句話描述修正}
- **F-02 ({嚴重度})**: ...
- ...（逐項列出所有 accept 的 F-XX）

### 影響檔案

- `path/to/file-1.md`
- `path/to/file-2.md`
- ...

### 繁中版同步狀態

- {已同步 / 已在同輪修正中處理 / 後續作業（具體說明）}
```

日期用今天實際日期（開 session 時的日期）。

## 第五步：更新 proposal 的實施紀錄（視需要）

若某個 accept 項目本質是「補齊 proposal 原本要求但實作時遺漏的要素」，在對應 `proposals/PROPOSAL-00X.md` 的「實施紀錄」段落追加一行：

```markdown
- **R1 審查修正（YYYY-MM-DD）**: F-XX — {一句話描述}
```

若 accept 項目只是「改善措辭、修 typo」這類不涉及 proposal 範圍的小改，不必更新 proposal。

判斷原則：**實作有無變動 proposal 範圍**——有就更新紀錄，沒有就只寫 CHANGELOG。

## 第六步：完成後回報（不自動 commit）

實施完成後，告訴使用者：

> 「Round 1 implement 階段完成。
> - 共實施 N 項修正（F-XX, F-YY, ...）
> - 影響檔案：{列出}
> - CHANGELOG 已更新
> - Proposal 實施紀錄已更新：{列出哪幾份；若無就說「無」}
> - 備註（若有）：{跨輪發現 / 疑似遺漏 / 其他注意事項}
> 
> 請檢視後決定是否 commit。」

**不要主動 commit**。等使用者確認。

## 禁止事項

- **不做 decisions 沒列的改動**——即使看起來該順手修。新發現寫備註，不偷改。
- **Reject 和 defer 項目完全不動**——審議結論要被尊重。
- **不重新評估 Finding**——即使你有不同看法，那是下一輪 Review / Approve 的事，不是 Implement 的事。
- **不自動 commit**——完成後等使用者檢視。
- **不改 review 報告和 decisions 文件**——這些是審議軌跡，實作階段不動它們。

## 跨輪發現如何處理

實施過程若發現屬於其他輪範圍的問題（例如改 P001 的 SKILL.md 時看到 P003 的 behavior.md 相關段落有問題），記入 `reviews/cross-round-notes.md`，不在本輪處理。格式：

```
## R1 Implement 發現、屬於 R{N} 的備忘

- 位置：`path:line-range`
- 描述：...
- 建議轉交：R{N}（{該輪主題}）
- 記錄時間：YYYY-MM-DD
```

---END PROMPT---
