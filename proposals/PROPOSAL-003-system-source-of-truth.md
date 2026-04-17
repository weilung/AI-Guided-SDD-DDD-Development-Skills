## PROPOSAL-003: 建立系統現狀 Source of Truth

**狀態**: `implemented`

**提出日期**: 2026-04-11

**優先級**: `高`

**來源**: OpenSpec 比較分析（見 `openspec-reference.md`）

---

### 問題描述

我們的 `specs/features/completed/` 只是歷史歸檔。要了解「某個 Bounded Context 現在的完整行為」，需要翻遍所有相關的已完成 spec，拼湊出全貌。這對新成員 onboarding、跨 context 設計、和 AI 理解系統現狀都是障礙。

OpenSpec 用 `specs/` 目錄作為系統行為的 source of truth，每次變更完成後自動 merge，隨時反映系統全貌。我們缺少對應的機制。

### 提議的解法

在每個 Bounded Context 的 domain 文件夾下，新增一個 `behavior.md`，作為該 context 行為規格的 consolidated source of truth。

```
specs/domain/{context}/
├── context.md        # Context 邊界定義（已有）
├── models.md         # Domain 模型定義（已有）
├── rules.md          # 業務規則目錄（已有）
├── events.md         # Domain Events（Core 版已有）
└── behavior.md       # 【新增】行為規格彙整
```

**`behavior.md` 的內容**：
- 按功能區域組織的 Given/When/Then 情境
- 每次 feature 完成時，把 spec 中的行為規格合併進來
- 修改功能時，用 P002 的 delta 格式更新

**與 OpenSpec 的差異**：
- 按 Bounded Context 組織（不是按 capability），與 DDD 模型對齊
- 與現有的 rules.md 互補：rules.md 是業務規則的「宣告式」列表，behavior.md 是「情境式」的行為描述
- 不建 CLI 工具做自動 merge，由 AI 在完成流程中引導更新

需同時新增一個 `templates/behavior.md` 模板，提供初始結構。

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| SKILL.md（兩版） | 修改 | Project Structure 加入 behavior.md，說明其用途 |
| `references/new-feature-flow.md`（兩版） | 修改 | 完成步驟加入「更新 behavior.md」 |
| `references/modify-existing-flow.md`（兩版） | 修改 | 完成步驟加入「更新 behavior.md」 |
| `templates/context-definition.md`（兩版） | 修改 | 提及 behavior.md 的存在 |
| `templates/behavior.md`（兩版） | 新增 | 提供初始結構模板 |

### 預估工作量

中

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-002 | 依賴 | behavior.md 的更新需要用 P002 的 delta 格式來累積變更 |
| PROPOSAL-005 | 互補 | 完成流程 checklist 需要包含「更新 behavior.md」這一步 |

### 評估紀錄

**評估日期**: 2026-04-17

**結論**: approved → implemented（用戶直接指示實施，理由：實際使用 OpenSpec 後確認「一覽行為全貌」是核心需求）

**理由**: 沒有 behavior.md，系統行為的 source of truth 散落在 `specs/features/completed/` 的歷史歸檔中，要了解一個 BC 的完整行為需拼湊多份 spec。用戶確認此功能是與 OpenSpec 可比性的基礎。

### 實施紀錄

**實施日期**: 2026-04-17

**實際變更**:

1. **新增 `templates/behavior.md`（兩版）**：
   - 定義 behavior.md 的結構：按功能區域組織，每個 BR-ID 一個 section 含 Given/When/Then
   - 頂部說明 purpose、maintenance 規則、與 rules.md 的 A+C 關係
   - Core 版額外強調 Aggregate 狀態轉換和 Domain Events
   - 底部 HTML comment 列出維護注意事項（merge/update/delete/rename 規則）

2. **兩版 `SKILL.md` 新增「Behavior Source of Truth」段**：
   - 解釋 rules.md（索引）+ behavior.md（內容）的 A+C 分工
   - 對比 OpenSpec 的 specs/ 目錄角色
   - Project Structure 樹狀圖加入 behavior.md
   - Templates 表格加入 behavior.md

3. **四個 flow 檔案完成流程段加入 behavior.md 更新項**：
   - new-feature-flow 8.3 / 6.3：「merge completed spec's Given/When/Then scenarios into consolidated behavior」
   - modify-existing-flow 6.3 / 5.3：「update scenarios to reflect Delta result (merge final state, not Delta markup)」

4. **兩版 `templates/context-definition.md`**：關鍵業務規則段更新提及 behavior.md

**影響檔案**（EN 12 個 + TW 8 個 = 20 個）：

EN:
- `sdd-ddd-webforms-skill/templates/behavior.md`（新增）
- `sdd-ddd-core-skill/templates/behavior.md`（新增）
- `sdd-ddd-webforms-skill/SKILL.md`
- `sdd-ddd-core-skill/SKILL.md`
- `sdd-ddd-webforms-skill/references/new-feature-flow.md`
- `sdd-ddd-core-skill/references/new-feature-flow.md`
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
- `sdd-ddd-core-skill/references/modify-existing-flow.md`
- `sdd-ddd-webforms-skill/templates/context-definition.md`
- `sdd-ddd-core-skill/templates/context-definition.md`

TW（繁中同步）：
- `sdd-ddd-webforms-skill-tw/SKILL_tw.md`
- `sdd-ddd-core-skill-tw/SKILL_tw.md`
- `sdd-ddd-webforms-skill-tw/new-feature-flow_tw.md`
- `sdd-ddd-core-skill-tw/new-feature-flow_tw.md`
- `sdd-ddd-webforms-skill-tw/modify-existing-flow_tw.md`
- `sdd-ddd-core-skill-tw/modify-existing-flow_tw.md`
