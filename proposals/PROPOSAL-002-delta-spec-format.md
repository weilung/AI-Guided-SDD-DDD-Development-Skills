## PROPOSAL-002: 引入 Delta Spec 變更描述格式

**狀態**: `draft`

**提出日期**: 2026-04-11

**優先級**: `高`

**來源**: OpenSpec 比較分析（見 `openspec-reference.md`）

---

### 問題描述

修改現有功能時，我們用 Current Behavior / Changed Behavior 的自由文字描述變更。這個格式有兩個問題：

1. 不夠結構化——沒有明確標記哪些是「新增的規則」、「修改的規則」、「移除的規則」。Reviewer 需要自己比對差異。

2. 不可累積——每份 spec 各自描述 before/after，但沒有辦法把多次修改的結果合併成「系統現在的完整行為」（與 P003 相關）。

OpenSpec 的 Delta Spec 用 ADDED / MODIFIED / REMOVED 標記來結構化變更描述，這個概念值得借鏡。

### 提議的解法

在 `modify-existing-flow` 中引入結構化的 Delta Spec 格式：

```markdown
## 行為變更（Delta）

### ADDED — 新增的行為
#### 規則：{規則名稱}
Given {狀態}
When {操作}
Then {新的預期結果}

### MODIFIED — 修改的行為
#### 規則：{規則名稱}
**原本**：Given ... When ... Then {舊結果}
**改為**：Given ... When ... Then {新結果}
**原因**：{為什麼改}

### REMOVED — 移除的行為
#### 規則：{規則名稱}
**原因**：{為什麼移除}
```

**與 OpenSpec 的差異**：
- 維持 Gherkin 風格的 Given/When/Then，不用 RFC 2119（SHALL/MUST）
- MODIFIED 區段保留「原本」和「改為」的對照，比 OpenSpec 只寫新版本更清楚
- 嵌入現有的 spec 模板中，不需要額外的檔案結構

同時更新 `lightweight-spec` 模板，讓 bug fix 也能用精簡版的 delta 格式。

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `references/modify-existing-flow.md`（WebForms） | 修改 | Step 2 加入 delta 格式說明和範例 |
| `references/modify-existing-flow.md`（Core） | 修改 | 同上 |
| `templates/lightweight-spec.md`（兩版） | 修改 | 「現有行為/預期行為」段改用 delta 格式 |

### 預估工作量

小

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-003 | 互補 | Delta 格式提供結構化的變更描述，P003 的 behavior.md 需要這個格式來累積更新 |
| PROPOSAL-004 | 互補 | Spec 模板重構時可以一併整合 delta 段落 |

### 評估紀錄（整合評估時填寫）

**評估日期**:

**結論**:

**理由**:
