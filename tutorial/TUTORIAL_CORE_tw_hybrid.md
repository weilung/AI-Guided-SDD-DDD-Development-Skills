# SDD/DDD Workflow Skill 使用教學 — ASP.NET Core 版（Hybrid 設計｜P001 實作後）

> 本文件展示 **P001 Hybrid 設計實作後** 的使用體驗。
> 核心差異：指令化為主入口、auto-trigger 作為安全網、流程狀態完全透明。
> 每個情境從開發者的一句話（或一個指令）開始，完整呈現 AI 引導的對話過程和最終產出物。

---

## 先看這張表：原版 vs 新版的差別

| 面向 | 原版（純 auto-trigger） | 新版（Hybrid） |
|---|---|---|
| **入口** | 只靠自然語言被動觸發 | 可用 7 個 slash command 明確叫用 |
| **判斷流程** | AI 默默決定 | AI 判斷後**停下來讓你背書** |
| **進度感** | 不透明 | 隨時 `/status` 可查；phase gate 告知 |
| **離開流程** | 不明確 | `/cancel` 明確退出 |
| **多 Aggregate 設計** | 流程中互相打斷 | 可 `/cancel` → 開新討論 → 回來 `/new-feature` 接續 |
| **失敗模式** | 沒觸發、觸發錯、不知道在哪 | 這三種都被消除 |

---

## 指令速查

| 指令 | 用途 |
|---|---|
| `/new-feature` | 進入新功能流程（含 Aggregate 設計引導）|
| `/modify-existing` | 進入修改現有功能流程 |
| `/bug-fix` | 進入 Bug 修復流程 |
| `/pr-review` | 進入 PR 審查流程（逐層檢查 Clean Architecture 合規）|
| `/status` | 查看目前流程、所在 step、已完成與待完成項 |
| `/next` | 確認進入下一階段 |
| `/cancel` | 中止目前流程，回到自由對話 |

**自然語言可替代 `/next`**：「好」、「對」、「繼續」、「可以」、「OK」、「沒問題」，或直接回答下一階段需要的問題（隱性確認）。

**DDD 建模諮詢**：用自然語言描述問題即可（例如「ApprovalTask 該怎麼建模？」），Skill 會判斷並告知它準備用 `references/ddd-modeling-guide.md` 來回答，請你確認是否繼續。

---

## 目錄

- [事前準備：建立專案與安裝 Skill](#事前準備建立專案與安裝-skill)
- [情境一：修 Bug — Value Object 驗證漏洞（`/bug-fix`）](#情境一修-bug--value-object-驗證漏洞bug-fix)
- [情境二：新功能 — 費用報銷單提交與審批（`/new-feature` 含 Aggregate 設計 gate）](#情境二新功能--費用報銷單提交與審批new-feature-含-aggregate-設計-gate)
- [情境三：領域建模 — 該怎麼設計這個 Aggregate？（自然語言諮詢）](#情境三領域建模--該怎麼設計這個-aggregateddd-自然語言諮詢)
- [情境四：PR Review — 檢查 Clean Architecture 合規性（`/pr-review`）](#情境四pr-review--檢查-clean-architecture-合規性pr-review)
- [Skill 觸發速查表（更新版）](#skill-觸發速查表更新版)

---

## 事前準備：建立專案與安裝 Skill

### 0. 先讀 DDD 觀念指引（10-15 分鐘）

如果你的團隊對 DDD 還不熟，建議每位開發者先讀一次
**[給資深工程師的 DDD 觀念翻轉指南](DDD_MINDSET_SHIFT_tw.md)**。

### 1. 放置 Skill 檔案

將 `sdd-ddd-core/` 資料夾放到你的專案根目錄：

```
ExpenseTracker/
├── sdd-ddd-core/              ← Skill 檔案
│   ├── SKILL.md
│   ├── references/
│   └── templates/
├── src/
├── tests/
├── specs/                     ← 第一次使用時建立
└── CLAUDE.md                  ← 第一次使用時建立
```

### 2. 建立 Clean Architecture 專案結構

第一次使用時，讓 Claude Code 引導你建立完整結構：

```
❯ claude

你：請讀取 sdd-ddd-core/SKILL.md 和所有 references/、templates/ 檔案。
   我要建立一個 ASP.NET Core 專案：費用報銷系統（ExpenseTracker）。
   請引導我建立 Clean Architecture 的方案結構和 specs 目錄。
```

Claude Code 會建立以下結構：

```
ExpenseTracker/
├── CLAUDE.md
├── specs/
│   ├── _overview.md
│   ├── _conventions.md
│   ├── domain/
│   │   ├── glossary.md
│   │   ├── context-map.md
│   │   └── expense/
│   │       ├── context.md
│   │       ├── models.md
│   │       ├── rules.md
│   │       └── events.md
│   ├── features/
│   │   ├── active/
│   │   ├── completed/
│   │   └── backlog/
│   └── architecture/
│       ├── decisions/
│       └── tech-debt.md
├── src/
│   ├── ExpenseTracker.Domain/
│   ├── ExpenseTracker.Application/
│   ├── ExpenseTracker.Infrastructure/
│   └── ExpenseTracker.WebAPI/
└── tests/
    ├── Domain.UnitTests/
    ├── Application.UnitTests/
    └── Integration.Tests/
```

---

## 情境一：修 Bug — Value Object 驗證漏洞（`/bug-fix`）

> **觸發的 Skill 檔案**：`modify-existing-flow.md`、`lightweight-spec.md`
>
> **背景**：上線後發現可以建立金額為負數的費用項目，資料庫裡出現了 -500 TWD 的報銷記錄。
>
> **📌 對比原版**：原版靠自然語言 + AI 默默判斷為 Domain 層驗證問題；新版用 `/bug-fix` 明確進入，且 AI 會先告知流程結構。

### 開發者用指令明確進入

```
❯ claude

你：/bug-fix 費用金額可以輸入負數，資料庫出現 -500 的報銷記錄。
```

### Claude Code 告知流程結構並開始

```
Claude：收到，進入 bug-fix 流程。

       ┌─── Bug Fix Flow ───┐
       │ Step 1: 評估變更 ← 現在這裡
       │ Step 2: 捕捉現有行為
       │ Step 3: 分析根因
       │ Step 4: 實作修復
       │ --- Phase gate ---
       │ Step 5: 更新文件與歸檔
       └────────────────────┘

       這是一個 Domain 層的驗證問題——Aggregate 的不變條件沒有被正確保護。
       先確認：負數金額是在哪裡被接受的？是建立 Money 的時候，
       還是 ExpenseReport.AddLineItem() 的時候？
```

```
你：看起來 Money 的建構式沒有檢查，直接接受任何 decimal。
```

```
Claude：了解。這是典型的 Value Object 驗證漏洞——
       Value Object 應該在建構時就拒絕無效狀態。

       Step 1 完成，進入 Step 2：捕捉現有行為。讓我檢查相關文件...

       > 讀取 specs/domain/expense/models.md
       > 讀取 specs/domain/expense/rules.md

       models.md 中有定義 Money，但沒有記錄金額約束。
       rules.md 也沒有「金額必須為正數」這條規則。

       > 建立 specs/features/active/BUG-015-negative-money-validation.md
```

```markdown
---
id: BUG-015
title: Money Value Object 允許負數金額
status: in-progress
bounded-context: Expense
created: 2025-03-15
branch: bugfix/BUG-015-negative-money-validation
---

# Money Value Object 允許負數金額

## 問題

Money Value Object 的建構式沒有驗證金額，
允許建立 Money(-500, Currency.TWD) 這樣的無效物件。

## 現有行為

​```gherkin
Given 一個 decimal 值 -500
When 用 new Money(-500, Currency.TWD) 建立 Money
Then 建立成功，不會拋出任何錯誤
​```

## 預期行為

​```gherkin
Given 一個 decimal 值 -500
When 用 new Money(-500, Currency.TWD) 建立 Money
Then 拋出 DomainException「金額不可為負數」
​```

## 根因

Value Object 建構式缺少驗證——違反了「建構時就拒絕無效狀態」的 DDD 原則。

## 修復方式

在 Money record 的建構式加入驗證。
同時需要確認：零元是否允許？（例如免費項目的報銷）
```

```
Claude：有一個問題需要你確認：金額為 0 的情況允許嗎？
       比如有些公司活動的免費項目也需要記錄。
```

```
你：不允許，0 元沒有意義，不需要報銷。
```

```
Claude：好，Step 2、3 完成（現有行為已記錄、根因已釐清），進入 Step 4：實作修復。

       修復在 Domain 層。這是正確的層——金額約束是業務規則，屬於 Domain。

       > 修改 src/ExpenseTracker.Domain/Expense/ValueObjects/Money.cs
```

```csharp
// 修復前
public record Money(decimal Amount, Currency Currency);

// 修復後
public record Money
{
    public decimal Amount { get; }
    public Currency Currency { get; }

    public Money(decimal amount, Currency currency)
    {
        if (amount <= 0)
            throw new DomainException("金額必須為正數");

        Amount = currency.Round(amount);
        Currency = currency;
    }

    // ... 其他方法不變
}
```

```
Claude：注意幾件事：
       1. 改用建構式而非 record 的 positional 語法，因為需要驗證邏輯
       2. 建構時同時做四捨五入，確保 Money 一建立就是合法狀態
       3. 拋出 DomainException，不是 ArgumentException——這是領域規則

       接下來寫測試：

       > 建立 tests/Domain.UnitTests/Expense/MoneyTests.cs
```

```csharp
public class MoneyTests
{
    [Fact]
    public void Should_reject_negative_amount()
    {
        var act = () => new Money(-500, Currency.TWD);
        act.Should().Throw<DomainException>()
            .WithMessage("*正數*");
    }

    [Fact]
    public void Should_reject_zero_amount()
    {
        var act = () => new Money(0, Currency.TWD);
        act.Should().Throw<DomainException>()
            .WithMessage("*正數*");
    }

    [Fact]
    public void Should_accept_positive_amount()
    {
        var money = new Money(100.5m, Currency.TWD);
        money.Amount.Should().Be(101); // 四捨五入到整數
    }
}
```

### Phase gate：實作完成 → 文件更新

```
Claude：Step 4 完成（Money 修復 + 測試通過）。

       --- Phase gate: Step 4 → Step 5 ---

       即將進入 Step 5：更新文件與歸檔。要繼續嗎？
       → 回覆「好」/`/next` 繼續
       → 或 `/cancel` 暫停（例如想先把 PR 送出 review）
```

```
你：好
```

```
Claude：✅ specs/domain/expense/rules.md — 新增 BR-M01：金額必須為正數
       ✅ specs/domain/expense/models.md — 更新 Money 定義，加入約束
       ✅ specs/features/active/BUG-015 狀態改為 completed，移到 completed/
       ✅ Domain 專案 .csproj 確認：零 NuGet 依賴 ✅

       Bug-fix 流程完成。
```

### 這個情境的產出物

| 產出物 | 路徑 |
|---|---|
| 輕量規格 | `specs/features/completed/BUG-015-negative-money-validation.md` |
| Domain 修復 | `src/ExpenseTracker.Domain/Expense/ValueObjects/Money.cs` |
| 單元測試 | `tests/Domain.UnitTests/Expense/MoneyTests.cs` |
| 規則文件更新 | `specs/domain/expense/rules.md`（+1 條） |
| 模型文件更新 | `specs/domain/expense/models.md`（更新 Money 定義） |

### 重點回顧

這個情境的關鍵學習是：**Value Object 必須在建構時就保證合法狀態**。這是 DDD 的核心原則之一——你永遠不應該拿到一個「無效的 Money」物件。

P001 Hybrid 設計帶來的改變：
- `/bug-fix` 讓入口明確，不用靠 AI 猜測是否為 bug。
- Step 內轉換只告知，phase gate 才停下來確認——節奏剛好。

> 💡 這就是 [DDD 觀念指引](DDD_MINDSET_SHIFT_tw.md) 觀念三所講的「Value Object 不只是 DTO」——它在建構時驗證、有行為、不可變。

---

## 情境二：新功能 — 費用報銷單提交與審批（`/new-feature` 含 Aggregate 設計 gate）

> **觸發的 Skill 檔案**：`new-feature-flow.md`、`feature-spec.md`、`ddd-modeling-guide.md`、`aggregate-design.md`
>
> **背景**：實作費用報銷系統的核心功能：員工建立報銷單、新增費用項目、提交審批。
>
> **📌 對比原版**：原版 AI 從「好的，這是核心功能」開始直接問問題；新版 AI 會告知完整流程結構，並在每個主要里程碑（含 Aggregate 設計完成）設 phase gate。

### 開發者用指令明確進入

```
❯ claude

你：/new-feature 費用報銷單的提交功能。員工填好報銷單後，送出給主管審核。
```

### Claude Code 告知 Core 版的完整流程

```
Claude：進入 new-feature-flow（Core 版，含 Aggregate 設計引導）。

       ┌─── New Feature Flow（Core 版）───┐
       │ Step 1: 需求理解 ← 現在這裡
       │ Step 2: Bounded Context / Aggregate 識別
       │ Step 3: Aggregate 設計（填 aggregate-design 模板）
       │ Step 4: 撰寫行為規格
       │ --- Phase gate ---
       │ Step 5: 逐層實作規劃（Domain → App → Infra → Pres）
       │ Step 6: 開 Git 分支
       │ --- Phase gate ---
       │ Step 7: 逐層實作（Domain 優先）
       │ --- Phase gate ---
       │ Step 8: Completion
       └────────────────────────────────────┘

       先了解完整流程：
       1. 員工建立報銷單 → 新增費用項目 → 提交
       2. 提交後主管會收到通知去審核

       幾個問題：
       - 一張報銷單可以有多少費用項目？有上限嗎？
```

```
你：有，最多 50 項。
```

```
Claude：提交之後，員工還能修改報銷單嗎？
```

```
你：不行，提交後就不能改了。要改的話主管要先退回。
```

```
Claude：了解。那報銷單有哪些狀態？
```

```
你：草稿、已提交、已核准、已退回、已撥款。
```

### Claude Code 引導 Aggregate 設計（Step 2-3）

```
Claude：Step 1 完成。進入 Step 2、3：Aggregate 設計——這是最重要的步驟。

       先忘掉資料庫和類別，讓我們想：什麼規則必須永遠為真？

       從你描述的情境中，我看到這些不變條件：
       1. 已提交的報銷單不能修改（不能新增/刪除費用項目）
       2. 最多 50 個費用項目
       3. 空的報銷單不能提交（至少要有一項）
       4. 金額必須為正數（我們已經有 Money 保證這點了）

       還有其他嗎？
```

```
你：金額有上限，單筆不能超過 100,000，整張報銷單不能超過 500,000。
```

```
Claude：好，這兩條也是不變條件。讓我整理 Aggregate 結構：

       ExpenseReport（Aggregate Root）
       ├── ExpenseLineItem（Entity，屬於 Aggregate 內部）
       ├── Money（Value Object）
       ├── ReportPeriod（Value Object）
       └── ReportStatus（Value Object / enum-like）

       外部引用：
       - EmployeeId — 引用 Employee，只存 ID
       - ApproverId — 引用主管，只存 ID

       為什麼 ExpenseLineItem 是 Entity 而不是 Value Object？
       因為每一項費用有自己的身份（ID），你可能會說「刪除第 3 項」，
       而不是「刪除金額 500 的那一項」。

       > 建立 specs/features/active/EXP-002-submit-expense-report.md
       > 同時建立 Aggregate 設計工作表
```

Claude Code 會根據 `templates/aggregate-design.md` 產生：

```markdown
---
aggregate: ExpenseReport
bounded-context: Expense
created: 2025-03-20
---

# ExpenseReport Aggregate

## 用途

代表一張費用報銷單，管理其中所有的費用項目，
並控制從草稿到撥款的完整生命週期。

## 不變條件（Invariants）

| 編號 | 不變條件 | 違反時的行為 |
|---|---|---|
| INV-01 | 已提交的報銷單不可修改費用項目 | 拋出 DomainException |
| INV-02 | 費用項目數量不超過 50 | 拋出 DomainException |
| INV-03 | 空報銷單不可提交 | 拋出 DomainException |
| INV-04 | 單筆費用不超過 100,000 | 拋出 DomainException |
| INV-05 | 報銷單總金額不超過 500,000 | 拋出 DomainException |

## 結構

​```
ExpenseReport (Aggregate Root)
├── List<ExpenseLineItem>  (Entity)
├── ReportPeriod            (Value Object)
├── ReportStatus            (Value Object)
└── Money TotalAmount       (Value Object, 計算得出)
​```

## 狀態變更方法

| 方法 | 前置條件 | 後置條件 | 產生的 Event |
|---|---|---|---|
| AddLineItem() | Status == Draft, Count < 50, 單筆 ≤ 100K, 總額 ≤ 500K | 新增項目 | LineItemAddedEvent |
| RemoveLineItem() | Status == Draft | 移除項目 | LineItemRemovedEvent |
| Submit() | Status == Draft, 至少一項 | Status → Submitted | ExpenseReportSubmittedEvent |

## 引用的其他 Aggregate（僅限 ID）

| Aggregate | 引用方式 | 用途 |
|---|---|---|
| Employee | EmployeeId | 報銷單的提交者 |
| Employee | EmployeeId (ApproverId) | 報銷單的審核者 |
```

### Claude Code 引導寫行為規格（Step 4）

```
Claude：Step 2、3 完成。進入 Step 4：撰寫行為規格。

       Aggregate 設計好了，把每個不變條件都變成 Given/When/Then：
```

```gherkin
Scenario: 成功提交報銷單
  Given 一張草稿狀態的報銷單，包含 3 個費用項目，總金額 5,000 TWD
  When 員工提交報銷單
  Then 報銷單狀態變為「已提交」
  And 產生 ExpenseReportSubmittedEvent（含 ReportId、EmployeeId、TotalAmount）
  And 報銷單不可再修改

Scenario: 嘗試提交空報銷單
  Given 一張草稿狀態的報銷單，沒有任何費用項目
  When 員工提交報銷單
  Then 拋出 DomainException「不可提交空的報銷單」

Scenario: 嘗試在已提交的報銷單新增項目
  Given 一張已提交狀態的報銷單
  When 嘗試新增費用項目
  Then 拋出 DomainException「已提交的報銷單不可修改」

Scenario: 新增費用項目導致超過總額上限
  Given 一張草稿狀態的報銷單，目前總金額 495,000 TWD
  When 新增一筆 10,000 TWD 的費用項目
  Then 拋出 DomainException「報銷單總金額不可超過 500,000」
```

### Phase gate：spec + Aggregate 設計完成 → 實作規劃

```
Claude：Step 4 完成：
       - 功能規格（4 個 Gherkin 情境、5 個 INV 不變條件、3 個 Domain Event）
       - Aggregate 設計工作表

       --- Phase gate: Step 4 → Step 5 ---

       即將進入 Step 5：逐層實作規劃。要繼續嗎？
       → 回覆「好」/`/next` 繼續
       → 或 `/cancel` 暫停（例如想先和後端架構師對 Aggregate 設計）
       → 或 `/status` 看整體進度
```

```
你：好
```

### Claude Code 引導逐層實作（Step 5-7）

```
Claude：我們依照 Clean Architecture 的順序：Domain → Application → Infrastructure → Presentation。

       先從 Domain 層開始：

       > 建立 src/ExpenseTracker.Domain/Expense/Entities/ExpenseReport.cs
```

```csharp
public class ExpenseReport : AggregateRoot
{
    private readonly List<ExpenseLineItem> _lineItems = new();
    public IReadOnlyList<ExpenseLineItem> LineItems => _lineItems.AsReadOnly();

    public EmployeeId SubmittedBy { get; private set; }
    public EmployeeId? ApproverId { get; private set; }
    public ReportPeriod Period { get; private set; }
    public ReportStatus Status { get; private set; }

    public Money TotalAmount =>
        _lineItems.Aggregate(Money.Zero(Currency.TWD),
            (sum, item) => sum.Add(item.Amount));

    // 私有建構式——外部透過工廠方法建立
    private ExpenseReport() { }

    public static ExpenseReport Create(EmployeeId submittedBy, ReportPeriod period)
    {
        var report = new ExpenseReport
        {
            Id = ExpenseReportId.New(),
            SubmittedBy = submittedBy,
            Period = period,
            Status = ReportStatus.Draft
        };
        report.AddDomainEvent(new ExpenseReportCreatedEvent(report.Id));
        return report;
    }

    public void AddLineItem(string description, Money amount, ExpenseCategory category)
    {
        // INV-01: 已提交不可修改
        if (Status != ReportStatus.Draft)
            throw new DomainException("已提交的報銷單不可修改");

        // INV-02: 最多 50 項
        if (_lineItems.Count >= 50)
            throw new DomainException("費用項目不可超過 50 項");

        // INV-04: 單筆上限
        if (amount.Amount > 100_000)
            throw new DomainException("單筆費用不可超過 100,000");

        // INV-05: 總額上限（先計算加入後的總額）
        var newTotal = TotalAmount.Add(amount);
        if (newTotal.Amount > 500_000)
            throw new DomainException("報銷單總金額不可超過 500,000");

        var lineItem = new ExpenseLineItem(description, amount, category);
        _lineItems.Add(lineItem);

        AddDomainEvent(new LineItemAddedEvent(Id, lineItem.Id, amount));
    }

    public void Submit()
    {
        // INV-01: 只有草稿可以提交
        if (Status != ReportStatus.Draft)
            throw new DomainException("只有草稿狀態可以提交");

        // INV-03: 不可為空
        if (!_lineItems.Any())
            throw new DomainException("不可提交空的報銷單");

        Status = ReportStatus.Submitted;
        AddDomainEvent(new ExpenseReportSubmittedEvent(Id, SubmittedBy, TotalAmount));
    }
}
```

```
Claude：注意這段程式碼的幾個 DDD 重點：

       1. private setter —— 外部不能直接改 Status
       2. 狀態透過方法改變 —— Submit() 不是 SetStatus()
       3. 不變條件在方法裡強制執行 —— 不靠呼叫端「記得檢查」
       4. Domain Event 在狀態改變後發出 —— 讓其他 Context 知道

       Domain 層完成，進入 Application 層：

       > 建立 src/ExpenseTracker.Application/Expense/Commands/SubmitExpenseReport/
```

```csharp
// Command
public record SubmitExpenseReportCommand(Guid ReportId) : IRequest<Unit>;

// Handler — 注意：不包含任何業務邏輯
public class SubmitExpenseReportCommandHandler
    : IRequestHandler<SubmitExpenseReportCommand, Unit>
{
    private readonly IExpenseReportRepository _repo;
    private readonly IUnitOfWork _unitOfWork;

    public async Task<Unit> Handle(
        SubmitExpenseReportCommand request,
        CancellationToken ct)
    {
        // 1. 從 Repository 取得 Aggregate
        var report = await _repo.GetByIdAsync(
            new ExpenseReportId(request.ReportId), ct)
            ?? throw new NotFoundException("報銷單不存在");

        // 2. 呼叫 Domain 方法（業務邏輯在這裡面）
        report.Submit();

        // 3. 儲存
        await _unitOfWork.SaveChangesAsync(ct);
        // Domain Events 在 SaveChanges 後自動 dispatch

        return Unit.Value;
    }
}
```

```
Claude：注意 Handler 有多「薄」——它只做三件事：
       1. 取出 Aggregate
       2. 呼叫 Domain 方法
       3. 儲存

       沒有 if/else，沒有業務規則。所有規則都在 Domain 層。
       如果你在 Handler 裡發現自己在寫 if/else，
       那就是業務邏輯洩漏到 Application 層的信號。

       Infrastructure 和 Presentation 層我也一起建好...

       --- Phase gate: Step 7 → Step 8 ---

       逐層實作完成、Domain 單元測試全綠。要進入 Step 8：Completion 嗎？
```

```
你：好
```

```
Claude：Step 8: Completion

       ✅ specs/features/active/EXP-002 狀態改為 completed
       ✅ 規格檔移到 completed/
       ✅ specs/domain/expense/models.md — 更新 ExpenseReport、ExpenseLineItem 定義
       ✅ specs/domain/expense/events.md — 新增 3 個 Domain Event
       ✅ specs/domain/expense/rules.md — 新增 INV-01 ~ INV-05
       ✅ specs/domain/glossary.md — 新增 ReportStatus、ReportPeriod 等術語

       new-feature-flow 完成。
```

### 這個情境的產出物

| 產出物 | 路徑 |
|---|---|
| 完整功能規格 | `specs/features/completed/EXP-002-submit-expense-report.md` |
| Aggregate 設計工作表 | `specs/domain/expense/aggregate-expense-report.md` |
| Domain Events 文件更新 | `specs/domain/expense/events.md`（+3 個事件） |
| 術語表更新 | `specs/domain/glossary.md` |
| 規則文件更新 | `specs/domain/expense/rules.md` |
| Domain 程式碼 | `src/ExpenseTracker.Domain/Expense/Entities/ExpenseReport.cs` |
| Domain 程式碼 | `src/ExpenseTracker.Domain/Expense/Entities/ExpenseLineItem.cs` |
| Domain 程式碼 | `src/ExpenseTracker.Domain/Expense/Events/*.cs` |
| Application 程式碼 | `src/ExpenseTracker.Application/Expense/Commands/SubmitExpenseReport/` |
| Domain 單元測試 | `tests/Domain.UnitTests/Expense/ExpenseReportTests.cs` |
| Git 分支 | `feature/EXP-002-submit-expense-report` |

### 重點回顧

這個情境展示了 Core 版的核心差異：**Aggregate 設計先行**。

P001 Hybrid 設計在這情境帶來的改變：
- **流程結構一開始就秀出來**：你看到 Step 1~8 和三個 phase gate 的位置，不用猜還剩多少。
- **Aggregate 設計變成明確的 Step 3**：在原版中只是 Step 2 裡的子流程，新版中它和 spec 撰寫是平行但獨立的 step。
- **Phase gate 在對的地方**：spec 完成、分支建好、實作完成——這三點才是你可能想找人 review 或暫停的時機，不會在 step 內轉換時被打斷。

> 💡 這裡同時用到了 [DDD 觀念指引](DDD_MINDSET_SHIFT_tw.md) 的觀念一（行為放在 Entity 裡——Submit() 不是 SetStatus()）和觀念四（Domain 零依賴——Application Handler 只做取出、呼叫、儲存）。

---

## 情境三：領域建模 — 該怎麼設計這個 Aggregate？（DDD 自然語言諮詢）

> **觸發的 Skill 檔案**：`ddd-modeling-guide.md`、`aggregate-design.md`
>
> **背景**：開發者在設計審批功能時，不確定 ApprovalTask 該怎麼建模。
>
> **📌 對比原版**：原版 AI 直接開始分析；新版 AI 先告知「這是建模諮詢，我會用 ddd-modeling-guide.md」並確認。這不是進入一個完整 flow，而是一次性諮詢。

### 開發者用自然語言提問

```
❯ claude

你：我在想審批功能要怎麼設計。
   ApprovalTask 應該是 ExpenseReport 裡面的一部分，
   還是獨立的 Aggregate？
```

### Auto-trigger 判斷為建模諮詢，告知來源

```
Claude：我判斷這是 **DDD 建模諮詢**（不是一個完整 flow，而是一次性問答）。
       會用 `references/ddd-modeling-guide.md` 的判斷原則來分析。

       如果最後結論是「獨立 Aggregate」，你可能需要緊接著 `/new-feature`
       來為 ApprovalTask 走完整流程；如果是「屬於 ExpenseReport 內部」，
       就在現有 EXP-002 裡擴充即可。

       要繼續分析嗎？
```

```
你：好
```

### Claude Code 用建模指南引導

```
Claude：讓我們用設計原則來分析。

       先回到基本問題：什麼必須在同一個交易裡保持一致？

       想想這個情境：
       - 主管核准報銷單
       - 同一時間，員工正在修改（但報銷單在草稿狀態的話才能改）

       如果 ApprovalTask 在 ExpenseReport 裡面，
       那「核准」和「修改費用項目」會鎖定同一個 Aggregate，
       一個人操作時另一個人就會被擋住。

       但實際上，核准和修改是可以獨立發生的嗎？
```

```
你：可以，而且應該要可以。審核中員工不能改，
   但如果被退回，員工改的時候主管不應該被擋住看其他報銷單。
```

```
Claude：那答案就很清楚了——ApprovalTask 應該是獨立的 Aggregate。

       判斷依據：

       ┌──────────────────────────────────────────┐
       │  獨立 Aggregate 的信號：                    │
       │  ✅ 可以被不同使用者獨立操作                  │
       │  ✅ 有自己的生命週期（建立→核准/退回→結束）    │
       │  ✅ 有自己的不變條件（不能核准已退回的任務）    │
       │  ✅ 不需要和 ExpenseReport 在同一個交易       │
       └──────────────────────────────────────────┘

       它們之間的關聯用 ID 就好：

       ​```
       ExpenseReport（Aggregate）
       └── 被 ApprovalTask 引用（透過 ExpenseReportId）

       ApprovalTask（Aggregate）
       ├── ExpenseReportId   ← 只存 ID，不存物件
       ├── ApproverId        ← 只存 ID
       └── Status: Pending → Approved / Rejected
       ​```
```

```
你：那核准之後，ExpenseReport 怎麼知道它被核准了？
```

```
Claude：透過 Domain Event。

       流程是這樣的：

       1. 主管呼叫 ApprovalTask.Approve()
       2. ApprovalTask 發出 ReportApprovedEvent(ExpenseReportId)
       3. Event Handler 收到事件，載入 ExpenseReport
       4. 呼叫 ExpenseReport.MarkAsApproved()
       5. 儲存 ExpenseReport

       注意：步驟 1-2 和步驟 3-5 是兩個不同的交易。
       這就是「最終一致性」—— ApprovalTask 先完成，
       ExpenseReport 過一小段時間後才更新。
```

```
你：如果步驟 3-5 失敗了怎麼辦？
```

```
Claude：好問題。有幾種處理方式：

       1. 重試（最常見）：Event Handler 設計成冪等的，失敗了就重新處理。
       2. Outbox Pattern：先把事件存到資料庫，確保事件不會丟失，非同步處理。

       對目前的規模來說，「重試」就夠了。等系統更複雜時再考慮 Outbox。
```

### 諮詢結束，轉入下一動作

```
Claude：DDD 建模諮詢結論：**ApprovalTask 應該是獨立的 Aggregate**。

       接下來建議的動作：
       → `/new-feature` 為 ApprovalTask 走完整的 new-feature-flow
         （包含新建 Approval Bounded Context 的 context.md 和 context-map 更新）
       → 或回覆「先幫我建 aggregate-design 工作表」我直接產出草稿，
         完整的 new-feature-flow 稍後再跑
       → 或 `/cancel` 結束諮詢，自行決定
```

```
你：/new-feature 為 ApprovalTask 走完整流程
```

```
Claude：進入 new-feature-flow（延續剛剛的建模結論）。

       ┌─── New Feature Flow（Core 版）───┐
       │ Step 1: 需求理解 ← 現在這裡（部分已釐清：來自 DDD 諮詢）
       │ ...
       └────────────────────────────────────┘

       剛剛的建模諮詢已經給了我很多背景。讓我補問幾個：
       - 需要記錄審批歷史嗎？（如果一張單被退回又重送，要保留所有紀錄？）
       - 審批有時限嗎？（例如 7 天內要處理）
```

[... 後續 flow 照原版進行，並在主要里程碑設 phase gate ...]

### 這個情境的產出物

| 產出物 | 路徑 |
|---|---|
| Aggregate 設計工作表 | `specs/domain/approval/aggregate-approval-task.md` |
| Context 定義 | `specs/domain/approval/context.md`（新建 Approval Context） |
| Context Map 更新 | `specs/domain/context-map.md`（Expense ↔ Approval 關係） |
| Events 文件 | `specs/domain/approval/events.md` |

### 重點回顧

這個情境展示了 P001 Hybrid 設計如何處理「非 flow 形式」的諮詢：
- **Auto-trigger 仍會介入**：判斷這是建模諮詢，告知將使用 `ddd-modeling-guide.md`。
- **諮詢結束給出銜接選項**：不只是結束對話，而是**提示接下來的明確動作**（`/new-feature` 或 `/cancel`）。
- **跨流程銜接**：諮詢得出的結論直接被下一個 flow 繼承，不用重複解釋。

> 💡 這正是 [DDD 觀念指引](DDD_MINDSET_SHIFT_tw.md) 觀念二的實際應用——Aggregate 邊界不是「相關的放一起」，而是「必須一致的放一起」。

---

## 情境四：PR Review — 檢查 Clean Architecture 合規性（`/pr-review`）

> **觸發的 Skill 檔案**：`pr-review-checklist.md`
>
> **背景**：同事完成了 ApprovalTask 的實作，發了 PR。

### 開發者用指令明確進入

```
❯ claude

你：/pr-review EXP-003 審批功能的實作
```

```
Claude：進入 pr-review 流程（Core 版，四層逐一檢查）。

       ┌─── PR Review（Core 版）───┐
       │ Step 0: Spec Intent（先讀 spec 理解意圖）
       │ Step 1: Domain 層
       │ Step 2: Application 層
       │ Step 3: Infrastructure 層
       │ Step 4: Presentation 層
       │ --- Phase gate ---
       │ Step 5: 架構評分 + 改善建議
       └───────────────────────────┘

       > 讀取 specs/features/active/EXP-003-approval-task.md

       先理解意圖：這個 PR 實作 ApprovalTask Aggregate，讓主管能核准/退回
       報銷單，並透過 ReportApprovedEvent / ReportRejectedEvent 通知 ExpenseReport。

       理解對嗎？確認後開始逐層 review。
```

```
你：對
```

### Claude Code 逐層檢查

```
Claude：Step 1: Domain 層

       > 檢查 src/ExpenseTracker.Domain/Approval/

       ✅ .csproj 零外部依賴
       ✅ 沒有 ORM 屬性（[Table], [Column]）
       ✅ 沒有序列化屬性（[JsonProperty]）
       ✅ Private setter，狀態透過方法改變
       ✅ 不變條件在 Approve() 和 Reject() 中強制執行
       ✅ Domain Events 正確發出
       ✅ 其他 Aggregate 透過 ID 引用

       ⚠️ 發現一個問題：
```

```csharp
// ApprovalTask.cs 第 35 行
public async Task<bool> Approve()  // ← async 在 Domain 層
{
    if (Status != ApprovalStatus.Pending)
        throw new DomainException("...");

    Status = ApprovalStatus.Approved;
    AddDomainEvent(new ReportApprovedEvent(ExpenseReportId));
    return true;
}
```

```
Claude：Domain 層不應該有 async/await。
       領域邏輯是同步的——它只是改變狀態和驗證規則，
       不需要等待任何 I/O 操作。

       async 屬於 Application 層（呼叫 Repository 時）
       和 Infrastructure 層（實際的資料庫操作）。

       建議改為：
```

```csharp
public void Approve()  // ← 同步，無回傳值
{
    if (Status != ApprovalStatus.Pending)
        throw new DomainException("...");

    Status = ApprovalStatus.Approved;
    AddDomainEvent(new ReportApprovedEvent(ExpenseReportId));
}
```

```
Claude：Step 2: Application 層

       > 檢查 src/ExpenseTracker.Application/Approval/

       ✅ CQRS 分離（Command 寫、Query 讀）
       ✅ Validator 使用 FluentValidation

       ⚠️ 問題：Handler 裡有業務邏輯
```

```csharp
// ApproveReportCommandHandler.cs 第 28-35 行
public async Task<Unit> Handle(ApproveReportCommand request, CancellationToken ct)
{
    var task = await _repo.GetByIdAsync(request.TaskId, ct);

    // ⚠️ 這段判斷是業務邏輯，應該在 Domain 層
    if (task.ExpenseReport.TotalAmount > 50000 && !request.HasManagerApproval)
    {
        throw new BusinessException("超過 50,000 需要經理核准");
    }

    task.Approve();
    await _unitOfWork.SaveChangesAsync(ct);
    return Unit.Value;
}
```

```
Claude：「超過 50,000 需要經理核准」是一條業務規則，
       不應該在 Application 層的 Handler 裡。

       有兩個改法：
       1. 移到 ApprovalTask.Approve() 裡——如果這是 Aggregate 自己的規則
       2. 建立 Domain Service——如果這個規則需要查詢其他 Aggregate 的資料

       從這個情境看，審批政策可能比較複雜（不同金額不同層級），
       建議用 Domain Service：

       ​```csharp
       // Domain Service
       public class ApprovalPolicyService
       {
           public bool RequiresManagerApproval(Money totalAmount)
               => totalAmount.Amount > 50_000;
       }
       ​```

       然後 Handler 變成：

       ​```csharp
       var policy = _policyService.RequiresManagerApproval(task.TotalAmount);
       task.Approve(hasRequiredApproval: policy ? request.HasManagerApproval : true);
       ​```

       Step 3: Infrastructure 層

       ✅ EF Config 使用 Fluent API
       ✅ Repository 只為 Aggregate Root 實作
       ✅ 沒有業務邏輯在 SQL/LINQ 中

       Step 4: Presentation 層

       ✅ Controller 很薄（parse → dispatch → respond）
       ✅ 沒有 Domain 物件暴露在 API 中
       ✅ 正確的 HTTP 狀態碼

       --- Phase gate: Step 4 → Step 5 ---

       四層檢查完成，共發現 2 項違規（Domain 有 async、業務邏輯洩漏到 Application）。
       要進入 Step 5：架構評分 嗎？
```

```
你：好
```

```
Claude：Step 5: 架構評分 — C 級

       好的部分：Domain 層結構正確、CQRS 分離、測試覆蓋不錯。

       扣分項：
       1. Domain 層有 async（應為同步）
       2. 業務規則洩漏到 Application 層（金額門檻判斷）

       修完這兩項就能到 A。要把這份 review 報告貼到 PR 留言嗎？
```

### 這個情境的產出物

| 產出物 | 說明 |
|---|---|
| 逐層審查報告 | Domain → Application → Infrastructure → Presentation |
| 架構評分 | C 級，附具體改善步驟 |
| 重構建議 | 移除 Domain 層 async、業務邏輯移回 Domain |

### 重點回顧

Core 版的 PR Review 比 WebForms 版更嚴格——它不只看「有沒有 spec」和「Domain 層有沒有 System.Web」，還會逐層檢查 Clean Architecture 的合規性。

P001 Hybrid 設計在這情境帶來的改變：
- 一開始就秀出 6 個 step 的地圖，同事可以預期 review 會涵蓋哪些面向。
- Step 0（Spec Intent）明確列為第一步，呼應 P007 的「先理解意圖再看程式碼」。
- Phase gate 在評分前，讓你可以先暫停、和作者討論某個違規的嚴重度，再決定是否影響評分。

> 💡 這兩個違規分別對應 [DDD 觀念指引](DDD_MINDSET_SHIFT_tw.md) 的觀念五（async 不屬於 Domain 層）和觀念一（行為放在 Entity 裡，不是 Service / Handler 裡）。

---

## Skill 觸發速查表（更新版）

### 明確指令（推薦）

| 指令 | 觸發的流程 | 主要 Skill 檔案 |
|---|---|---|
| `/new-feature` | 新功能流程 | `new-feature-flow.md` + `feature-spec.md` + `aggregate-design.md` |
| `/modify-existing` | 修改現有流程 | `modify-existing-flow.md` + `feature-spec.md` |
| `/bug-fix` | Bug 修復流程 | `modify-existing-flow.md` + `lightweight-spec.md` |
| `/pr-review` | PR 審查（逐層 Clean Architecture 檢查）| `pr-review-checklist.md` |
| `/status` | 回報目前 flow / step / 未完項 | — |
| `/next` | 確認進入下一階段 | — |
| `/cancel` | 中止目前流程 | — |

### 自然語言（auto-trigger 安全網）

| 你說的話 | AI 判斷 | AI 行為 |
|---|---|---|
| 「我要加一個新功能」 | new-feature-flow | 判斷後停下，要求你背書 |
| 「有個 bug 要修」 | bug-fix | 判斷後停下，要求你背書 |
| 「該怎麼設計這個 Aggregate？」 | DDD 建模諮詢 | 告知將使用 ddd-modeling-guide.md，要求背書 |
| 「幫我 review 這個 PR」 | pr-review | 判斷後停下，要求你背書 |

### Ceremony Scaling 速查

| 改什麼 | Spec 層級 | DDD 建模 | 記技術債？ |
|---|---|---|---|
| 新功能 | 完整 spec | 必須 | 視情況 |
| 新 Aggregate / BC | 完整 spec + context map | 必須 | — |
| 業務規則變更 | 標準 spec | 必須 | 必須 |
| Bug 修復（邏輯） | 輕量 spec | 評估 | 有發現就記 |
| UI / API 變更 | 輕量 spec | 不需要 | 不需要 |
| Infrastructure 變更 | 不需要 | 不需要 | 評估 |

### Clean Architecture 層級速查

```
哪些東西放哪一層？

Domain（核心，零依賴）
├── Aggregate Root、Entity、Value Object
├── Domain Event（record 型別）
├── Domain Service（跨 Aggregate 的規則）
├── Repository Interface（IXxxRepository）
└── Specification（查詢邏輯）

Application（編排，不含業務邏輯）
├── Command + CommandHandler
├── Query + QueryHandler
├── CommandValidator（FluentValidation）
├── Domain Event Handler
└── DTO（輸入/輸出）

Infrastructure（實作技術細節）
├── EF Core DbContext + Configuration
├── Repository 實作
├── 外部 API 客戶端
└── Email / 檔案 / 訊息佇列

Presentation（HTTP 入口）
├── Controller / Minimal API
├── Request / Response Model
└── Middleware
```

---

## 為什麼這個改動值得採用（說服同事的三個理由）

### 1. 可預測性

原版：「我這句話會觸發什麼？AI 會自己判斷還是等我說？」
新版：**你說什麼，AI 就做什麼**。指令 = 明確意圖；自然語言 = 會先判斷並讓你背書。

### 2. 可中斷（Core 版特別有價值）

Core 版的一個新功能可能要走完 8 個 step，中間很可能被別的事打斷（例如 spec 寫好後要找後端架構師對 Aggregate 設計）。

原版：一旦進入流程，中途想暫停沒有明確出口。
新版：**`/cancel` 一鍵退出**，spec 保持 in-progress，之後 `/new-feature` 接續——AI 會偵測到 in-progress spec 並接著跑。

### 3. 可驗證

原版：做到一半不知道自己在哪，也不知道還剩多少。
新版：**`/status` 隨時可查**；phase gate 在對的地方（spec 完成前、開分支前、completion 前）明確告知。

### 4. 對 DDD 新手特別友善

Core 版 Aggregate 設計 + Clean Architecture 四層 + Domain Events，對 DDD 新手是資訊量很大的流程。

原版：AI 在流程中推進，新手很容易跟不上「我現在在做什麼？為什麼？」
新版：**流程地圖一開始就秀出來**，新手能看到「喔，我現在在 Step 3，還有 Step 4~8」——心理負擔小很多。

---

## 接下來

如果你想從頭練習完整的 DDD 流程，請參考 `PRACTICE_PLAN_tw.md`——那是一個 7 階段、15-22 小時的模擬練習計畫，用費用報銷系統作為練習題目，從建立專案結構一路做到整合測試。
