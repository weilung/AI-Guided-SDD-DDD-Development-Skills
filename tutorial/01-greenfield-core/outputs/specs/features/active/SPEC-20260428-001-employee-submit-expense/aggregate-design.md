---
aggregate: ExpenseReport
bounded-context: Expense
created: 2026-04-28
---

# ExpenseReport Aggregate

## Purpose

代表員工針對一次出差或一段差旅期間提交的整份費用申報單。它是 Expense BC 的核心 Aggregate，承擔「申報資料一致性」與「狀態變遷」的責任：員工建立 Draft → 加入若干 ExpenseItem → 提交（Submit）→ （phase 2+）主管審核。

## Invariants

> 必須永遠為真的規則。這是 Aggregate 存在的理由。

| ID | Invariant | Behavior on Violation |
|---|---|---|
| INV-01 | ExpenseReport 提交時必須至少含 1 個 ExpenseItem | `Submit()` 拋 `DomainException("ExpenseReport must contain at least one ExpenseItem to submit.")` |
| INV-02 | 一旦 Status 變為 Submitted，Items 集合與其內容不可再被改變 | `AddItem()` / `RemoveItem()` / `ModifyItem()` 入口檢查 Status，非 Draft 時拋 `DomainException("Cannot modify a submitted ExpenseReport.")` |
| INV-03 | 每個 ExpenseItem 的 Money.Amount 必須 > 0 | `Money` record 建構式拋 `ArgumentException("Amount must be positive.")`；ExpenseItem 永遠不會帶著無效金額存在 |
| INV-04 | 同一 ExpenseReport 內，相同 ReceiptReference 不可重複出現 | `AddItem()` 偵測到 Items 已含相同 ReceiptReference 時拋 `DomainException("Receipt {refValue} already attached to this report.")` |
| INV-05 | 只有 Draft 狀態的 ExpenseReport 能被 Submit | `Submit()` 檢查 Status，非 Draft 時拋 `DomainException("Only Draft reports can be submitted, current status: {Status}.")` |

## Structure

```
ExpenseReport (Aggregate Root)
├── ExpenseReportId         (Value Object — Guid wrapper)
├── EmployeeId              (Value Object — 簡單的 string wrapper，跨 BC 引用)
├── Status                  (Enum — Draft / Submitted；MVP 只實作這兩態)
├── CreatedAt / SubmittedAt (DateTimeOffset)
└── _items: List<ExpenseItem>   (Entity collection — internal field; expose IReadOnlyCollection)
        ├── ExpenseItemId    (Value Object — Guid wrapper, 在 Report 內唯一)
        ├── Money            (Value Object — Amount + Currency)
        ├── ExpenseCategory  (Value Object — Code)
        ├── ReceiptReference (Value Object — Value)
        └── OccurredOn       (DateOnly)
```

## Aggregate Root

| Property | Type | Description |
|---|---|---|
| Id | ExpenseReportId | 唯一識別 |
| EmployeeId | EmployeeId | 提交者，跨 BC ID 引用（Identity BC，未建模） |
| Status | ExpenseReportStatus | Draft / Submitted（MVP 範圍） |
| Items | IReadOnlyCollection&lt;ExpenseItem&gt; | 對外唯讀；內部 `_items: List<ExpenseItem>` |
| CreatedAt | DateTimeOffset | 建立時戳 |
| SubmittedAt | DateTimeOffset? | Submit 成功時填入；Draft 時為 null |
| DomainEvents | IReadOnlyCollection&lt;IDomainEvent&gt; | 待發布事件集合（基底 AggregateRoot 提供） |

## State Transition Methods

| Method | Preconditions | Postconditions | Domain Event |
|---|---|---|---|
| `Create(employeeId)` (static factory) | employeeId 非空 | 回傳新 ExpenseReport，Status = Draft，Items 空 | _(無)_ |
| `AddItem(money, category, receiptRef, occurredOn)` | Status == Draft；money.Amount > 0；receiptRef 不在現有 Items 中 | 新 ExpenseItem 加入 _items | _(無)_ |
| `RemoveItem(itemId)` | Status == Draft；itemId 存在 | 對應 Item 從 _items 移除 | _(無)_ |
| `ModifyItem(itemId, ...)` | Status == Draft；itemId 存在 | Item 屬性更新 | _(無)_ |
| `Submit()` | Status == Draft；Items.Count >= 1 | Status = Submitted；SubmittedAt = now | **ExpenseReportSubmitted** (ReportId, EmployeeId, SubmittedAt, TotalAmount) |

## Domain Events

| Event | Trigger | Consumer |
|---|---|---|
| ExpenseReportSubmitted | `Submit()` 成功 | _(MVP 無 consumer；phase 2 Approval BC 將訂閱)_ |

## Referenced Aggregates (ID only)

| Aggregate | Reference Type | Purpose |
|---|---|---|
| Employee (Identity BC, 未建模) | EmployeeId | 標識提交者；MVP 不驗證存在性，假設前端傳入合法 ID |

## Design Decisions

**為什麼 ExpenseItem 是 Entity 而非 Value Object？**
最初考慮過把 ExpenseItem 設成 VO（不可變、用整體值比較相等性）。但 review 場景時發現「員工填錯金額需要修改某筆 Item」是常見動作——VO 的話每次修改都要 remove + re-add，並且失去「這就是同一筆 Item」的識別性。所以選 Entity（有穩定的 ExpenseItemId），允許 ModifyItem(...) 直接改屬性。Trade-off：要多承擔 identity 管理，但業務語義更貼近真實。

**為什麼 Aggregate 邊界劃在「一份 ExpenseReport」而不是「員工的所有 Reports」？**
員工的多份 Reports 之間沒有需要原子一致性的不變條件——A Report 提交不影響 B Report 狀態。把它們塞進同一 Aggregate 會讓「載入員工所有 Report」變成載入超大物件圖，而且每次提交都要鎖定全部。所以以「一份 Report」為邊界，員工層級的查詢交給 read model（phase 2 才會出現）。

**為什麼 Money 是 VO，而不是 primitive decimal？**
金額永遠帶幣別語義；用 primitive 早晚會出現「忘記轉換幣別 / 不同幣別直接相加」的 bug。MVP 雖然只用 TWD，但用 VO 把 Currency 一併綁定，未來支援多幣別不需要改 schema。建構式驗證 Amount > 0 也讓 INV-03 在型別層級就被守住。

**為什麼 ReceiptReference 是 VO 而不是字串？**
語意化型別。AddItem 簽名收 `ReceiptReference` 而非 `string`，呼叫端就不會誤把 ExpenseItemId 之類的字串塞進來。建構式可加格式驗證（如「不可空白、長度 <= 64」），把驗證集中在一處。
