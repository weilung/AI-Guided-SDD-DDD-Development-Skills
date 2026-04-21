# Round 5 Implement Prompt — P006a + P007a（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：修改實作檔 + 更新 `CHANGELOG.md` + 修改 proposal 的「實施紀錄」段（視需要）
> **禁止事項**：不做 decisions 沒列的改動。不自動 commit。不要擅自同步另一版。
> **範圍提醒**：本輪只改英文 Skill，且 **P006a 只動 Core、P007a 只動 WebForms**。

---BEGIN PROMPT---

這是 P006a（Ceremony Scaling 加入 DDD 建模深度欄）和 P007a（Brownfield 系統性逆向文件化）審查修正的**實作階段**（Round 5 / Implement）。

## 第一步：依序讀入下列檔案

1. `proposals/review-workflow.md`（本流程的規範——特別是第三節「Implementation 紀錄」格式、第七節 Post-Review Closeout 說明）
2. `reviews/round-5-decisions.md`（**主要工作依據**——列出所有已核准的修改）
3. `proposals/PROPOSAL-006a-ddd-depth-column.md`（若 decisions 裡有 accept 項目對應 P006a）
   - **特別注意**：只動 Core；4 級分類；P006-2 defer 方案 γ
4. `proposals/PROPOSAL-007a-brownfield-reverse-documentation.md`（同上，對應 P007a）
   - **特別注意**：只動 WebForms；opportunistic 策略
5. `reviews/round-5-report.md`——**僅在 decisions 的「具體改動」寫得不夠細時才讀**。優先信任 decisions 文件。

若 `reviews/round-5-decisions.md` 不存在，回報使用者「找不到 R5 decisions 文件，是否 Approve 階段尚未完成？」然後停止。

## 第二步：本輪範圍邊界

**在範圍內**：decisions 文件裡標為 **accept** 的項目，應只指向兩份英文 Skill 檔的指定部分：
- `sdd-ddd-core-skill/SKILL.md`（**只改 Ceremony Scaling 表段**）
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`（**只改 Step 2 內的 `Systematic Baseline Capture` 子段**）

**不在範圍內**（特別重要，違反會破壞單版本設計）：
- `sdd-ddd-webforms-skill/SKILL.md`——**不要動**，即使看到 WebForms SKILL.md 也有 Ceremony Scaling 表格缺 DDD 深度欄
- `sdd-ddd-core-skill/references/modify-existing-flow.md`——**不要動**，即使看到 Core 版 Step 2 也沒有 Systematic Baseline Capture
- 繁中版（`*-tw/`）——延後至 Post-Review Closeout
- Tutorial（`tutorial/`）
- 兩份檔案中非指定段落的其他段落（R1/R2/R3/R4 範圍）
- decisions 裡標為 reject / defer / out-of-scope 的項目

若 decisions 文件裡某個 accept 項目意外指向：
- 繁中版 / tutorial
- WebForms SKILL.md（P006a 範圍外）
- Core modify-existing-flow.md（P007a 範圍外）
- R5 指定段落以外的部分

**停下來回報使用者**，不要執行。這類問題應在 Approve 階段被 out-of-scope 濾掉。

## 第三步：逐項實施 Accept 項目

對 decisions 文件中標記為 **accept** 的每一項：

1. 讀出該 Finding 的「具體改動」指引
2. 實施改動——僅限指引列出的檔案、僅限指引描述的範圍
3. **P006a/P007a 的特別提醒**：
   - **單版本設計必須保留**——P006a 改完 Core SKILL.md 後，**不要**打開 WebForms SKILL.md「一併同步」；P007a 改完 WebForms modify-existing-flow.md 後，**不要**打開 Core modify-existing-flow.md「一併同步」
   - 4 級分類保持（Full / Standard / Lightweight / Skip）——不要降級回 3 級
   - P007a 保持 opportunistic 語氣——不要改寫成 mandatory（「必須每次掃描」）
   - P007a 保持 Step 2 子段位置——不要拆出成獨立 Step
4. 確認改動後回頭檢查：
   - 改動範圍是否完全符合「具體改動」描述
   - 有沒有意外影響到 decisions 沒提到的檔案或段落
   - **特別檢查**：只改了 P006a 指定的 Core SKILL.md 或 P007a 指定的 WebForms modify-existing-flow.md，沒有跨版本動到另一版
   - **不要跟改繁中版**
5. 改動完成後，在心中（不寫入檔案）記錄：哪些檔案改了哪些行、有無疑問/意外發現

**平行 vs 循序**：R5 兩份 proposal 動的是不同檔案（一個 Core SKILL.md、一個 WebForms modify-existing-flow.md），完全可以平行處理，無衝突風險。

## 第四步：處理 Reject 和 Defer 項目

**完全不動**。即使你覺得 Codex 的 Finding 是真問題、decisions 的 reject 理由不太對。

若實施過程發現 reject/defer 項目在 accept 項目中有牽連，記在最終報告的「備註給下一輪」段落。

## 第五步：更新 CHANGELOG.md

所有 accept 項目實施完成後，在 `CHANGELOG.md` **最上方**新增一段：

```markdown
## YYYY-MM-DD — Round 5 審查修正：Ceremony 與逆向文件化（P006a + P007a）

**關聯 Proposal**: P006a, P007a
**審查輪次**: R5
**對應決議文件**: `reviews/round-5-decisions.md`
**設計性質**: 單版本（P006a 只動 Core，P007a 只動 WebForms，刻意不同步另一版）

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

日期用今天實際日期。繁中版同步欄位內容固定為上述字串。**「設計性質」欄位是 R5 特有**，明示單版本設計給未來 reviewer。

## 第六步：更新 proposal 的實施紀錄（視需要）

若某個 accept 項目本質是「補齊 proposal 原本要求但實作時遺漏的要素」，在對應 `proposals/PROPOSAL-006a.md` 或 `PROPOSAL-007a.md` 的「實施紀錄」段落追加一行：

```markdown
- **R5 審查修正（YYYY-MM-DD）**: F-XX — {一句話描述}
```

判斷原則：**實作有無變動 proposal 範圍**——有就更新紀錄，沒有就只寫 CHANGELOG。

## 第七步：完成後回報（不自動 commit）

實施完成後，告訴使用者：

> 「Round 5 implement 階段完成。
> - 共實施 N 項修正（F-XX, F-YY, ...）
> - 影響檔案：{列出}（均為英文 Skill，且保持 P006a Core only / P007a WebForms only 單版本設計）
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
- **不改 WebForms SKILL.md**（P006a 刻意不動）——這是 R5 最重要的禁止項。
- **不改 Core modify-existing-flow.md**（P007a 刻意不動）——同上。
- **不改指定段落以外的其他段落**（R1/R2/R3/R4 範圍）。
- **不要把 DDD 深度 4 級降回 3 級**——實施時主動升級是正確改善。
- **不要把 P007a 改寫為 mandatory**——opportunistic 是設計選擇。
- **不要把 P007a 拆出成獨立 Step**——Step 2 子段是設計選擇。
- **不要在任何 accept 實施後「順便同步到另一版」**——單版本是核心設計。

## 跨輪發現如何處理

實施過程若發現屬於其他輪範圍的問題，記入 `reviews/cross-round-notes.md`。格式：

```
## R5 Implement 發現、屬於 R{N} 的備忘

- 位置：`path:line-range`
- 描述：...
- 建議轉交：R{N}（{該輪主題}）
- 記錄時間：YYYY-MM-DD
```

若發現繁中版或 tutorial 有對應落差，記入 Post-Review Closeout 區段：

```
## R5 Implement 發現、屬於 Post-Review Closeout 的備忘

- 位置：`path:line-range`（繁中版或 tutorial 檔）
- 描述：英文 Skill 的 F-XX 修正後，此處應同步更新
- 記錄時間：YYYY-MM-DD
```

---END PROMPT---
