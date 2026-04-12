---
aggregate: {AggregateName}
bounded-context: {ContextName}
created: {YYYY-MM-DD}
---

# {AggregateName} Aggregate

## 用途

> 這個 Aggregate 代表什麼業務概念？一句話描述。

## 不變條件（Invariants）

> 必須永遠為真的規則。這是 Aggregate 存在的理由。

| 編號 | 不變條件 | 違反時的行為 |
|---|---|---|
| INV-01 | {必須為真的條件} | 拋出 DomainException |
| INV-02 | {必須為真的條件} | 拋出 DomainException |

## 結構

```
{AggregateName} (Aggregate Root)
├── {ChildEntity}       (Entity)
├── {ValueObject1}      (Value Object)
└── {ValueObject2}      (Value Object)
```

## Aggregate Root

| 屬性 | 類型 | 說明 |
|---|---|---|
| Id | {AggregateName}Id | 唯一識別 |
| {Property} | {Type} | {說明} |

## 狀態變更方法

| 方法 | 前置條件 | 後置條件 | 產生的 Event |
|---|---|---|---|
| {MethodName}() | {必須滿足什麼} | {狀態如何改變} | {EventName} |

## Domain Events

| Event | 何時觸發 | 誰消費 |
|---|---|---|
| {EventName} | {觸發條件} | {處理者或 Context} |

## 引用的其他 Aggregate（僅限 ID）

| Aggregate | 引用方式 | 用途 |
|---|---|---|
| {OtherAggregate} | {OtherAggregate}Id | {為什麼需要引用} |

## 設計決策

> 為什麼 Aggregate 的邊界劃在這裡？有沒有考慮過其他方案？
