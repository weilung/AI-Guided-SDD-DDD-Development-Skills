# 新功能開發流程 — ASP.NET Core

使用完整 DDD 和 Clean Architecture 新增功能的逐步引導。

## 步驟 1：需求理解

自然地詢問：
1. **這個功能是什麼？** 白話描述。
2. **誰需要它？** 利害關係人或使用者角色。
3. **為什麼現在要做？** 優先順序和緊急程度。

檢查既有資產：
- 搜尋 `specs/domain/` 尋找相關概念
- 搜尋 `specs/features/` 尋找相關功能
- 檢查 `specs/domain/glossary.md` 和 `context-map.md`

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

## 步驟 6：建立 Git 分支

```
分支：feature/{SPEC-ID}-{簡短描述}
範例：feature/EXP-001-submit-expense-report
```

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

## 步驟 8：完成

1. 更新規格狀態為 `completed`
2. 將規格移到 `completed/`
3. 確認術語表、models.md、rules.md、events.md 已更新
4. 如果新增了跨 Context 互動，確認 `context-map.md` 已更新
5. 確保測試涵蓋領域不變條件
