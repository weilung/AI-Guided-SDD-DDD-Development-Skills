# 新功能開發流程 — ASP.NET Core

使用完整 DDD 和 Clean Architecture 新增功能的逐步引導。

當開發者打 `/dflow:new-feature`（或自然語言表達新功能意圖、經安全網確認後）時觸發——詳見 `SKILL_tw.md` § 工作流程透明度的自動觸發安全網。

**本流程的 Phase Gate 位置**（跨越前要停下來等確認）：
- Step 4 → Step 5（規格寫完 → 規劃實作）
- Step 6 → Step 7（分支建好 → 開始實作）
- Step 7 → Step 8（實作完成 → 走完成流程）

其餘 step 間的轉換都是 **step-internal**：只宣告「Step N 完成，進入 Step N+1」，不等確認。完整透明度規則見 `SKILL_tw.md` § 工作流程透明度。

## 步驟 1：需求理解

自然地詢問：
1. **這個功能是什麼？** 白話描述。
2. **誰需要它？** 利害關係人或使用者角色。
3. **為什麼現在要做？** 優先順序和緊急程度。

檢查既有資產：
- 搜尋 `specs/domain/` 尋找相關概念
- 搜尋 `specs/features/` 尋找相關功能
- 檢查 `specs/domain/glossary.md` 和 `context-map.md`

**→ Step-internal 轉換**：Step 1 完成。宣告「Step 1 完成（需求理解），進入 Step 2：識別 Bounded Context」後繼續。

## 步驟 2：識別 Bounded Context

檢查 `specs/domain/context-map.md`：

```
「這個功能涉及 [概念]。它似乎屬於 [Context] 這個
Bounded Context。你的理解也是這樣嗎？」
```

如果需要新 Context → 使用 context-definition 模板。

如果跨 Context：
```
「這會同時觸及 [Context A] 和 [Context B]。我們需要決定：
- 哪個 Context 擁有這個操作？
- 另一個 Context 怎麼被通知？（Domain Event？Query？）
- 需不需要防腐層（Anti-Corruption Layer）？」
```

**→ Step-internal 轉換**：Step 2 完成。宣告「Step 2 完成（BC 已識別），進入 Step 3：領域建模」後繼續。

## 步驟 3：領域建模

這是 ASP.NET Core 流程與 WebForms 版本最大的差異所在。
詳細模式請參考 `references/ddd-modeling-guide_tw.md`。

逐步討論：

### Aggregate 識別
```
「在單一交易中什麼必須保持一致？
這些東西構成一個 Aggregate。其他的東西都是最終一致性。」
```

- 不變條件是什麼？
- Aggregate Root 是什麼？
- 哪些 Entity 屬於這個 Aggregate 內部？
- 有哪些 Value Object 可以抽取？

### Domain Events
```
「這件事發生之後，系統的其他部分需要知道什麼？」
```

- 這個功能會產生什麼事件？
- 它會消費什麼事件？
- Handler 在同一個 Context 內（同步）還是跨 Context（非同步）？

### 介面定義
```
「Domain 需要什麼外部資料才能做決策？」
```

- 持久化用的 Repository 介面
- 外部系統用的 Service 介面
- 在 Domain 層定義，在 Infrastructure 層實作

**→ Step-internal 轉換**：Step 3 完成。宣告「Step 3 完成（Aggregate / VO / Events 已識別），進入 Step 4：撰寫規格」後繼續。

## 步驟 4：撰寫規格

使用功能規格模板，在 `specs/features/active/{ID}-{功能名稱}.md` 建立文件。

與 WebForms 版本相比的關鍵新增項目：
- **Aggregate 狀態轉換**：記錄 Aggregate 狀態如何改變
- **Domain Events**：列出產生的事件和預期的處理器
- **CQRS 分類**：識別哪些是 Command（寫操作）、哪些是 Query（讀操作）

### 行為規格
```gherkin
Scenario: 提交費用報銷單
  Given 一份草稿狀態的費用報銷單，包含 3 個項目，合計 5,000 TWD
  When 員工提交報銷單
  Then 報銷單狀態變為 Submitted
  And 發出 ExpenseReportSubmitted 事件
  And 報銷單不能再被修改
```

**→ Phase Gate：Step 4 → Step 5**

向開發者宣告：
> 「規格初稿已完成——行為情境、Aggregate 狀態轉換、Domain Events、CQRS 分類都備妥了。準備開始規劃逐層實作（Domain → Application → Infrastructure → Presentation）嗎？打 `/dflow:next` 或回「好」繼續，或先告訴我規格還要再改一輪。」

等確認（`/dflow:next`、口語 OK、或隱含確認——見 `SKILL_tw.md` § 確認訊號）後才進 Step 5。

## 步驟 5：規劃實作（逐層）

### 領域層（最先實作）
```
1. 建立/更新 Aggregate Root，包含狀態變更方法
2. 建立 Value Objects，建構式驗證
3. 定義 Domain Events（record 類型）
4. 定義 Repository 介面
5. 撰寫 Domain 單元測試
```

### 應用層（第二個實作）
```
1. 建立 Command + CommandHandler（寫操作）
2. 建立 Query + QueryHandler（讀操作）
3. 建立 CommandValidator（FluentValidation）
4. 建立 Domain Event Handler（如需要）
5. 定義輸入/輸出的 DTO
```

### 基礎設施層（第三個實作）
```
1. EF Core 實體設定（Fluent API，不是屬性）
2. Repository 實作
3. 外部服務轉接器
4. Migration 腳本
```

### 展示層（最後實作）
```
1. API 端點（Controller 或 Minimal API）
2. Request/Response 模型
3. Swagger 文件
```

### 產生實作任務清單

逐層計畫定稿後，AI 根據計畫產生具體任務清單，寫入 spec 的「實作任務」段（見 feature-spec 模板）。每個任務格式為 `[LAYER]-[NUMBER]：描述`，對應 Clean Architecture 各層：

- `DOMAIN` — Aggregate / Entity / VO / Domain Event / Domain Service / Repository Interface
- `APP`    — Command/Query / Handler / Validator / DTO / Event Handler
- `INFRA`  — EF Configuration / Repository 實作 / 外部 adapter / Migration
- `API`    — Controller / Minimal API / Request/Response / Swagger
- `TEST`   — 各層的測試

建議產出順序與實作順序一致：`DOMAIN → APP → INFRA → API`（`TEST` 穿插其中）。

範例（實際內容依功能調整）：

```markdown
- [ ] DOMAIN-1：ExpenseReport aggregate 含 Submit() 狀態轉換
- [ ] DOMAIN-2：ExpenseReportSubmitted domain event
- [ ] APP-1：SubmitExpenseReportCommand + Handler
- [ ] INFRA-1：EF 設定 + ExpenseReportRepository
- [ ] API-1：POST /expense-reports/{id}/submit 端點
- [ ] TEST-1：Aggregate invariants + handler 單元測試
```

這份清單會成為 Step 7 的執行清單，也是 Step 8 完成流程 checklist 的依據。

**→ Step-internal 轉換**：Step 5 完成。宣告「Step 5 完成（逐層計畫 + 任務清單備妥），進入 Step 6：建立 Git 分支」後繼續。

## 步驟 6：建立 Git 分支

```
分支：feature/{SPEC-ID}-{簡短描述}
範例：feature/EXP-001-submit-expense-report
```

**→ Phase Gate：Step 6 → Step 7**

向開發者宣告：
> 「分支 `feature/{SPEC-ID}-{描述}` 已建立。要開始逐層實作了嗎（Domain 層先行）？打 `/dflow:next` 前進，或先討論各層順序 / 範圍。」

等確認後才進 Step 7。

## 步驟 7：實作檢查清單

實作過程中持續確認：

**領域層**
- [ ] Aggregate 保護所有不變條件
- [ ] 狀態只能透過方法變更（沒有 public setter）
- [ ] Value Objects 是不可變的，有建構式驗證
- [ ] 重要的狀態變更都有發出 Domain Events
- [ ] 零外部依賴（檢查 .csproj）
- [ ] 單元測試涵蓋不變條件和業務規則

**應用層**
- [ ] Handler 中沒有業務邏輯（只有編排）
- [ ] Command/Query 分離有維持
- [ ] 驗證在 Validator 中，不在 Handler 裡
- [ ] DTO 有正確映射 Domain 物件（API 不暴露 Domain 物件）

**基礎設施層**
- [ ] EF 設定使用 Fluent API（Domain 實體上沒有屬性標註）
- [ ] Repository 正確實作 Domain 介面
- [ ] 查詢中沒有業務邏輯

**展示層**
- [ ] Controller 是精簡的（解析 → 派發 → 回應）
- [ ] 沒有 Domain 物件暴露給 API 消費者
- [ ] 使用正確的 HTTP 狀態碼

**→ Phase Gate：Step 7 → Step 8**

向開發者宣告：
> 「四層實作看起來都完成了。要開始跑完成流程 checklist（對照 spec 驗證、更新 domain 文件 + context-map、確認測試涵蓋、歸檔 spec）嗎？打 `/dflow:next` 前進。」

等確認後才進 Step 8。這個 Phase Gate 是完成流程 checklist 的觸發點——不可略過。

## 步驟 8：完成

由 Step 7 → Step 8 Phase Gate 觸發。AI 依下面順序跑 checklist，**不可**略過任何一段。

### 8.1 驗證 — AI 可獨立執行

AI 一次檢查完所有項目，用 ✓/✗ 一份列表回報後才開始動文件。標註 *(8.3 後)* 的項目要等 8.3 合併動作完成後再驗證：

- [ ] spec 的每個 Given/When/Then 情境都有實作或測試覆蓋
- [ ] spec 的每個 BR-*（業務規則）都有實作或測試覆蓋
- [ ] spec 的每個 EC-*（邊界情況）都有處理
- [ ] spec 列出的每個 Domain Event 在實作裡都有 raise
- [ ] Domain 層專案沒有外部 NuGet 依賴（檢查 `*.Domain.csproj`）
- [ ] 變更後 Aggregate invariants 仍然成立（狀態變更都透過方法，沒有 public setter）
- [ ] EF Core 設定只用 Fluent API（Domain 實體上沒有 `[Table]`/`[Column]` 屬性）
- [ ] `實作任務` 段：全部勾選，或未勾選項目明確標註為 follow-up（連結到 spec 或 tech-debt）
- [ ] *(8.3 後)* `specs/domain/{context}/behavior.md` 對本 spec 引入的每個 `BR-*` 都有對應 section anchor（`/dflow:verify` 的機械輸入）
- [ ] *(8.3 後)* `specs/domain/{context}/behavior.md` 的 `last-updated` 晚於本 spec 的 `created` 日期（機械 drift 防線）

任何一項失敗就回報差距並暫停——不要往下進 8.2。

### 8.2 驗證 — 需開發者確認

AI 一次列一項、等開發者確認後再列下一項：

- [ ] 每條 BR 的實作是否**忠實表達意圖**？（AI 列出 BR → 實作位置，開發者判斷）
- [ ] 邊界情況的處理方式是否**恰當**？（AI 列出 EC → 處理方式，開發者判斷）
- [ ] Domain Event 的 payload 與 handler 擺放位置（同 context 同步 vs 跨 context 非同步）是否正確？（AI 列出，開發者確認）
- [ ] 有沒有漏掉的技術債？
- [ ] 合併進 `behavior.md` 的情境（含 Aggregate 轉換 + Events）是否**忠實表達預期行為**？（AI 列出合併後的 anchor，開發者判斷）
- [ ] 完成後要不要把 spec 的 `實作任務` 段**摺疊 / 移除**？（看團隊慣例，開發者決定）

一次問一項，不要一次倒六個。

### 8.3 文件更新

- [ ] `specs/domain/glossary.md` — 新術語已加入
- [ ] `specs/domain/{context}/models.md` — 模型定義已更新
- [ ] `specs/domain/{context}/rules.md` — 業務規則已更新
- [ ] `specs/domain/{context}/behavior.md` — 把完成的 spec 的 Given/When/Then 情境（含 Aggregate 轉換 + Events）合併進行為彙整。子步驟：
      - 把 Phase 3 draft 段落（B3 中途同步）轉為正式段落
      - 更新對應 `rules.md` anchor 的 `last-updated` 日期（B4）
- [ ] `behavior.md` 草稿清理 — 若本 spec 中途放棄，保留 `## 提案中變更` 段的歷史，或明確 REMOVE
- [ ] `specs/domain/{context}/events.md` — Domain Events 已更新
- [ ] `specs/domain/context-map.md` — 如果新增或變更了跨 context 互動則更新
- [ ] `specs/architecture/tech-debt.md` — 實作中發現的技術債已記錄

### 8.4 歸檔

- [ ] spec 的 `status` 欄位改為 `completed`
- [ ] spec 檔案從 `specs/features/active/` 搬到 `specs/features/completed/`

只有 8.4 做完才能宣告「功能完成」。
