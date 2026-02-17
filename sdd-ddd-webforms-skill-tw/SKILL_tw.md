# SDD/DDD 工作流程守衛

## 概述

這是一個 AI 引導的開發流程規範，用於 ASP.NET WebForms 專案中導入 **規格驅動開發（SDD）** 和 **領域驅動設計（DDD）**，同時為未來遷移到 ASP.NET Core 做準備。

當開發者開始任何功能開發、Bug 修復、或程式碼修改時，AI 會自動介入引導，確保每一次變更都遵循「先寫規格、再寫程式」的流程。

---

## 為什麼要這樣做

這個專案目前的問題：業務邏輯散落在 Code-Behind 檔案裡、頁面處理器中有直接的 SQL 查詢、多個頁面之間有重複的計算邏輯。每一個沒有規格就開發的功能，都會讓未來遷移更困難；反之，每一份規格文件和每一個被抽離的領域概念，都會讓遷移更容易。

AI 的角色不是說教，而是在對的時機問對的問題，讓開發者在每次變更中自然產出三樣資產：

1. **規格文件** — 未來的需求文件
2. **Domain 層程式碼** — 可直接搬到 ASP.NET Core
3. **技術債記錄** — 遷移指南

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
    ├─「快速問一下…」/「X 是怎麼運作的？」
    │   └─ → 先查 specs/domain/ 裡的知識回答
    │       如果沒有文件 → 建議把答案記錄為領域知識
    │
    ├─「我在做 Code Review」/「看一下 PR」
    │   └─ → PR 審查檢查清單（詳見 references/pr-review-checklist_tw.md）
    │
    ├─「接下來要做什麼？」/ Sprint 規劃
    │   └─ → 檢視 specs/features/backlog/，依遷移價值建議優先順序
    │
    ├─「我要建分支」
    │   └─ → Git Flow 整合（詳見 references/git-flow-integration_tw.md）
    │       確認分支命名、確認 spec 存在才開始寫程式
    │
    └─ 其他和程式碼相關的事
        └─ → 評估：這會動到業務邏輯嗎？
            是 → 引導走上面對應的流程
            否 → 直接協助，不需要額外流程
```

---

## 核心原則

1. **先規格、後程式碼** — 沒有至少一份輕量規格，就不開始實作
2. **領域抽離** — 業務邏輯屬於 `src/Domain/`，不屬於 Code-Behind
3. **統一語言** — 一致使用 `specs/domain/glossary.md` 中定義的術語
4. **遷移意識** — 每個決策都要考慮未來 ASP.NET Core 遷移
5. **務實不教條** — 緊急修復不需要 50 行的規格。流程的嚴謹度應該與影響程度成正比

---

## 流程嚴謹度對照表

不是所有變更都需要完整流程。根據影響程度調整：

| 變更類型 | 規格等級 | 領域抽離 | 技術債記錄 |
|---|---|---|---|
| 新功能 | 完整規格 | 必須 | 視情況 |
| 業務規則變更 | 標準規格 | 必須 | 是 |
| Bug 修復（邏輯錯誤） | 輕量規格 | 評估 | 有發現就記 |
| 純 UI 變更 | 跳過 | 跳過 | 跳過 |
| 設定/環境變更 | 跳過 | 跳過 | 跳過 |
| 效能修復 | 輕量規格 | 評估 | 是 |

**輕量規格** = 問題描述 + 預期行為 + 1-2 個 Given/When/Then。可以直接寫在註解中，或在 `specs/features/active/` 建一個簡短條目。

---

## 專案目錄結構

```
WebFormsProject/
├── CLAUDE.md                         # AI 工作流程規則
├── specs/
│   ├── _overview.md                  # 系統現況與遷移策略
│   ├── _conventions.md               # 規格撰寫慣例與模板
│   ├── domain/                       # 領域知識（為 DDD 做準備）
│   │   ├── glossary.md               # 統一語言術語表
│   │   └── {bounded-context}/        # 例如：expense/, hr/, leave/
│   │       ├── context.md            # Context 邊界與職責
│   │       ├── models.md             # Entity、VO、Aggregate 定義
│   │       └── rules.md              # 業務規則目錄
│   ├── features/
│   │   ├── active/                   # 目前開發中
│   │   ├── completed/                # 已完成（文件歸檔）
│   │   └── backlog/                  # 待處理
│   └── migration/
│       └── tech-debt.md              # 新系統中需要修復的問題
├── src/
│   ├── Domain/                       # 抽離的領域邏輯（純 C#）
│   │   ├── {BoundedContext}/         # 例如：Expense/
│   │   │   ├── Entities/
│   │   │   ├── ValueObjects/
│   │   │   ├── Services/
│   │   │   └── Interfaces/           # Repository 介面等
│   │   └── SharedKernel/             # 跨 Context 共用概念
│   └── Pages/                        # 既有 WebForms 頁面
```

---

## 引導問題（按階段）

開發者開始工作時，依序引導這些問題。不要一次全部丟出來——隨著對話自然推進。

### 第一階段：理解需求（做什麼、為什麼）
- 這要解決什麼問題？誰提出的需求？
- 從使用者的角度，預期行為是什麼？
- 有沒有相關的既有規格或領域文件？

### 第二階段：領域分析（放在哪裡）
- 這屬於哪個 Bounded Context？（檢查 `specs/domain/`）
- 涉及哪些領域概念？（Entity、Value Object、Service）
- 有沒有新術語？→ 更新 `glossary.md`
- 有沒有新增或變更的業務規則？→ 記錄在 `rules.md`

### 第三階段：撰寫規格（先文件再寫程式）
- 使用模板撰寫規格（見 `templates/feature-spec.md`）
- 定義關鍵行為的 Given/When/Then 情境
- 找出邊界情況和業務規則之間的互動

### 第四階段：實作規劃（怎麼做）
- 業務邏輯能不能放在 `src/Domain/` 裡當純 C#？
- 需要哪些介面？（Repository、外部服務）
- Code-Behind 能多薄？（理想：解析輸入 → 呼叫 Domain → 顯示結果）

### 第五階段：技術債察覺（發現了什麼）
- 有沒有發現散落的業務邏輯？→ 記錄到 `tech-debt.md`
- 有沒有重複的計算？→ 記錄
- Code-Behind 裡有直接 SQL？→ 記錄
- Magic number 或未文件化的狀態碼？→ 記錄並加入術語表

---

## Domain 層規範（`src/Domain/`）

`src/Domain/` 中的程式碼必須遵守以下限制。這些是不可妥協的，因為這些程式碼將直接遷移到 ASP.NET Core：

- **不可引用 System.Web** — 零 WebForms 依賴
- **不可直接存取資料庫** — 使用介面（Repository pattern）
- **不可使用 HttpContext、Session、ViewState** — 純業務邏輯
- **不可有 UI 相關邏輯** — 不做顯示格式化、不引用 Page
- 所有公開行為都應該能在沒有任何 Web 基礎設施的情況下測試

---

## 術語表維護

`specs/domain/glossary.md` 是業務術語的唯一事實來源。

遇到業務術語時：
1. 先檢查術語表中是否已存在
2. 如果不存在，向開發者確認定義
3. 立即新增——不要拖延
4. 在規格和程式碼中一致使用術語表中的名稱（類別名、方法名、變數名）

格式：`| 術語 | 定義 | Bounded Context | 對應程式碼 |`

---

## 參考文件

需要詳細步驟時閱讀這些文件：

| 文件 | 閱讀時機 |
|---|---|
| `references/new-feature-flow_tw.md` | 開發者要新增功能時 |
| `references/modify-existing-flow_tw.md` | 開發者要修改既有功能時 |
| `references/pr-review-checklist_tw.md` | Code Review 或 PR 討論時 |
| `references/git-flow-integration_tw.md` | 建立分支、合併策略、發布流程時 |

## 模板

| 模板 | 用途 |
|---|---|
| `templates/feature-spec.md` | 完整功能規格 |
| `templates/lightweight-spec.md` | Bug 修復/小變更的快速規格 |
| `templates/context-definition.md` | 新 Bounded Context 定義 |
| `templates/CLAUDE.md` | 放在專案根目錄的 CLAUDE.md |
