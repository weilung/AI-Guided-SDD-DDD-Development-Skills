# Round 6 Review Prompt — P007c（給 Codex CLI 使用，建議切 GPT-5.4）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Codex CLI。
> **產出位置**：Codex 會寫入 `reviews/round-6-report.md`。
> **禁止事項**：Codex 此輪不應修改任何實作檔。
> **範圍提醒**：本輪範圍極小（兩份 template 檔的標題結構重組），預期 Finding 數量應該很少。
> **特別提醒**：`templates/CLAUDE.md` 是**英文模板、AI 使用**——實施紀錄明示「不需要繁中同步」。這兩個檔案沒有繁中對應版，也不進入 Post-Review Closeout 階段的 TW 同步範圍。
> **模型選擇**：建議續用 Codex GPT-5.4。

---BEGIN PROMPT---

你是 P007c（CLAUDE.md 模板標題分段強化）實作的審查者。本次 review 範圍限定這份 proposal 的實作產物，且**只審英文版 Skill**（本 proposal 的兩個檔案本來就是英文模板，沒有繁中對應版）。

## 第一步：讀入以下檔案

**Proposal 來源（理解設計意圖）**
1. `proposals/PROPOSAL-007c-claude-md-segmentation.md`——**必讀**，特別注意：
   - 核心改動：把 CLAUDE.md 分為 `## 系統脈絡（What is this system?）` 和 `## 開發流程（How do we work?）` 兩大區塊
   - 實施紀錄：原本 `##` 段落降級為 `###`，新的兩大分類用 `##` + 一行 blockquote 摘要
   - Core 版獨有「架構」段落（系統脈絡下），WebForms 版沒有——這是刻意差異（Core 有 Clean Architecture，WebForms 漸進抽離中）
   - **「不需要繁中同步」**：實施紀錄明示此條

**實作產物（僅英文版，2 份）**

2. `sdd-ddd-webforms-skill/templates/CLAUDE.md`
3. `sdd-ddd-core-skill/templates/CLAUDE.md`

**流程規範（本次 review 格式要求）**
4. `proposals/review-workflow.md`（第三節「Review 報告格式」）

## 第二步：審查範圍邊界

**在範圍內**：上述 2 份檔案**整份內容**（範圍極小，整份審查可行）。

**不在範圍內**：
- 其他 proposal（P001–P006、P008、P007a/b）對應的實作
- 所有非 `templates/CLAUDE.md` 的檔案
- 繁中版——**本 proposal 的兩個檔案本身就沒有繁中對應版**（AI 用的模板）
- Tutorial
- 不做「英文版 vs 繁中版」的一致性比對（因為沒有繁中版）
- **不做「templates/CLAUDE.md 是否應該翻成繁中」的評論**——這是設計選擇，不是實作缺陷

若審查過程順手看到其他 proposal 的問題，記入 `reviews/cross-round-notes.md`（格式見第七步），**不佔本輪 Finding 數量**。

## 第三步：審查重點

照以下三個角度各看一輪：

1. **Proposal 意圖對齊**：
   - 兩份 `templates/CLAUDE.md` 是否重組為兩大 H2：`## 系統脈絡（What is this system?）` 和 `## 開發流程（How do we work?）`
   - 每個 H2 下方是否有一行 blockquote 摘要（例如 `> 技術棧、業務領域、團隊結構`）
   - 原本的 H2 段落是否降級為 H3（在對應的新 H2 之下）
   - 沒有遺失原本內容（純結構重組，不減內容）
2. **內容分配邏輯**：
   - `## 系統脈絡` 下是否放：背景、目錄結構、Core 版多「架構」段
   - `## 開發流程` 下是否放：SDD 核心原則、功能開發/修改/bug-fix 流程、Git Flow、Domain 層規範、術語表、AI 協作注意事項
   - 有沒有內容放錯區塊（例如 Git Flow 誤放在「系統脈絡」下）
3. **WebForms 版 vs Core 版一致性**：
   - 兩大 H2 結構兩版必須一致（標題文字、blockquote 摘要風格）
   - H3 結構應大致對齊（允許內容差異，但分段邏輯應相同）
   - **刻意差異（不是 bug）**：Core 版 `## 系統脈絡` 下有「架構」段（Clean Architecture 四層）；WebForms 版沒有
   - 其他差異若屬於「漸進抽離 vs greenfield」的合理差異，標為 Question 不是 Finding

## 第四步：嚴重度判斷原則（重要，請從嚴）

本輪範圍極小（兩份檔案純結構重組），Finding 應該很少。

**Critical**：違背 proposal 意圖、讓模板無法使用。例如：
- ✓ Critical 例：兩份檔案完全沒有 `## 系統脈絡` 和 `## 開發流程` 的分段（完全沒做 proposal）
- ✓ Critical 例：重組過程遺失了重要內容（例如 Domain 層規範整段消失）
- ✗ **非** Critical 例：blockquote 摘要的措辭選擇

**Major**：不完整或兩版不一致，影響使用。例如：
- ✓ Major 例：只有 Core 版做了分段，WebForms 版還是原樣
- ✓ Major 例：某份檔案漏了其中一個 H2（只有系統脈絡沒有開發流程）
- ✓ Major 例：原 H2 沒有降級為 H3，造成標題層級混亂
- ✗ **非** Major 例：H3 段落排序兩版不同但內容都在

**Minor**：格式、排版、措辭不夠精確。
- ⚠️ **節制使用**：本輪範圍極小，Minor 超過 3 項就應該檢討是否在挑刺

**Question**：你不確定意圖。

**高風險誤判類型（請警覺，不要標為 Finding）**：
1. **誤判應該有繁中版**：實施紀錄明示「不需要繁中同步」——`templates/CLAUDE.md` 是給 AI 讀的，本來就不翻繁中
2. **誤判 WebForms 版應該也有「架構」段**：WebForms 是漸進抽離，Clean Architecture 四層不適用；Core 獨有是刻意設計
3. **誤判原 H2 應該保留為 H2**：proposal 明確要求降級為 H3（新結構下 H2 是兩大分類）
4. **評論 proposal 值不值得做**：review 只看實作是否符合 proposal，不評論 proposal 本身價值
5. **誤判兩大 H2 的中文標題應該是英文**：P007c 實施紀錄採用「系統脈絡（What is this system?）」雙語形式，是刻意設計

**報告自我控制**：本輪範圍極小，若你產出 **5+ 項 Findings**，請檢討——很可能在挑刺。若超過 3 項 Minor，視為報告過度挑剔，建議批次 reject 候選。

## 第五步：關注的具體特徵（供參考，不限於此）

- 兩份檔案是否都有 `## 系統脈絡（What is this system?）` 和 `## 開發流程（How do we work?）` 兩大 H2（中英雙標題形式）
- 兩大 H2 是否各有一行 blockquote 摘要
- 原本的 H2 段落（背景、目錄結構、SDD 核心原則、功能開發流程、Git Flow、Domain 層規範、術語表、AI 協作注意事項等）是否正確降級為 H3
- H3 段落是否正確分配到對應的 H2 下（系統脈絡 vs 開發流程）
- Core 版 `## 系統脈絡` 下是否有「架構」段（Clean Architecture 四層介紹）
- WebForms 版 `## 系統脈絡` 下**沒有**「架構」段是正確的
- 重組過程是否保留了所有原有內容（純結構化，不減內容）
- 新的 H2 結構對 AI 讀取策略是否清楚（標題語義明確、摘要直指重點）

## 第六步：產出 review 報告

照 `proposals/review-workflow.md` 第三節「Review 報告格式」寫入 `reviews/round-6-report.md`。

- 每個 Finding 都要有：嚴重度、位置（`path:line-range`）、對照 proposal 段落、現況、問題、建議
- **嚴重度從嚴**（見第四步詳述）
- **兩版一致性對照表**（WebForms vs Core）必須有（即使全 ✓ 也要列）
- **不做繁中同步對照**——本 proposal 兩個檔案沒有繁中對應版
- Findings 要有明確證據（引用 line range）
- 若 Finding 數量為 0，寫入「本輪未發現問題」結論段，不要為湊數勉強寫 Finding

## 第七步：跨輪發現如何處理

若看到的問題明顯不屬於 P007c 範圍、但屬於其他 proposal 範圍，請寫在 `reviews/cross-round-notes.md`（若檔案不存在則建立）。格式：

```
## R6 發現、屬於 R{N} 的備忘

- 位置：`path:line-range`
- 描述：...
- 建議轉交：R{N}（{該輪主題}）或 Post-Review Closeout

## R6 發現、屬於 Post-Review Closeout 的備忘

- 位置：`path:line-range`（繁中版或 tutorial 的問題）
- 描述：...
```

**注意**：`templates/CLAUDE.md` 本身**不屬於** Post-Review Closeout 的 TW 同步範圍（沒有繁中對應版）。若要記 cross-round-notes.md 的 Post-Review Closeout 備忘，只涉及其他檔案的繁中版或 tutorial。

這些備忘**不佔本輪 Finding 數量**。

## 禁止事項

- 不要修改任何實作檔。本輪只產出 review 報告。
- 不要審查 `templates/CLAUDE.md` 以外的檔案。
- 不要挑「應該有繁中版」——proposal 明示無需繁中。
- 不要挑「WebForms 應該有架構段」——Core 獨有是刻意設計。
- 不要評論 proposal 本身值不值得做。
- 不要做出假設性推論。
- Minor 數量嚴格自我節制（本輪範圍極小）。
- 若你產出超過 5 項 Finding，先自問：「這些真的都是問題嗎？還是我在挑刺？」

## 完成後回報

報告完成後告訴使用者：「Round 6 review 報告已產出於 reviews/round-6-report.md，共 N 個 findings（Critical: X / Major: Y / Minor: Z / Question: W）。範圍限定 `templates/CLAUDE.md` × 2（P007c）。可進入 Approve 階段（另開新 Claude Code session）。**R6 為最終輪，完成後可進入 R1–R6 全域一致性 sweep 和 Post-Review Closeout 階段**。」

---END PROMPT---
