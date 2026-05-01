# Round 8a Approve Prompt — PROPOSAL-012 Draft Findings 決議（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：
>   - `reviews/round-8a-decisions.md`（主要產出）
>   - PROPOSAL-012 的**修訂**（若有 accept / accept-with-choice finding，**直接在本 session 改 proposal**）
>   - 若所有 finding 處理完畢且 proposal 狀態滿足推進條件，將 proposal 狀態從 `draft` 推進到 `approved`
> **與 R7 Approve 的差異**：
>   - R7：3 份相互依賴的 proposal，需處理跨 proposal 聯動修訂（如 F-04）
>   - R8a：單一 proposal，無跨 proposal 聯動；finding 預期 2-6 項、多為精準度型（影響範圍漏列、邊界描述）
>   - R8a 仍沿用「Approve 當場修訂 proposal」的 R7 慣例（不延到 revise session）
> **本輪 finding 概況**：待 R8a Review 完成後填入；預期多為 Major / Minor 等級的精準度修正，少量 Question 類型。
> **預估時程**：~30-60 分鐘（範圍小，2-6 個 finding 的逐項處理）

---BEGIN PROMPT---

這是 PROPOSAL-012（目錄與檔名英文化的社群散佈預備調整）draft review 的**決議階段**（Round 8a / Approve）。本輪是 R8 兩波中的第一波（前置 path policy），R8b 將處理 PROPOSAL-013（樣板覆蓋）。

與 R1–R6 不同：本輪決議後，若使用者確認 accept，你需要**直接在本 session 內修訂 PROPOSAL-012**，並在全部處理完後推進 proposal 狀態。

## 第一步：依序讀入下列檔案

1. `reviews/round-8a-report.md`（Reviewer 產出的審查報告——主要分析對象）
2. `reviews/prompts/round-8a-review.md`（R8a Review 的邊界定義——特別第二步 Dimension 3 影響範圍與 Dimension 4 邊界守住）
3. `proposals/PROPOSAL-012-distribution-friendly-paths.md`（待修訂）
4. `planning/public-distribution-backlog.md`（背景：邊界對方檔；若 finding 涉及 backlog 邊界爭議時引用）
5. `planning/project-review-brief.md`（背景：已知 P012 落地後此檔的 3 處 `_共用` 引用會過時；若 finding 處理此問題時引用）
6. `proposals/review-workflow.md`（流程規範骨幹）
7. `reviews/cross-round-notes.md`（若存在）——僅供參考

若 `reviews/round-8a-report.md` 不存在，回報使用者「找不到 R8a review 報告，是否 review 尚未完成？」然後停止。

## 第二步：本輪範圍邊界

**在範圍內**：
- `reviews/round-8a-report.md` 的所有 Finding 的決議
- 對 accept / accept-with-choice 的 Finding：**直接修訂 PROPOSAL-012 對應段落**
- 全部處理完後推進 PROPOSAL-012 狀態（draft → approved，條件見第七步）

**不在範圍內**：
- **PROPOSAL-013**（R8b 的 target，本輪不處理）
- **任何 skill 檔**（`sdd-ddd-*-skill/` 下所有檔）— 改 skill 是 R8a Implement 階段的工作
- **繁中版**（`*-tw/`）— 延到 Closeout C1
- **`planning/project-review-brief.md` 的實際更新** — 若 finding 處理此檔，方式是把它加入 PROPOSAL-012 §影響範圍，而不是現在直接改該檔（直接改是 Implement 階段的事）
- 超出 review 報告 Finding 的議題 — 若有發現，轉記 `reviews/cross-round-notes.md`

若使用者在互動中提出「順便改一下 skill 某段」類需求，**提醒使用者這是 Implement 階段工作**，不在本輪處理。

## 第三步：誤判警覺（R8a 版）

R8a 範圍小、scope 機械，預期誤判率極低。仍留意以下可能：

1. **影響範圍對照表的「✗ 應列入但未列」判定**
   - Reviewer 應該已用 grep `_共用` 證據填表；確認對照表的「實際出現位置（行號）」欄是否有實際數據
   - 若 reviewer 只填 ✓/✗ 而沒附行號，視為證據不足 → 你需要在本 session 自行 grep 補證

2. **`templates/CLAUDE.md` 是否該保留在影響範圍**
   - P012 列為「修改」，但 grep 顯示這兩檔目前可能不含 `_共用` 引用
   - 若 reviewer 建議移出，先確認該檔是否有專案結構範例段落需更新；若無，accept 移出
   - 若 reviewer 建議改為「檢查」標記（無變動但需確認），合理 → accept

3. **`planning/project-review-brief.md` 的處理方式**
   - 此檔有 3 處前瞻性 `_共用` 引用（line 51, 59, 111），P012 落地後變過時描述
   - 可能的 finding 建議：(a) 加入 §影響範圍 / (b) 由實作者自行同步、不入 P012 / (c) 完全不處理
   - 若 reviewer 標 Question，你需要呈現 path table 讓使用者選

4. **邊界越界的指控**
   - 若 reviewer 指控 P012 踩進 P013 / backlog 範圍，先回到 backlog 與 P013 對應段落確認，再判斷是否真的越界
   - 若 reviewer 沒附對方檔段落引用，視為證據不足，可能是誤判

5. **「Reviewer 沒填對照表」的處理**
   - R8a Review prompt 第四步明確要求對照表必填且要附行號
   - 若 reviewer 漏填 / 簡略填，本 session 你需要自行補完對照表（用 grep）；若補完後與 reviewer 結論不符，回報使用者

## 第四步：你的角色定位

你有**雙重身份**：

1. **分析助理**：逐項分析 finding、提供建議決議、列出選項讓使用者拍板
2. **Proposal 修訂執行者**：使用者確認 accept 後，立即用 Edit 工具修訂 PROPOSAL-012

你**不是**裁判——accept/reject/defer 的決定權在使用者。但你負責呈現清楚的選項讓使用者判斷。

## 第五步：決議類型（沿用 R7 慣例）

| 決議 | 何時採用 | 後續動作 |
|---|---|---|
| **accept** | Finding 是真問題，且修訂路徑明確單一 | 使用者確認後，當場用 Edit 改 proposal；在 decisions 記錄「具體改動摘要」 |
| **accept-with-choice** | Finding 是真問題，但修訂需使用者從 2+ 個 path 選一 | 呈現選項 table → 等使用者選 → 當場用 Edit 改 proposal → 在 decisions 記錄「選定 path + 具體改動摘要」 |
| **reject** | 誤判；能清楚說出誤判點（引用第三步或具體反證） | 在 decisions 記錄 reject 理由 |
| **defer** | 真問題但現在不修（例如延到 Implement 階段、延到 backlog） | 在 decisions 記錄 defer 理由 + 延到何時處理 |
| **clarify** | 不確定 finding 意圖或 proposal 原意，需設計者釐清 | 整理疑問點 → 等使用者回覆 → 轉為 accept / reject / defer |
| **out-of-scope** | 屬於其他輪範圍或 Closeout | 轉記到 `reviews/cross-round-notes.md`，從本輪排除 |
| **defer-to-revise** | Accept 但改動太複雜，本 session 無法當場修訂 | 在 decisions 明確寫「具體改動指引」給另起的 revise session |

**重要**：R8a 優先使用 `accept`（當場改）。範圍小、findings 多為精準度型，預期不需要 `defer-to-revise`。

## 第六步：處理方式（一次一項，嚴格不 dump）

對所有 Finding，依編號順序（F-01 → F-NN）處理。

### 對「精準度型」Finding（預期多數）

1. 重述核心（不超過 2 句）
2. 說出建議決議（預期多為 accept）
3. 給出具體改動草案（PROPOSAL-012 的哪段、改成什麼，儘量引用現有文字）
4. 等使用者確認
5. 使用者 accept → **立刻用 Edit 工具改 proposal**
6. 改完後簡短回報「已修訂 `PROPOSAL-012.md § 某段`，改動摘要：...」
7. 進下一 Finding

### 對「設計決策型」Finding（預期少數，可能出現在 D5 風險與假設）

1. 重述核心 + 為什麼需要使用者拍板
2. 列出 **2-3 個可選 path**（table 形式）
3. 給出**你的推薦 path** + 理由（一句話）
4. 等使用者選 path
5. 使用者定案後，用 Edit 改 proposal
6. 改完後簡短回報改動範圍

### 互動範例（精準度型）

> **F-NN：`planning/project-review-brief.md` 有 3 處 `_共用` 引用未列入 P012 影響範圍**
>
> 核心：reviewer 確認該檔 line 51, 59, 111 含前瞻性 `_共用` 引用，P012 落地後此寫法會過時。P012 §影響範圍 未列入此檔。
>
> **建議決議：accept**（屬 Major，影響範圍完整性）
>
> **具體改動草案**（PROPOSAL-012 § 影響範圍表，新增一列）：
> ```markdown
> | `planning/project-review-brief.md` | 修改 | 將 line 51, 59, 111 的前瞻性 `_共用` 引用更新為 `shared`；移除「P012 後預期改名」的過渡性註解 |
> ```
>
> 你確認這個決議嗎？或想另行措辭（例如改為「實作時同步、不入正式影響範圍」）？

## 第七步：Proposal 狀態推進規則

全部 Finding 處理完後判斷是否推進狀態：

### 推進條件（draft → approved）

PROPOSAL-012 滿足**全部**條件才推進：
- (a) 所有 accept / accept-with-choice 的 finding 已在本 session 實際修訂完成
- (b) 沒有 `clarify` 類 finding 未解決
- (c) 沒有 `defer-to-revise` 類 finding
- (d) `defer` 不擋 approve（延到後續處理，但 proposal 本身可 approved）
- (e) `reject` 不擋 approve（誤判已澄清）

### 狀態維持 draft 的情境

有未解決的 clarify 或 defer-to-revise → 維持 draft，decisions 標註「待 revise session 處理完後再次評估」。

### 推進動作

若 PROPOSAL-012 滿足推進條件：
1. 用 Edit 改 `proposals/PROPOSAL-012-distribution-friendly-paths.md` 的 `**狀態**:` 欄從 `draft` 改為 `approved`
2. 填寫 proposal 末尾的「評估紀錄」欄：
   ```markdown
   **評估日期**: {YYYY-MM-DD}

   **結論**: approved

   **理由**: 經 R8a review（{review 日期}，{Cursor 模型}）+ R8a approve（本 session 日期）處理 {N} 個 finding（accept: {X}, accept-with-choice: {Y}, reject: {Z}, defer: {W}），所有需修訂部分已在 R8a approve session 內完成。下一步進入 R8a Implement 階段（見 `reviews/prompts/round-8a-implement.md`）。
   ```

## 第八步：Decisions 文件產出

寫入 `reviews/round-8a-decisions.md`，格式：

```markdown
# Round 8a Decisions — Draft Proposal Review (PROPOSAL-012)

**對應 review 報告**: `reviews/round-8a-report.md`
**決議日期**: YYYY-MM-DD
**決議者**: {使用者}（Claude 整理 + 當場修訂）

## 決議彙總

| Finding | 嚴重度 | 維度 | 決議 | 說明 |
|---|---|---|---|---|
| F-01 | Major | D3 | accept | {一句話摘要} |
| F-02 | ... | ... | ... | ... |

## Accept / Accept-with-choice 項目的修訂紀錄

### F-01 (accept)
- **修訂檔案**: `proposals/PROPOSAL-012-distribution-friendly-paths.md`
- **修訂段落**: § 影響範圍
- **改動摘要**: {具體措辭變更摘要}

### F-02 (...)
- ...

（逐項）

## Reject / Defer 項目備忘

（若有）

## Out-of-scope 項目

（若有，已轉 cross-round-notes.md）

## Proposal 狀態推進紀錄

| Proposal | 推進前 | 推進後 | 推進理由 / 維持理由 |
|---|---|---|---|
| PROPOSAL-012 | draft | approved | 所有 accept finding 已修訂完成 |

## 下一步

- R8a Implement 階段：執行 PROPOSAL-012 的影響範圍修改（路徑改名）；建議由 Cursor 或 Claude Code 執行，範圍小（~10 檔），單 session 可完成
- R8a Implement 完成後進入 R8a Implement Review（仿 R7 pattern）
- R8b（PROPOSAL-013）的 review/approve/implement 為平行 wave，不擋 R8a 進度

## 跨輪發現備忘

（若有）
```

## 禁止事項

- **不要改任何 skill 檔**（`sdd-ddd-*-skill/` 下所有檔）—— 那是 Implement 階段的職責
- **不要改 `planning/project-review-brief.md`** —— 若 finding 處理此檔，方式是更新 P012 的影響範圍說明，實際同步留到 Implement
- **不要改繁中版檔**（`*-tw/`）—— 延到 Closeout C1
- **不要修改 review 報告**（`reviews/round-8a-report.md`）
- **不要在單次回覆處理多個 Finding**（設計決策型選項呈現除外）
- **不要自行替使用者拍板設計決策型 Finding 的 path 選擇**
- **不要在 proposal 狀態尚未滿足推進條件時就改 `approved`**

## 完成後回報

> 「Round 8a approve 階段完成。
> - 共 {N} 個 Finding 處理完畢：accept {X} / accept-with-choice {Y} / reject {Z} / defer {W} / clarify→XX {V} / out-of-scope {U}
> - PROPOSAL-012 狀態：draft → {approved / 仍 draft，理由 ...}
> - 決議文件：`reviews/round-8a-decisions.md`
> - 修訂的 proposal 段落：列出具體段落數
> - 下一步：
>   - 若 approved：進入 R8a Implement 階段（建議啟動提示詞另起 `reviews/prompts/round-8a-implement.md`）
>   - 若仍 draft：說明阻塞原因 + 建議的 revise / clarify session 下一步」

---END PROMPT---
