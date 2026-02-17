# DDD 建模指南

當開發者問「X 應該怎麼建模？」或正在設計領域結構時，使用本指南逐步引導 DDD 戰術模式。

## 建模對話的起點

從業務出發，不是從程式碼出發：

```
「我們先不管資料庫和類別。
告訴我：規則是什麼？什麼必須永遠為真？
什麼絕對不能發生？」
```

這些不變條件（Invariants）決定了整個模型的設計。

---

## 模式選擇流程圖

```
它是否有一個持續存在的 ID 來識別？
    │
    ├─ 是 → 它是否是保護一致性邊界的「老大」？
    │   ├─ 是 → AGGREGATE ROOT（聚合根）
    │   └─ 否 → ENTITY（實體，屬於某個 Aggregate 內部）
    │
    └─ 否 → 它是否完全由其屬性來定義？
        ├─ 是 → VALUE OBJECT（值物件）
        └─ 否 → 它是否代表一個跨多個 Aggregate 的操作？
            ├─ 是 → DOMAIN SERVICE（領域服務）
            └─ 否 → 重新審視——它大概還是上面其中一種
```

---

## Aggregate（聚合）設計

Aggregate 是 DDD 中最重要也最常被誤解的概念。

### 什麼是 Aggregate？

一組物件被當作一個整體來處理資料變更。Aggregate Root 是唯一的入口——外部程式碼不能繞過它直接修改內部的子實體或值物件。

### Aggregate 設計規則

1. **保護不變條件** — Aggregate 存在的理由就是強制執行跨內部多個物件的業務規則。

2. **一次交易一個 Aggregate** — 單一操作只應該修改一個 Aggregate。如果需要修改兩個 Aggregate，使用 Domain Events 實現最終一致性。

3. **只透過 ID 引用其他 Aggregate** — 永遠不要持有另一個 Aggregate 的直接物件引用。儲存它的 ID 就好。

4. **保持小巧** — 太大的 Aggregate 會造成並行問題。如果兩個使用者可以獨立修改不同部分，那些部分大概應該是不同的 Aggregate。

### 範例：費用報銷單 Aggregate

```csharp
// ExpenseReport 是 Aggregate Root
public class ExpenseReport : AggregateRoot
{
    private readonly List<ExpenseLineItem> _lineItems = new();

    public EmployeeId SubmittedBy { get; private set; }   // 透過 ID 引用
    public ReportPeriod Period { get; private set; }       // Value Object
    public Money TotalAmount => CalculateTotal();          // 衍生值
    public ReportStatus Status { get; private set; }       // Value Object（類似列舉）

    // 透過明確的方法改變狀態——不是 property setter
    public void AddLineItem(string description, Money amount, ExpenseCategory category)
    {
        // 強制不變條件
        if (Status != ReportStatus.Draft)
            throw new DomainException("無法在已提交的報銷單中新增項目。");

        if (_lineItems.Count >= 50)
            throw new DomainException("每份報銷單最多 50 個項目。");

        var lineItem = new ExpenseLineItem(description, amount, category);
        _lineItems.Add(lineItem);

        // 發出領域事件
        AddDomainEvent(new LineItemAddedEvent(Id, lineItem.Id, amount));
    }

    public void Submit()
    {
        if (Status != ReportStatus.Draft)
            throw new DomainException("只有草稿狀態的報銷單可以提交。");

        if (!_lineItems.Any())
            throw new DomainException("不能提交空白的報銷單。");

        Status = ReportStatus.Submitted;
        AddDomainEvent(new ExpenseReportSubmittedEvent(Id, SubmittedBy, TotalAmount));
    }
}
```

### 設計時要問的問題

設計新的 Aggregate 時：

1. **不變條件是什麼？** 哪些業務規則必須永遠為真？
2. **一致性邊界在哪裡？** 什麼必須原子性地更新？
3. **什麼可以獨立變更？** 可以獨立變更的部分應該是不同的 Aggregate。
4. **誰會修改它？** 有多少並行使用者？（影響 Aggregate 大小）
5. **它會產生什麼事件？** 系統的其他部分需要知道什麼？

---

## Value Object（值物件）

### 何時使用 Value Object

如果以下問題的答案全部是「是」，它就是 Value Object：
- 它是否由屬性來定義，而非由 ID？
- 一旦建立後是否不可變？
- 兩個具有相同屬性的實例是否可以被視為相等？

### 常見的 Value Objects

```csharp
// Money — 經典範例
public record Money(decimal Amount, Currency Currency)
{
    public static Money Zero(Currency currency) => new(0, currency);

    public Money Add(Money other)
    {
        if (Currency != other.Currency)
            throw new CurrencyMismatchException(Currency, other.Currency);
        return new Money(Amount + other.Amount, Currency);
    }

    public Money ConvertTo(Currency target, ExchangeRate rate)
    {
        return new Money(rate.Convert(Amount), target);
    }
}

// DateRange（日期範圍）
public record DateRange(DateOnly Start, DateOnly End)
{
    public DateRange
    {
        if (Start > End) throw new DomainException("開始日期必須在結束日期之前。");
    }

    public bool Contains(DateOnly date) => date >= Start && date <= End;
    public int Days => End.DayNumber - Start.DayNumber + 1;
}

// Currency（幣別——受限字串）
public record Currency
{
    public string Code { get; }
    public int DecimalPlaces { get; }

    public static readonly Currency TWD = new("TWD", 0);
    public static readonly Currency USD = new("USD", 2);
    public static readonly Currency JPY = new("JPY", 0);

    private Currency(string code, int decimalPlaces)
    {
        Code = code;
        DecimalPlaces = decimalPlaces;
    }

    public decimal Round(decimal amount) =>
        Math.Round(amount, DecimalPlaces, MidpointRounding.AwayFromZero);
}
```

### Value Object 設計問題

1. **它有行為嗎？** 好的 VO 有方法，不只是屬性。
2. **它有限制條件嗎？** 建構式應該拒絕無效狀態。
3. **它可以重複使用嗎？** `Money` 可以在很多 Aggregate 中使用。

---

## Domain Events（領域事件）

### 什麼是 Domain Events？

領域中發生的事情，而系統的其他部分需要知道。使用過去式命名：`ExpenseReportSubmitted`、`LineItemAdded`、`ReportApproved`。

### 何時使用 Domain Events

- 當一個 Aggregate 需要觸發另一個 Aggregate 的變更
- 當副作用（Email、通知、稽核紀錄）應該在領域動作之後發生
- 當不同的 Bounded Context 需要溝通

### Event 設計

```csharp
public record ExpenseReportSubmittedEvent(
    ExpenseReportId ReportId,
    EmployeeId SubmittedBy,
    Money TotalAmount
) : IDomainEvent;
```

### Event 流程

```
1. 呼叫 Aggregate 方法 → 狀態變更 → 事件加到 DomainEvents 清單
2. Repository 儲存 Aggregate
3. 儲存之後（在同一交易中或透過 outbox）：
   - 程序內處理器：更新讀取模型、觸發其他 command
   - 跨 Context：發布到訊息佇列
```

### Event 處理指導原則

- **同一 Bounded Context 內**：同步處理（同一交易，適合讀取模型更新）
- **跨 Bounded Context**：非同步處理（最終一致性）
- Event Handler 應該是冪等的（重複處理也安全）

---

## Specifications（規格模式）

用於屬於領域的複雜查詢邏輯：

```csharp
public class PendingApprovalSpec : Specification<ExpenseReport>
{
    private readonly EmployeeId _approverId;

    public PendingApprovalSpec(EmployeeId approverId)
    {
        _approverId = approverId;
    }

    public override Expression<Func<ExpenseReport, bool>> ToExpression()
    {
        return report =>
            report.Status == ReportStatus.Submitted &&
            report.ApproverId == _approverId;
    }
}
```

---

## Domain Services（領域服務）

當操作符合以下條件時使用 Domain Service：
- 涉及多個 Aggregate（對第二個 Aggregate 只有讀取權限）
- 需要外部資訊（透過介面）來做領域決策
- 不自然地屬於任何單一 Entity

```csharp
// Domain Service — 在 Domain 層中
public class ExpenseApprovalService
{
    private readonly IApprovalPolicyRepository _policyRepo;

    public ApprovalResult Evaluate(ExpenseReport report, ApprovalPolicy policy)
    {
        if (report.TotalAmount.Amount > policy.AutoApprovalLimit)
            return ApprovalResult.RequiresManagerApproval;

        if (policy.RestrictedCategories.Overlaps(report.Categories))
            return ApprovalResult.RequiresComplianceReview;

        return ApprovalResult.AutoApproved;
    }
}
```

---

## Bounded Context 之間的關係

記錄在 `specs/domain/context-map.md`：

| 關係 | 模式 | 範例 |
|---|---|---|
| Context A 呼叫 Context B | 客戶-供應商（Customer-Supplier） | Expense → HR（取得員工資訊） |
| Context 之間共享資料 | 共享核心（Shared Kernel） | Currency、Money 在 SharedKernel/ |
| Context A 翻譯 B 的語言 | 防腐層（Anti-Corruption Layer） | Expense → 外部會計系統 |
| 發送後不管 | Domain Events | Expense → Notification（報銷單已提交） |

---

## 常見錯誤檢查

1. **貧血領域模型（Anemic Domain Model）** — Entity 只有 getter/setter，所有邏輯在 Service 裡
   → 把行為移進 Entity/Aggregate 中

2. **Aggregate 太大** — Aggregate 載入整個物件圖
   → 拆成更小的 Aggregate，透過 ID 引用

3. **業務邏輯在 Application 層** — Command Handler 中有 if-else 規則
   → 移到 Domain（Entity 方法或 Domain Service）

4. **業務邏輯在 Infrastructure 層** — SQL 查詢或 EF 設定中有規則
   → Domain 定義「什麼」，Infrastructure 定義「怎麼做」

5. **直接跨 Aggregate 修改** — 一個 Command 修改兩個 Aggregate
   → 用 Domain Events 處理第二個 Aggregate
