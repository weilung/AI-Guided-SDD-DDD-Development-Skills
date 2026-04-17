## PROPOSAL-007c: CLAUDE.md 標題分段強化

**狀態**: `implemented`

**提出日期**: 2026-04-11（從 PROPOSAL-007 拆分）

**優先級**: `低`

**來源**: PROPOSAL-007 拆分。原 P007 捆綁三個影響不同檔案、不同概念的子項，整合評估（2026-04-15）決定拆分為獨立 proposal。

**實施排程**: Wave C（獨立微調，無前置依賴）

---

### 問題描述

CLAUDE.md 模板的「專案脈絡」和「流程規則」混在一起。分開可以讓 AI 在不同情境下更有效地讀取相關資訊。

### 提議的解法

`templates/CLAUDE.md` 用更明確的標題分離：

```markdown
## 系統脈絡（What is this system?）
> 技術棧、業務領域、團隊結構

## 開發流程（How do we work?）
> SDD 流程、Git Flow、Domain 層規範
```

目前的 CLAUDE.md 模板已經有類似分段，只需標題更明確。工作量極小。

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `sdd-ddd-webforms-skill/templates/CLAUDE.md` | 修改 | 標題分段強化 |
| `sdd-ddd-core-skill/templates/CLAUDE.md` | 修改 | 同上 |

### 預估工作量

小

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-007（原） | 拆分自 | 本 proposal 是原 P007 的第三個子項 |

### 關鍵假設

- 假設：標題明確化能實際影響 AI 讀取策略（需要觀察）
- 驗證點：實施後觀察 Claude Code 是否在不同情境下更精準地引用對應段落

### 評估紀錄

**評估日期**: 2026-04-15

**結論**: approved

**理由**: 工作量極小（改兩份 CLAUDE.md 模板的標題），風險近零。效果需觀察，但實作成本低到不需要嚴格驗證假設才動手。

### 實施紀錄

**Wave**: C

**實施日期**: 2026-04-17

**實際變更**:

1. **兩版 `templates/CLAUDE.md`** 重構為兩大區塊：
   - `## 系統脈絡（What is this system?）` — 背景、架構（Core 版）、目錄結構
   - `## 開發流程（How do we work?）` — SDD 核心原則、功能開發/修改/bug-fix 流程、Git Flow、Domain 層規範、術語表、AI 協作注意事項
   - 原本 `##` 段落降級為 `###`，新的兩大分類用 `##` + 一行 blockquote 摘要

2. **不需要繁中同步**：模板是 AI 使用的（英文），不翻繁中版。

**影響檔案**（2 個）：

- `sdd-ddd-webforms-skill/templates/CLAUDE.md`
- `sdd-ddd-core-skill/templates/CLAUDE.md`
