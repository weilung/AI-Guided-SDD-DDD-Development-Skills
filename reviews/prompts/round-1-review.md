# Round 1 Review Prompt — P001 + P007b（給 Codex CLI 使用）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Codex CLI。
> **產出位置**：Codex 會寫入 `reviews/round-1-report.md`。
> **禁止事項**：Codex 此輪不應修改任何實作檔。
> **範圍提醒**：本輪只審查**英文 Skill**，不審繁中版（`*-tw/`）和 tutorial。

---BEGIN PROMPT---

你是 P001（Workflow Transparency / Hybrid 指令化入口）和 P007b（PR Review Spec Intent Step 0）實作的審查者。本次 review 範圍限定這兩份 proposal 的實作產物，不含其他 proposal，且**只審英文版 Skill**（繁中版延後至 Post-Review Closeout 階段統一處理）。

## 第一步：讀入以下檔案

**Proposal 來源（理解設計意圖）**
1. `proposals/PROPOSAL-001-workflow-transparency.md`
2. `proposals/PROPOSAL-007b-pr-review-spec-intent.md`
3. `proposals/evaluation-p002-p008.md`（讀「P005/P007 區塊」和「第二之二節 D1 修正」——這兩處記錄了 P007b 從 Wave C 提前到 Wave A 的決策脈絡）

**實作產物（僅英文版）**
4. `sdd-ddd-webforms-skill/SKILL.md`
5. `sdd-ddd-core-skill/SKILL.md`
6. `sdd-ddd-webforms-skill/references/new-feature-flow.md`
7. `sdd-ddd-core-skill/references/new-feature-flow.md`
8. `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
9. `sdd-ddd-core-skill/references/modify-existing-flow.md`
10. `sdd-ddd-webforms-skill/references/pr-review-checklist.md`
11. `sdd-ddd-core-skill/references/pr-review-checklist.md`

**流程規範（本次 review 格式要求）**
12. `proposals/review-workflow.md`（第三節「Review 報告格式」定義輸出結構）

## 第二步：審查範圍邊界

**在範圍內**：上述 8 份英文 Skill 檔（WebForms 版 4 份 + Core 版 4 份）。

**不在範圍內**：
- `sdd-ddd-webforms-skill-tw/`、`sdd-ddd-core-skill-tw/`（繁中版，延後處理）
- `tutorial/` 目錄所有檔案（延後處理）
- 其他 proposal（P002–P006、P007a、P007c、P008）對應的實作
- 不比對「英文版 vs 繁中版」的一致性——這不是本輪的工作

若審查過程順手看到繁中版或 tutorial 有明顯錯誤，記入 `reviews/cross-round-notes.md`（格式見第六步），**不佔本輪 Finding 數量**。

## 第三步：審查重點

照以下四個角度各看一輪。不是 checklist——是 lens：

1. **Proposal 意圖對齊**：實作是否忠於 proposal 要求？有沒有遺漏 proposal 明列的要素（例如 P001 要求的 7 個 `/dflow:` 指令全到齊嗎？三級透明度三層都有嗎？P007b 的 Step 0 確實在 checklist 開頭嗎？）
2. **邏輯自洽**：實作內部是否有矛盾或自相牴觸的指引？例如 SKILL.md Decision Tree 和 Workflow Transparency 段落是否互相支持、還是彼此打架？
3. **WebForms 版 vs Core 版一致性**：共通機制（決策樹結構、Phase Gate 定義、Confirmation Signals）應該在措辭和結構上對齊；差異（例如 Core modify-existing 是 5 步、WebForms 是 6 步）應該明確標示、不是漏掉的 bug。
4. **範圍邊界**：實作有沒有夾帶超出 P001/P007b 範圍的改動？若有發現可能是其他 proposal（P002 Delta、P003 behavior.md 等）相關的改動，記下但不在本輪深查。

## 第四步：關注的具體特徵（供參考，不限於此）

不要把下方當成完整清單，只是提醒你哪些地方容易出問題。你的獨立判斷比清單重要。

**P001 相關**
- `/dflow:` 指令族是否在兩版 SKILL.md 都列齊：`new-feature`、`modify-existing`、`bug-fix`、`pr-review`、`status`、`next`、`cancel`
- Workflow Transparency 章節的三級透明度定義是否一致
- Auto-Trigger Safety Net 規則（NL 判斷後等確認、不自動前進）是否在決策樹中體現
- Phase Gate markers 在 flow 檔案的位置：
  - WebForms `new-feature-flow` 應有若干 Step 邊界標為 Phase Gate（其餘為 step-internal）
  - Core `new-feature-flow` 對應的 Step 邊界
  - WebForms `modify-existing-flow` 6 步流程的 Phase Gate 位置
  - Core `modify-existing-flow` 5 步流程的 Phase Gate 位置（**這是兩版刻意的結構差異**，不是 bug）
- `/dflow:status` 的回應格式定義是否提供、且兩版一致
- NL ↔ command 等價規則的表述

**P007b 相關**
- Step 0 是否位於 pr-review-checklist 最開頭（先於任何 Code Review 項）
- Step 0 是否包含四個要素：讀 spec、讀 Delta（若是修改既有功能）、確認理解、才進入程式碼 review
- 兩版是否一致
- 與 `/dflow:pr-review` 指令的銜接是否明確（/dflow:pr-review 進入點第一步就是 Step 0）

## 第五步：產出 review 報告

照 `proposals/review-workflow.md` 第三節「Review 報告格式」寫入 `reviews/round-1-report.md`。

- 每個 Finding 都要有：嚴重度、位置（`path:line-range`）、對照 proposal 段落、現況、問題、建議
- **嚴重度從嚴**：Critical 只保留給「違背 proposal 意圖」或「讓流程無法運作」
  - Major：不完整或兩版不一致，影響使用
  - Minor：格式、措辭問題
  - Question：你不確定意圖，需要設計者釐清
- **兩版一致性對照表**（WebForms vs Core）必須有（即使全 ✓ 也要列，證明你檢查過）
- **不做繁中同步對照**——這不是本輪範圍
- Findings 要有明確證據（引用 line range），不是印象描述

## 第六步：跨輪發現如何處理

若你看到的問題明顯不屬於 P001/P007b 範圍、但屬於另一輪將審查的範圍（例如 R2 的 Delta 格式、R4 的 behavior.md），或屬於 Post-Review Closeout 階段的範圍（繁中版、tutorial 的問題），請寫在 `reviews/cross-round-notes.md`（若檔案不存在則建立）。格式：

```
## R1 發現、屬於 R{N} 的備忘

- 位置：`path:line-range`
- 描述：...
- 建議轉交：R{N}（{該輪主題}）或 Post-Review Closeout

## R1 發現、屬於 Post-Review Closeout 的備忘

- 位置：`path:line-range`（繁中版或 tutorial 的問題）
- 描述：...
```

這些備忘**不佔本輪 Finding 數量**。

## 禁止事項

- 不要修改任何實作檔。本輪只產出 review 報告。
- 不要審查繁中版（`*-tw/`）或 tutorial 的內容——那是 Post-Review Closeout 階段的工作。
- 不要評論 P001/P007b 以外的 proposal 實作（若發現牽涉到 P002/P003 等，在 cross-round-notes.md 另記，不計入本輪 Finding）。
- 不要重寫 proposal——對 proposal 本身的看法（「應該這樣設計才對」）不在 review 範圍；review 只檢查「實作是否符合 proposal」。
- 若 proposal 本身就有問題，標為 Question 並說明哪裡矛盾，交給 Approve 階段判斷。
- 不要做出假設性推論（「未來可能會...」）——審查當前實作即可。

## 完成後回報

報告完成後告訴使用者：「Round 1 review 報告已產出於 reviews/round-1-report.md，共 N 個 findings（Critical: X / Major: Y / Minor: Z / Question: W）。範圍限定英文 Skill。可進入 Approve 階段（另開新 Claude Code session）。」

---END PROMPT---
