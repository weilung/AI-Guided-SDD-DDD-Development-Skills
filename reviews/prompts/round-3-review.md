# Round 3 Review Prompt — P005a + P005b（給 Codex CLI 使用，建議切 GPT-5.4）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Codex CLI。
> **產出位置**：Codex 會寫入 `reviews/round-3-report.md`。
> **禁止事項**：Codex 此輪不應修改任何實作檔。
> **範圍提醒**：本輪只審查**英文 Skill**，不審繁中版（`*-tw/`）和 tutorial。
> **模型選擇**：建議續用 Codex GPT-5.4（與 R2 同家族，便於同家族盲點累積可比性；若 R2 結果顯示 GPT 家族過度機械化，可改 Opus）。

---BEGIN PROMPT---

你是 P005a（完成流程 Checklist — Wave A 子集：驗證 + 文件更新 + 歸檔）和 P005b（完成流程 Checklist — Wave B 子集：behavior.md + Tasks 補完）實作的審查者。本次 review 範圍限定這兩份 proposal 的實作產物，不含其他 proposal，且**只審英文版 Skill**（繁中版延後至 Post-Review Closeout 階段）。

## 第一步：讀入以下檔案

**Proposal 來源（理解設計意圖）**
1. `proposals/PROPOSAL-005a-completion-checklist-wave-a.md`——**必讀**，特別注意：
   - 四段分層結構：8.1 AI 獨立驗證 / 8.2 開發者確認 / 8.3 文件更新 / 8.4 歸檔
   - 執行順序嚴格為 8.1 → 8.2 → 8.3 → 8.4
   - Phase Gate 是唯一觸發點（不另做自然語言偵測）
2. `proposals/PROPOSAL-005b-completion-checklist-wave-b.md`——**必讀**，特別注意：
   - 「實施期判斷」一欄：8.1 的 post-merge 項選擇用 `*(post-8.3)*` 標籤嵌入 8.1，**不另開 8.5 段**（刻意設計）
   - B3/B4 前提是「前瞻性」——P003 尚未建立對應機械結構（簡化版），checklist 語言先到位，等 P003 後續補強時不必再改文字
3. `proposals/PROPOSAL-005-completion-flow.md`（可選讀）——原始整合版，已於 2026-04-15 拆為 005a/005b；P005 狀態應為 superseded
4. `proposals/PROPOSAL-003-behavior-md.md`（可選讀）——理解 P005b 依賴的 behavior.md 是哪種簡化版
5. `proposals/PROPOSAL-004-spec-template-restructure.md`（可選讀）——理解 tasks 段的產生與驗證銜接
6. `proposals/evaluation-p002-p008.md`（讀「P005 區塊」、B5 拆分、B6 驗證分層——確認 Wave A/B 拆分邏輯和分層原則）

**實作產物（僅英文版，6 份）**

7. `sdd-ddd-webforms-skill/references/new-feature-flow.md`（**只看 Step 8 全段**——8.1 / 8.2 / 8.3 / 8.4）
8. `sdd-ddd-core-skill/references/new-feature-flow.md`（同上）
9. `sdd-ddd-webforms-skill/references/modify-existing-flow.md`（**只看最終 Step：WebForms Step 6 全段**——6.1 / 6.2 / 6.3）
10. `sdd-ddd-core-skill/references/modify-existing-flow.md`（**只看最終 Step：Core Step 5 全段**——5.1 / 5.2 / 5.3）
11. `sdd-ddd-webforms-skill/SKILL.md`（**只看 `Completion Checklist Execution` 子節**——不重審整份 SKILL.md）
12. `sdd-ddd-core-skill/SKILL.md`（同上）

**流程規範（本次 review 格式要求）**
13. `proposals/review-workflow.md`（第三節「Review 報告格式」定義輸出結構）

## 第二步：審查範圍邊界

**在範圍內**：上述 6 份檔案的**指定段落**（不是整份檔案）：
- new-feature-flow.md 的 **Step 8 全段**
- modify-existing-flow.md 的 **最終 Step 全段**（WebForms Step 6、Core Step 5）
- SKILL.md 的 **`Completion Checklist Execution` 子節**

**不在範圍內**：
- 上述檔案的**其他段落**（R1/R2 已審）
- `sdd-ddd-webforms-skill-tw/`、`sdd-ddd-core-skill-tw/`（繁中版，延後處理）
- `tutorial/` 目錄所有檔案（延後處理）
- `references/new-feature-flow.md` 的 Step 5（R2 範圍）
- 其他 proposal（P001、P002、P003、P004、P007、P008）對應的實作
- 不比對「英文版 vs 繁中版」的一致性

若審查過程順手看到繁中版、tutorial、或其他段落有明顯錯誤，記入 `reviews/cross-round-notes.md`（格式見第七步），**不佔本輪 Finding 數量**。

## 第三步：審查重點

照以下四個角度各看一輪：

1. **Proposal 意圖對齊**：
   - 四段分層（驗證獨立 / 驗證確認 / 文件 / 歸檔）是否都到齊
   - 8.1 的 `*(post-8.3)*` 標籤是否存在（P005b 特徵；若完全找不到，可能漏實作）
   - 執行順序（8.1 → 8.2 → 8.3 → 8.4）是否在 SKILL.md 明確表達
   - `/dflow:next` Phase Gate 是否寫明為觸發點
2. **邏輯自洽**：
   - 8.1 獨立驗證項是否都是「AI 真的能機械檢查」的（例如「Domain 層無 System.Web 引用」可機械檢查 ✓；「意圖是否一致」是 8.2 領域，放 8.1 就是誤分）
   - 8.2 確認項是否需要人類判斷（若 AI 能自己答，應該移到 8.1）
   - new-feature-flow 的 Step 8 和 modify-existing-flow 的對應 Step 是否在分層結構上同構（只有驗證項的具體內容應該不同）
3. **WebForms 版 vs Core 版一致性**：
   - 四段結構兩版必須一致
   - 具體驗證項**刻意不同**：
     - WebForms 8.1 側重 `Domain 層無 System.Web 引用`
     - Core 8.1 補 `Aggregate invariants / Domain Events raised / EF Fluent API only / Domain 專案無外部 NuGet`、REMOVED delta anchor 刪除
   - 這些技術棧差異是刻意的、不是 bug
4. **範圍邊界**：
   - 實作有沒有夾帶超出 P005a/P005b 範圍的改動？
   - 特別留意：P004 的 tasks 驗證在 R2 已審過——R3 看的是「P005b 如何把 tasks 驗證整併進分層 checklist」，不重評 P004 的 tasks 段本身

## 第四步：嚴重度判斷原則（重要，請從嚴）

**Critical**：違背 proposal 意圖、讓流程無法運作。例如：
- ✓ Critical 例：SKILL.md 完全沒有 `Completion Checklist Execution` 子節，沒有指明 Phase Gate 觸發
- ✗ **非** Critical 例：8.3 的 `glossary.md` 排在 `models.md` 後面而不是前面（排序差異非致命）

**Major**：不完整或兩版不一致，影響使用。例如：
- ✓ Major 例：Core 版 8.1 沒有「Aggregate invariants」項（proposal 明列）
- ✓ Major 例：`*(post-8.3)*` 標籤只在 WebForms 8.1 有，Core 8.1 沒有（兩版不一致）
- ✗ **非** Major 例：8.2 的兩項順序兩版不同（內容都在、順序差異）

**Minor**：格式、排版、措辭不夠精確。
- ✓ Minor 例：checklist 項目符號有的用 `- [ ]` 有的用 `- [x]` 混用
- ⚠️ **節制使用**：純措辭級別的 Minor 若超過 5 項，考慮是不是在挑刺。

**Question**：你不確定意圖。**P005b 的 `*(post-8.3)*` 機制若不理解，標 Question 而非 Critical/Major**——proposal 的「實施期判斷」欄有說明。

**高風險誤判類型（請警覺，不要標為 Finding）**：
1. **誤讀 post-8.3 應另開 8.5 段**：P005b 刻意選擇嵌入 8.1，不是漏 refactor
2. **誤判 B3/B4 機械結構不存在是 bug**：P005b 明確是前瞻性設計，等 P003 補強時無需改文字
3. **誤判「執行順序嚴格」為機械限制**：這是 Skill 指引而非程式碼約束——若實作寫「AI 需嚴格按 8.1→8.2→...」，那是對的；若實作把順序寫死成程式分支，那反而是過度實作

**報告自我控制**：若你產出 15+ 項 Findings、其中 10+ 是 Minor，請檢討。寧可漏記 2 個爭議 Minor 讓 Approve 階段補抓。

## 第五步：關注的具體特徵（供參考，不限於此）

**P005a 相關**
- Step 8 有沒有拆成 **8.1 / 8.2 / 8.3 / 8.4** 四段
- 8.1 項目是否都是 AI 可機械檢查的（BR-* 覆蓋、EC-* 覆蓋、Given/When/Then 覆蓋、Domain 層純淨度、tasks 勾選狀態）
- Core 版 8.1 是否補 Aggregate invariants / Domain Events raised / EF Fluent API only / Domain 專案無外部 NuGet
- 8.2 項目是否都是需要開發者判斷的（意圖一致性、邊界情況合理性、tech-debt 遺漏、Aggregate boundary、Domain Event placements）
- 8.3 文件更新是否涵蓋：glossary / models / rules / events / context-map / tech-debt
- 8.4 歸檔是否包含 status → completed + 檔案移到 `completed/`
- modify-existing-flow 的 X.1 / X.2 / X.3 三段結構是否完整（注意：不含 X.4 歸檔的分層，或是否有歸檔步驟整併在 X.3）
- modify-existing-flow 的驗證項是否對齊 Delta 格式（ADDED / MODIFIED / REMOVED / RENAMED 覆蓋）
- bug-fix 搭配 lightweight-spec 時，`(full spec only)` 項是否正確標記為可跳過
- SKILL.md `Completion Checklist Execution` 子節是否明訂：
  - Phase Gate 是唯一觸發點
  - 執行順序 8.1 → 8.2 → 8.3 → 8.4
  - AI-independent 一次報完 ✓/✗；Developer-confirmation 一次問一項
  - auto-trigger 安全網條款（開發者跳過 Phase Gate 直接 commit 時）

**P005b 相關**
- 8.1 是否新增兩項標 `*(post-8.3)*`：`behavior.md` 對本 spec 的 `BR-*` 有對應 anchor、`behavior.md` `last-updated` 晚於 spec `created` 日期
- Core 版 8.1 post-8.3 項是否特別提 REMOVED delta anchor 刪除
- 8.2 是否新增兩項：behavior.md 情境忠實度、tasks 段摺疊/移除決定
- 8.3 的 `behavior.md` bullet 是否擴充為兩個子步驟（B3 Phase 3 draft 轉正式段落 + B4 rules.md anchor last-updated 更新）
- 8.3 是否新增 `behavior.md 草稿清理` bullet（中途放棄時保留 `## 提案中變更` 段或 REMOVE）
- modify-existing-flow 的 X.1 / X.2 / X.3 是否做對應擴充（措辭針對 Delta 流程調整）
- SKILL.md 是否在 AI-independent 段落拆成「合併前」/「8.3 / 6.3 / 5.3 之後」兩類時機
- SKILL.md Developer-confirmation 段落是否加入 P005b 兩題

## 第六步：產出 review 報告

照 `proposals/review-workflow.md` 第三節「Review 報告格式」寫入 `reviews/round-3-report.md`。

- 每個 Finding 都要有：嚴重度、位置（`path:line-range`）、對照 proposal 段落、現況、問題、建議
- **嚴重度從嚴**（見第四步詳述）
- **兩版一致性對照表**（WebForms vs Core）必須有
- **不做繁中同步對照**——不在本輪範圍
- Findings 要有明確證據（引用 line range）

## 第七步：跨輪發現如何處理

若看到的問題明顯不屬於 P005a/P005b 範圍、但屬於另一輪將審查的範圍（例如 R4 的 behavior.md 本身結構問題、R1 的 SKILL.md 非 Completion 段落），或屬於 Post-Review Closeout 階段的範圍（繁中版、tutorial 的問題），請寫在 `reviews/cross-round-notes.md`（若檔案不存在則建立）。格式：

```
## R3 發現、屬於 R{N} 的備忘

- 位置：`path:line-range`
- 描述：...
- 建議轉交：R{N}（{該輪主題}）或 Post-Review Closeout

## R3 發現、屬於 Post-Review Closeout 的備忘

- 位置：`path:line-range`（繁中版或 tutorial 的問題）
- 描述：...
```

這些備忘**不佔本輪 Finding 數量**。

## 禁止事項

- 不要修改任何實作檔。本輪只產出 review 報告。
- 不要審查繁中版（`*-tw/`）或 tutorial。
- 不要重審 6 份檔案中**本輪指定段落以外**的部分（R1/R2 範圍）——若順手看到問題，記 cross-round-notes.md。
- 不要評論 P005a/P005b 以外的 proposal 實作。
- 不要挑「P003 的機械結構還不存在所以 P005b 的 post-8.3 項不可用」——這是前瞻性設計，不是 bug。
- 不要挑「post-8.3 應該另開 8.5 段」——這是刻意設計選擇。
- 不要做出假設性推論——審查當前實作即可。
- Minor 數量自我節制（見第四步）。

## 完成後回報

報告完成後告訴使用者：「Round 3 review 報告已產出於 reviews/round-3-report.md，共 N 個 findings（Critical: X / Major: Y / Minor: Z / Question: W）。範圍限定英文 Skill（P005a + P005b）。可進入 Approve 階段（另開新 Claude Code session）。」

---END PROMPT---
