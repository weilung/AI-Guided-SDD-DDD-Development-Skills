# Project: {系統名稱} — ASP.NET WebForms

**重要：所有開發工作都必須遵循本文件定義的流程。**

---

## 系統脈絡（What is this system?）

> 技術棧、業務領域、目錄結構

### 背景

這是一個運行中的 ASP.NET WebForms 系統，目前持續新增與修改功能。
未來將遷移至 ASP.NET Core。目前採用 SDD 流程，同時為 DDD 做準備。

### 目錄結構

```
specs/
├── _overview.md              # 系統現況與遷移策略
├── _conventions.md           # 規格撰寫慣例與模板
├── domain/                   # 領域知識
│   ├── glossary.md           # 術語表（Ubiquitous Language）
│   └── {context}/            # 按 Bounded Context 分
│       ├── context.md        # Context 邊界與職責
│       ├── models.md         # 領域模型定義
│       └── rules.md          # 業務規則目錄
├── features/
│   ├── active/               # 進行中的功能規格
│   ├── completed/            # 已完成（文件歸檔）
│   └── backlog/              # 待處理
└── migration/
    └── tech-debt.md          # 技術債與遷移備忘

src/
├── Domain/                   # 抽離的領域邏輯（純 C#）
│   ├── {Context}/
│   │   ├── Entities/
│   │   ├── ValueObjects/
│   │   ├── Services/
│   │   └── Interfaces/
│   └── SharedKernel/
└── Pages/                    # 既有 WebForms 頁面
```

---

## 開發流程（How do we work?）

> SDD 流程、Git Flow、Domain 層規範、術語表、AI 協作

### 核心原則
1. **Spec Before Code** — 沒有規格就不寫實作
2. **Domain Extraction** — 業務邏輯屬於 `src/Domain/`，不屬於 Code-Behind
3. **Ubiquitous Language** — 使用 `specs/domain/glossary.md` 中定義的術語
4. **Migration Awareness** — 每個決策都要考慮未來 ASP.NET Core 遷移

### 新增功能
1. 先在 `specs/features/active/` 建立功能規格
2. 識別涉及的領域概念，更新 `specs/domain/` 下的對應文件
3. 盡可能將業務邏輯實作在 `src/Domain/` 中（純 C# class，不依賴 WebForms）
4. Code-Behind 僅負責 UI 綁定，呼叫 Domain 層處理邏輯
5. 撰寫測試驗證 Domain 層行為符合規格

### 修改既有功能
1. 先為該功能補寫規格（記錄現有行為 + 預期變更）
2. 如果該功能的邏輯還在 Code-Behind 中，評估是否值得先抽離到 Domain 層
3. 在 `specs/migration/tech-debt.md` 記錄發現的技術債

### Bug 修復
1. 建立輕量規格（問題 + 現有行為 + 預期行為 + 修復方式）
2. 修 Bug 時順便記錄發現的技術債

### Git Flow

**分支命名**
```
feature/{SPEC-ID}-{short-description}    # 新功能
bugfix/BUG-{NUMBER}-{short-description}  # Bug 修復
hotfix/HOT-{NUMBER}-{short-description}  # 緊急修復
release/{version}                         # 發布版本
```

**Commit Message**
```
[SPEC-ID] 簡述變更
```

**分支規則**
- **feature/** — 必須先有 spec 才能開始編碼
- **bugfix/** — 至少要有輕量 spec
- **hotfix/** — 先修，24 小時內補 spec

### Domain 層規範（`src/Domain/`）

此目錄中的程式碼必須遵守：
- ❌ 不可引用 `System.Web` 或任何 WebForms 命名空間
- ❌ 不可直接存取資料庫（使用 interface + Repository pattern）
- ❌ 不可使用 `HttpContext`、`Session`、`ViewState`
- ❌ 不可有 UI 相關邏輯（格式化顯示、Page 引用）
- ✅ 純 C# 類別，可直接搬到 ASP.NET Core 專案
- ✅ 所有公開行為都能在沒有 Web 基礎設施的情況下測試

### 術語表

所有業務術語必須使用 `specs/domain/glossary.md` 中定義的名稱。
遇到新術語時，先新增到術語表再使用。

### AI 協作注意事項

- 開發者提出任何功能需求時，先引導建立 spec
- 在回答 Domain 相關問題時，優先參考 `specs/domain/` 中的文件
- 發現 Code-Behind 中的業務邏輯時，建議抽離到 `src/Domain/`
- 每次開發循環結束時，提醒更新術語表和技術債記錄
- 建立分支前，確認命名符合規範且對應 spec 存在
