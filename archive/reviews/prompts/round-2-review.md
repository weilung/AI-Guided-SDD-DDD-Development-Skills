# Round 2 Review Prompt — P002 + P004（給 Codex CLI 使用，建議切 GPT-5.4）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Codex CLI。
> **產出位置**：Codex 會寫入 `reviews/round-2-report.md`。
> **禁止事項**：Codex 此輪不應修改任何實作檔。
> **範圍提醒**：本輪只審查**英文 Skill**，不審繁中版（`*-tw/`）和 tutorial。
> **模型選擇**：R1 已由 Opus 家族審過一次（歸檔 v1），本輪建議用 Codex GPT-5.4 換個家族的盲點角度，補「精細比對」一面。

---BEGIN PROMPT---

你是 P002（Delta Spec 變更描述格式）和 P004（Spec 模板重構 — 精簡版）實作的審查者。本次 review 範圍限定這兩份 proposal 的實作產物，不含其他 proposal，且**只審英文版 Skill**（繁中版延後至 Post-Review Closeout 階段統一處理）。

## 第一步：讀入以下檔案

**Proposal 來源（理解設計意圖）**
1. `proposals/PROPOSAL-002-delta-spec-format.md`——**必讀**，特別注意：
   - 原始提議只含 ADDED / MODIFIED / REMOVED
   - evaluation 階段追加了 **B1 RENAMED** 和 **B2 UNCHANGED（可選）**
   - 「重要澄清」：delta 標記只用於變更 spec，不累積到 P003 的 behavior.md
2. `proposals/PROPOSAL-004-spec-template-restructure.md`——**必讀**，特別注意：
   - 原始提議是「重構為五段式」（一、Proposal / 二、領域分析 / ...）
   - 但「評估紀錄」欄明確標記：**採用精簡版**——**不**重構段落結構，改用 HTML comment 標記 phase + 新增「實作任務」段
   - **若你挑毛病說「模板沒有重構為五段」，那是誤讀**。精簡版才是 approved 版本。
3. `proposals/evaluation-p002-p008.md`（讀「第二之二節 B1/B2 盲點追加」和「P002/P004 區塊」——確認精簡版決議脈絡）

**實作產物（僅英文版，8 份）**

P002 相關：
4. `sdd-ddd-webforms-skill/references/modify-existing-flow.md`（Step 2 delta 格式段落）
5. `sdd-ddd-core-skill/references/modify-existing-flow.md`（同上）
6. `sdd-ddd-webforms-skill/templates/lightweight-spec.md`（精簡版 delta 格式）
7. `sdd-ddd-core-skill/templates/lightweight-spec.md`（同上）

P004 相關：
8. `sdd-ddd-webforms-skill/templates/feature-spec.md`（HTML comment phase 標記 + 實作任務段）
9. `sdd-ddd-core-skill/templates/feature-spec.md`（同上）
10. `sdd-ddd-webforms-skill/references/new-feature-flow.md`（只看 **Step 5 末尾** 的 Generate Implementation Tasks List + **Step 8 第一項** 的 tasks 驗證）
11. `sdd-ddd-core-skill/references/new-feature-flow.md`（同上）

**流程規範（本次 review 格式要求）**
12. `proposals/review-workflow.md`（第三節「Review 報告格式」定義輸出結構）

## 第二步：審查範圍邊界

**在範圍內**：上述 8 份英文 Skill 檔。

**不在範圍內**：
- `sdd-ddd-webforms-skill-tw/`、`sdd-ddd-core-skill-tw/`（繁中版，延後處理）
- `tutorial/` 目錄所有檔案（延後處理）
- `SKILL.md`（雖然 P004 也改動了 Guiding Questions by Phase 的開頭段落，但 SKILL.md 是 R1 的核心檔案，為避免重疊不在 R2 重審；若順手看到問題，記入 cross-round-notes.md）
- `references/new-feature-flow.md` 除 Step 5 / Step 8 以外的段落（那是 R1/R3 範圍）
- 其他 proposal（P001、P003、P005–P008）對應的實作
- 不比對「英文版 vs 繁中版」的一致性

若審查過程順手看到繁中版、tutorial、或 SKILL.md 有明顯錯誤，記入 `reviews/cross-round-notes.md`（格式見第六步），**不佔本輪 Finding 數量**。

## 第三步：審查重點

照以下四個角度各看一輪。不是 checklist——是 lens：

1. **Proposal 意圖對齊**：實作是否忠於 proposal 要求？**注意 P004 是精簡版，不是原始五段式**。P002 的 RENAMED 和 UNCHANGED 是追加項，要特別確認有沒有實作。
2. **邏輯自洽**：實作內部是否有矛盾或自相牴觸的指引？例如 modify-existing-flow 的 delta 格式範例和 lightweight-spec 的簡化 delta 是否互相支持、還是彼此打架？
3. **WebForms 版 vs Core 版一致性**：
   - P002 共通結構（ADDED/MODIFIED/REMOVED/RENAMED/UNCHANGED）兩版應該一致
   - P004 的「實作任務」段分類**刻意不同**（WebForms 是 DOMAIN / PAGE / DATA / TEST；Core 是 DOMAIN / APP / INFRA / API / TEST，且有建議產出順序）——這是刻意差異，不是 bug
4. **範圍邊界**：實作有沒有夾帶超出 P002/P004 範圍的改動？

## 第四步：嚴重度判斷原則（重要，請從嚴）

**Critical**：違背 proposal 意圖、讓流程無法運作。例如：
- ✓ Critical 例：P002 完全沒實作 RENAMED，然後 P003 的 behavior.md 又依賴這個標記
- ✗ **非** Critical 例：MODIFIED 的「原本 / 改為」英文小大寫不一致

**Major**：不完整或兩版不一致，影響使用。例如：
- ✓ Major 例：Core 版 feature-spec.md 的「實作任務」段沒有 API 分類，與 proposal 明列不符
- ✗ **非** Major 例：兩版範例使用不同情境（例如 WebForms 版用「訂單」、Core 版用「支付」）——這是措辭層級，除非情境差異產生語義衝突

**Minor**：格式、排版、措辭不夠精確。
- ✓ Minor 例：HTML comment 後多了空格
- ⚠️ **節制使用**：純措辭級別的 Minor 若超過 5 項，考慮是不是在挑刺。Review 報告不是越長越好，而是越準越好。

**Question**：你不確定意圖，需要設計者釐清。

**報告自我控制**：若你產出 15+ 項 Findings，其中 10+ 是 Minor，請檢討——可能是把風格偏好當成問題記錄。寧可漏記 2 個爭議 Minor 讓 Approve 階段補抓，也不要塞 10 個爭議 Minor 讓使用者逐一 reject。

## 第五步：關注的具體特徵（供參考，不限於此）

不要把下方當成完整清單，只是提醒你哪些地方容易出問題。你的獨立判斷比清單重要。

**P002 相關**
- `modify-existing-flow.md` Step 2 有沒有展示 5 個 delta markers（ADDED / MODIFIED / REMOVED / RENAMED / UNCHANGED）
- MODIFIED 範例有沒有保留「**原本**」和「**改為**」對照（這是相對 OpenSpec 的刻意差異）
- 範例用的是 Given/When/Then Gherkin 風格，**不是** RFC 2119 的 SHALL/MUST（刻意差異）
- UNCHANGED 明確標示為「可選」——不是必填
- RENAMED 範例有沒有說明使用情境（brownfield 概念改名）
- `lightweight-spec.md` 的 delta 是否為精簡版格式，而不是照搬完整 delta（bug-fix 不需要 5 個 markers）
- 兩版是否一致

**P004 相關**
- `feature-spec.md` 是否在每個既有段落 heading 加 `<!-- 填寫時機：Phase N -->` HTML comment（Phase 1-4）
- 是否**沒有**把模板重構為五段式（一、二、三、四、五）——精簡版明確不做這件事
- 是否在文件頂部加了 template note 區塊，說明 phase 標記用途和對應 SKILL.md
- 「實作任務」段：
  - WebForms 版分類：DOMAIN / PAGE / DATA / TEST
  - Core 版分類：DOMAIN / APP / INFRA / API / TEST
  - Core 版是否標註建議產出順序（DOMAIN → APP → INFRA → API）
- `new-feature-flow.md` Step 5 末尾是否加入「Generate Implementation Tasks List」子段（含格式與範例）
- `new-feature-flow.md` Step 8 第一項是否是「驗證實作任務段全部勾選或未勾選項標註 follow-up」
- `modify-existing-flow.md` 是否在 planning phase 末尾加入 tasks 產生步驟（WebForms Step 4、Core Step 3），且限制為「使用完整 feature-spec 時才產生；lightweight-spec bug-fix 模式跳過」
- `modify-existing-flow.md` 收尾 step（WebForms Step 6、Core Step 5）是否加入 tasks 驗證項

## 第六步：產出 review 報告

照 `proposals/review-workflow.md` 第三節「Review 報告格式」寫入 `reviews/round-2-report.md`。

- 每個 Finding 都要有：嚴重度、位置（`path:line-range`）、對照 proposal 段落、現況、問題、建議
- **嚴重度從嚴**（見第四步詳述）
- **兩版一致性對照表**（WebForms vs Core）必須有（即使全 ✓ 也要列，證明你檢查過）
- **不做繁中同步對照**——不在本輪範圍
- Findings 要有明確證據（引用 line range），不是印象描述

## 第七步：跨輪發現如何處理

若看到的問題明顯不屬於 P002/P004 範圍、但屬於另一輪將審查的範圍（例如 R1 的 SKILL.md Guiding Questions preamble、R4 的 behavior.md 和 delta 銜接），或屬於 Post-Review Closeout 階段的範圍（繁中版、tutorial 的問題），請寫在 `reviews/cross-round-notes.md`（若檔案不存在則建立）。格式：

```
## R2 發現、屬於 R{N} 的備忘

- 位置：`path:line-range`
- 描述：...
- 建議轉交：R{N}（{該輪主題}）或 Post-Review Closeout

## R2 發現、屬於 Post-Review Closeout 的備忘

- 位置：`path:line-range`（繁中版或 tutorial 的問題）
- 描述：...
```

這些備忘**不佔本輪 Finding 數量**。

## 禁止事項

- 不要修改任何實作檔。本輪只產出 review 報告。
- 不要審查繁中版（`*-tw/`）或 tutorial 的內容。
- 不要重新審查 SKILL.md（R1 已審），即使 P004 改了 Guiding Questions preamble——那歸入 cross-round-notes.md。
- 不要評論 P002/P004 以外的 proposal 實作（若發現牽涉到 P001/P003/P005 等，記入 cross-round-notes.md，不計入本輪 Finding）。
- 不要挑「原始 P004 proposal body 要求五段式但實作沒做」——精簡版才是 approved 版本。
- 不要做出假設性推論（「未來可能會...」）——審查當前實作即可。
- Minor 數量自我節制（見第四步）。

## 完成後回報

報告完成後告訴使用者：「Round 2 review 報告已產出於 reviews/round-2-report.md，共 N 個 findings（Critical: X / Major: Y / Minor: Z / Question: W）。範圍限定英文 Skill（P002 + P004）。可進入 Approve 階段（另開新 Claude Code session）。」

---END PROMPT---
