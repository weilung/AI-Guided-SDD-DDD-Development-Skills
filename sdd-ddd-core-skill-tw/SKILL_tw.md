# SDD/DDD 工作流程守衛 — ASP.NET Core 版

## 概述

這是 ASP.NET Core 專案使用 Clean Architecture 和 Domain-Driven Design 的 AI 引導開發流程規範。當開發者開始任何功能開發、Bug 修復、或程式碼修改時，AI 會自動介入引導，確保遵循 SDD 規格優先的流程，並強制執行 DDD 戰術模式和分層架構。

---

## 架構概覽

本專案使用 Clean Architecture 四層架構。依賴方向永遠朝內：

```
┌─────────────────────────────────────────────┐
│  展示層（Presentation）                       │
│  Controllers, ViewModels, Middleware         │
│  依賴：Application                           │
├─────────────────────────────────────────────┤
│  應用層（Application）                        │
│  Commands, Queries, DTOs, Validators         │
│  依賴：Domain                                │
├─────────────────────────────────────────────┤
│  領域層（Domain）— 核心                       │
│  Entities, Value Objects, Aggregates,        │
│  Domain Events, Domain Services,             │
│  Repository Interfaces, Specifications       │
│  依賴：無（零外部依賴）                        │
├─────────────────────────────────────────────┤
│  基礎設施層（Infrastructure）                  │
│  EF Core DbContext, Repository 實作,          │
│  外部 API 客戶端, 訊息佇列, Email, 檔案存取    │
│  依賴：Domain, Application                    │
└─────────────────────────────────────────────┘
```

**關鍵規則**：Domain 層零外部依賴。除了純 .NET 型別外不引用任何 NuGet 套件。不使用 EF Core 屬性、JSON 序列化屬性、或任何框架相關的東西。

---

## 決策樹：AI 依開發者輸入決定流程

```
開發者輸入
    │
    ├─ /dflow: 指令（明確進入，直接路由）
    │   ├─ /dflow:new-feature     → 新功能流程（references/new-feature-flow_tw.md）
    │   ├─ /dflow:modify-existing → 修改既有功能流程（references/modify-existing-flow_tw.md）
    │   ├─ /dflow:bug-fix         → 修改既有功能流程（走輕量 ceremony）
    │   ├─ /dflow:pr-review       → PR 審查檢查清單（references/pr-review-checklist_tw.md）
    │   ├─ /dflow:verify [<bc>]   → Drift 驗證（references/drift-verification_tw.md）
    │   ├─ /dflow:status          → 回報目前工作流程狀態（見 § 工作流程透明度）
    │   ├─ /dflow:next            → 確認進入下一階段（僅在流程進行中）
    │   └─ /dflow:cancel          → 取消當前流程，回到自由對話
    │
    ├─ 自然語言表達開發意圖（自動觸發安全網）
    │   └─ 不要自動進入流程。改做（見 § 工作流程透明度）：
    │       1. 宣告判斷：「我判斷這是 [新功能 / 修改既有 / bug-fix] 任務」
    │       2. 建議對應的 /dflow: 指令
    │       3. 等開發者確認後才進入流程
    │
    ├─「我在設計 Domain Model」/「X 應該怎麼建模？」
    │   └─ → DDD 建模指南（references/ddd-modeling-guide_tw.md）
    │
    ├─「快速問一下…」/「X 是怎麼運作的？」
    │   └─ → 先查 specs/domain/ 裡的知識回答
    │
    ├─「我要建分支」
    │   └─ → Git Flow 整合（references/git-flow-integration_tw.md）
    │
    └─ 其他和程式碼相關的事
        └─ → 評估：這會動到業務邏輯嗎？
            是 → 走自動觸發安全網（建議 /dflow: 指令、等確認）
            否 → 直接協助，不需要額外流程
```

---

## 工作流程透明度

本 Skill 採 **Hybrid 設計**：slash 指令是主要入口，自然語言自動觸發是安全網，透過分層透明度讓開發者隨時知道自己在流程的哪裡。

### Slash 指令

**Flow entry 指令**（啟動一個流程）：
- `/dflow:new-feature` — 進入新功能流程
- `/dflow:modify-existing` — 進入修改既有功能流程
- `/dflow:bug-fix` — 進入修改既有功能流程（走輕量 ceremony）
- `/dflow:pr-review` — 進入 PR 審查檢查清單

**控制指令**（管理進行中的流程）：
- `/dflow:status` — 回報目前流程、步驟、進度
- `/dflow:next` — 確認進入下一階段（等同於「好」/「繼續」）
- `/dflow:verify [<bc>]` — 執行 drift 驗證：rules.md ↔ behavior.md 一致性檢查（獨立指令，不需要進行中的流程）
- `/dflow:cancel` — 取消當前流程,回到自由對話(已建立的檔案保留)

### 自動觸發安全網

當自然語言隱含開發任務時,Skill 仍會偵測意圖——但**不要**自動進入流程。按這個模式處理:

1. 清楚宣告判斷:
   > 「我判斷這是新功能任務。」
2. 提供開發者三個選項:
   - 打 `/dflow:new-feature` 明確啟動
   - 回「好」/「繼續」確認走這個流程
   - 或糾正流程類型(例如「不對,這是 bug-fix」)
3. 等確認後才進入流程。

這樣處理解決純自動觸發的三個失敗模式:錯過觸發、選錯流程、狀態不可見。

### 三層透明度

流程進行中,以下面三種層級溝通——不多也不少:

| 層級 | 觸發點 | AI 行為 |
|---|---|---|
| **Flow entry（必須確認）** | 從 NL 判斷出流程後 | 停下來等確認(指令 / 「好」/ 隱含確認) |
| **Phase gate（宣告 + 可能需確認）** | 重要里程碑前 | 宣告轉換;若開發者提供下一階段輸入即視為隱含確認 |
| **Step-internal（只宣告）** | Step N → Step N+1 | 宣告「Step N 完成,進入 Step N+1」——不等待 |

**Phase Gate 位置：**

new-feature-flow（8 個步驟）：
- Step 4 → 5（規格寫完 → 規劃實作）
- Step 6 → 7（分支建好 → 開始實作）
- Step 7 → 8（實作完成 → 走完成流程）

modify-existing-flow（5 個步驟——Core 版因分層已清楚，抽離/分析合併成一步）：
- Step 2 → 3（基準線記錄完 → 評估 DDD 影響）
- Step 3 → 4（DDD 影響決策 → 實作）
- Step 4 → 5（實作完成 → 更新文件）

### 完成流程 Checklist 的執行方式

new-feature-flow Step 7 → Step 8 Phase Gate、modify-existing-flow Step 4 → Step 5 Phase Gate 是**完成流程 checklist 的觸發點**。不要機會性地跑 checklist——要等開發者跨越這個 gate（透過 `/dflow:next`、確認詞、或隱含確認）後才執行。

觸發後，嚴格按下面順序執行：

1. **AI 可獨立執行的驗證**（Section 8.1 / 5.1）：所有項目 AI 自己跑完，用 `✓` / `✗` 一份列表回報。項目分兩類時機：
   - **合併前**（預設）：動文件之前就可以驗——Given/When/Then 與 BR/EC 覆蓋、Domain Events 是否 raise、Domain 專案純淨度（無外部 NuGet）、Aggregate invariants 是否保留、EF 設定是否只用 Fluent API、`實作任務` 段完整度。
   - **8.3 / 5.3 之後**（標 `*(...後)*`）：合併動作完成後再驗——`behavior.md` 對 `BR-*` 的 anchor 對應（含 REMOVED delta 的 anchor 刪除）與 `last-updated` 日期（`/dflow:verify` 的機械輸入）。
   任一失敗就暫停修正，不往下。
2. **需開發者確認的驗證**（Section 8.2 / 5.2）：AI 一次問一項（意圖一致性、Aggregate 邊界合理性、Domain Event 擺放位置、漏記的技術債），等開發者判斷後才問下一項。**不可**一次倒多個問題。P005b 多加兩題：合併進 `behavior.md` 的情境（含 Aggregate 轉換 + Events）是否忠實表達預期行為、以及 spec 的 `實作任務` 段要不要摺疊 / 移除（看團隊慣例）。
3. **文件更新**（Section 8.3 / 5.3）：更新 glossary / models / rules / events / context-map / tech-debt。`behavior.md` 的合併包含兩個子步驟：把 Phase 3 draft 段落（B3 中途同步）轉為正式段落、更新對應 `rules.md` anchor 的 `last-updated` 日期（B4）。若 spec 中途放棄，要清理 `## 提案中變更` 段（保留歷史或明確 REMOVE）。
4. **歸檔**（Section 8.4 / 5.4）：spec 搬到 `completed/`，翻 `status`。

只有歸檔完才能宣告「功能完成」/「變更完成」。如果開發者跳過 Phase Gate 直接 commit，自動觸發安全網要攔截：「看起來你要收尾了——要我跑 Step 8 checklist 嗎？」之後才給 commit 指引。

### 確認訊號（自然語言 ↔ 指令等價）

下列任何一種都算「進入下一階段」——開發者用哪種 AI 就認哪種：

- **指令**：`/dflow:next`
- **口語（英文）**：OK / yes / continue / go ahead / sounds good / proceed
- **口語（中文）**：好 / 對 / 繼續 / 可以 / 沒問題
- **隱含**：開發者直接提供下一階段需要的資訊
  （例如 AI 問「哪個 Aggregate？」開發者直接回答 Aggregate 名稱 → 視為隱含確認）

隱含確認規則很重要——不要把每個轉換都搞成儀式，讓開發者每句話前都要說「好」。

### `/dflow:status` 回應格式

開發者打 `/dflow:status` 或用 NL 詢問（「現在到哪了？」「目前狀態？」）時，用這個格式回應：

```
目前流程：new-feature-flow
目前步驟：Step 3 — 領域概念發掘

已完成：
- [x] Step 1：需求理解 — 需求已理解
- [x] Step 2：識別 BC — 歸入 Expense context

進行中：
- [ ] Step 3：領域概念發掘 — 識別 Aggregate / VO / Events

剩餘：
- [ ] Step 4：撰寫規格
- [ ] Step 5：規劃實作
- [ ] Step 6：建立 Git 分支
- [ ] Step 7：實作
- [ ] Step 8：完成
```

如果沒有流程進行中，回應：「目前沒有進行中的流程。可以打 `/dflow:new-feature`、`/dflow:modify-existing`、`/dflow:bug-fix` 或 `/dflow:pr-review` 啟動。」

---

## 核心原則

1. **先規格、後程式碼** — 沒有至少一份輕量規格，就不開始實作
2. **Domain 是核心** — 業務邏輯只存在於 Domain 層
3. **統一語言** — 所有程式碼使用 `specs/domain/glossary.md` 中的術語
4. **一次交易一個 Aggregate** — 單一操作只修改一個 Aggregate
5. **依賴反轉** — Domain 定義介面，Infrastructure 實作
6. **務實不教條** — 流程嚴謹度與影響程度成正比

---

## 流程嚴謹度對照表

| 變更類型 | 規格等級 | DDD 建模深度 | 技術債記錄 |
|---|---|---|---|
| 新 Aggregate / BC | 完整規格 | 完整：Aggregate 設計 + Events + Context Map | — |
| 新功能（現有 BC 內） | 完整規格 | 標準：確認 Aggregate 歸屬 + 新 VO/Event | 視情況 |
| 業務規則變更 | 標準規格 | 標準：更新 rules.md + 檢查 invariants | 是 |
| Bug 修復（邏輯錯誤） | 輕量規格 | 輕量：確認修在正確的層 | 有發現就記 |
| 純 UI / API 合約 | 輕量規格 | 跳過 | 跳過 |
| 基礎設施變更 | 跳過 | 跳過 | 評估 |

**DDD 建模深度定義：**
- **完整** — 用 `templates/aggregate-design.md`、更新 `context-map.md`、定義 `events.md`
- **標準** — 確認 Aggregate 歸屬、更新 `models.md` 和 `rules.md`
- **輕量** — 只確認修改在正確的架構層
- **跳過** — 不涉及 domain logic

---

## 專案目錄結構

```
ExpenseTracker/
├── CLAUDE.md
├── specs/
│   ├── _overview.md
│   ├── _conventions.md
│   ├── domain/
│   │   ├── glossary.md                 # 統一語言術語表
│   │   ├── context-map.md              # Bounded Context 關係圖
│   │   └── {bounded-context}/
│   │       ├── context.md              # Context 邊界與職責
│   │       ├── models.md               # Aggregates, Entities, VOs
│   │       ├── rules.md                 # 業務規則索引（BR-ID + 一行摘要）
│   │       ├── behavior.md             # 行為規格彙整（Given/When/Then）
│   │       └── events.md               # Domain Events 目錄
│   ├── features/
│   │   ├── active/                     # 目前開發中
│   │   ├── completed/                  # 已完成（文件歸檔）
│   │   └── backlog/                    # 待處理
│   └── architecture/
│       ├── decisions/                  # 架構決策記錄（ADR）
│       └── tech-debt.md
│
├── src/
│   ├── ExpenseTracker.Domain/          # 領域層 — 零依賴
│   │   ├── Common/
│   │   │   ├── Entity.cs               # 基礎 Entity（含 ID）
│   │   │   ├── AggregateRoot.cs        # 基礎（含 Domain Events）
│   │   │   ├── ValueObject.cs          # 基礎（值相等性）
│   │   │   └── IDomainEvent.cs
│   │   ├── {BoundedContext}/
│   │   │   ├── Entities/
│   │   │   ├── ValueObjects/
│   │   │   ├── Events/
│   │   │   ├── Services/
│   │   │   ├── Specifications/
│   │   │   └── Interfaces/            # IXxxRepository 等
│   │   └── SharedKernel/              # 跨 Context 共用型別
│   │
│   ├── ExpenseTracker.Application/     # 應用層
│   │   ├── Common/
│   │   │   ├── Interfaces/            # IUnitOfWork 等
│   │   │   └── Behaviors/            # 驗證、日誌 pipeline
│   │   └── {BoundedContext}/
│   │       ├── Commands/              # 寫操作
│   │       ├── Queries/               # 讀操作
│   │       ├── DTOs/
│   │       └── EventHandlers/
│   │
│   ├── ExpenseTracker.Infrastructure/  # 基礎設施層
│   │   ├── Persistence/
│   │   │   ├── AppDbContext.cs
│   │   │   ├── Configurations/        # EF Core Fluent API 設定
│   │   │   └── Repositories/
│   │   ├── ExternalServices/
│   │   └── DependencyInjection.cs
│   │
│   └── ExpenseTracker.WebAPI/          # 展示層
│       ├── Controllers/
│       ├── Middleware/
│       └── Program.cs
│
└── tests/
    ├── Domain.UnitTests/
    ├── Application.UnitTests/
    └── Integration.Tests/
```

---

## 各層規範（不可妥協）

### 領域層（Domain）
- 零 NuGet 套件（除了 MediatR.Contracts 的 IDomainEvent，如果使用的話）
- 不使用 `[Table]`、`[Column]`、`[JsonProperty]` 或任何 ORM/序列化屬性
- 不使用 `DbContext`、`IConfiguration`、`HttpClient`
- 不使用 `async/await`（領域邏輯是同步的；非同步屬於 Application/Infrastructure）
- Entity 使用 private setter；狀態變更透過明確的方法
- Aggregate 透過 `DomainEvents` 集合暴露領域事件
- Value Object 基於屬性實作相等性

### 應用層（Application）
- 編排領域操作；不包含業務邏輯
- Commands/Queries 使用 CQRS 模式
- 只依賴 Domain 層的介面
- 負責 DTO 與 Domain 物件之間的映射
- 處理橫切關注點：驗證、授權、日誌

### 基礎設施層（Infrastructure）
- 實作 Domain 的介面（Repository、外部服務）
- EF Core 設定放在這裡，不放在 Domain
- 不包含業務邏輯 — 純技術實作

### 展示層（Presentation）
- 精簡的 Controller：解析 HTTP → 呼叫 Application → 回傳回應
- 不包含業務邏輯，不直接操作 Domain 物件
- 負責 API 合約與 Application DTO 之間的映射

---

## 行為規格 Source of Truth（rules.md + behavior.md）

每個 Bounded Context 有兩份互補的檔案，合起來描述系統目前的行為：

- **`rules.md`** — 宣告式索引：列出每個 BR-ID 和一行摘要。快速查找、方便掃讀。
- **`behavior.md`** — 情境層細節：每個 BR-ID 的完整 Given/When/Then 情境（含 Aggregate 狀態轉換和 Domain Events）。這是「系統現在到底在做什麼」的彙整 source of truth。

`specs/features/completed/` 是歷史歸檔（個別變更記錄）。`behavior.md` 是**合併後的現狀**——功能完成時，AI 把 spec 裡的情境合併進 `behavior.md`；行為被修改時，AI 更新對應段落反映新行為（git 保留歷史）。模板見 `templates/behavior.md`。

這相當於 OpenSpec 的 `specs/` 目錄作為系統行為 source of truth 的角色，但按 Bounded Context 而非 capability 組織。

---

## 引導問題（按階段）

**Phase 標記約定**：`templates/feature-spec.md` 每個段落 heading 行尾有 HTML comment（例如 `<!-- 填寫時機：Phase 2 -->`）標示該段應在哪一階段填寫。標記對應下面的階段，`/dflow:status` 和完成流程 checklist 會用它追蹤進度。引導開發者時按階段順序填寫；不要在 Phase 3（撰寫規格）還沒定稿前就跳進 Phase 4（規劃實作）。模板最末的「實作任務」段由 AI 在 Phase 4 結束時產生——詳見 `references/new-feature-flow_tw.md` Step 5 / `references/modify-existing-flow_tw.md` Step 3。

### 第一階段：理解需求（做什麼、為什麼）
- 這要解決什麼問題？誰提出的需求？
- 從使用者的角度，預期行為是什麼？
- 有沒有相關的既有規格或領域文件？

### 第二階段：領域建模（DDD）
- 屬於哪個 Bounded Context？（檢查 `context-map.md`）
- 屬於哪個 Aggregate？還是需要新的 Aggregate？
- 這個 Aggregate 必須保護哪些不變條件？
- 有沒有可以抽取的 Value Objects？（Money, DateRange, Address…）
- 會產生 Domain Events 嗎？誰消費這些事件？
- 會跨 Aggregate/Context 邊界嗎？→ 需要整合策略

### 第三階段：撰寫規格
- 使用模板撰寫規格
- 定義 Given/When/Then（包含 Aggregate 狀態轉換）
- 記錄產生和消費的 Domain Events
- 找出 Aggregate 不變條件相關的邊界情況

### 第四階段：實作規劃（逐層）
- 領域層：Aggregate 設計、Value Objects、Domain Events
- 應用層：Command/Query、Handler、Validation
- 基礎設施層：Repository 實作、EF 設定
- 展示層：API 端點設計

### 第五階段：測試策略
- 領域單元測試：不變條件、業務規則、Value Object 相等性
- 應用層測試：Command/Query Handler 行為
- 整合測試：Repository、外部服務

---

## 術語表維護

與 WebForms 版本相同 — `specs/domain/glossary.md` 是業務術語的唯一事實來源。
格式：`| 術語 | 定義 | Bounded Context | 對應程式碼 |`

---

## 參考文件

| 文件 | 閱讀時機 |
|---|---|
| `references/new-feature-flow_tw.md` | 開發新功能時 |
| `references/modify-existing-flow_tw.md` | 修改既有功能時 |
| `references/ddd-modeling-guide_tw.md` | Domain Model 設計問題時 |
| `references/pr-review-checklist_tw.md` | Code Review 時 |
| `references/drift-verification_tw.md` | `/dflow:verify` — rules.md ↔ behavior.md 一致性檢查 |
| `references/git-flow-integration_tw.md` | 分支管理時 |

## 模板

| 模板 | 用途 |
|---|---|
| `templates/feature-spec.md` | 完整功能規格 |
| `templates/lightweight-spec.md` | Bug 修復/小變更的快速規格 |
| `templates/context-definition.md` | 新 Bounded Context 定義 |
| `templates/aggregate-design.md` | 新 Aggregate 設計工作表 |
| `templates/CLAUDE.md` | 放在專案根目錄的 CLAUDE.md |
