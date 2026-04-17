## PROPOSAL-006a: Ceremony Scaling 加入 DDD 建模深度欄

**狀態**: `implemented`

**提出日期**: 2026-04-11（從 PROPOSAL-006 拆分）

**優先級**: `中`

**來源**: PROPOSAL-006 拆分。原 P006 捆綁「DDD 深度欄」與「漸進導入指引」兩個不共享實作的子項，整合評估（2026-04-15）決定拆分為獨立 proposal。

**實施排程**: Wave C（獨立微調，無前置依賴）

---

### 問題描述

Ceremony Scaling 只管 spec 層級，不管 DDD 建模深度。目前表格有 Spec Level、Domain Extraction、Tech Debt Record 三欄，但沒有告訴開發者：這次修改需不需要做完整的 Aggregate 設計？需不需要定義 Domain Events？

對 DDD 新手來說，容易在簡單修改上過度建模，或在複雜功能上建模不足。

### 提議的解法

Core 版 Ceremony Scaling 表格加入「DDD 建模深度」欄位：

| 變更類型 | Spec 層級 | DDD 建模深度 | Tech Debt |
|---|---|---|---|
| 新 Aggregate / BC | Full spec | 完整：Aggregate 設計 + Events + Context Map | — |
| 新功能（現有 BC 內） | Full spec | 標準：確認 Aggregate 歸屬 + 新 VO/Event | 如有 |
| 業務規則變更 | Standard spec | 標準：更新 rules.md + 檢查 invariants | 是 |
| Bug fix（邏輯錯誤） | Lightweight | 輕量：確認修在正確的層 | 如有 |
| API / UI 變更 | Lightweight | 跳過 | 跳過 |

DDD 建模深度定義：
- **完整**：需要用 aggregate-design 模板、更新 context-map、定義 events
- **標準**：確認歸屬、更新 models.md 和 rules.md
- **輕量**：只確認修改在正確的架構層
- **跳過**：不涉及 domain logic

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `sdd-ddd-core-skill/SKILL.md` | 修改 | Ceremony Scaling 表格加入 DDD 建模深度欄 |

### 預估工作量

小

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-004 | 互補 | 模板段落的「何時填寫」標註與 ceremony 級別呼應 |
| PROPOSAL-006（原） | 拆分自 | 本 proposal 是原 P006 的第一個子項 |

### 關鍵假設

- 假設：新手的學習曲線是任務驅動的（做到什麼學到什麼），Ceremony Scaling 加上 team lead 手動介入就夠照顧新手，不需要制式的時間分段指引
- 驗證點：實施 Wave A-B 後觀察新成員有沒有卡關點

### 備註：P006-2 漸進導入（defer，方案 γ）

原 P006 的第二個子項「漸進導入指引（Stage 1-4）」不獨立建檔，暫以方案 γ defer。等 2-3 個月新成員加入數據後，在 Wave D 決定：
- **方案 α**：併入 P006a（認定 Ceremony Scaling 自動涵蓋新手友善）
- **方案 β**：重新定位為「Team Lead 觀察清單」，移到 `docs/team-adoption-playbook.md`（不是 Skill 內容）
- **方案 γ**（目前選定）：暫不處理

若未來採 α 或 β，**應使用條件式 Stage 而非時間綁定**（雙盲對照 D2 備註）。

### 評估紀錄

**評估日期**: 2026-04-15

**結論**: approved

**理由**: 工作量極小（一張表格加一欄），對 DDD 新手有直接指引價值。情境推演顯示 P006a（DDD 深度欄）已大致涵蓋漸進導入（P006-2）要解的「按任務調整嚴謹度」問題，拆分後可獨立推進。

### 實施紀錄

**Wave**: C

**實施日期**: 2026-04-17

**實際變更**:

1. **`sdd-ddd-core-skill/SKILL.md`** Ceremony Scaling 表格：
   - 欄位 `DDD Modeling` → `DDD Modeling Depth`，值從 Required/Evaluate/Skip 改為 Full/Standard/Lightweight/Skip 四級
   - 「New feature」拆為「New Aggregate / BC」（Full）和「New feature (within existing BC)」（Standard）
   - 表格後附 DDD Modeling Depth 定義說明

**影響檔案**（1 個英文 + 1 個繁中）：

- `sdd-ddd-core-skill/SKILL.md`
- `sdd-ddd-core-skill-tw/SKILL_tw.md`（繁中同步）
