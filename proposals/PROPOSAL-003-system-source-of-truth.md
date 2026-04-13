## PROPOSAL-003: 建立系統現狀 Source of Truth

**狀態**: `draft`

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

### 評估紀錄（整合評估時填寫）

**評估日期**:

**結論**:

**理由**:
