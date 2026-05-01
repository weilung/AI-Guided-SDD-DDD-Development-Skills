# Round 4 Implement Prompt — P003 + P008（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：修改實作檔 + 更新 `CHANGELOG.md` + 修改 proposal 的「實施紀錄」段（視需要）
> **禁止事項**：不做 decisions 沒列的改動。不自動 commit。
> **範圍提醒**：本輪只改英文 Skill。

---BEGIN PROMPT---

這是 P003（系統 Source of Truth — behavior.md + A+C 結構）和 P008（Drift Verification — `/dflow:verify`）審查修正的**實作階段**（Round 4 / Implement）。

## 第一步：依序讀入下列檔案

1. `proposals/review-workflow.md`（本流程的規範——特別是第三節「Implementation 紀錄」格式、第七節 Post-Review Closeout 說明）
2. `reviews/round-4-decisions.md`（**主要工作依據**——列出所有已核准的修改）
3. `proposals/PROPOSAL-003-system-source-of-truth.md`（若 decisions 裡有 accept 項目對應 P003）
   - **特別注意**：P003 採簡化版，Phase 3 draft / anchor last-updated forward-looking
4. `proposals/PROPOSAL-008-drift-verification.md`（同上，對應 P008）
   - **特別注意**：明確排除語意層
5. `reviews/round-4-report.md`——**僅在 decisions 的「具體改動」寫得不夠細時才讀**。優先信任 decisions 文件。

若 `reviews/round-4-decisions.md` 不存在，回報使用者「找不到 R4 decisions 文件，是否 Approve 階段尚未完成？」然後停止。

## 第二步：本輪範圍邊界

**在範圍內**：decisions 文件裡標為 **accept** 的項目，應只指向 8 份英文 Skill 檔的指定部分：
- `sdd-ddd-webforms-skill/templates/behavior.md`（整份）
- `sdd-ddd-core-skill/templates/behavior.md`（整份）
- `sdd-ddd-webforms-skill/templates/context-definition.md`（behavior.md 提及段）
- `sdd-ddd-core-skill/templates/context-definition.md`（同上）
- `sdd-ddd-webforms-skill/references/drift-verification.md`（整份）
- `sdd-ddd-core-skill/references/drift-verification.md`（同上）
- `sdd-ddd-webforms-skill/SKILL.md`（`Behavior Source of Truth` 段 + `/dflow:verify` 相關內容）
- `sdd-ddd-core-skill/SKILL.md`（同上）

**不在範圍內**：
- 繁中版（`*-tw/`）
- Tutorial（`tutorial/`）
- SKILL.md 中非 `Behavior Source of Truth` / `/dflow:verify` 的其他段落（R1/R3 範圍）
- flow 檔（new-feature-flow、modify-existing-flow）的任何段落（R2/R3 範圍）
- decisions 裡標為 reject / defer / out-of-scope 的項目

若 decisions 文件裡某個 accept 項目意外指向繁中版、tutorial、或 R4 指定範圍以外的部分，**停下來回報使用者**。

## 第三步：逐項實施 Accept 項目

對 decisions 文件中標記為 **accept** 的每一項：

1. 讀出該 Finding 的「具體改動」指引
2. 實施改動——僅限指引列出的檔案、僅限指引描述的範圍
3. **P003/P008 的特別提醒**：
   - behavior.md template 保持「consolidated 當前狀態」語義——不要引入 Delta 歷史追蹤
   - A+C 結構保持分檔（rules.md 索引 + behavior.md 內容）——不要合併
   - drift-verification 維持**只有機械層**（三項機械檢查）——不要加語意層判斷
   - 保持 P003 簡化版範圍——不要擅自補 Phase 3 draft 段落或 anchor last-updated 欄位（那是 P003 後續版本或 P008 Wave D 延伸的範圍）
   - Core vs WebForms 刻意差異保留（Core 補 Aggregate / Domain Events / events.md 警告）
4. 確認改動後回頭檢查：
   - 改動範圍是否完全符合「具體改動」描述
   - 有沒有意外影響到 decisions 沒提到的檔案或段落
   - WebForms 版改了，Core 版是否也需要對應改？（若 decisions 沒明說但明顯是共通機制，標為「疑似遺漏，回報使用者」而非擅自跟改）
   - **不要跟改繁中版**
5. 改動完成後，在心中（不寫入檔案）記錄：哪些檔案改了哪些行、有無疑問/意外發現

**平行 vs 循序**：涉及同一檔案的 accept 項目循序處理；不同檔案可平行，但建議按 F-XX 編號順序。

## 第四步：處理 Reject 和 Defer 項目

**完全不動**。即使你覺得 Codex 的 Finding 是真問題、decisions 的 reject 理由不太對。

若實施過程發現 reject/defer 項目在 accept 項目中有牽連，記在最終報告的「備註給下一輪」段落。

## 第五步：更新 CHANGELOG.md

所有 accept 項目實施完成後，在 `CHANGELOG.md` **最上方**新增一段：

```markdown
## YYYY-MM-DD — Round 4 審查修正：Source of Truth 與漂移驗證（P003 + P008）

**關聯 Proposal**: P003, P008
**審查輪次**: R4
**對應決議文件**: `reviews/round-4-decisions.md`

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

日期用今天實際日期。繁中版同步欄位內容固定為上述字串。

## 第六步：更新 proposal 的實施紀錄（視需要）

若某個 accept 項目本質是「補齊 proposal 原本要求但實作時遺漏的要素」，在對應 `proposals/PROPOSAL-003.md` 或 `PROPOSAL-008.md` 的「實施紀錄」段落追加一行：

```markdown
- **R4 審查修正（YYYY-MM-DD）**: F-XX — {一句話描述}
```

判斷原則：**實作有無變動 proposal 範圍**——有就更新紀錄，沒有就只寫 CHANGELOG。

## 第七步：完成後回報（不自動 commit）

實施完成後，告訴使用者：

> 「Round 4 implement 階段完成。
> - 共實施 N 項修正（F-XX, F-YY, ...）
> - 影響檔案：{列出}（均為英文 Skill）
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
- **不改 SKILL.md 中非 `Behavior Source of Truth` / `/dflow:verify` 的段落**（R1/R3 範圍）。
- **不改 flow 檔**（R2/R3 範圍）。
- **不要把 behavior.md 改為記錄 Delta 歷史**——P002 澄清過，consolidated 當前狀態。
- **不要把 A+C 合併為單一檔案**——核心設計。
- **不要在 drift-verification 加語意層判斷**——明確排除。
- **不要擅自補 P003 的 B3/B4 機械結構**——forward-looking 範圍。

## 跨輪發現如何處理

實施過程若發現屬於其他輪範圍的問題，記入 `reviews/cross-round-notes.md`。格式：

```
## R4 Implement 發現、屬於 R{N} 的備忘

- 位置：`path:line-range`
- 描述：...
- 建議轉交：R{N}（{該輪主題}）
- 記錄時間：YYYY-MM-DD
```

若發現繁中版或 tutorial 有對應落差，記入 Post-Review Closeout 區段：

```
## R4 Implement 發現、屬於 Post-Review Closeout 的備忘

- 位置：`path:line-range`（繁中版或 tutorial 檔）
- 描述：英文 Skill 的 F-XX 修正後，此處應同步更新
- 記錄時間：YYYY-MM-DD
```

---END PROMPT---
