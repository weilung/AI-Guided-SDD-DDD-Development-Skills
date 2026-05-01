# Round 8b Implement Review Approve Prompt — 12 findings 決議 + Fix（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：
>   - `reviews/round-8b-implement-review-decisions.md`（主要產出）
>   - 對 accept / accept-with-choice 的 finding：**直接在本 session 修訂對應 skill / planning / proposal 檔**（仿 R8a Implement Review Fix 合併 session pattern）
> **與 R8a Implement Review Fix 的差異**：R8a 是 3 finding 全部 accept；R8b 是 12 finding（0 Critical / 2 Major / 6 Minor / 4 Question），含**設計決策叢集** —— 4 個 Question 集中於 2 個設計議題（inline bold labels 結構性 vs 散文 + GLOSSARY 收錄門檻），需要使用者拍板。
> **與 R7 Approve 的差異**：R7 Approve 改的是 draft proposal 文字 + 推進 proposal 狀態；R8b Implement Review 改的是**實作產物**（skill 檔 / planning 檔 + 可能微調 P013）+ **不推進 proposal 狀態**（P013 已 approved，本輪只 fix implement quality）。
> **本輪 finding 概況**：12 finding（0 Critical / 2 Major / 6 Minor / 4 Question），Reviewer 建議 `minor-fix`。Major 全是機械 cascade 補完；Minor 多為 cascade / 措辭；Question 全是設計邊界釐清。
> **預估時程**：~60-90 分鐘（4 個 Question 拍板 + 8 個機械 fix + decisions 整理）

---BEGIN PROMPT---

這是 R8b Implement Review 的**決議階段**（Round 8b Implement Review Approve）。Reviewer（Cursor Claude Opus 4.7）對 R8b 4 sub-waves 實施產物產出 12 findings + 跨輪議題 1 項。本輪在本 session 內**直接處理 12 findings**（仿 R8a Implement Review Fix 合併 session pattern）。

PROPOSAL-013 狀態為 `approved`，本輪**不變動**該狀態。本輪只修 implement quality（skill / planning / 可能微調 P013 內表格），不影響 R8b implement 已落地的核心意圖。

## 第一步：依序讀入下列檔案

1. `reviews/round-8b-implement-review-report.md`（Cursor 產出的審查報告 —— 12 findings 主要分析對象）
2. `reviews/round-8b-decisions.md`（R8b approve 決議；用於追溯每個 finding 對應的 R8b decisions 條目）
3. `reviews/prompts/round-8b-implement-review.md`（R8b Implement Review prompt 邊界 —— 特別 Dimension 2 / Dimension 5 的 CW / D5 檢查點）
4. `proposals/PROPOSAL-013-system-document-template-coverage.md`（implementation 權威；F-06 可能需要修 §1 anchor 表）
5. `TEMPLATE-LANGUAGE-GLOSSARY.md`（F-05 / F-12 可能需要補 inclusion criteria 或新 term）
6. `TEMPLATE-COVERAGE.md`（F-06 verify Section anchors 欄與 P013 §1 表的對齊）
7. `proposals/review-workflow.md`（流程規範骨幹：決議格式、cross-round-notes 溢出機制）

接下來按 finding 處理時逐一讀對應的 skill / planning 檔。

若 `reviews/round-8b-implement-review-report.md` 不存在，回報「找不到 R8b implement review 報告」並停止。

## 第二步：本輪範圍邊界

**在範圍內**：
- `reviews/round-8b-implement-review-report.md` 的 12 findings 決議
- 對 accept / accept-with-choice 的 finding：**直接修訂對應 skill / planning / proposal 檔**
- `TEMPLATE-LANGUAGE-GLOSSARY.md` 補 inclusion criteria（若 F-05 / F-12 拍板需要）
- `TEMPLATE-COVERAGE.md` 微調（若 F-06 拍板需要）
- `PROPOSAL-013` §1 anchor 表微調（若 F-06 Path A 拍板）
- `CHANGELOG.md` R8b 段內措辭修訂（F-03）
- 跨輪議題（CHANGELOG 自述精準度）—— Reviewer 已標明不轉記，本輪 inline 處理 F-03 時順手核對

**不在範圍內**：
- **PROPOSAL-013 狀態變動**（已 `approved`，本輪不動）
- **R8b implement 重做某個 sub-wave**（finding 都是 cascade 補完 / 設計邊界釐清，不需重做）
- **R8a 任何檔**（R8a 已收尾，不重審）
- **繁中版**（`*-tw/`）—— 延到 Closeout C1
- **新增任何 templates / scaffolding 檔**（Sub-wave 1 已完成，本輪不擴）
- 超出 12 findings 的議題 —— 若有發現，轉記 `reviews/cross-round-notes.md`

若使用者在互動中提出「順便重新規劃 R8b 結構」類需求，**提醒使用者本輪只 fix 12 findings，重結構是 Closeout / Post-R8 範圍**。

## 第三步：誤判警覺（R8b 版）

Reviewer（Cursor Claude Opus 4.7）的精準度**整體偏高**，每個 finding 都附 git 行號 + 對照表 + 對應 R8b decisions / Sub-wave cascade 編號。但仍留意以下可能：

1. **「cascade 漏網」vs「設計選擇」的判定**
   - F-01 是真 cascade 漏網（references 用「業務規則」但 templates 已 `Business Rules`）—— accept
   - F-10（finish-feature-flow.md Step 5 list items 中文）是 reviewer 標 Minor 但本質是設計選擇（範例 vs 結構），可能應移到 Question 或 reject
   - 處理時對 reviewer 標 Major / Minor 的「cascade 漏網」逐一 verify 是否真為漏網（vs 刻意保留為使用者範例）

2. **「Question」聚類成設計決策**
   - F-04 / F-05 / F-08 / F-12 共 4 個 Question 集中於 **2 個設計議題**：
     - **DD-A**：inline bold labels（`**目前進展** / **下一個動作** / **原本** / **改為** / **原因**`）算結構性還是散文 → 影響 F-04 + F-08
     - **DD-B**：GLOSSARY 收錄門檻（minimum viable vs 完整 H2 + sub-headings + inline labels）+ 使用位置語意 → 影響 F-05 + F-12
   - **建議將兩議題各做一次 path 拍板，連帶決議 4 個 Question** —— 不要 4 次重複討論

3. **F-06 的 path 偏好**
   - Reviewer 已主動建議「優先採前者（修 P013 §1 表 + 加 design note）」+ 提供具體修訂文字
   - 若使用者同意，accept；若使用者要求補 anchor（後者），accept-with-choice Path B

4. **F-10 是否屬「使用者範例邊界」**
   - Reviewer 對 F-10 給出兩個 path（一致英文化 vs 保留範例 + 補 note）
   - 與 F-04 / F-08 同邏輯（「結構性 vs 範例」邊界），可能應與 DD-A 一起拍板

5. **CHANGELOG 自述精準度（F-03）**
   - 真問題（CHANGELOG 寫「修改 _overview.md」實際未動）
   - 建議寫法 reviewer 已給；accept

## 第四步：你的角色定位

你有**雙重身份**：

1. **分析助理**：逐項分析 finding、提供建議決議、列出選項讓使用者拍板（特別 4 個 Question 的設計決策）
2. **Implement Fix 執行者**：使用者確認 accept 後，立即用 Edit 工具修訂對應 skill / planning / proposal 檔

你**不是**裁判 —— accept/reject/defer 的決定權在使用者。但你負責呈現清楚的選項讓使用者判斷，特別 2 個設計議題（DD-A / DD-B）需要把對 4 findings 的影響說清楚。

## 第五步：決議類型（沿用 R7 / R8a 慣例）

| 決議 | 何時採用 | 後續動作 |
|---|---|---|
| **accept** | Finding 是真問題，且修訂路徑明確單一 | 使用者確認後，當場用 Edit 改檔；在 decisions 記錄「具體改動摘要」 |
| **accept-with-choice** | Finding 是真問題，但修訂需使用者從 2+ 個 path 選一 | 呈現選項 table → 等使用者選 → 當場用 Edit 改檔 → 在 decisions 記錄「選定 path + 具體改動摘要」 |
| **reject** | 誤判；能清楚說出誤判點（引用第三步或具體反證） | 在 decisions 記錄 reject 理由 |
| **defer** | 真問題但現在不修（例如延到 Closeout） | 在 decisions 記錄 defer 理由 + 延到何時處理 |
| **clarify** | 不確定 finding 意圖或 reviewer 原意，需 reviewer 釐清 | 整理疑問點 → 等使用者回覆 → 轉為 accept / reject / defer |
| **out-of-scope** | 屬於其他輪範圍或 Closeout | 轉記到 `reviews/cross-round-notes.md`，從本輪排除 |

**重要**：R8b Implement Review Fix 優先使用 `accept`（當場改）。本輪預期：
- 8 個 Major + Minor 大多 `accept`
- 4 個 Question 大多 `accept-with-choice`（需使用者拍板 path）

## 第六步：處理方式（建議分批；不是一次處理一項）

對 12 findings，**建議分 3 批處理**（不是按 F-NN 數字順序）：

### Batch A — 機械 cascade 補完（先做，建立信心）

順序：F-01 → F-02 → F-09 → F-11

這 4 個都是「reviewer 已給具體替換清單，accept 後立刻改」性質。每項：

1. 重述核心（不超過 2 句）
2. 引用 reviewer 給的「具體建議」（替換清單 / 補欄字串）
3. 等使用者一次性 accept Batch A 全部（或逐項）
4. **連續用 Edit 工具改檔**（多檔多 Edit 並行）
5. 簡短回報：「Batch A 完成，修訂了 {X 檔 Y 處}」

### Batch B — 設計議題拍板（中段，影響後續）

順序：DD-A（影響 F-04 + F-08 + 可能 F-10）→ DD-B（影響 F-05 + F-12）

這 2 個議題的拍板會連帶處理 4 個 Question + 可能 1 個 Minor。每議題：

1. 引述涉及的 finding 編號與核心問題
2. 列出 **2-3 個 path table**：

   | Path | 描述 | 涵蓋的 finding | 影響範圍 |
   |---|---|---|---|
   | A | ... | F-04, F-08 | ... |
   | B | ... | F-04, F-08 | ... |

3. 給出**你的推薦 path** + 理由（一句話）
4. 等使用者選 path（可能需要 1-2 輪討論）
5. 使用者定案後，**基於該 path 連帶 accept 對應 finding，並 Edit 改對應檔**
6. 簡短回報：「DD-A Path X 拍板，連帶 accept F-04 / F-08，修訂了 {檔案清單}」

### Batch C — 剩餘 finding（最後）

順序：F-03 → F-06 → F-07 → F-10（若 DD-A 未涵蓋）

- F-03 (Minor accept): CHANGELOG 措辭修訂
- F-06 (Minor accept-with-choice): P013 §1 anchor 表修訂 vs 補 anchor 二選一
- F-07 (Minor accept): lightweight-spec.md Behavior Delta H3/H4 cascade
- F-10 (Minor): 視 DD-A 拍板而定，或獨立處理

每項標準互動模式（accept / accept-with-choice 二選一）。

### Batch 不適用「設計決策型」內含 cascade 跨檔聯動

DD-A / DD-B 的拍板可能涉及多檔聯動修訂。建議：
- 拍板後立刻 grep 確認影響範圍（例如 `grep -rn "目前進展\|下一個動作" templates/ references/`）
- decisions 文件需明列「DD-A Path X 拍板，影響 F-04 (檔X) / F-08 (檔Y) / F-10 (檔Z)，修訂位置：...」

## 第七步：互動範例

### Batch A 範例（一次性 accept）

> **Batch A — 4 個機械 cascade fix（reviewer 已給具體替換清單）：**
>
> **F-01 (Major, accept)**：references / templates/CLAUDE.md 對 templates 內已英文化 column / heading 的中文殘留引用，cascade 補完。Reviewer 給的替換清單：
>
> | 中文 | Canonical English | 影響檔 |
> |---|---|---|
> | 業務規則 | Business Rules | new-phase-flow.md L57 / L134 / L143 |
> | 現況規則 | Current Rule | new-phase-flow.md L167 等 |
> | 最後修訂 | Last Updated | new-phase-flow.md L165 等；modify-existing-flow.md L126 / L130 |
> | 首次出現 | First Seen | new-phase-flow.md L165 / L176；modify-existing-flow.md L125 / L129 |
> | 接續入口 | Resume Pointer | templates/CLAUDE.md 雙版 |
> | 輕量修改紀錄 | Lightweight Changes | templates/CLAUDE.md 雙版 |
>
> **F-02 (Major, accept)**：Core SKILL.md Scaffolding 表補一列 `architecture-decisions-README.md`。Reviewer 給的具體 markdown 行：`| `scaffolding/architecture-decisions-README.md` | ADR directory README seeded into ... |`
>
> **F-09 (Minor, accept)**：context-definition.md 雙版 L45 「行為情境」→ canonical `Behavior Scenarios`（保留中文 prose 包裝 canonical）
>
> **F-11 (Minor, accept)**：templates/CLAUDE.md 雙版 directory tree comment 對 `_index.md` 段名改英文（涵蓋 F-01 內 templates/CLAUDE.md 部分）
>
> 你 Batch A 全部 accept 嗎？或有特定 finding 想單獨討論？

### Batch B 範例（設計議題拍板）

> **DD-A — Inline bold labels 歸類：結構性 vs 散文（涵蓋 F-04 / F-08 + 可能 F-10）**
>
> 涉及位置：
> - `_index.md` Resume Pointer 段內 `**目前進展** / **下一個動作**`
> - `lightweight-spec.md` / `phase-spec.md` Behavior Delta 段內 `**原本** / **改為** / **原因**`
> - `finish-feature-flow.md` Step 5 list items（部分中文）
>
> P013 §1 第 6 點寫「H2/H3 結構性標題與 SKILL.md / flow 對齊處同步英文化，散文段落仍為使用者填寫範例，不在覆蓋範圍」—— 但 inline bold labels 介於兩者。
>
> **可選 path**：
>
> | Path | 描述 | F-04 | F-08 | F-10 | GLOSSARY 影響 |
> |---|---|---|---|---|---|
> | A（全結構性，英文化）| 視 inline bold labels 為結構性 fields | `**Current Progress** / **Next Action**` | `**Before** / **After** / **Reason**` | list items 英文化 | +5 term |
> | B（全散文，保留中文 + 補 note）| 視 inline bold labels 為填寫範例 | 保留中文 + template note | 保留中文 + template note | 保留中文 + note | 不變 |
> | C（混合：bold labels 結構 / list items 範例）| 區分 bold labels 與 prose list | A | A | B（list 中文）| +5 term |
>
> **我的推薦**：Path B（全散文）。理由：P013 §1 已強調「散文段落仍為使用者填寫範例」；inline labels 是讓讀者了解該段該寫什麼的範例性 hint，跟 H2/H3 級結構性 heading 性質不同。Path A 會讓 GLOSSARY 與每個 template inline 都需同步維護（高 overhead）；Path B 維持單純規則（只有 H2/H3 是結構性）+ 補 note 一勞永逸。
>
> 你傾向哪個 path？

## 第八步：完成判定

全部 12 finding 處理完後：

### 完成條件

- 所有 accept / accept-with-choice 已實際修訂完成
- 所有 reject / defer 在 decisions 記錄理由
- 沒有 clarify 未解決
- 跨輪議題（CHANGELOG 自述精準度）已在 F-03 順手處理

### Proposal 狀態

PROPOSAL-013 狀態**維持 `approved`**（本輪不推進）。

### Sub-wave 重做判定

本輪 finding 0 Critical + 2 Major（皆為 cascade 補完）+ 8 Minor / Question；**無需重做任何 sub-wave**。R8b implement 整體保留。

## 第九步：Decisions 文件產出

寫入 `reviews/round-8b-implement-review-decisions.md`：

```markdown
# Round 8b Implement Review Decisions — 12 findings 處理 + Fix

**對應 review 報告**: `reviews/round-8b-implement-review-report.md`
**決議日期**: YYYY-MM-DD
**決議者**: 使用者（Claude Opus 4.7 整理 + 當場修訂）
**Reviewer 模型**: Cursor Claude Opus 4.7
**Session 模式**: Approve + Fix 合併 session（仿 R8a Implement Review Fix pattern）

## 決議彙總

| Finding | 嚴重度 | 維度 | 決議 | 說明 |
|---|---|---|---|---|
| F-01 | Major | D2 | accept | references / templates/CLAUDE.md cascade 補完 |
| F-02 | Major | D2 | accept | Core SKILL.md Scaffolding 表補列 architecture-decisions-README.md |
| F-03 | Minor | D1 | accept | CHANGELOG R8b 段 _overview.md 措辭修訂 |
| F-04 | Question | D5 | accept-with-choice (DD-A Path X) | inline bold labels 歸類 |
| F-05 | Question | D5 | accept-with-choice (DD-B Path X) | GLOSSARY 收錄門檻 |
| F-06 | Minor | D5 | accept-with-choice (Path X) | P013 §1 anchor 表 vs _index.md 補 anchor |
| F-07 | Minor | D5 | accept | lightweight-spec.md Behavior Delta H3/H4 cascade |
| F-08 | Question | D5 | accept-with-choice (DD-A Path X) | inline bold labels 歸類（連 F-04） |
| F-09 | Minor | D2 | accept | context-definition.md prose 引用 cascade |
| F-10 | Minor | D3 | {accept / accept-with-choice / reject} | finish-feature-flow.md Step 5 list items |
| F-11 | Minor | D3 | accept | templates/CLAUDE.md directory tree comment cascade |
| F-12 | Question | D5 | accept-with-choice (DD-B Path X) | GLOSSARY 使用位置語意（連 F-05） |

**統計**：accept {N} / accept-with-choice {N} / reject {N} / defer {N} / clarify {N} / out-of-scope {N}

## 設計議題拍板紀錄

### DD-A · Inline bold labels 歸類（影響 F-04 / F-08 / 可能 F-10）

- **拍板 path**: {A / B / C}
- **理由**: {使用者拍板理由}
- **連帶決議**: F-04 → {accept-with-choice DD-A Path X}；F-08 → {同上}；F-10 → {視拍板}

### DD-B · GLOSSARY 收錄門檻 / 使用位置語意（影響 F-05 / F-12）

- **拍板 path**: {A / B / C}
- **理由**: {使用者拍板理由}
- **連帶決議**: F-05 → {accept-with-choice DD-B Path X}；F-12 → {同上}

## Accept / Accept-with-choice 項目的修訂紀錄

### F-01 (accept)
- **修訂檔案**: ...
- **改動摘要**: ...

### F-02 (accept)
- ...

（逐項）

## Reject / Defer 項目備忘

（若有）

## 修改檔案彙總

本輪 fix 共修改 {N} 檔：

| 檔案 | 對應 Finding | 修改性質 |
|---|---|---|
| ... | ... | ... |

新增 1 檔：
- `reviews/round-8b-implement-review-decisions.md`（本文件）

## R8b 整體收尾狀態

| 項目 | 狀態 |
|---|---|
| PROPOSAL-013 狀態 | approved（本輪不變動） |
| R8b Implement | 完成（4 sub-waves + 9 implement commits） |
| R8b Implement Review | 完成（Cursor Claude Opus 4.7，12 findings） |
| R8b Implement Review Fix | 完成（本 session） |
| 雙版 skill / scaffolding / templates / references / SKILL.md cascade 一致性 | ✓ |
| 4 項設計決策（canonical English / anchors / COVERAGE / GLOSSARY）執行品質 | ✓ |

**R8b 全程結束。**

## 下一步

1. **R8b 收尾 commit**：將本輪 fix + 本決議文件打包 commit。
2. **R8 全程結束** → 進入 Post-R8：
   - Closeout C1（繁中版同步）
   - Closeout C2（Tutorial 重建）
   - 改名遷移新 repo（`dflow-greenfield-skill` / `dflow-brownfield-skill`）→ Dflow V1 ships
3. **跨輪議題追蹤**：CHANGELOG 自述精準度議題（F-03 已 inline 處理）；R8a 期 cross-round-notes 議題 4（README 引用 git-flow-integration.md 殘留）仍待 Closeout 處理

## 跨輪發現備忘

（若有新發現）
```

## 禁止事項

- **不要動 PROPOSAL-013 狀態**（已 approved，本輪不變動）
- **不要重做任何 sub-wave**（finding 不需 redo level）
- **不要動 R8a 任何檔**（R8a 已收尾）
- **不要動繁中版檔**（`*-tw/`）—— 延到 Closeout C1
- **不要修改 review 報告**（`reviews/round-8b-implement-review-report.md`）
- **不要在單次回覆處理多個 finding**（除 Batch A 機械 cascade 一次性 accept、與 Batch B 設計議題 path 拍板呈現外）
- **不要自行替使用者拍板 DD-A / DD-B**

## 完成後回報

> 「Round 8b Implement Review Approve / Fix 階段完成。
> - 共 12 finding 處理完畢：accept {X} / accept-with-choice {Y} / reject {Z} / defer {W} / clarify→XX {V} / out-of-scope {U}
> - 2 個設計議題拍板：
>   - DD-A (inline bold labels): Path {A/B/C}
>   - DD-B (GLOSSARY 收錄): Path {A/B/C}
> - PROPOSAL-013 狀態：approved（不變）
> - 決議文件：`reviews/round-8b-implement-review-decisions.md`
> - 修訂的檔案：列出具體檔案數
> - R8b 全程結束。
> - 下一步：Post-R8（Closeout C1 / C2 / 改名遷移新 repo / Dflow V1 ships）」

---END PROMPT---
