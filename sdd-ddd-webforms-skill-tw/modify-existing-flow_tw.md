# 修改既有功能流程

當開發者說「我要修改/修正既有功能」時的逐步引導。

## 心態

修改既有功能是漸進式提取領域知識和業務邏輯的最佳機會。把每次修改都視為一個契機：
1. 記錄目前存在的東西（如果還沒有規格的話）
2. 將業務邏輯從 Code-Behind 抽離到 Domain 層
3. 為未來遷移記錄技術債

## 步驟 1：評估變更

詢問：
1. **要改什麼？** 取得具體的修改內容。
2. **為什麼？** 是 Bug 修復、新需求、還是行為變更？
3. **這個變更有多大？** 決定流程嚴謹度：
   - 純 UI 修正 → 跳過規格，直接協助
   - 業務邏輯變更 → 標準規格
   - 計算/規則的 Bug → 輕量規格

檢查既有資產：
- `specs/features/completed/` 中是否已有這個功能的規格？
- 涉及的領域概念是否已記錄在 `specs/domain/`？

## 步驟 2：記錄現有行為（如果沒有規格）

這很重要。在改任何東西之前，先記錄目前的狀態：

```
「在我們改之前，讓我幫你記錄一下目前的行為。
這樣我們有一個基準線，變更也可以追溯。」
```

建立一份狀態為 `in-progress` 的規格，包含：
- 目前的行為描述
- 目前的業務規則（從 Code-Behind 中提取）
- 清楚標記的擬議變更

在規格中使用這個模式：

```markdown
## 目前行為
Given [目前的狀態]
When [使用者操作]
Then [目前的結果]

## 變更後行為（本次 PR）
Given [相同或修改後的狀態]
When [使用者操作]
Then [新的預期結果]

## 變更原因
[為什麼要改——如有的話連結到 ticket/需求]
```

## 步驟 3：分析 Code-Behind

閱讀既有的 Code-Behind，識別：

### 可以抽離的業務邏輯
尋找：
- **計算** — 任何涉及數學運算、比較、或轉換的邏輯
- **驗證規則** — 任何檢查業務條件的 if/else
- **狀態轉換** — 狀態變更、審批流程
- **資料轉換** — 格式、幣別、單位之間的轉換

### 需要記錄的技術債
尋找：
- Code-Behind 中的直接 SQL 查詢
- 跨多個頁面重複的業務邏輯
- Magic number（例如 `if (status == 3)`）
- 用 Session/ViewState 儲存業務狀態
- 靜默吞掉例外的 try/catch 區塊
- 用字串串接組 SQL（SQL Injection 風險）

每個發現記錄到 `specs/migration/tech-debt.md`：
```markdown
- [ ] {檔案}:{行號} — {描述} — 嚴重度: {高|中|低}
```

## 步驟 4：評估抽離機會

對正在修改的程式碼，詢問：

```
「[X] 的業務邏輯目前在 {PageName}.aspx.cs 裡。
既然我們已經在改這段程式碼了，要不要順便把它抽離到
src/Domain/{Context}/？這樣做的好處：
- 可以寫測試
- 可以重複使用
- 為 ASP.NET Core 遷移做好準備」
```

決策框架：
- **現在就抽離**：如果邏輯本來就要大幅修改
- **現在就抽離**：如果邏輯在其他地方有重複，需要單一事實來源
- **延後抽離**：如果只是改一行，而且周圍的程式碼太糾纏
- **一定要記錄**：即使延後抽離，也要在 `tech-debt.md` 記錄這個機會

## 步驟 5：實作變更

如果要抽離到 Domain 層：

```csharp
// 修改前（Code-Behind）
protected void Calculate()
{
    decimal amount = decimal.Parse(txtAmount.Text);
    decimal rate = GetExchangeRate(ddlCurrency.SelectedValue);
    decimal result = Math.Round(amount * rate, 0); // JPY 沒有小數
    lblResult.Text = result.ToString("N0");
}

// 修改後（Domain 層）
// src/Domain/Expense/ValueObjects/Money.cs
public record Money(decimal Amount, Currency Currency)
{
    public Money ConvertTo(Currency target, ExchangeRate rate)
    {
        var converted = Amount * rate.Rate;
        return new Money(target.Round(converted), target);
    }
}

// Code-Behind 變得精簡：
protected void Calculate()
{
    var money = new Money(decimal.Parse(txtAmount.Text), selectedCurrency);
    var rate = _exchangeRateService.GetRate(selectedCurrency, Currency.TWD, reportDate);
    var result = money.ConvertTo(Currency.TWD, rate);
    lblResult.Text = result.Amount.ToString("N0");
}
```

## 步驟 6：更新文件資產

變更完成後：
1. 更新或建立功能規格
2. 如果業務規則有變更，更新 `specs/domain/{context}/rules.md`
3. 如果領域模型有變更，更新 `specs/domain/{context}/models.md`
4. 如果出現新術語，更新 `specs/domain/glossary.md`
5. 將發現的問題記錄到 `specs/migration/tech-debt.md`
6. 更新規格狀態

## 輕量規格範例（Bug 修復用）

小型 Bug 修復用輕量規格就夠了：

```markdown
---
id: BUG-042
title: 修正費用計算中的四捨五入不一致
status: in-progress
bounded-context: Expense
created: 2025-02-12
---

## 問題
Page A 使用 Math.Round(amount, 0, MidpointRounding.AwayFromZero)（四捨五入）
Page B 使用 Math.Floor(amount)（無條件捨去）
兩者應該使用相同的捨入規則。

## 預期行為
Given 費用金額為 123.5 TWD
When 在任何頁面顯示
Then 應顯示 124（依會計準則四捨五入）

## 根因
重複的計算邏輯——已記錄到 tech-debt.md

## 修復方式
將捨入邏輯抽離到 Domain 層的 Money.Round()，兩個頁面都呼叫它。
```
