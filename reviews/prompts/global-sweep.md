# Global Sweep Prompt — R1–R6 全域一致性審查（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：`reviews/global-sweep-report.md` + 可能的小幅 coherence 修正（CHANGELOG 排序、evaluation 文件第七節標記）
> **禁止事項**：不做實質性 Skill 內容修改。不碰繁中版 / tutorial。若發現需實質改動的議題，記入 `reviews/cross-round-notes.md` 作為未來 proposal 候選。
> **範圍提醒**：這是 R1–R6 結束後、Post-Review Closeout 之前的**全域檢視**步驟（見 `proposals/review-workflow.md` §7.1）。

---BEGIN PROMPT---

這是 R1–R6 審查流程全部結束後的**全域一致性 sweep 階段**。R1–R6 已經跑完，實施結果已入 CHANGELOG，現在要做的是橫向稽核。

## 第一步：讀入上下文

1. `proposals/review-workflow.md`（特別注意第七節 7.1 全域 sweep 定義、7.2 Closeout 範圍邊界）
2. `CHANGELOG.md`（本 sweep 的主要查核對象之一）
3. `reviews/round-1-decisions.md` 至 `reviews/round-6-decisions.md`（六份決議文件）
4. `reviews/cross-round-notes.md`（跨輪備忘）
5. `proposals/evaluation-p002-p008.md` 第七節（「後續行動」——需要在本 sweep 標記 review 階段完成）

## 第二步：審查範圍邊界

**在範圍內**（英文 Skill 範圍內的橫向稽核）：

- R1–R6 各輪「已 accept 並實施」的改動：彼此之間有無矛盾、有無被後續輪次不小心覆寫
- CHANGELOG 的連貫性：日期序、關聯 Proposal 標示、R1–R6 條目的完整性
- Decisions 文件與實作檔的對齊：每個 accept 項目的「具體改動指引」是否確實反映在檔案中（抽樣驗證即可，不逐行逐字）
- `cross-round-notes.md` 裡的跨輪備忘：是否都已被對應輪次處理或明確 defer
- `evaluation-p002-p008.md` 第七節：標記「審查階段已完成」

**不在範圍內**：

- **繁中版（`*-tw/`）**——屬於 Closeout C1，這階段不處理
- **Tutorial（`tutorial/`）**——屬於 Closeout C2，這階段不處理
- **任何實質性 Skill 內容修改**——sweep 是診斷 + 輕量 coherence 修正，不是新一輪 review
- **Wave D「Skill 精簡化審查」**——sweep 是它的前置步驟，不取代它；若看到冗餘規則、重複指引，**記入 cross-round-notes.md 給 Wave D**，不在這階段砍

**關鍵分界**：本 sweep 可以做的修正類型極度有限——見第五步白名單。

## 第三步：六個檢查角度

依序每個角度過一輪：

### 3.1 跨輪改動矛盾檢查

對每個 R1–R6 accept 項目實際改動的檔案，檢查有無後續輪次在同段落做了可能衝突的改動。典型風險：

- 同一個 SKILL.md 段落被多輪修改，後輪改動是否保留了前輪意圖
- 同一條規則在不同檔案（SKILL.md / references / templates）中的表述是否仍一致
- R4 新增的 `/dflow:verify` Standalone commands 分類，是否與 R1 的 `/dflow:status` Response Format 表述風格一致

### 3.2 CHANGELOG 連貫性檢查

- 日期是否遞增（最新在最上方）
- 每段是否有「關聯 Proposal / 審查輪次 / 對應決議文件」三欄
- R1/R2/R4 條目都在；R3/R5/R6 因 0 Finding 無條目——確認這是 decisions 文件有記載的事實
- 「繁中版同步狀態」欄位內容一致（R1/R2/R4 應為「延後至 Post-Review Closeout」）

### 3.3 Decisions ↔ 實作對齊抽樣

對 R1/R2/R4 三輪已 accept 的 F-XX 項目，抽樣驗證：

- Decisions 文件寫的「具體改動」指引，是否確實出現在檔案對應位置
- 如 R1 F-01：Core SKILL.md 的 `/dflow:status` 範例 Step 3 是否已改為 `Domain Modeling`（line 179 status line、line 186 checklist）
- 若抽樣發現對齊問題，記為 Finding（但嚴重度從嚴，多半應是 Minor 或 Question）

### 3.4 Cross-round-notes 清理檢查

讀 `reviews/cross-round-notes.md`，對每則備忘判斷：

- 已被對應輪次處理（如 R1→R4 備忘已由 R4 隱含確認）→ 可以加註「已由 R{N} 確認」
- 未處理且屬於 R1–R6 任一輪範圍 → 標為遺漏，記入 sweep 報告
- 屬於 Wave D / 未來 proposal / Closeout → 保留備忘，加註分類

### 3.5 Proposal 實施紀錄檢查

對每份 `proposals/PROPOSAL-00{1..8}*.md`（不含 000 template），檢查：

- 狀態是否為 `implemented`
- 實施紀錄段落是否提及對應 R{N} 審查修正（若該輪有 accept 項目觸發 proposal 範圍變動）

### 3.6 Evaluation 文件第七節標記

讀 `proposals/evaluation-p002-p008.md` 第七節「後續行動」清單：

- 目前「2. 盲點修正」已標 ✓ 完成
- 需要新增一項標記：「R1–R6 審查階段已完成（YYYY-MM-DD）」
- 位置：第七節適當位置（不破壞原有編號邏輯）

## 第四步：嚴重度判斷原則

本 sweep 是**橫向 coherence 檢視**，不是新 review。Finding 應該很少。

**Critical**：跨輪改動直接互相矛盾、讓 Skill 自我衝突無法使用
- ✓ 例：R4 把某段從「A 位置」移到「B 位置」，但 R1 的參照還指向「A 位置」——指引失效
- ✗ **非** Critical：CHANGELOG 某行贅字

**Major**：實作與 decisions 文件不符、Proposal 未標 implemented、重要 cross-round-note 漏處理
- ✓ 例：R2 F-01 decisions 說「Step 8.1 清單第 1 項」，實際檔案卻在第 3 項
- ✓ 例：某份 PROPOSAL 狀態仍為 `approved` 未改為 `implemented`

**Minor**：CHANGELOG 排版、小筆誤、evaluation 第七節文字微調建議
- ⚠️ **嚴格節制**：sweep 範圍即使涵蓋全 repo，本身是 coherence 檢查，Minor 超過 3 項就應該檢討

**Observation**（不是 Finding）：看到「值得 Wave D 處理」的冗餘或不一致，但不屬於 sweep 可處理範圍——記入 cross-round-notes.md「Wave D 候選」區段

## 第五步：可以直接修正的白名單

sweep 期間**可以直接修正**（不記為 Finding，但在 sweep 報告列為「coherence 修正」）的項目：

1. **CHANGELOG 日期序錯誤**：如果條目順序不對，可以直接調整
2. **CHANGELOG 欄位標示不一致**：例如某段缺「對應決議文件」欄，可以補
3. **`cross-round-notes.md` 備忘加註「已由 R{N} 確認」**：純加註，不刪除原文
4. **`evaluation-p002-p008.md` 第七節新增完成標記**（見 3.6）
5. **Proposal 狀態欄更新**：若某份 Proposal 實測為 `implemented` 但狀態還停在 `approved`，可以直接改——但要在 sweep 報告列明哪幾份改了

**白名單外的任何改動都要停下來問使用者**。典型停下情境：

- 看到 SKILL.md / references / templates 裡有文字想改
- 看到兩版內容不一致想同步
- 想重寫某段讓表述更好
- 想刪除冗餘指引

**一律不做**——這些都屬於未來一輪 review 或 Wave D 的範疇，記入 cross-round-notes.md 即可。

## 第六步：產出 sweep 報告

寫入 `reviews/global-sweep-report.md`，建議結構：

```markdown
# Global Sweep Report — R1–R6 全域一致性審查

**Sweep 日期**: YYYY-MM-DD
**Sweep 範圍**: R1–R6 英文 Skill 改動、CHANGELOG、decisions 文件、cross-round-notes、evaluation §7
**前置條件**: R1–R6 全部跑完（R1/R2/R4 有 accept + 實施；R3/R5/R6 0 Finding）

---

## 一、六角度檢查結果

### 3.1 跨輪改動矛盾
- 結論：{無矛盾 / N 項 Finding}
- Finding 列表（若有）

### 3.2 CHANGELOG 連貫性
- 結論：...

### 3.3 Decisions ↔ 實作對齊（抽樣）
- 抽樣項目：{F-XX 列表}
- 結論：...

### 3.4 Cross-round-notes 清理
- 已處理備忘：...
- 遺漏備忘：...
- 轉為 Wave D 候選：...

### 3.5 Proposal 實施紀錄
- 全部狀態正確：{是 / 否，哪幾份需更新}

### 3.6 Evaluation §7 標記
- 動作：已新增「R1–R6 審查階段已完成（YYYY-MM-DD）」

---

## 二、Coherence 修正清單（白名單內直接處理）

- {修正項目 1}
- {修正項目 2}
- ...

## 三、Finding 清單（需使用者判斷）

| # | 嚴重度 | 位置 | 問題 | 建議 |
|---|---|---|---|---|
| S-01 | ... | ... | ... | ... |

## 四、Wave D 候選（轉入 cross-round-notes.md）

- {項目 1}
- {項目 2}

## 五、結論

- R1–R6 審查流程{完整 / 有 N 項遺漏}
- 是否可進入 Post-Review Closeout 階段：{可以 / 需先處理 Finding}
- 下一步：{Closeout C1 繁中同步 / 處理遺留 Finding / 其他}
```

## 第七步：完成後回報（不自動 commit）

報告寫完、coherence 修正完成後，告訴使用者：

> 「Global sweep 完成。
> - 六角度檢查結果：{一句話總結}
> - Coherence 修正：N 項（{列出}）
> - Finding（需使用者判斷）：M 項（Critical: X / Major: Y / Minor: Z）
> - Wave D 候選：K 項已記入 `cross-round-notes.md`
> - Sweep 報告：`reviews/global-sweep-report.md`
>
> 下一步：
> - {若 Finding=0 且 coherence 修正完成}：可進入 **Post-Review Closeout**（§7.2）——C1 繁中同步 + C2 Tutorial 重建，建議分別另開新 session
> - {若有 Finding}：請逐項判斷，必要時進入一輪 mini-round 處理
>
> 請檢視後決定是否 commit coherence 修正。」

**不要主動 commit**。等使用者確認。

## 禁止事項

- **不做實質性 Skill 內容修改**（白名單外）。
- **不改繁中版 / tutorial**。
- **不自動 commit**。
- **不新增 proposal**——新議題只記入 cross-round-notes.md 作為候選。
- **不重新評估 R1–R6 的 decisions**——這些已定案。
- **不把 Wave D 該做的事做掉**——sweep 是它的前置步驟，不是替代。
- **不在單次回覆處理多項 Finding 的決議**——sweep 報告產出後交使用者，不自行決議。
- **Minor 嚴格自我節制**——超過 3 項要自問是否在挑刺。

## 範圍外的事情如何處理

若 sweep 過程看到：

- **繁中版 / tutorial 問題**：記入 `cross-round-notes.md` 的「Post-Review Closeout 備忘」區段（C1 或 C2 對應）
- **Wave D 冗餘/重複/精簡議題**：記入 cross-round-notes.md 的「Wave D 候選」區段
- **未來新 proposal 候選**：記入 cross-round-notes.md 的「未來議題」區段，清楚標示觸發情境

格式：

```
## Global Sweep 發現、屬於 Wave D 的候選

- 位置：`path:line-range`
- 描述：...
- 建議處理時機：Wave D Skill 精簡化審查
- 記錄時間：YYYY-MM-DD

## Global Sweep 發現、屬於 Post-Review Closeout 的備忘

- 位置：`path:line-range`（繁中版 / tutorial 檔）
- 描述：...
- 建議處理時機：Closeout C1 / C2
- 記錄時間：YYYY-MM-DD
```

---END PROMPT---
