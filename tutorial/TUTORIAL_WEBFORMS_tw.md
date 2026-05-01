# SDD/DDD Workflow Skill 使用教學 — WebForms 版

> 本文件透過實際開發情境，展示如何在 Claude Code CLI 中使用 SDD/DDD Workflow Skill。
> 每個情境從開發者的一句話開始，完整呈現 AI 引導的對話過程和最終產出物。

---

## 目錄

- [事前準備：安裝 Skill 到你的專案](#事前準備安裝-skill-到你的專案)
- [情境一：修 Bug — 費用計算四捨五入不一致](#情境一修-bug--費用計算四捨五入不一致)
- [情境二：新功能 — 支援日幣報銷](#情境二新功能--支援日幣報銷)
- [情境三：PR Review — 審查同事的程式碼](#情境三pr-review--審查同事的程式碼)
- [情境四：開分支 — 準備開始一個新任務](#情境四開分支--準備開始一個新任務)
- [Skill 觸發速查表](#skill-觸發速查表)

---

## 事前準備：安裝 Skill 到你的專案

### 0. 先讀 DDD 觀念指引（10-15 分鐘）

如果你的團隊對 DDD 還不熟，建議每位開發者先讀一次
**[給資深工程師的 DDD 觀念翻轉指南](DDD_MINDSET_SHIFT_tw.md)**。

那份指南只講 5 個會違反你直覺的觀念，每個附程式碼對比。
不需要記住所有細節——之後在下面的情境中遇到時，Claude Code 會提醒你。

### 1. 放置 Skill 檔案

將 `sdd-ddd-workflow/` 資料夾放到你的專案根目錄：

```
MyWebFormsProject/
├── sdd-ddd-workflow/          ← Skill 檔案
│   ├── SKILL.md
│   ├── references/
│   └── templates/
├── src/
├── dflow/specs/                     ← 第一次使用時建立
└── CLAUDE.md                  ← 第一次使用時建立
```

### 2. 建立 Dflow baseline

第一次使用時，在專案 root 執行 Dflow CLI：

```
$ npx dflow init
```

CLI 會詢問 project type、edition、tech stack、migration context、prose language 與 optional starter files，然後建立 `dflow/specs/` baseline 與可選的 `CLAUDE.md`。

### 3. 之後的每次使用

不需要每次都手動叫 Claude Code 讀取 Skill。只要 Skill 檔案在專案目錄中，
Claude Code 的 CLAUDE.md 會指引它在適當時機自動參考對應的 reference 和 template。

---

## 情境一：修 Bug — 費用計算四捨五入不一致

> **觸發的 Skill 檔案**：`modify-existing-flow.md`、`lightweight-spec.md`
>
> **背景**：QA 回報費用報表的合計金額，在「費用明細頁」和「費用總覽頁」顯示不同數字。

### 開發者說了一句話

```
❯ claude

你：費用報表有個 bug，明細頁和總覽頁的合計金額不一樣，
   明細頁是 1,234 但總覽頁是 1,233。
```

### Claude Code 開始引導（Phase 1：評估變更）

```
Claude：這聽起來是計算邏輯的 bug，不是 UI 問題。

       先讓我確認幾件事：
       1. 這個差異是在什麼幣別下發生的？所有幣別都有問題還是特定幣別？
```

```
你：台幣，金額有小數的時候就會不一樣。
```

```
Claude：了解，這很可能是四捨五入的方式不一致。

       讓我先看看有沒有相關的規格文件...

       > 搜尋 dflow/specs/domain/ 和 dflow/specs/features/completed/，
       > 沒有找到關於四捨五入的規格。

       目前沒有文件記錄四捨五入的規則。
       在我們修之前，先用輕量規格記下來——這只需要幾分鐘，
       但能確保修完之後有跡可循。

       正確的四捨五入規則是什麼？依會計標準，台幣應該四捨五入到整數，對嗎？
```

```
你：對，四捨五入到整數。
```

### Claude Code 引導寫輕量規格

```
Claude：好，我幫你建立輕量規格。

       > 建立 dflow/specs/features/active/BUG-042-rounding-inconsistency.md
```

Claude Code 會根據 `templates/lightweight-spec.md` 產生以下內容：

```markdown
---
id: BUG-042
title: 費用計算四捨五入不一致
status: in-progress
bounded-context: Expense
created: 2025-02-12
branch: bugfix/BUG-042-rounding-inconsistency
---

# 費用計算四捨五入不一致

## 問題

明細頁（ExpenseDetail.aspx）使用 Math.Round(amount, 0, MidpointRounding.AwayFromZero)（四捨五入），
總覽頁（ExpenseSummary.aspx）使用 Math.Floor(amount)（無條件捨去），
導致同一筆費用在兩個頁面顯示不同金額。

## 現有行為

​```gherkin
Given 一筆費用原始金額為 1233.5 TWD
When 在明細頁顯示時
Then 顯示 1,234（四捨五入）

Given 同一筆費用原始金額為 1233.5 TWD
When 在總覽頁顯示時
Then 顯示 1,233（無條件捨去）
​```

## 預期行為

​```gherkin
Given 一筆費用原始金額為 1233.5 TWD
When 在任何頁面顯示時
Then 都顯示 1,234（四捨五入，依會計標準）
​```

## 根因

四捨五入邏輯分散在不同的 Code-Behind 中，沒有統一的計算方式。

## 修復方式

抽取到 Domain 層：建立 Money.Round() 方法，兩個頁面都呼叫它。

## 發現的技術債

- [ ] ExpenseDetail.aspx.cs:42 — 直接在 Code-Behind 做四捨五入
- [ ] ExpenseSummary.aspx.cs:78 — 直接在 Code-Behind 做無條件捨去
- [ ] 其他頁面可能也有類似問題，需全面排查
```

### Claude Code 引導抽離到 Domain 層（Phase 2：評估抽離機會）

```
Claude：規格寫好了。現在來看修復方式。

       這個 bug 的根因是四捨五入邏輯分散在 Code-Behind 裡。
       既然我們已經要改這段程式碼了，這是一個很好的機會
       把它抽到 src/Domain/Expense/ 裡。這樣做的好處：

       1. 以後只有一個地方定義四捨五入規則
       2. 可以寫單元測試
       3. 未來遷移到 ASP.NET Core 時，這段程式碼可以直接搬過去

       要不要趁這次一起做？
```

```
你：好，要怎麼做？
```

```
Claude：先建立一個 Money Value Object：

       > 建立 src/Domain/Expense/ValueObjects/Money.cs
```

```csharp
// src/Domain/Expense/ValueObjects/Money.cs
public record Money(decimal Amount, Currency Currency)
{
    /// <summary>
    /// 依幣別規則四捨五入（TWD: 整數、USD: 兩位小數）
    /// </summary>
    public Money Round()
    {
        var rounded = Math.Round(Amount, Currency.DecimalPlaces,
            MidpointRounding.AwayFromZero);
        return this with { Amount = rounded };
    }
}
```

```
Claude：然後修改兩個頁面的 Code-Behind，
       把計算邏輯替換成呼叫 Domain 層：
```

```csharp
// 修改前（ExpenseDetail.aspx.cs）
decimal result = Math.Round(amount, 0, MidpointRounding.AwayFromZero);
lblTotal.Text = result.ToString("N0");

// 修改後（ExpenseDetail.aspx.cs）
var money = new Money(amount, Currency.TWD);
lblTotal.Text = money.Round().Amount.ToString("N0");

// 修改前（ExpenseSummary.aspx.cs）
decimal result = Math.Floor(amount);  // ← 這就是 bug
lblTotal.Text = result.ToString("N0");

// 修改後（ExpenseSummary.aspx.cs）
var money = new Money(amount, Currency.TWD);
lblTotal.Text = money.Round().Amount.ToString("N0");
```

### Claude Code 更新相關文件

```
Claude：程式碼改完了，讓我更新相關文件：

       1. ✅ 規格狀態改為 completed
       2. ✅ dflow/specs/domain/glossary.md — 新增「Money」和「Currency」術語
       3. ✅ dflow/specs/domain/expense/models.md — 新增 Money Value Object 定義
       4. ✅ dflow/specs/domain/expense/rules.md — 新增四捨五入規則 BR-R01
       5. ✅ dflow/specs/architecture/tech-debt.md — 記錄發現的重複計算邏輯
       6. ✅ 規格檔移到 dflow/specs/features/completed/
```

### 這個情境的產出物

| 產出物 | 路徑 |
|---|---|
| 輕量規格 | `dflow/specs/features/completed/BUG-042-rounding-inconsistency.md` |
| Domain 程式碼 | `src/Domain/Expense/ValueObjects/Money.cs` |
| 術語表更新 | `dflow/specs/domain/glossary.md`（+2 筆） |
| 模型文件更新 | `dflow/specs/domain/expense/models.md` |
| 規則文件更新 | `dflow/specs/domain/expense/rules.md`（+1 條） |
| 技術債記錄 | `dflow/specs/architecture/tech-debt.md`（+3 筆） |

### 重點回顧

這個情境展示了 Skill 的核心精神：**趁修改時漸進式改善**。一個看似簡單的 Bug 修復，透過 Skill 的引導，同時完成了三件事：修復 bug、抽離業務邏輯到 Domain 層、記錄技術債。這些全是未來遷移到 ASP.NET Core 的資產。

> 💡 這裡用到了兩個 DDD 觀念：**行為放在 Entity 裡**（Money.Round() 而不是到處寫 Math.Round）和 **Value Object 不只是 DTO**（Money 在建構時就確保合法狀態）。詳見 [DDD 觀念指引](DDD_MINDSET_SHIFT_tw.md) 的觀念一和觀念三。

---

## 情境二：新功能 — 支援日幣報銷

> **觸發的 Skill 檔案**：`new-feature-flow.md`、`feature-spec.md`、`context-definition.md`
>
> **背景**：公司開始有日本出差的需求，費用報表系統需要支援日幣報銷和匯率轉換。

### 開發者說了一句話

```
❯ claude

你：我要加一個新功能，讓費用報表支援日幣報銷，
   需要能輸入日幣金額然後自動轉換成台幣。
```

### Claude Code 開始引導（Step 1：需求理解）

```
Claude：好的，支援日幣報銷。讓我先了解一些背景：

       1. 這個需求是誰提出的？是財務部還是業務部門？
```

```
你：財務部提出的，因為開始有日本出差的費用需要報銷。
```

```
Claude：了解。讓我看看現有的資產...

       > 搜尋 dflow/specs/domain/expense/
       > 找到 models.md 中有 Money Value Object（在修 BUG-042 時建立的）
       > 找到 glossary.md 中有 Money、Currency 術語

       很好，我們在之前修 Bug 時已經建立了 Money 和 Currency 的概念。
       這次的新功能可以在這個基礎上擴展。

       目前 Currency 支援哪些幣別？只有 TWD 嗎？
```

```
你：對，目前只有台幣。
```

```
Claude：所以這次需要：
       1. 新增 JPY 幣別
       2. 匯率轉換機制
       3. 使用者介面上的幣別選擇

       匯率的來源是什麼？是手動輸入、每日固定匯率、還是即時查詢？
```

```
你：財務部每月初公告當月匯率，手動設定。
```

### Claude Code 引導識別領域概念（Step 2-3：Bounded Context 與概念發掘）

```
Claude：這裡出現了一個新的領域概念：「匯率」。
       讓我確認它屬於哪個 Bounded Context。

       匯率是由財務部管理的，跟費用報銷的核心流程不太一樣。
       它比較像是一個查詢資料——報銷時去查「當月的 JPY→TWD 匯率是多少」。

       我建議把它放在 Expense Context 裡，因為：
       - 目前規模還不大（只有 JPY→TWD 一種）
       - 它的存在是為了服務費用報銷

       等未來幣別多了，再考慮拆出獨立的 Currency Context。
       你覺得呢？
```

```
你：同意，先放 Expense 裡。
```

```
Claude：好，讓我整理一下這個功能涉及的領域概念：

       新概念（需要加入術語表）：
       - ExchangeRate（匯率）：兩個幣別之間的轉換比率
       - ReportDate（報表日期）：用來決定適用哪個月的匯率

       擴展的概念：
       - Currency：新增 JPY，目前有 TWD、JPY
       - Money：需要支援 ConvertTo() 方法

       需要的新介面：
       - IExchangeRateRepository：查詢匯率用

       這些我先更新到術語表和 models.md 裡。
```

### Claude Code 引導寫完整規格（Step 4：撰寫規格）

```
Claude：現在來寫功能規格。我幫你建立：

       > 建立 dflow/specs/features/active/EXP-001-jpy-currency-support.md
```

Claude Code 會根據 `templates/feature-spec.md` 的結構，引導開發者逐段填寫。
以下是引導「行為規格」段落的過程：

```
Claude：讓我們先走過主要的成功情境。

       使用者在報銷時選擇日幣，
       Given 使用者正在填寫費用報銷單，
       When 他選擇幣別為 JPY 並輸入金額 10,000，
       Then 會發生什麼？
```

```
你：系統自動查出當月匯率，換算成台幣顯示出來。
```

```
Claude：好，所以是即時換算顯示。再往下想：

       如果那個月的匯率還沒設定呢？
```

```
你：不應該讓他送出，要顯示錯誤訊息。
```

```
Claude：了解。那如果報銷日期是上個月，但已經用了這個月的匯率呢？
       匯率是依「費用發生日」還是「報銷日」來查？
```

```
你：費用發生日。出差是上個月，就用上個月的匯率。
```

```
Claude：很好，這是一個重要的業務規則，我記下來。
       來整理目前的情境：
```

最終產出的規格摘錄：

```markdown
## 行為規格

### 主要成功情境

​```gherkin
Scenario: 使用日幣報銷並自動換算台幣
  Given 2025 年 2 月的 JPY→TWD 匯率為 0.215
  And 使用者正在填寫費用報銷單
  When 選擇幣別為 JPY，輸入金額 10,000，費用發生日為 2025-02-15
  Then 系統顯示換算金額為 2,150 TWD（10,000 × 0.215，四捨五入到整數）
​```

### 替代情境

​```gherkin
Scenario: 當月匯率尚未設定
  Given 2025 年 3 月的 JPY→TWD 匯率尚未設定
  When 使用者選擇幣別為 JPY，費用發生日為 2025-03-10
  Then 系統顯示錯誤「2025 年 3 月的日幣匯率尚未設定，請聯繫財務部」
  And 無法送出報銷單
​```

## 業務規則

| 編號 | 規則 | 備註 |
|---|---|---|
| BR-01 | 匯率依「費用發生日」所屬月份查詢，不是報銷日 | 財務部確認 |
| BR-02 | 匯率未設定時不允許送出該幣別的報銷 | |
| BR-03 | 換算結果依目標幣別規則四捨五入（TWD→整數） | 沿用 BUG-042 的規則 |

## 邊界情況

| 編號 | 情況 | 預期處理方式 |
|---|---|---|
| EC-01 | 日幣金額為 0 | 不允許，顯示驗證錯誤 |
| EC-02 | 匯率為 0 或負數 | 資料驗證阻擋，不應出現 |
| EC-03 | 費用發生日在未來 | 不允許 |
```

### Claude Code 引導實作規劃（Step 5：實作設計）

```
Claude：規格寫好了。現在來規劃實作。

       Domain 層需要：
       1. 擴展 Currency —— 新增 JPY
       2. Money.ConvertTo() —— 幣別轉換方法
       3. ExchangeRate Value Object —— 封裝匯率和轉換邏輯
       4. IExchangeRateRepository 介面 —— 查詢匯率

       Code-Behind 的部分會很薄：
```

```csharp
// 理想的 Code-Behind 模式
protected void ddlCurrency_SelectedIndexChanged(object sender, EventArgs e)
{
    // 1. 解析 UI 輸入
    var currency = Currency.FromCode(ddlCurrency.SelectedValue);
    var amount = decimal.Parse(txtAmount.Text);
    var expenseDate = DateTime.Parse(txtExpenseDate.Text);

    // 2. 呼叫 Domain 層
    var money = new Money(amount, currency);
    var rate = _exchangeRateRepo.GetRate(currency, Currency.TWD, expenseDate);

    if (rate == null)
    {
        lblError.Text = $"{expenseDate:yyyy 年 M 月}的{currency.Code}匯率尚未設定";
        return;
    }

    var converted = money.ConvertTo(Currency.TWD, rate);

    // 3. 顯示結果
    lblConvertedAmount.Text = converted.Amount.ToString("N0");
}
```

```
Claude：注意 Code-Behind 只做三件事：
       1. 解析 UI 輸入
       2. 呼叫 Domain 層
       3. 顯示結果

       所有匯率查詢和換算邏輯都在 Domain 層，
       完全不依賴 System.Web，將來可以直接搬到 ASP.NET Core。

       準備好開始寫程式了。要建立 Git 分支嗎？
```

```
你：好。
```

```
Claude：分支名稱依照規範：

       > git checkout -b feature/EXP-001-jpy-currency-support

       Spec ID（EXP-001）對應到規格文件，
       之後所有 commit 都用 [EXP-001] 開頭。
```

### 這個情境的產出物

| 產出物 | 路徑 |
|---|---|
| 完整功能規格 | `dflow/specs/features/active/EXP-001-jpy-currency-support.md` |
| 術語表更新 | `dflow/specs/domain/glossary.md`（+2 筆：ExchangeRate、ReportDate） |
| 模型文件更新 | `dflow/specs/domain/expense/models.md`（+ExchangeRate VO、擴展 Money） |
| 規則文件更新 | `dflow/specs/domain/expense/rules.md`（+3 條） |
| Domain 程式碼 | `src/Domain/Expense/ValueObjects/ExchangeRate.cs` |
| Domain 程式碼 | `src/Domain/Expense/Interfaces/IExchangeRateRepository.cs` |
| Domain 程式碼 | 擴展 `src/Domain/Expense/ValueObjects/Money.cs`（+ConvertTo） |
| Git 分支 | `feature/EXP-001-jpy-currency-support` |

### 重點回顧

這個情境展示了完整的 SDD 流程：從需求理解 → 領域概念發掘 → 術語表更新 → 撰寫規格 → 實作規劃。注意 Claude Code 不是一次問完所有問題，而是順著對話自然推進，在對的時機問對的問題（例如「匯率依什麼日期查？」這個邊界情況的發掘）。

---

## 情境三：PR Review — 審查同事的程式碼

> **觸發的 Skill 檔案**：`pr-review-checklist.md`
>
> **背景**：同事完成了 EXP-001 日幣報銷功能的實作，發了 PR 請你 review。

### 開發者說了一句話

```
❯ claude

你：我要 review 一個 PR，是 EXP-001 日幣報銷功能。
   幫我用 PR checklist 檢查一下。
```

### Claude Code 開始逐項檢查

```
Claude：好，我來對照 PR Review Checklist 檢查。

       ── 規格符合度 ──

       > 讀取 dflow/specs/features/active/EXP-001-jpy-currency-support.md
       > 比對實作程式碼

       ✅ 規格存在
       ✅ 主要成功情境已實作（日幣輸入 → 匯率查詢 → 台幣換算）
       ✅ BR-01 已實作（依費用發生日查匯率）
       ✅ BR-02 已實作（匯率未設定時阻擋送出）
       ⚠️ BR-03 部分實作 — 換算後有四捨五入，但沒有用 Money.Round()，
          而是直接寫了 Math.Round()。應該呼叫我們在 BUG-042 建立的 Money.Round()。

       ── Domain 層品質 ──

       > 掃描 src/Domain/ 中的程式碼

       ✅ 沒有 System.Web 引用
       ✅ 沒有直接資料庫存取
       ⚠️ ExchangeRate.cs 有一行 HttpContext.Current.Cache —— 這是 Domain 層不允許的！
          快取機制應該在 Infrastructure 層實作，不是在 Domain 層。

       ── Code-Behind 厚度 ──

       > 檢查 ExpenseEntry.aspx.cs

       ✅ UI 輸入解析 → 呼叫 Domain → 顯示結果的模式正確
       ⚠️ 但是有一段匯率範圍檢查邏輯：
```

```csharp
// ExpenseEntry.aspx.cs 第 95-102 行
// 這是業務邏輯，應該在 Domain 層
if (rate.Rate > 1 || rate.Rate < 0.001m)
{
    lblError.Text = "匯率異常，請確認";
    return;
}
```

```
Claude：這段匯率範圍檢查是業務邏輯，應該移到 Domain 層。
       建議在 ExchangeRate 的建構式或工廠方法裡驗證。

       ── 術語一致性 ──

       ⚠️ 程式碼中使用 `exchangeRate` 但 glossary.md 定義的是 `ExchangeRate`（PascalCase）
          變數命名符合 C# 慣例，但類別名稱確認一致 ✅

       ── 技術債 ──

       ⚠️ 發現 ExpenseList.aspx.cs 裡有另一段硬編碼的匯率計算
          （看起來是之前寫死的測試用途），但沒有記錄到 tech-debt.md。

       ── 遷移準備度評分 ──

       這個 PR 我給 C 級：

       好的部分：有 spec、Domain 層結構正確、Code-Behind 大致精簡。
       需要改進：
       1. Domain 層有 HttpContext.Current.Cache（嚴重：違反零依賴原則）
       2. 匯率範圍檢查邏輯在 Code-Behind 而不是 Domain 層
       3. 沒用現有的 Money.Round() 而是重寫了一遍

       修完這三項就能到 A 級。要現在修還是記到 tech-debt？
```

### 這個情境的產出物

| 產出物 | 說明 |
|---|---|
| Review 結果 | 逐項檢查報告，含具體程式碼位置和修改建議 |
| 遷移準備度評分 | C 級，附改善到 A 級的具體步驟 |
| 新發現的技術債 | ExpenseList.aspx.cs 的硬編碼匯率 |

### 重點回顧

PR Review Checklist 不只看「程式碼能不能動」，它系統性地檢查五個面向：規格符合度、Domain 層純淨度、Code-Behind 厚度、術語一致性、技術債。遷移準備度評分（A-F）讓團隊對「這段程式碼離可遷移有多遠」有具體的衡量。

> 💡 Review 中發現的 HttpContext.Current.Cache 違規，就是 [DDD 觀念指引](DDD_MINDSET_SHIFT_tw.md) 觀念四所講的「Domain 層不依賴任何外部東西」。

---

## 情境四：開分支 — 準備開始一個新任務

> **觸發的 Skill 檔案**：`git-flow-integration.md`
>
> **背景**：開發者直接要建分支開始寫程式，但還沒有規格。

### 開發者說了一句話

```
❯ claude

你：我要開一個分支來做請假單的簽核流程。
```

### Claude Code 做閘門檢查

```
Claude：等一下，在建立 feature 分支之前，讓我做閘門檢查。

       > 搜尋 dflow/specs/features/ 中是否有請假簽核的規格
       > 沒有找到

       目前沒有「請假簽核流程」的規格文件。
       根據我們的流程，feature 分支需要在 spec 存在之後才能建立。

       分支的用途是這樣的：

       ┌──────────────────────────────────────────┐
       │  feature 分支閘門：                        │
       │  ✅ spec 存在於 dflow/specs/features/active/     │
       │  ✅ 至少一個 Given/When/Then               │
       │  ✅ Bounded Context 已識別                 │
       │  ✅ 關鍵領域概念已記錄                      │
       └──────────────────────────────────────────┘

       不用擔心，寫 spec 通常只需要 10-15 分鐘，
       而且能幫你在寫程式前想清楚邊界情況。

       要不要我現在引導你建立規格？
```

```
你：好吧，但我趕時間，能快一點嗎。
```

```
Claude：沒問題，我們用快速版。只抓最核心的部分：

       這個請假簽核流程，最基本的情境是什麼？
       比如：員工送出請假單 → 主管核准 → 扣假？
```

```
你：對，就是這樣。但超過三天的假要再加一層副總核准。
```

```
Claude：好，這樣就有兩個核心情境了。
       讓我快速建立 spec...

       > 建立 dflow/specs/features/active/LV-001-leave-approval-workflow.md

       規格的關鍵部分已經有了：
       - 兩個 Given/When/Then 情境
       - 一條核心業務規則（三天以上需副總核准）
       - 識別到 Leave Bounded Context

       現在可以建立分支了：

       > git checkout -b feature/LV-001-leave-approval-workflow

       你可以開始寫程式了。剩下的 spec 細節
       （邊界情況、Domain 層設計）可以邊做邊補。
```

### 這個情境的產出物

| 產出物 | 路徑 |
|---|---|
| 功能規格（草稿） | `dflow/specs/features/active/LV-001-leave-approval-workflow.md` |
| 術語表更新 | `dflow/specs/domain/glossary.md`（如有新術語） |
| Git 分支 | `feature/LV-001-leave-approval-workflow` |

### 重點回顧

這個情境展示了兩個重要的 Skill 特性：

1. **閘門機制**：不是死板地阻擋，而是在對的時候提醒。開發者可能覺得「我已經知道要做什麼了，直接開寫就好」，但 Skill 會溫和地提醒「先花 10 分鐘寫 spec」。

2. **Ceremony Scaling**：開發者說趕時間，Claude Code 就用精簡版流程，只抓最核心的情境和規則。不需要每次都填完 feature-spec template 的每一個欄位。

---

## Skill 觸發速查表

以下整理什麼情境會觸發哪些 Skill 檔案：

| 你說的話 | 觸發的流程 | 主要 Skill 檔案 |
|---|---|---|
| 「我要加一個新功能」 | 新功能流程 | `new-feature-flow.md` + `feature-spec.md` |
| 「有個 bug 要修」 | 修改現有流程 | `modify-existing-flow.md` + `lightweight-spec.md` |
| 「我要改現有的功能」 | 修改現有流程 | `modify-existing-flow.md` + `feature-spec.md` |
| 「幫我 review 這個 PR」 | PR 審查 | `pr-review-checklist.md` |
| 「我要開一個分支」 | Git Flow 閘門 | `git-flow-integration.md` |
| 「X 是什麼意思？」 | 術語查詢 | 查 `glossary.md`，沒有就建議新增 |
| 「接下來做什麼？」 | Sprint 規劃 | 檢查 `dflow/specs/features/backlog/` |

### Ceremony Scaling 速查

| 改什麼 | Spec 層級 | 要抽 Domain？ | 記技術債？ |
|---|---|---|---|
| 新功能 | 完整 spec | 必須 | 視情況 |
| 業務規則變更 | 標準 spec | 必須 | 必須 |
| Bug 修復（邏輯） | 輕量 spec | 評估 | 有發現就記 |
| 純 UI 修改 | 不需要 | 不需要 | 不需要 |
| 設定/環境 | 不需要 | 不需要 | 不需要 |
| 效能修復 | 輕量 spec | 評估 | 必須 |

---

## 接下來

如果你的下一個專案是用 ASP.NET Core 建新系統，請參考 **Core 版使用教學**，
那裡的情境會展示 Clean Architecture 四層結構、Aggregate 設計、Domain Events 等進階概念。
