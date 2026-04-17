## PROPOSAL-004: Spec 模板重構（段落分工 + 實作任務清單）

**狀態**: `implemented`（精簡版，見下方實施紀錄）

**提出日期**: 2026-04-11

**實施日期**: 2026-04-16

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

### 評估紀錄

**評估日期**: 2026-04-15（Wave A 排程時評估）

**結論**: 採用「精簡版」approach — 不改動現有段落結構，改用 HTML comment 註記每個段落的填寫 Phase，並新增「實作任務」段落。

**理由**:
- 原方案要求將模板重構為五段式（一、Proposal / 二、領域分析 / 三、行為規格 / 四、技術設計 / 五、實作任務）。評估後認為這會破壞既有 spec 的可搜尋性與既有引用路徑，且 Phase 歸屬本身可以用更輕量的 HTML comment 表達，不需要外顯的章節編號。
- 精簡版保留現有段落順序，在 heading 行尾加 `<!-- 填寫時機：Phase N -->`，對人無視覺干擾，對 AI 和 `/dflow:status` 仍可機讀。
- 「實作任務」段落是本 proposal 的另一個核心目的（持久化 checklist），這部分全量實施。

### 實施紀錄

**Wave**: A

**實施日期**: 2026-04-16

**實際變更**:

1. **兩版 `templates/feature-spec.md`**：
   - 在每個既有段落 heading 加 HTML comment phase 標記（Phase 1-4）。
   - 文件頂部加 template note 區塊說明 phase 標記用途、對應 SKILL.md 的 Guiding Questions by Phase、以及「實作任務」段的產生時機。
   - 新增 `## 實作任務` 段：
     - WebForms 版分類：`DOMAIN` / `PAGE` / `DATA` / `TEST`。
     - Core 版分類對應 Clean Architecture：`DOMAIN` / `APP` / `INFRA` / `API` / `TEST`，並標註建議產出順序 `DOMAIN → APP → INFRA → API`。

2. **兩版 `references/new-feature-flow.md`**：
   - Step 5（Plan Implementation）末尾加入「Generate Implementation Tasks List」子段，規定 AI 在計畫定稿後把 task 清單寫入 spec 的「實作任務」段，並提供格式與範例。
   - Step 8（Completion）加入第一項：驗證「實作任務」段全部勾選或未勾選項標註 follow-up。

3. **兩版 `references/modify-existing-flow.md`**：
   - 在 planning phase 末尾（WebForms Step 4、Core Step 3）加入 tasks 產生步驟，但限制為「使用完整 feature-spec 時才產生；lightweight-spec bug-fix 模式跳過」。
   - 在收尾 step（WebForms Step 6、Core Step 5）加入 tasks 驗證項。

4. **兩版 `SKILL.md`**：
   - 在 `## Guiding Questions by Phase` 開頭新增段落，說明 template 的 HTML comment phase 標記約定，以及「實作任務」段由 AI 在 Phase 4 結束時產生（指向對應 flow 的 step）。

**影響檔案總覽**（8 個）：

- `sdd-ddd-webforms-skill/templates/feature-spec.md`
- `sdd-ddd-core-skill/templates/feature-spec.md`
- `sdd-ddd-webforms-skill/references/new-feature-flow.md`
- `sdd-ddd-core-skill/references/new-feature-flow.md`
- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
- `sdd-ddd-core-skill/references/modify-existing-flow.md`
- `sdd-ddd-webforms-skill/SKILL.md`
- `sdd-ddd-core-skill/SKILL.md`

**待同步**：

- 繁體中文版（`sdd-ddd-webforms-skill-tw/`、`sdd-ddd-core-skill-tw/`）尚未同步，列為後續作業。
