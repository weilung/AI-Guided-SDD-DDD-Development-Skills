---
id: {CONTEXT}-{NUMBER}
title: Feature title
status: draft | in-progress | completed
bounded-context: {ContextName}
created: {YYYY-MM-DD}
author: {developer-name}
branch: feature/{CONTEXT}-{NUMBER}-{short-description}
---

# {Feature Title}

## 問題描述

這個功能要解決什麼問題？誰需要它？

> 用使用者的角度描述，避免技術用語。

## 領域概念

涉及的 Domain 概念（引用 `specs/domain/{context}/models.md`）：

| 概念 | 類型 | 說明 |
|---|---|---|
| {ConceptName} | Entity / Value Object / Domain Service | 簡述角色 |

如有新概念，先更新：
- [ ] `specs/domain/glossary.md` — 新增術語
- [ ] `specs/domain/{context}/models.md` — 新增模型定義

## 行為規格

### 主要成功情境

```gherkin
Scenario: {情境名稱}
  Given {初始狀態}
  When {使用者操作}
  Then {預期結果}
```

### 替代情境

```gherkin
Scenario: {替代情境}
  Given {不同初始狀態}
  When {使用者操作}
  Then {不同的預期結果}
```

## 業務規則

| 編號 | 規則 | 備註 |
|---|---|---|
| BR-01 | {規則描述} | |
| BR-02 | {規則描述} | |

## 邊界情況

| 編號 | 情況 | 預期處理方式 |
|---|---|---|
| EC-01 | {邊界描述} | {處理方式} |
| EC-02 | {邊界描述} | {處理方式} |

## 實作備註

### 目前 WebForms 實作方式

> 在現有架構下如何實作？哪些 Code-Behind 會被修改？

### Domain 層設計

> 哪些邏輯放到 `src/Domain/{Context}/`？需要哪些 interface？

```csharp
// 關鍵 Domain 類別草稿
```

### Code-Behind 保持精簡

> Code-Behind 只負責：解析 UI 輸入 → 呼叫 Domain 層 → 顯示結果

### 未來 ASP.NET Core 遷移考量

> 遷移時需要注意的事項，或者現在的設計如何幫助未來遷移。

## 資料結構變更

> 涉及的資料表與欄位變更（如有）

| 資料表 | 欄位 | 變更類型 | 說明 |
|---|---|---|---|
| {Table} | {Column} | 新增/修改/刪除 | |

## 測試策略

> Domain 層的單元測試應驗證哪些行為？

- [ ] {測試案例 1}
- [ ] {測試案例 2}
