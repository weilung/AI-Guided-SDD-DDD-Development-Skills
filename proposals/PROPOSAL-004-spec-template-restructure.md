## PROPOSAL-004: Spec 模板重構（段落分工 + 實作任務清單）

**狀態**: `draft`

**提出日期**: 2026-04-11

**優先級**: `中`

**來源**: OpenSpec 比較分析（S1-1 拆分模板 + S1-4 加入 tasks 合併）

---

### 問題描述

兩個問題合在一起看：

1. **feature-spec 模板太臃腫**。把「為什麼做」、「做什麼」、「怎麼做」、「檢查什麼」全部塞在一個模板裡，開發者面對一大片空白容易跳過不填。職責混在一起也讓 AI 引導時不好分階段處理。

2. **沒有可追蹤的實作任務清單**。實作階段的任務拆分完全在 AI 的對話中進行，沒有持久化的 checklist。OpenSpec 的 tasks.md 讓 AI 可以逐項勾選、斷點續作。我們缺少對應機制。

### 提議的解法

**不拆成多個獨立檔案**（我們的 spec 同時承載 DDD 資訊，拆開會讓關聯性斷裂），而是重構為五個明確區塊，每個區塊標註「何時填寫」：

```markdown
# {Feature Title}

## 一、Proposal（Phase 1 填寫）
### 問題描述
### 利害關係人

## 二、領域分析（Phase 2 填寫）
### Bounded Context
### 領域概念
### 術語更新

## 三、行為規格（Phase 3 填寫）
### 情境（Given/When/Then）
### 業務規則
### 邊界情況
### Domain Events（Core 版）

## 四、技術設計（Phase 4 填寫）
### Domain 層設計
### 實作方式
### 資料結構變更
### 測試策略
### 遷移考量

## 五、實作任務（Phase 4 完成後填寫）
- [ ] 任務 1：{描述}
- [ ] 任務 2：{描述}
```

**Tasks 段的規則**：
- 每個任務應該是可獨立完成、可驗證的單元
- 任務粒度：一個任務 ≈ 一個 commit
- AI 在 Phase 4 結束時根據技術設計自動產生任務清單
- 實作時逐項勾選，支援中斷後續做

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `templates/feature-spec.md`（WebForms） | 修改 | 重構為五段式 + 加入 tasks 段 |
| `templates/feature-spec.md`（Core） | 修改 | 同上，含 Domain Events 和逐層設計 |
| SKILL.md（兩版） | 修改 | Guiding Questions by Phase 對應新的模板段落編號 |
| `references/new-feature-flow.md`（兩版） | 修改 | Step 5 加入「產生 tasks 清單」 |

### 預估工作量

中

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-002 | 互補 | 模板重構時可以一併整合 delta 段落 |
| PROPOSAL-005 | 互補 | Tasks 清單是完成流程 checklist 的追蹤依據 |

### 評估紀錄（整合評估時填寫）

**評估日期**:

**結論**:

**理由**:
