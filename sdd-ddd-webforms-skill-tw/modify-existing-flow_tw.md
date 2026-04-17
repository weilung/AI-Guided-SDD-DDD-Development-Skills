# 修改既有功能流程

當開發者打 `/dflow:modify-existing` 或 `/dflow:bug-fix`（或自然語言表達修改意圖、經安全網確認後）的逐步引導。

**本流程的 Phase Gate 位置**（跨越前要停下來等確認）：
- Step 2 → Step 3（基準線記錄完 → 分析 Code-Behind）
- Step 4 → Step 5（抽離決策完 → 開始實作）
- Step 5 → Step 6（實作完成 → 更新文件資產）

其餘 step 間的轉換都是 **step-internal**：只宣告「Step N 完成，進入 Step N+1」，不等確認。完整透明度規則見 `SKILL_tw.md` § 工作流程透明度。

**`/dflow:bug-fix` 的 ceremony 調整**：走輕量路線——用文末的輕量規格模板取代完整 feature-spec；Step 4（抽離評估）預設「延後並記錄到 tech-debt.md」，除非 bug 本身就在可抽離的邏輯裡。

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

**→ Step-internal 轉換**：Step 1 完成。宣告「Step 1 完成（變更已評估，ceremony 等級已決定），進入 Step 2：記錄現有行為」後繼續。

## 步驟 2：記錄現有行為（如果沒有規格）

這很重要。在改任何東西之前，先記錄目前的狀態：

```
「在我們改之前，讓我幫你記錄一下目前的行為。
這樣我們有一個基準線,變更也可以追溯。」
```

建立一份狀態為 `in-progress` 的規格,包含：
- 目前的行為描述
- 目前的業務規則(從 Code-Behind 中提取)
- 明確標記的擬議變更——用下面的 **Delta 格式**

### Delta 規格格式(修改用)

用 ADDED / MODIFIED / REMOVED / RENAMED 加一個可選的 UNCHANGED 段。每條規則都保留 Given/When/Then；Delta 段只存在於本次 spec 裡,**不**累積到 `specs/domain/{context}/behavior.md`(git 歷史已經覆蓋變更軌跡)。

```markdown
## 行為變更（Delta）

### ADDED — 新增的行為
#### 規則：BR-NN 規則名稱
Given {狀態}
When {操作}
Then {新的預期結果}

### MODIFIED — 修改的行為
#### 規則：BR-NN 規則名稱
**原本**：Given … When … Then {舊結果}
**改為**：Given … When … Then {新結果}
**原因**：{為什麼改}

### REMOVED — 移除的行為
#### 規則：BR-NN 規則名稱
**原因**：{為什麼移除}

### RENAMED — 重新命名
#### 規則：{舊名稱} → {新名稱}
**原因**：{為什麼改名，例如術語演化 / 對齊 glossary}

### UNCHANGED — 明確不受影響（可選）
- BR-003 金額上限
- BR-005 提交後不可修改
```

**段落規則**：
- 每個行為變更都要用 **ADDED / MODIFIED / REMOVED / RENAMED** 分類；沒條目的子段就省略。
- `MODIFIED` 必填「原本 / 改為」對照，讓 reviewer 不用猜 before/after。
- `RENAMED` 只處理命名（例如「簽核」→「審批」）。如果行為也變了，拆成 RENAMED + MODIFIED 兩條。
- `UNCHANGED` **建議但非必填**；regression 風險高或 MODIFIED 條目很多時主動填。
- 一定要搭配 `## 變更原因`（為什麼有這個 PR——ticket / 利害關係人需求）。

### 系統性 Baseline Capture（沒有既有 spec 時）

如果被修改的功能沒有現成的 spec，趁這次機會做更廣泛的 baseline capture——不只記錄這次要改的單一行為。主動：

1. 讀取相關的 Code-Behind 檔案（正在改的頁面 + 共用邏輯的頁面）
2. 提取所有找到的業務規則（if/else 條件、計算、驗證）
3. 識別領域概念（潛在的 Entity、Value Object、Service）
4. 檢查是否有跨頁面的重複邏輯
5. 將發現記錄到對應的 domain docs（`models.md`、`rules.md`）和 `tech-debt.md`

這是**機會主義**策略——「趁我們已經在這裡了，順便多看一些」。不需要做完整的 codebase 掃描；範圍限定在被修改的功能和它的鄰近頁面。把發現分享給開發者：

```
「因為這個功能還沒有 spec，我多看了一下相關的 Code-Behind。發現：
- {PageName}.aspx.cs 裡有 3 條業務規則（已記到 rules.md）
- 和 {OtherPage}.aspx.cs 有重複的驗證邏輯（已記到 tech-debt.md）
- 第 {N} 行的計算邏輯裡藏了一個潛在的 Money value object
這樣我們改之前有更完整的基準線。」
```

**→ Phase Gate：Step 2 → Step 3**

向開發者宣告：
> 「基準線已備妥——現有行為已記錄，擬議變更也標示清楚了。要開始分析 Code-Behind（找可抽離邏輯和技術債）嗎？打 `/dflow:next` 或回「好」繼續。」

等確認後才進 Step 3。

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

**→ Step-internal 轉換**：Step 3 完成。宣告「Step 3 完成（Code-Behind 分析完、技術債已記錄），進入 Step 4：評估抽離機會」後繼續。

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

### 產生實作任務清單（僅限完整 spec）

如果這次修改用的是完整 feature-spec 模板（不是輕量規格），AI 在計畫定稿後產生具體任務清單，寫入 spec 的「實作任務」段。格式與 new-feature-flow Step 5 一致：`[LAYER]-[NUMBER]：描述`（DOMAIN / PAGE / DATA / TEST）。bug-fix 走輕量規格時跳過這步。

**→ Phase Gate：Step 4 → Step 5**

向開發者宣告：
> 「抽離決策已定——{現在就抽離 / 延後並記錄}。要開始實作了嗎？打 `/dflow:next` 前進，或先調整抽離範圍。」

等確認後才進 Step 5。

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

**→ Phase Gate：Step 5 → Step 6**

向開發者宣告：
> 「實作看起來完成了。要開始跑完成流程 checklist（驗證 Delta 覆蓋、更新 domain 文件、歸檔 spec）嗎？打 `/dflow:next` 前進。」

等確認後才進 Step 6。這個 Phase Gate 是完成流程 checklist 的觸發點——不可略過。

## 步驟 6：更新文件資產

由 Step 5 → Step 6 Phase Gate 觸發。AI 依下面順序跑 checklist，**不可**略過任何一段。bug-fix 走輕量規格時，標註「（僅限完整 spec）」的項目可以跳過。

### 6.1 驗證 — AI 可獨立執行

AI 一次檢查完所有項目，用 ✓/✗ 一份列表回報後才開始動文件。標註 *(6.3 後)* 的項目要等 6.3 合併動作完成後再驗證：

- [ ] Delta 段的每條 ADDED / MODIFIED / REMOVED / RENAMED 都有實作或測試覆蓋
- [ ] Domain 層沒有 `System.Web` 引用（grep `src/Domain/`）
- [ ] 如果 Step 4 決定「現在就抽離」，抽離出的邏輯已放進 `src/Domain/`，純 C# 無 WebForms 依賴
- [ ] `實作任務` 段：全部勾選，或未勾選項目明確標註為 follow-up（連結到 spec 或 tech-debt）*（僅限完整 spec）*
- [ ] *(6.3 後)* `specs/domain/{context}/behavior.md` 對 ADDED / MODIFIED 條目的每個 `BR-*` 都有對應 section anchor；REMOVED 條目的 anchor 已刪除（`/dflow:verify` 的機械輸入）
- [ ] *(6.3 後)* `specs/domain/{context}/behavior.md` 的 `last-updated` 晚於本 spec 的 `created` 日期（機械 drift 防線）

任何一項失敗就回報差距並暫停——不要往下進 6.2。

### 6.2 驗證 — 需開發者確認

AI 一次列一項、等開發者確認後再列下一項：

- [ ] 修改有沒有**忠實表達** Delta 條目的意圖？（AI 列 delta → 實作位置，開發者判斷）
- [ ] Step 3 分析時有沒有漏記什麼技術債？
- [ ] 如果延後抽離，`tech-debt.md` 的條目夠不夠清楚，讓未來接手的人看得懂？
- [ ] 合併進 `behavior.md` 的情境是否**忠實表達 Delta 的最終行為**？（AI 列出更新後的 anchor，開發者判斷）
- [ ] 完成後要不要把 spec 的 `實作任務` 段**摺疊 / 移除**？*（僅限完整 spec——看團隊慣例）*

一次問一項，不要一次倒五個。

### 6.3 文件更新

- [ ] 更新或建立 feature / bug spec；`status` 改為 `completed`
- [ ] `specs/domain/{context}/rules.md` — 業務規則已更新
- [ ] `specs/domain/{context}/models.md` — 領域模型已更新
- [ ] `specs/domain/glossary.md` — 新術語/重新命名已加入（Delta 的 RENAMED 條目這裡要對應更新）
- [ ] `specs/domain/{context}/behavior.md` — 依 Delta 結果更新行為情境（合併最終狀態，不是 Delta 標記本身）。子步驟：
      - 把 Phase 3 draft 段落（B3 中途同步）轉為正式段落
      - 更新對應 `rules.md` anchor 的 `last-updated` 日期（B4）
- [ ] `behavior.md` 草稿清理 — 若 Delta 中途放棄，保留 `## 提案中變更` 段的歷史，或明確 REMOVE
- [ ] `specs/migration/tech-debt.md` — 實作中發現的技術債已記錄

### 6.4 歸檔

- [ ] spec 檔案搬到 `specs/features/completed/`（如果本次流程有開 spec）

只有 6.4 做完才能宣告「變更完成」。

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
