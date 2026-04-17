## PROPOSAL-005b: 完成流程 Checklist — behavior.md + Tasks 補完（Wave B 子集）

**狀態**: `implemented`

**提出日期**: 2026-04-11（從 PROPOSAL-005 拆分）

**實施排程**: Wave B（P003 + P004 完成後接續）

**優先級**: `中`

**來源**: PROPOSAL-005 拆分（B5）

---

### 問題描述

原 P005 的完成 checklist 有一部分項目**硬依賴 P003（behavior.md）/ P004（Tasks 段）**，這些項目無法在 P003/P004 尚未到位時實施。

P005a 已把不依賴的子集提前到 Wave A。本 proposal 負責補上依賴 P003/P004 的項目，等 P003 的 A+C 結構 pilot 完成、P004 的 tasks 段進入穩定使用後接上。

### 提議的解法

在 P005a 已建立的分層 checklist 基礎上，補入下列項目：

```markdown
### 文件更新（補完）
- [ ] behavior.md — 行為規格已合併（P003）
      - B3 的 Phase 3 draft 段落轉為正式段落
      - 更新對應 rules.md anchor 的 last-updated 日期（B4）
- [ ] behavior.md 草稿清理 — 若中途放棄，保留 `## 提案中變更` 段的歷史或明確 REMOVE

### 歸檔前清理（補完）
- [ ] spec 的實作任務段：全部勾選，或未勾選項目標註 follow-up（P004）
- [ ] 是否需要摺疊 / 移除 tasks 段（看團隊慣例，本項為開發者確認）
```

同時延伸 SKILL.md 的「AI 可獨立驗證 vs 需開發者確認」分層：

| 類別 | 新增項 |
|---|---|
| AI 可獨立驗證 | tasks 全部勾選、behavior.md 是否有對應 BR-* anchor、behavior.md last-updated 是否晚於本次 spec 建立日 |
| 需開發者確認 | behavior.md 情境是否完整表達預期行為、tasks 是否該摺疊 |

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `references/new-feature-flow.md`（兩版） | 修改 | Step 8 checklist 補上 behavior.md + tasks 項 |
| `references/modify-existing-flow.md`（兩版） | 修改 | 精簡 checklist 補上 behavior.md 項（tasks 項已在 P004 實施中加入，本 proposal 整併進 checklist） |
| `SKILL.md`（兩版） | 修改 | 分層表補上新增項 |

### 預估工作量

小

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-003 | 依賴 | behavior.md A+C 結構與 B3 中途同步機制需已上線 |
| PROPOSAL-004 | 依賴 | tasks 段已納入 feature-spec 模板，flow 中有 tasks 產生與驗證（此已由 P004 先行實施，P005b 將驗證步驟整併進分層 checklist） |
| PROPOSAL-005a | 拆分互補 | Wave A 已建立基礎 checklist 結構，本 proposal 補完 |
| PROPOSAL-008 | 互補 | behavior.md last-updated 與實作日期對比可作為 drift verification 機械層輸入 |

### 關鍵假設

- 假設 1：P003 的 A+C 結構在 pilot 後被採用，behavior.md 成為穩定檔案
- 假設 2：開發者願意維護 behavior.md 的 last-updated 日期
- 風險：若 P003 pilot 後決定不全面 rollout，P005b 的 behavior.md 項需退回「僅 pilot BC 生效」

### 評估紀錄

**評估日期**: 2026-04-15

**結論**: approved（Wave B）→ implemented（2026-04-17）

**理由**: 項目本身有價值，但依賴的 proposal 尚未到位。提前實施會讓 checklist 出現「勾不到」的項目，反而削弱流程信任。排到 Wave B，等 P003/P004 穩定後補上。

### 實施紀錄

**實施日期**: 2026-04-17

**實際變更**:

1. **兩版 new-feature-flow.md Step 8.1 / 8.2 / 8.3 擴充**：
   - 8.1 新增兩項標 `*(post-8.3)*` 的項目——`behavior.md` 對本 spec 的 `BR-*` 有對應 anchor、`behavior.md` `last-updated` 晚於 spec `created` 日期（機械輸入給 `/dflow:verify`）。前綴說明 8.1 分「合併前」/「8.3 後」兩個時機。
   - 8.2 新增兩項開發者確認——合併進 `behavior.md` 的情境是否忠實表達預期行為、`實作任務` 段要不要摺疊 / 移除（團隊慣例）。
   - 8.3 的 `behavior.md` bullet 擴充為含兩個子步驟（B3 Phase 3 draft 轉正式段落 + B4 `rules.md` anchor `last-updated` 更新），另增 `behavior.md 草稿清理` bullet（中途放棄時保留 `## 提案中變更` 段或 REMOVE）。
   - Core 版 8.1 的 post-8.3 項特別提 REMOVED delta 的 anchor 刪除，以配合 modify-existing 的 Delta 流程。

2. **兩版 modify-existing-flow.md X.1 / X.2 / X.3 擴充**：
   - WebForms Step 6、Core Step 5 做了對應擴充，措辭針對 Delta 流程調整（ADDED / MODIFIED 的 anchor 存在、REMOVED 的 anchor 已刪除）。

3. **兩版 SKILL.md `Completion Checklist Execution` 子節改寫**：
   - AI-independent 段落拆成「合併前」/「8.3 / 6.3 / 5.3 之後」兩類時機說明。
   - Developer-confirmation 段落加入 P005b 的兩題（behavior.md 情境忠實度、tasks 段摺疊決定）。
   - 文件更新段落加入 `behavior.md` 的兩個子步驟（B3 / B4）與草稿清理說明。

4. **PROPOSAL-005b 狀態**：`approved` → `implemented`。

**影響檔案**（EN 6 個 + TW 6 個 = 12 個）：

EN：
- `sdd-ddd-webforms-skill/references/new-feature-flow.md`
- `sdd-ddd-core-skill/references/new-feature-flow.md`
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
- `sdd-ddd-core-skill/references/modify-existing-flow.md`
- `sdd-ddd-webforms-skill/SKILL.md`
- `sdd-ddd-core-skill/SKILL.md`

TW（同步）：
- `sdd-ddd-webforms-skill-tw/new-feature-flow_tw.md`
- `sdd-ddd-core-skill-tw/new-feature-flow_tw.md`
- `sdd-ddd-webforms-skill-tw/modify-existing-flow_tw.md`
- `sdd-ddd-core-skill-tw/modify-existing-flow_tw.md`
- `sdd-ddd-webforms-skill-tw/SKILL_tw.md`
- `sdd-ddd-core-skill-tw/SKILL_tw.md`

**實施期判斷**：

- **8.1 放「post-merge」項還是另闢新段？** 選擇放 8.1，用 `*(post-8.3)*` 標籤區分時機，避免多出一個 8.5 段使 checklist 結構變胖。SKILL.md 的描述段明確標示兩類時機。
- **B3 / B4 的前提**：P003 實作時採精簡版，尚未正式建立「Phase 3 draft 段落」與「rules.md anchor last-updated 欄位」的機械結構。本次 checklist 的引導語為前瞻性——等 P003 後續補強 B3/B4（或 P008 drift verification 擴充到語意層）時，文字無需再改。
