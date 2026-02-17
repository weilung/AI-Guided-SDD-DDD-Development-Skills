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

## 決策樹：AI 根據開發者的意圖決定流程

```
開發者說了什麼
    │
    ├─「我要新增一個功能」
    │   └─ → 新功能流程（詳見 references/new-feature-flow_tw.md）
    │
    ├─「我要修改/修正既有的…」
    │   └─ → 修改既有功能流程（詳見 references/modify-existing-flow_tw.md）
    │
    ├─「我在設計 Domain Model」/「X 應該怎麼建模？」
    │   └─ → DDD 建模指南（詳見 references/ddd-modeling-guide_tw.md）
    │
    ├─「快速問一下…」/「X 是怎麼運作的？」
    │   └─ → 先查 specs/domain/ 裡的知識回答
    │
    ├─「我在做 Code Review」/「看一下 PR」
    │   └─ → PR 審查檢查清單（詳見 references/pr-review-checklist_tw.md）
    │
    ├─「我要建分支」
    │   └─ → Git Flow 整合（詳見 references/git-flow-integration_tw.md）
    │
    └─ 其他和程式碼相關的事
        └─ → 評估：這會動到業務邏輯嗎？
            是 → 引導走上面對應的流程
            否 → 直接協助，不需要額外流程
```

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

| 變更類型 | 規格等級 | DDD 建模 | 技術債記錄 |
|---|---|---|---|
| 新功能 | 完整規格 | 必須 | 視情況 |
| 新 Aggregate / BC | 完整規格 + Context Map | 必須 | — |
| 業務規則變更 | 標準規格 | 必須 | 是 |
| Bug 修復（邏輯錯誤） | 輕量規格 | 評估 | 有發現就記 |
| 純 UI / API 合約 | 輕量規格 | 跳過 | 跳過 |
| 基礎設施變更 | 跳過 | 跳過 | 評估 |

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
│   │       ├── rules.md                # 業務規則目錄
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

## 引導問題（按階段）

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
| `references/git-flow-integration_tw.md` | 分支管理時 |

## 模板

| 模板 | 用途 |
|---|---|
| `templates/feature-spec.md` | 完整功能規格 |
| `templates/lightweight-spec.md` | Bug 修復/小變更的快速規格 |
| `templates/context-definition.md` | 新 Bounded Context 定義 |
| `templates/aggregate-design.md` | 新 Aggregate 設計工作表 |
| `templates/CLAUDE.md` | 放在專案根目錄的 CLAUDE.md |
