# Round 6 Implement Prompt — P007c（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：修改實作檔 + 更新 `CHANGELOG.md` + 修改 proposal 的「實施紀錄」段（視需要）
> **禁止事項**：不做 decisions 沒列的改動。不自動 commit。不要補「繁中版」（本輪兩個檔案本來就沒有繁中對應版）。
> **範圍提醒**：本輪只改兩份 `templates/CLAUDE.md`。R6 是 R1–R6 的**最終輪**。
> **N=0 情境**：若 decisions 文件無 accept 項目，跳過實作、僅在完成回報中說明「無需實作」並指向全域 sweep。

---BEGIN PROMPT---

這是 P007c（CLAUDE.md 模板標題分段強化）審查修正的**實作階段**（Round 6 / Implement）。這是 R1–R6 的**最終輪**。

## 第一步：依序讀入下列檔案

1. `proposals/review-workflow.md`（本流程的規範——特別是第三節「Implementation 紀錄」格式、第七節 Post-Review Closeout 說明）
2. `reviews/round-6-decisions.md`（**主要工作依據**——列出所有已核准的修改）
3. `proposals/PROPOSAL-007c-claude-md-segmentation.md`（若 decisions 裡有 accept 項目）
   - **特別注意**：不需要繁中同步；Core 版獨有「架構」段；原 H2 降級為 H3；新的兩大 H2 用雙語標題
4. `reviews/round-6-report.md`——**僅在 decisions 的「具體改動」寫得不夠細時才讀**。優先信任 decisions 文件。

若 `reviews/round-6-decisions.md` 不存在，回報使用者「找不到 R6 decisions 文件，是否 Approve 階段尚未完成？」然後停止。

## 第二步：N=0 情境處理（本輪範圍極小，此情境機率不低）

若 decisions 文件的「決議彙總表」明確標示**本輪無 accept 項目**（可能全 reject、或 Finding 數量本來就是 0）：

1. **不做任何實作改動**
2. **不更新 CHANGELOG.md**（沒有修正內容可記）
3. **不更新 proposal 實施紀錄**
4. 直接跳到第七步的「N=0 完成回報」版本

## 第三步：本輪範圍邊界（N>0 時適用）

**在範圍內**：decisions 文件裡標為 **accept** 的項目，應只指向：
- `sdd-ddd-webforms-skill/templates/CLAUDE.md`
- `sdd-ddd-core-skill/templates/CLAUDE.md`

**不在範圍內**（特別重要）：
- **「補繁中版」類改動**——proposal 明示「不需要繁中同步」，這兩個檔案是給 AI 讀的英文模板
- **「讓 WebForms 版也加架構段」類改動**——Core 獨有是刻意設計
- **「把原 H2 還原回 H2」類改動**——proposal 明確要求降級為 H3
- 繁中版（`*-tw/`）——本 proposal 不涉及
- Tutorial（`tutorial/`）
- 兩份檔案中非標題分段相關的其他段落（R1–R5 範圍）
- decisions 裡標為 reject / defer / out-of-scope 的項目

若 decisions 文件裡某個 accept 項目意外指向：
- 繁中版 / tutorial
- 非 `templates/CLAUDE.md` 的檔案
- 「同步另一版」類指引

**停下來回報使用者**，不要執行。這類問題應在 Approve 階段被 out-of-scope 濾掉。

## 第四步：逐項實施 Accept 項目

對 decisions 文件中標記為 **accept** 的每一項：

1. 讀出該 Finding 的「具體改動」指引
2. 實施改動——僅限指引列出的檔案、僅限指引描述的範圍
3. **P007c 的特別提醒**：
   - **兩大 H2 結構必須保留**：`## 系統脈絡（What is this system?）` 和 `## 開發流程（How do we work?）`——雙語標題形式不要改
   - **H2 下方一行 blockquote 摘要**保留——不要刪、不要改為段落文
   - **原 H2 降級為 H3** 的結構保留——不要還原回 H2
   - **Core 版「架構」段獨有**——不要為了「兩版一致」把它刪掉或複製到 WebForms 版
   - **WebForms 版沒有「架構」段**——不要為了「補齊」而新增
   - **不要翻繁中**——即使手滑想到「給人閱讀方便一下」，也不要建立 `templates/CLAUDE_tw.md`
4. 確認改動後回頭檢查：
   - 改動範圍是否完全符合「具體改動」描述
   - 有沒有意外影響到 decisions 沒提到的檔案或段落
   - **特別檢查**：只動了 `templates/CLAUDE.md`，沒有波及 `sdd-ddd-*-skill/SKILL.md`、`references/*.md`、`tutorial/` 或繁中版
5. 改動完成後，在心中（不寫入檔案）記錄：哪些檔案改了哪些行、有無疑問/意外發現

**平行 vs 循序**：R6 兩份檔案是獨立的 template，可平行處理，無衝突風險。

## 第五步：處理 Reject 和 Defer 項目

**完全不動**。即使你覺得 Codex 的 Finding 是真問題、decisions 的 reject 理由不太對。

若實施過程發現 reject/defer 項目在 accept 項目中有牽連，記在最終報告的「備註給下一輪/全域 sweep」段落。

## 第六步：更新 CHANGELOG.md（N>0 時）

所有 accept 項目實施完成後，在 `CHANGELOG.md` **最上方**新增一段：

```markdown
## YYYY-MM-DD — Round 6 審查修正：CLAUDE.md 模板標題分段（P007c）

**關聯 Proposal**: P007c
**審查輪次**: R6（R1–R6 最終輪）
**對應決議文件**: `reviews/round-6-decisions.md`

### 修正項目

- **F-01 ({嚴重度})**: {一句話描述問題} → {一句話描述修正}
- **F-02 ({嚴重度})**: ...
- ...（逐項列出所有 accept 的 F-XX）

### 影響檔案

- `sdd-ddd-webforms-skill/templates/CLAUDE.md`
- `sdd-ddd-core-skill/templates/CLAUDE.md`
（實際僅列 accept 項目涉及的檔案）

### 繁中版同步狀態

不適用——本 proposal 的兩個 `templates/CLAUDE.md` 設計為英文 AI 用模板，無繁中對應版，亦不進入 Post-Review Closeout 階段的 TW 同步範圍。
```

日期用今天實際日期。繁中版同步欄位內容**與 R1–R5 不同**（不是「延後至 Closeout」，而是「不適用」），這是 P007c 的設計特性。

## 第七步：更新 proposal 的實施紀錄（視需要）

若某個 accept 項目本質是「補齊 proposal 原本要求但實作時遺漏的要素」，在 `proposals/PROPOSAL-007c-claude-md-segmentation.md` 的「實施紀錄」段落追加一行：

```markdown
- **R6 審查修正（YYYY-MM-DD）**: F-XX — {一句話描述}
```

判斷原則：**實作有無變動 proposal 範圍**——有就更新紀錄，沒有就只寫 CHANGELOG。

## 第八步：完成後回報（不自動 commit）

### N>0 版本

> 「Round 6 implement 階段完成。這是 R1–R6 的**最終輪**。
> - 共實施 N 項修正（F-XX, F-YY, ...）
> - 影響檔案：{列出}（均為 `templates/CLAUDE.md`，無繁中同步需求）
> - CHANGELOG 已更新（繁中版同步狀態標為「不適用」）
> - Proposal 實施紀錄已更新：{列出；若無就說「無」}
> - 備註（若有）：{跨輪發現 / 疑似遺漏 / 其他注意事項}
>
> 下一步：
> 1. 請檢視後決定是否 commit。
> 2. Commit 完成後，R1–R6 全部完結，進入 **R1–R6 全域一致性 sweep**（見 `proposals/review-workflow.md` 第七節 7.1）。
> 3. 全域 sweep 後才進入 **Post-Review Closeout**（TW 同步 + Tutorial 重建，見第七節 7.2）。」

### N=0 版本

> 「Round 6 implement 階段完成。這是 R1–R6 的**最終輪**。
> - Decisions 文件無 accept 項目——本輪無需實作改動
> - CHANGELOG 未更新（無修正內容）
> - Proposal 實施紀錄未更新
>
> 下一步：
> 1. R1–R6 全部完結，直接進入 **R1–R6 全域一致性 sweep**（見 `proposals/review-workflow.md` 第七節 7.1）。
> 2. 全域 sweep 後才進入 **Post-Review Closeout**（TW 同步 + Tutorial 重建，見第七節 7.2）。」

**不要主動 commit**。等使用者確認。

## 禁止事項

- **不做 decisions 沒列的改動**。
- **Reject 和 defer 項目完全不動**。
- **不重新評估 Finding**。
- **不自動 commit**。
- **不改 review 報告和 decisions 文件**。
- **不建立 `templates/CLAUDE_tw.md`**——proposal 明示無需繁中版。
- **不改 tutorial**。
- **不改 `sdd-ddd-*-skill/SKILL.md`、`references/*.md`**（R1–R5 範圍）。
- **不改兩份檔案中非標題分段相關的段落**。
- **不要「為了兩版一致」把 Core 架構段刪掉或複製給 WebForms**——刻意差異。
- **不要把原 H2 還原為 H2**——proposal 要求降級為 H3。
- **不要把雙語標題簡化為純英文**——雙語是刻意設計。
- **不要在 N=0 情境下為了「交差」而勉強寫 CHANGELOG**。

## 跨輪發現如何處理

實施過程若發現屬於其他輪範圍的問題，記入 `reviews/cross-round-notes.md`。格式：

```
## R6 Implement 發現、屬於 R{N} 的備忘

- 位置：`path:line-range`
- 描述：...
- 建議轉交：R{N}（{該輪主題}）或全域 sweep
- 記錄時間：YYYY-MM-DD
```

若發現繁中版或 tutorial 有對應落差，記入 Post-Review Closeout 區段：

```
## R6 Implement 發現、屬於 Post-Review Closeout 的備忘

- 位置：`path:line-range`（繁中版或 tutorial 檔）
- 描述：英文 Skill 的 F-XX 修正後，此處應同步更新
- 記錄時間：YYYY-MM-DD
```

**注意**：`templates/CLAUDE.md` 本身不屬於 Closeout TW 同步範圍（無繁中對應版）。Closeout 備忘僅涉及其他檔案的繁中版或 tutorial。

---END PROMPT---
