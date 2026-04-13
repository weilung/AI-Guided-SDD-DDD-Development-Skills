## PROPOSAL-006: Ceremony Scaling 擴展（DDD 深度分級 + 漸進導入）

**狀態**: `draft`

**提出日期**: 2026-04-11

**優先級**: `中`

**來源**: OpenSpec 比較分析（S3-2 DDD 分級 + S7-1 漸進導入合併）

---

### 問題描述

1. **Ceremony Scaling 只管 spec 層級，不管 DDD 深度**。目前表格有 Spec Level、Domain Extraction、Tech Debt Record 三欄，但沒有告訴開發者：這次修改需不需要做完整的 Aggregate 設計？需不需要定義 Domain Events？對 DDD 新手來說，容易在簡單修改上過度建模，或在複雜功能上建模不足。

2. **沒有漸進式導入路徑**。目前 Skill 一次給出完整的 SDD + DDD 流程。對 DDD 經驗尚淺的團隊，一開始就要求完整流程會造成抗拒。OpenSpec 能快速導入的一個原因就是門檻低——3 個指令、5 分鐘上手。

### 提議的解法

#### 1. 擴展 Ceremony Scaling 表格（Core 版）

加入「DDD 建模深度」欄位：

| 變更類型 | Spec 層級 | DDD 建模深度 | Tech Debt |
|---|---|---|---|
| 新 Aggregate / BC | Full spec | 完整：Aggregate 設計 + Events + Context Map | — |
| 新功能（現有 BC 內） | Full spec | 標準：確認 Aggregate 歸屬 + 新 VO/Event | 如有 |
| 業務規則變更 | Standard spec | 標準：更新 rules.md + 檢查 invariants | 是 |
| Bug fix（邏輯錯誤） | Lightweight | 輕量：確認修在正確的層 | 如有 |
| API / UI 變更 | Lightweight | 跳過 | 跳過 |
| Infrastructure 變更 | 跳過 | 跳過 | 評估 |

DDD 建模深度定義：
- **完整**：需要用 aggregate-design 模板、更新 context-map、定義 events
- **標準**：確認歸屬、更新 models.md 和 rules.md
- **輕量**：只確認修改在正確的架構層
- **跳過**：不涉及 domain logic

#### 2. 建立漸進導入指引

在 SKILL.md 或新增獨立檔案，定義團隊導入路徑：

```
Stage 1（第 1-2 週）：只要求 Lightweight Spec
  目標：養成「先想再寫」的習慣

Stage 2（第 3-4 週）：要求 Full Spec + Glossary
  目標：養成文件化習慣

Stage 3（第 2 個月）：引入 Domain Extraction
  目標：開始建立 Domain 層

Stage 4（第 3 個月起）：完整 SDD + DDD
  目標：按 Ceremony Scaling 表格執行
```

主要給團隊 lead 參考，讓他們知道怎麼分階段推動。AI 在引導時也可以根據開發者的熟練度調整 ceremony 程度。

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| SKILL.md（Core） | 修改 | Ceremony Scaling 表格加入 DDD 建模深度欄 |
| SKILL.md（WebForms） | 修改 | Ceremony Scaling 表格微調，確認 Domain Extraction 欄的分級合理 |
| SKILL.md（兩版）或新檔案 | 新增/修改 | 加入漸進導入指引 |

### 預估工作量

小

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-004 | 互補 | 模板段落的「何時填寫」標註與 ceremony 級別呼應 |

### 評估紀錄（整合評估時填寫）

**評估日期**:

**結論**:

**理由**:
