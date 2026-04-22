# Round 7 Approve Prompt — PROPOSAL-009 / 010 / 011 Draft Findings 決議（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：
>   - `reviews/round-7-decisions.md`（主要產出）
>   - 3 份 draft proposal 的**修訂**（若有 accept / accept-with-choice finding，**直接在本 session 改 proposal**）
>   - 若所有 finding 處理完畢且 proposal 狀態滿足推進條件，將 proposal 狀態從 `draft` 推進到 `approved`
> **與 R1–R6 Approve 的根本差異**：
>   - R1–R6：Approve 只整理決議，改動延到 Implement session（因 Implement 改的是實作檔，需隔一層保護）
>   - R7：Approve 決議**+ 當場修訂 proposal**（因為 proposal 本身就是設計文件，accept 的 finding 就是改 proposal 文字；隔一層 revise session 反而增加成本）
>   - R7 Implement 階段（分 3 session）改的是 skill 本體，不動 proposal
> **本輪 finding 概況**：Codex 產出 7 個 finding（0 Critical / 6 Major / 1 Minor / 0 Question），建議三份均 revise-and-resubmit；其中 F-01 / F-02 / F-04 為**設計決策型**（需使用者拍板選項），F-03 / F-05 / F-06 / F-07 為**精準度型**（相對機械修正）。
> **預估時程**：~60-90 分鐘（逐項互動 + 當場修訂 + 狀態推進）；若中途判斷改動過複雜，可提前結束並用 `defer-to-revise` 類型 finding 交棒給另起的 revise session。

---BEGIN PROMPT---

這是 PROPOSAL-009 / 010 / 011 draft review 的**決議階段**（Round 7 / Approve），也是 R7 的核心人類判斷點。與 R1–R6 的 Approve 不同：本輪決議後，若使用者確認 accept，你需要**直接在本 session 內修訂對應的 draft proposal**，並在全部處理完後推進 proposal 狀態。

## 第一步：依序讀入下列檔案

1. `reviews/round-7-report.md`（Codex 產出的審查報告——主要分析對象，7 個 finding）
2. `proposals/review-workflow.md`（流程規範骨幹：角色分工、決議格式、cross-round-notes 溢出機制）
3. `reviews/prompts/round-7-review.md`（R7 Review 的邊界定義——特別第二步 Dimension 3 的 8 項 cross-consistency 檢查點）
4. `proposals/PROPOSAL-011-git-flow-decoupling.md`（待修訂 1/3）
5. `proposals/PROPOSAL-009-feature-directory-and-phases.md`（待修訂 2/3）
6. `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md`(待修訂 3/3)
7. `reviews/field-reference-candidates.md`（背景：候選決議脈絡；若使用者對某 finding 的拍板需要回到原始決議時引用）
8. `reviews/cross-round-notes.md`（若存在）——僅供參考

若 `reviews/round-7-report.md` 不存在，回報使用者「找不到 R7 review 報告，是否 Codex review 尚未完成？」然後停止。

## 第二步：本輪範圍邊界

**在範圍內**：
- `reviews/round-7-report.md` 的 7 個 Finding（F-01 ~ F-07）的決議
- 對 accept / accept-with-choice 的 Finding：**直接修訂 3 份 draft proposal 的對應段落**
- 全部處理完後推進 proposal 狀態（draft → approved，條件見第七步）

**不在範圍內**：
- **任何 skill 檔**（`sdd-ddd-*-skill/` 下所有檔）— 改 skill 是 R7 Implement 階段的工作
- **繁中版**（`*-tw/`）— 延到 Post-Review Closeout C1
- **新增 proposal 或生成新的 scaffolding / template / flow 檔**— 本輪只修訂 draft proposal 的**文字**
- 超出 7 個 Finding 的議題 — 若有發現，轉記 `reviews/cross-round-notes.md`

若使用者在互動中提出「順便改一下 skill 某段」類需求，**提醒使用者這是 Implement 階段工作**，不在本輪處理。

## 第三步：誤判警覺（R7 版）

Codex 本輪報告的精準度**整體偏高**，每個 finding 都有具體行號對照與問題診斷；不預期大量誤判。但仍留意以下可能：

1. **把「proposal 刻意保留的設計彈性」誤判為「模糊」**
   - 某些段落確實需要「實施時評估」的彈性（例如範本格式細節）
   - 但 F-01 的 `phase-spec / feature-spec / lightweight-spec` 終局策略**不是**可延後的彈性——它會直接決定多個文件的引用，Codex 判 Major 合理
   - 處理時區分「真彈性」vs「核心未決」

2. **Major 應該是 Minor 的可能**
   - F-07（README 措辭自相矛盾）Codex 已標 Minor，合理
   - F-03（`/dflow:verify` scope 混淆）理論上可降為 Minor（純 scope 精準度問題）；但 Codex 判 Major 的理由是「會引入與現行 verify 無關的新需求」，這個顧慮合理
   - 若有類似爭議，讓使用者判斷

3. **跨 proposal finding 的分類**
   - F-04 同時影響 009 / 010 / 011，Codex 歸給 009（因為主要主張來自 009 的 `/dflow:finish-feature`）
   - 處理 F-04 的 accept 時，**三份 proposal 的相關段落可能都要改**，不只改 009

4. **越界評論 proposal 的設計方向**
   - R6 曾見「評論 proposal 本身值不值得做」的誤判
   - R7 Codex 沒有這類錯，但若後續追問中出現，回到「proposal 本身的設計方向已由 field-reference-candidates.md Phase 1-2 背書」拒絕

5. **「現況對照」引用行號的正確性**
   - Codex 引用的行號（例如 `sdd-ddd-core-skill/SKILL.md:331-338`）若你實際讀檔時對不上，**先懷疑 Codex 引用的檔版本是否已變**，再判斷是否 finding 本身失效
   - 若確認引用錯誤但 finding 描述的問題仍成立，視為 accept（只是 Codex 引用行號待 implement 時再校正）

## 第四步：你的角色定位

你有**雙重身份**：

1. **分析助理**：逐項分析 finding、提供建議決議、列出選項讓使用者拍板
2. **Proposal 修訂執行者**：使用者確認 accept 後，立即用 Edit 工具修訂對應 proposal

你**不是**裁判——accept/reject/defer 的決定權在使用者。但你負責呈現清楚的選項讓使用者判斷。

## 第五步：決議類型（R7 擴充）

| 決議 | 何時採用 | 後續動作 |
|---|---|---|
| **accept** | Finding 是真問題，且修訂路徑明確單一 | 使用者確認後，當場用 Edit 改 proposal；在 decisions 記錄「具體改動摘要」 |
| **accept-with-choice** | Finding 是真問題，但修訂需使用者從 2+ 個 path 選一 | 呈現選項 table → 等使用者選 → 當場用 Edit 改 proposal → 在 decisions 記錄「選定 path + 具體改動摘要」 |
| **reject** | 誤判；能清楚說出誤判點（引用第三步或具體反證） | 在 decisions 記錄 reject 理由 |
| **defer** | 真問題但現在不修（例如需跨多 wave 協調、或超出 R7 實施容量） | 在 decisions 記錄 defer 理由 + 延到何時處理 |
| **clarify** | 不確定 finding 意圖或 proposal 原意，需設計者釐清 | 整理疑問點 → 等使用者回覆 → 轉為 accept / reject / defer |
| **out-of-scope** | 屬於其他輪範圍或 Closeout | 轉記到 `reviews/cross-round-notes.md`，從本輪排除 |
| **defer-to-revise** | Accept 但改動太複雜，本 session 無法當場修訂 | 在 decisions 明確寫「具體改動指引」給另起的 revise session |

**重要**：R7 優先使用 `accept`（當場改）而非 `defer-to-revise`（另起 session）。只有在改動涉及大段重寫、或使用者明確要求分 session 時才用 `defer-to-revise`。

## 第六步：處理方式（一次一項，嚴格不 dump）

對 7 個 Finding，依編號順序（F-01 → F-07）處理：

### 對「精準度型」Finding（F-03, F-05, F-06, F-07）

1. 重述核心（不超過 2 句）
2. 說出建議決議（預期多為 accept）
3. 給出具體改動草案（proposal 的哪段、改成什麼，儘量引用現有文字）
4. 等使用者確認
5. 使用者 accept → **立刻用 Edit 工具改 proposal**
6. 改完後簡短回報「已修訂 `PROPOSAL-0XX.md § 某段`，改動摘要：...」
7. 進下一 Finding

### 對「設計決策型」Finding（F-01, F-02, F-04）

這類 Finding 的修訂**需要使用者先做設計選擇**。處理模式不同：

1. 重述核心 + 為什麼需要使用者拍板（引用 finding 的 `建議` 欄）
2. 列出 **2-4 個可選 path**（table 形式）：

   | Path | 描述 | 優點 | 缺點 | 對其他 proposal 的影響 |
   |---|---|---|---|---|
   | A | ... | ... | ... | ... |
   | B | ... | ... | ... | ... |

3. 給出**你的推薦 path** + 理由（一句話）
4. 等使用者選 path（可能需要 1-2 輪來回討論）
5. 使用者定案後，**基於該 path 用 Edit 改 proposal**（可能涉及三份 proposal 的多個段落）
6. 改完後簡短回報改動範圍

### 特別處理：F-04（跨三份 proposal 的 finding）

F-04 涉及 009 的 `/dflow:finish-feature`、011 的 Git 中立目標、010 的 Git-principles 設計。處理時：
1. 先呈現 F-04 的選項 table（例如：path A「改措辭為 integration summary」/ path B「維持但限定條件」/ path C「整段移除」）
2. 使用者定案後，**同時檢查三份 proposal 是否需要聯動修訂**（Codex 的對照欄已點出三處：P009:77-85 / P011:49-54 / P010:106-110）
3. 每處修訂都要用 Edit；decisions 文件標示「影響 009 § X.X / 011 § Y.Y / 010 § Z.Z」

### 互動範例（精準度型）

> **F-07：PROPOSAL-010 的 README 範例寫死 `.claude/skills/`，與自己的風險緩解（風險 6）互相抵觸**
>
> 核心：proposal 在範例段落寫「copy ... to your `.claude/skills/`」，但同一份 proposal 的風險 6 又警告「不寫死路徑細節」。內部自相矛盾。
>
> **建議決議：accept**（屬 Minor，但改動極輕）
>
> **具體改動草案**（PROPOSAL-010 § 3. 更新 README.md，約 proposals/PROPOSAL-010-dflow-init-and-scaffolding.md:114-121）：
> 把範例從
> > ```
> > 1. Install the skill (copy sdd-ddd-core-skill/ or sdd-ddd-webforms-skill/ to your .claude/skills/)
> > ```
> 改為
> > ```
> > 1. Install the appropriate skill using the current Claude Code skill mechanism (see Claude Code official docs)
> > ```
>
> 你確認這個決議嗎？或想另行措辭？

### 互動範例（設計決策型）

> **F-01：PROPOSAL-009 對 phase-spec / feature-spec / lightweight-spec 的終局策略停在選項層**
>
> 核心：proposal 同時寫「新增（或重命名）」、「刪除 / 重命名」、「修改或保留」，把是否保留獨立 lightweight 模板留到實施時決定。Codex 指出這會讓 new-feature-flow、modify-existing-flow、SKILL.md 的修訂無法一致落稿。
>
> **建議決議：accept-with-choice**（需你選一個終局 path）
>
> **可選 path**：
>
> | Path | phase-spec.md | feature-spec.md | lightweight-spec.md | 影響面 |
> |---|---|---|---|---|
> | A（純重命名）| 由 feature-spec.md `git mv` 重命名而來 | 不存在（被重命名） | 保留獨立檔，不併入 `_index.md` | 最小改動；多數 flow 只需改檔名 |
> | B（雙軌過渡）| 新增，與 feature-spec.md 並存 6 個月 | 標 deprecated，過渡期後移除 | 保留 | 短期雙軌但長期複雜 |
> | C（併入 `_index.md`）| 重命名 | 不存在 | **刪除**，輕量修改段併入 `_index.md` | 範本減 1；但 `_index.md` 範本會變厚 |
>
> **我的推薦**：Path A。理由：最小改動、維持現有使用者 mental model、lightweight-spec.md 是獨立職責（不是 phase-spec 的「低階版本」）。
>
> 你傾向哪個 path？或有其他 path 想討論？

### Finding 數量為 0 的情境

本輪不適用（Codex 已產 7 個 finding）。

## 第七步：Proposal 狀態推進規則

全部 Finding 處理完後，對每份 proposal 判斷是否推進狀態：

### 推進條件（draft → approved）

一份 proposal 滿足**全部**條件才推進：
- (a) 所有 accept / accept-with-choice 的 finding 已在本 session 實際修訂完成
- (b) 沒有 `clarify` 類 finding 未解決
- (c) 沒有 `defer-to-revise` 類 finding（因為還需後續 session 才能完成）
- (d) `defer` 不擋 approve（延到後續 wave 處理，但 proposal 本身可 approved）
- (e) `reject` 不擋 approve（誤判已澄清）

### 狀態維持 draft 的情境

任一 proposal 有未解決的 clarify 或 defer-to-revise → 維持 draft，decisions 標註「待 revise session 處理完後再次評估」。

### 推進動作（每份獨立評估）

若 proposal X 滿足推進條件：
1. 用 Edit 改 `proposals/PROPOSAL-0XX-*.md` 的 `**狀態**:` 欄從 `draft` 改為 `approved`
2. 填寫 proposal 末尾的「評估紀錄」欄：
   ```markdown
   ### 評估紀錄
   
   **評估日期**: {YYYY-MM-DD}
   
   **結論**: approved
   
   **理由**: 經 R7 review（2026-04-22，Codex）+ R7 approve（本 session 日期）處理 {N} 個 finding（accept: {X}, accept-with-choice: {Y}, reject: {Z}, defer: {W}），所有需修訂部分已在 R7 approve session 內完成。下一步進入 R7 Implement 階段（見 `field-reference-candidates.md` Phase 3 與 `reviews/prompts/round-7-implement-0XX.md`）。
   ```

## 第八步：Decisions 文件產出

寫入 `reviews/round-7-decisions.md`，格式仿 `proposals/review-workflow.md` §三，但增加 R7 專屬段落。

```markdown
# Round 7 Decisions — Draft Proposal Review (P009 / P010 / P011)

**對應 review 報告**: `reviews/round-7-report.md`
**決議日期**: YYYY-MM-DD
**決議者**: {使用者}（Claude 整理 + 當場修訂）

## 決議彙總

| Finding | Proposal | 嚴重度 | 維度 | 決議 | 說明 |
|---|---|---|---|---|---|
| F-01 | 009 | Major | D1 | accept-with-choice (Path A) | 選定：feature-spec.md 重命名為 phase-spec.md；lightweight-spec.md 保留獨立檔 |
| F-02 | 009 | Major | D1 | accept | 補 completed feature reopen lifecycle 規則 |
| F-03 | 009 | Major | D1 | ... | ... |
| F-04 | 009/010/011 | Major | D3 | ... | ... |
| F-05 | 010 | Major | D4 | ... | ... |
| F-06 | 011 | Major | D1 | ... | ... |
| F-07 | 010 | Minor | D2 | ... | ... |

## Accept / Accept-with-choice 項目的修訂紀錄

### F-01 (accept-with-choice, Path A)
- **修訂檔案**: `proposals/PROPOSAL-009-feature-directory-and-phases.md`
- **修訂段落**: § 影響範圍（Templates）；§ 結構類（決議 5）；§ 命名類（決議 7）
- **改動摘要**: {具體措辭變更摘要}
- **交叉影響**: {若有，列出其他 proposal 的聯動修訂}

### F-02 (accept)
- ...

（逐項）

## Reject / Defer 項目備忘

（若有）

## Out-of-scope 項目

（若有，已轉 cross-round-notes.md）

## Proposal 狀態推進紀錄

| Proposal | 推進前 | 推進後 | 推進理由 / 維持理由 |
|---|---|---|---|
| PROPOSAL-011 | draft | approved | 所有 accept finding 已修訂完成（F-04 交叉段、F-06） |
| PROPOSAL-009 | draft | approved | 所有 accept / accept-with-choice finding 已修訂完成（F-01~F-04） |
| PROPOSAL-010 | draft | approved | 所有 accept finding 已修訂完成（F-04 交叉段、F-05、F-07） |

## 下一步

- R7 Implement 階段（分 3 個 session，依 `field-reference-candidates.md` Phase 3 AI 分派表執行）：
  - Session 1: PROPOSAL-011（small，建議 Cursor Opus 4.7 High）
  - Session 2: PROPOSAL-009（large，建議 Claude Code 主力）
  - Session 3: PROPOSAL-010（medium，建議 Cursor Opus 4.7 High）
- 各 session 的啟動提示詞：待 R7-Approve 完成後另起（`reviews/prompts/round-7-implement-011.md` / `...-009.md` / `...-010.md`）
- R7 Implement 完成後進入 R7 實作品質審查（Codex），仿 R1-R6 pattern

## 跨輪發現備忘

（若有）
```

## 禁止事項

- **不要改任何 skill 檔**（`sdd-ddd-*-skill/` 下所有檔）—— 那是 Implement 階段的職責
- **不要改繁中版檔**（`*-tw/`）—— 延到 Closeout C1
- **不要修改 review 報告**（`reviews/round-7-report.md`）
- **不要在單次回覆處理多個 Finding**（設計決策型選項呈現除外）
- **不要自行替使用者拍板設計決策型 Finding 的 path 選擇**
- **不要在 proposal 狀態尚未滿足推進條件時就改 `approved`**（特別是還有 clarify 未解決時）

## 特別提醒：F-04 的跨 proposal 聯動

F-04 是本輪唯一跨三份 proposal 的 finding。處理時：
- 使用者定案「merge commit 訊息」的最終措辭後，**檢查三份 proposal**：
  - 009 § 命令類（決議 10）—— 改 `/dflow:finish-feature` 的產出描述
  - 011 § 雙層區分 —— 若有提及 merge strategy，確認措辭中立
  - 010 § Git-principles 二版本 —— 若有提及 merge commit 訊息格式，確認與 009 的新措辭協調
- Codex 已點出具體行號（report F-04 對照欄），逐行 verify 後用 Edit 修訂

## 完成後回報

> 「Round 7 approve 階段完成。
> - 共 7 個 Finding 處理完畢：accept {X} / accept-with-choice {Y} / reject {Z} / defer {W} / clarify→XX {V} / out-of-scope {U}
> - Proposal 狀態推進：
>   - PROPOSAL-011: draft → {approved / 仍 draft，理由 ...}
>   - PROPOSAL-009: draft → {approved / 仍 draft，理由 ...}
>   - PROPOSAL-010: draft → {approved / 仍 draft，理由 ...}
> - 決議文件：`reviews/round-7-decisions.md`
> - 修訂的 proposal 檔：列出 3 份的具體修訂段落數
> - 下一步：
>   - 若三份皆 approved：進入 R7 Implement 階段，各 session 的啟動提示詞待另起
>   - 若有 proposal 仍 draft：說明阻塞原因 + 建議的 revise / clarify session 下一步」

---END PROMPT---
