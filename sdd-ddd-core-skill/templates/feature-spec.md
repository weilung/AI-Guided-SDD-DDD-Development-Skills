---
id: {CONTEXT}-{NUMBER}
title: 功能標題
status: draft | in-progress | completed
bounded-context: {ContextName}
created: {YYYY-MM-DD}
author: {developer-name}
branch: feature/{CONTEXT}-{NUMBER}-{short-description}
---

# {功能標題}

## 問題描述

> 用使用者的角度描述，避免技術用語。

## 領域概念

涉及的 Domain 概念（引用 `specs/domain/{context}/models.md`）：

| 概念 | 類型 | 說明 |
|---|---|---|
| {Name} | Aggregate Root / Entity / Value Object / Domain Service | 角色 |

更新檢查：
- [ ] `specs/domain/glossary.md` — 新術語
- [ ] `specs/domain/{context}/models.md` — 模型定義
- [ ] `specs/domain/{context}/events.md` — Domain Events

## 行為規格

### 主要成功情境

```gherkin
Scenario: {情境名稱}
  Given {Aggregate 的初始狀態}
  When {呼叫的 Aggregate 方法或 Command}
  Then {Aggregate 的新狀態}
  And {產生的 Domain Event}
```

### 替代情境

```gherkin
Scenario: {替代情境}
  Given {不同初始狀態}
  When {相同或不同操作}
  Then {不同結果}
```

## 業務規則

| 編號 | 規則 | 實作位置 |
|---|---|---|
| BR-01 | {規則描述} | Domain: {Entity/VO/Service} |
| BR-02 | {規則描述} | Domain: {Entity/VO/Service} |

## 邊界情況

| 編號 | 情況 | 預期處理方式 |
|---|---|---|
| EC-01 | {邊界描述} | {處理方式} |

## Domain Events

| Event | 觸發條件 | 處理者 | 同步/異步 |
|---|---|---|---|
| {EventName} | {何時觸發} | {Handler} | 同步/異步 |

## 實作計畫（逐層）

### Domain 層
> Aggregate 設計、Value Objects、Events、Interfaces

### Application 層
> Command/Query、Handler、Validator、DTO

### Infrastructure 層
> EF 設定、Repository 實作、外部服務

### Presentation 層
> API Endpoint 設計、Request/Response 模型

## 資料結構變更

| 資料表 | 欄位 | 變更類型 | 說明 |
|---|---|---|---|
| {Table} | {Column} | 新增/修改/刪除 | |

## 測試策略

### Domain 單元測試
- [ ] {不變條件測試}
- [ ] {業務規則測試}
- [ ] {Value Object 相等性測試}

### Application 測試
- [ ] {Command Handler 測試}
- [ ] {Query Handler 測試}

### 整合測試
- [ ] {Repository 測試}
