# Project: {系統名稱} — ASP.NET Core + DDD

**重要：所有開發工作都必須遵循本文件定義的流程。**

---

## 系統脈絡（What is this system?）

> 技術棧、架構、業務領域、目錄結構

### 背景

這是一個使用 Clean Architecture 和 Domain-Driven Design 的 ASP.NET Core 系統。
採用 SDD 流程，所有開發工作必須遵循本文件定義的流程。

### 架構（Clean Architecture）

```
Presentation → Application → Domain ← Infrastructure
```

依賴方向永遠朝內。Domain 層是核心，不依賴任何外部套件。

**各層職責**

| 層 | 職責 | 不可以做的事 |
|---|---|---|
| Domain | 業務規則、Aggregate、Value Object、Domain Event | 依賴外部套件、存取資料庫、處理 HTTP |
| Application | 編排領域操作、CQRS、驗證、DTO | 包含業務邏輯、直接存取資料庫 |
| Infrastructure | EF Core、外部 API、檔案存取 | 包含業務邏輯 |
| Presentation | HTTP 端點、Request/Response | 包含業務邏輯、直接操作 Domain 物件 |

### 目錄結構

```
specs/
├── domain/
│   ├── glossary.md
│   ├── context-map.md
│   └── {context}/
│       ├── context.md
│       ├── models.md
│       ├── rules.md
│       └── events.md          # Domain Events 目錄
├── features/
│   ├── active/
│   ├── completed/
│   └── backlog/
└── architecture/
    ├── decisions/              # ADR
    └── tech-debt.md

src/
├── {Project}.Domain/
├── {Project}.Application/
├── {Project}.Infrastructure/
└── {Project}.WebAPI/

tests/
├── Domain.UnitTests/
├── Application.UnitTests/
└── Integration.Tests/
```

---

## 開發流程（How do we work?）

> SDD 流程、Git 整合、Domain 層規範、AI 協作

### 核心原則
1. **Spec Before Code** — 沒有規格就不寫實作
2. **Domain at the Center** — 業務邏輯只存在於 Domain 層
3. **Ubiquitous Language** — 使用 `specs/domain/glossary.md` 中定義的術語
4. **One Aggregate per Transaction** — 單一操作只修改一個 Aggregate
5. **Dependency Inversion** — Domain 定義介面，Infrastructure 實作

### 新增功能
1. 建立功能規格 (`specs/features/active/`)
2. 設計 Aggregate（不變條件、狀態變更方法、Domain Events）
3. 實作順序：Domain → Application → Infrastructure → Presentation
4. 撰寫測試：Domain 單元測試 → Application 測試 → 整合測試

### Bug 修復
1. 建立輕量規格
2. 找到問題所在的層
3. 在正確的層修復

### Git 整合

> 本流程只規定 SDD 必要的最小 Git 耦合（feature branch per feature、
> `git mv`、commit 對應 SPEC-ID）。實際採用的分支策略（Git Flow /
> GitHub Flow / trunk-based / 單一 main）由專案決定，不在此強制。
> 若採用 Git Flow，可參考 `scaffolding/Git-原則-gitflow.md` 範本。

**分支命名**
```
feature/{SPEC-ID}-{short-description}    # 新功能（SDD 必須）
bugfix/{BUG-ID}-{short-description}      # Bug 修復（SDD 必須）
```

**Commit Message**
```
[SPEC-ID] 簡述變更
```

> `/dflow:bug-fix` 不綁定任何分支策略；採 Git Flow 的專案可選擇把緊急修復
> 放在 `hotfix/` 分支，但這是專案決策，Dflow 不代為規定。

### Domain 層規範

- ❌ 不可有任何 NuGet 套件依賴（純 .NET 類型）
- ❌ 不可有 ORM 屬性（[Table], [Column] 等）
- ❌ 不可有序列化屬性（[JsonProperty] 等）
- ❌ 不可有 DbContext、IConfiguration、HttpClient
- ✅ Entity 使用 private setter，透過方法改變狀態
- ✅ Value Object 使用 record，建構式驗證
- ✅ Aggregate Root 管理 DomainEvents 集合
- ✅ 其他 Aggregate 只透過 ID 引用

### AI 協作注意事項

- 開發者提出任何需求時，先引導建立 spec 和 Aggregate 設計
- 確認實作順序：Domain → Application → Infrastructure → Presentation
- 發現業務邏輯在錯誤的層時，指出並建議搬移
- 每次開發循環結束時，提醒更新術語表、models.md、events.md
- Review 時檢查 Domain 層的純淨度（零外部依賴）
