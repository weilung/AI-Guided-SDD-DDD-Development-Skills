## PROPOSAL-002: 引入 Delta Spec 變更描述格式

**狀態**: `implemented`

**提出日期**: 2026-04-11

**實施排程**: Wave A

**優先級**: `高`

**來源**: OpenSpec 比較分析（見 `openspec-reference.md`）+ evaluation-p002-p008 B1/B2 追加

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

**追加 B1：RENAMED 操作**（evaluation 階段加入）：

```markdown
### RENAMED — 重新命名
#### 規則：{舊名稱} → {新名稱}
**原因**：{為什麼改名}
```

**使用情境**：brownfield 常見的概念改名，例如「簽核」→「審批」、「客戶」→「帳戶持有人」。保持追蹤鏈，避免 behavior.md（P003）留下孤立條目。

**追加 B2：可選的 UNCHANGED 段**（evaluation 階段加入）：

```markdown
### UNCHANGED — 明確不受影響（可選）
- BR-003 金額上限
- BR-005 提交後不可修改
```

**定位**：建議但非必填。適合 MODIFIED 規則較多、或 regression 風險高的 refactor 類變更，主動回答「其他規則有沒有被影響」。

**重要澄清**：Delta 標記僅用於「變更 spec」，不累積到 P003 的 behavior.md（consolidated source of truth）。behavior.md 讀取 delta 後 merge 成當前真相，不保留歷史軌跡（git 本身已有）。

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

### 評估紀錄

**評估日期**: 2026-04-15

**結論**: approved，附重要澄清 + B1（RENAMED）+ B2（UNCHANGED 可選）追加。

**理由**:
- 結構化 delta 格式比自由文字更利於 reviewer 與 AI 理解變更意圖，且為 P003 的 behavior.md 提供清晰 input 源。
- **澄清**：delta 標記只用在變更 spec、不累積到 consolidated behavior.md。否則 MODIFIED 對照會不斷堆疊變成歷史雜訊；consolidated 文件應反映「當前真相」，歷史由 git 保留。
- **B1（RENAMED）**：brownfield 場景概念演化常見，單獨標示可保持追蹤鏈、避免 behavior.md 留下孤立條目；也讓 P007b Step 0 讀 delta 時語義清楚。
- **B2（UNCHANGED 建議非必填）**：regression 風險高時主動填可減少 review 摩擦；標為選填避免 ceremony 膨脹。

### 實施紀錄

**Wave**: A（與 P001 / P004 並行；於 P004 完成後接著實施）

**關鍵假設（待實施後觀察驗證）**：
- A1：delta 格式比自由文字描述的效益大於格式負擔
- A11：RENAMED 的使用頻率足夠支持學習成本；若一年用不到兩次，可能成為冗餘格式
- A12：UNCHANGED「建議非必填」不會造成「建議但沒人填」的空轉
