## PROPOSAL-005a: 完成流程 Checklist — 驗證 + 文件更新 + 歸檔（Wave A 子集）

**狀態**: `implemented`

**提出日期**: 2026-04-11（從 PROPOSAL-005 拆分）

**實施日期**: 2026-04-16

**實施排程**: Wave A

**優先級**: `高`

**來源**: PROPOSAL-005 拆分（B5）+ 驗證分層（B6）

---

### 問題描述

原 P005 整體核准但全部排到 Wave B，造成「驗證文化」建立晚半拍。評估後發現 checklist 內多數項目**不依賴 P003（behavior.md）/ P004（tasks 段）**，這些可獨立提前到 Wave A。

本 proposal 只涵蓋不依賴 P003/P004 的子集：驗證 + 文件更新 + 歸檔三段 checklist。

### 提議的解法

#### 1. 在 new-feature-flow Step 8 加入結構化完成 Checklist（分層）

```markdown
## 完成 Checklist

### 驗證（AI 可獨立執行）
- [ ] 對照 spec 的每個 Given/When/Then，確認實作覆蓋
- [ ] 對照 spec 的每個 BR-*（業務規則），確認實作覆蓋
- [ ] 對照 spec 的每個 EC-*（邊界情況），確認有處理
- [ ] Domain 層無 System.Web 引用（WebForms）/ 無外部 NuGet（Core）

### 驗證（需開發者確認）
- [ ] 業務規則的實作是否符合意圖（AI 列出，由開發者判斷）
- [ ] 邊界情況的處理方式是否恰當
- [ ] tech-debt 是否有遺漏

### 文件更新
- [ ] glossary.md — 新術語已加入
- [ ] models.md — 模型定義已更新
- [ ] rules.md — 業務規則已更新
- [ ] events.md — Domain Events 已更新（Core 版）
- [ ] tech-debt.md — 發現的技術債已記錄

### 歸檔
- [ ] Spec 狀態改為 completed
- [ ] Spec 搬移到 completed/
```

**Wave B（P005b）會補上**：
- [ ] behavior.md — 行為規格已合併（依賴 P003）
- [ ] Tasks 清單全部勾選完成（依賴 P004）
- [ ] Tasks 段歸檔前清理（依賴 P004）

#### 2. 完成信號偵測直接用 P001 Phase Gate（S3 簡化）

不另做自然語言偵測。Step 7→8 的 Phase Gate（`/dflow:next` 或確認回覆）就是觸發點：AI 收到後先跑「AI 可獨立驗證」項目，報 ✓/✗ 列表；全數通過才逐項引導開發者確認「需開發者確認」項。

#### 3. modify-existing-flow 的精簡版（B5 範圍內）

在 WebForms Step 6 / Core Step 5 加入相同分層，聚焦在文件更新 + 歸檔；修 bug 時的驗證可簡化為「對照 delta 的 MODIFIED / ADDED / REMOVED，確認實作覆蓋」。

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `references/new-feature-flow.md`（兩版） | 修改 | Step 8 擴為分層 checklist（驗證 / 文件 / 歸檔） |
| `references/modify-existing-flow.md`（兩版） | 修改 | Step 6（WebForms）/ Step 5（Core）加精簡 checklist |
| `SKILL.md`（兩版） | 修改 | 在 Workflow Transparency 或完成流程段，加「Phase Gate 觸發完成 checklist」說明 + AI 執行順序（先獨立驗證，再引導確認） |

### 預估工作量

小（不依賴其他 proposal，主要是既有 checklist 結構化 + 分層）

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-001 | 依賴 | 完成觸發點使用 Phase Gate |
| PROPOSAL-003 | 互補 | P005a 不含 behavior.md 項，留 P005b |
| PROPOSAL-004 | 互補 | P005a 不含 tasks 勾選驗證，留 P005b（P004 實作已先把 tasks 驗證寫在各 flow，P005b 會整併進 checklist） |
| PROPOSAL-005b | 拆分互補 | Wave B 補上 P003/P004 相關項 |
| PROPOSAL-008 | 互補 | P008 drift verification 屬於「AI 可獨立驗證」類，分層定義給 P008 機械層做參考 |

### 關鍵假設

- 假設 1：`/dflow:next` Phase Gate 足以替代自然語言完成信號偵測
- 假設 2（B6）：AI 可獨立驗證的項目清單實際上 AI 能可靠執行；需在 Wave A pilot 觀察失敗率
- 風險：開發者跳過 Phase Gate 直接 commit & push，checklist 不會觸發；緩解靠 P001 auto-trigger 安全網與 Git Flow 整合

### 評估紀錄

**評估日期**: 2026-04-15

**結論**: approved（Wave A 提前），配合 B6 驗證分層

**理由**: 不依賴 P003/P004 的驗證項（Domain 純淨度、Given/When/Then 覆蓋）可立即建立驗證文化；B6 分層避免 AI 偽裝驗證，開發者清楚知道哪些是 AI 把關、哪些需自己看。

### 實施紀錄

**Wave**: A（於 P001 / P002 / P004 完成後接上）

**實施日期**: 2026-04-16

**實際變更**:

1. **兩版 `references/new-feature-flow.md` Step 8**：從簡易列表改為四段分層 checklist：
   - `8.1 Verification — AI runs independently`：BR/EC/Given-When-Then 覆蓋、Domain 層純淨度、Tasks 狀態；Core 版補 Aggregate invariants / Domain Events raised / EF Fluent API only / Domain 專案無外部 NuGet 等項。
   - `8.2 Verification — needs developer confirmation`：意圖一致性、邊界情況合理性、tech-debt 補漏；Core 版補 Aggregate boundary / Domain Event placements。
   - `8.3 Documentation updates`：glossary / models / rules / events / context-map / tech-debt。
   - `8.4 Archival`：status → completed、檔案移到 `completed/`。

2. **兩版 `references/modify-existing-flow.md`**（WebForms Step 6 / Core Step 5）：相同四段分層結構，但驗證項對齊 Delta 格式（ADDED / MODIFIED / REMOVED / RENAMED 覆蓋）；bug-fix 搭配 lightweight-spec 時 `(full spec only)` 項跳過。

3. **兩版 `SKILL.md`**：在 Workflow Transparency 新增 `Completion Checklist Execution` 子節，明訂：
   - Step 7→8（new-feature）/ Step 5→6 或 4→5（modify-existing）Phase Gate 是唯一觸發點。
   - 執行順序嚴格為 8.1 → 8.2 → 8.3 → 8.4（或 5.x / 6.x）。
   - AI-independent 一次報完 ✓/✗；Developer-confirmation 一次問一項。
   - 若開發者跳過 Phase Gate 直接 commit，auto-trigger 安全網須先問「要跑 Step 8 checklist 嗎」。

**影響檔案總覽**（6 個）：

- `sdd-ddd-webforms-skill/references/new-feature-flow.md`
- `sdd-ddd-core-skill/references/new-feature-flow.md`
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
- `sdd-ddd-core-skill/references/modify-existing-flow.md`
- `sdd-ddd-webforms-skill/SKILL.md`
- `sdd-ddd-core-skill/SKILL.md`

**待同步**：繁體中文版（`sdd-ddd-webforms-skill-tw/`、`sdd-ddd-core-skill-tw/`）尚未同步，列為後續作業。
