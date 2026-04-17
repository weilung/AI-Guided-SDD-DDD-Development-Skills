# SDD/DDD Workflow Skill 使用教學 — WebForms 版（P001–P008 實作後）

> 本文件展示 **P001 Hybrid 設計 + P002–P008 後續強化** 實作後的使用體驗。
> 核心面向：指令化主入口、auto-trigger 安全網、流程狀態透明、Delta 規格格式、behavior.md / rules.md A+C 結構、`實作任務` 段、四層完成 checklist、`/dflow:verify` 漂移防線、Brownfield 基準線補寫。
> 每個情境從開發者的一句話（或一個指令）開始，完整呈現 AI 引導的對話過程和最終產出物。

---

## 先看這張表：原版 vs 新版的差別

| 面向 | 原版（純 auto-trigger） | 新版（P001–P008） |
|---|---|---|
| **入口** | 只靠自然語言被動觸發，容易沒觸發或觸發錯 | 可用 8 個 slash command 明確叫用（新增 `/dflow:verify`）|
| **判斷流程** | AI 默默決定，猜錯了才能事後救 | AI 判斷後**停下來讓你背書**，避免走錯 |
| **進度感** | 做到一半不知道自己在哪 | 隨時 `/dflow:status` 可查；階段轉換會告知 |
| **離開流程** | 不明確，只能「自由發揮」 | `/dflow:cancel` 明確退出 |
| **修改既有功能** | 重寫整段描述，看不出 before/after | Delta 格式（ADDED / MODIFIED / REMOVED / RENAMED） |
| **規格結構** | 單一檔案，情境和規則混在一起 | A+C 結構：`rules.md` 宣告索引 + `behavior.md` 情境細節 |
| **完成檢查** | AI 憑記憶跑 checklist | 四層分工（AI 可獨立驗證 / 需開發者確認 / 文件更新 / 歸檔）|
| **文件漂移** | 只能人工比對 | `/dflow:verify` 機械檢查 |
| **既有系統補寫** | 沒有引導 | Brownfield 基準線補寫路徑 |
| **對新成員** | 「Skill 是不是啟動了？」心理摩擦 | 可預測、可驗證，心理負擔小 |

---

## 指令速查

| 指令 | 用途 |
|---|---|
| `/dflow:new-feature` | 進入新功能流程 |
| `/dflow:modify-existing` | 進入修改現有功能流程（Delta 格式：ADDED / MODIFIED / REMOVED / RENAMED）|
| `/dflow:bug-fix` | 進入 Bug 修復流程（modify-existing 的輕量版）|
| `/dflow:pr-review` | 進入 PR 審查流程（Step 0 先讀 spec intent）|
| `/dflow:verify` | 機械 drift 檢查——比對 spec / rules / behavior / 實作是否一致（P008）|
| `/dflow:status` | 查看目前流程、所在 step、已完成與待完成項 |
| `/dflow:next` | 確認進入下一階段 |
| `/dflow:cancel` | 中止目前流程，回到自由對話 |

**自然語言可替代 `/dflow:next`**：「好」、「對」、「繼續」、「可以」、「OK」、「沒問題」，或**直接回答下一階段需要的問題（隱性確認）**。

---

## 目錄

- [事前準備：安裝 Skill 到你的專案](#事前準備安裝-skill-到你的專案)
- [情境一：修 Bug — 費用計算四捨五入不一致（`/dflow:bug-fix` 指令）](#情境一修-bug--費用計算四捨五入不一致bug-fix-指令)
- [情境二：新功能 — 支援日幣報銷（自然語言 + auto-trigger 安全網）](#情境二新功能--支援日幣報銷自然語言--auto-trigger-安全網)
- [情境三：PR Review — 審查同事的程式碼（`/dflow:pr-review` 指令）](#情境三pr-review--審查同事的程式碼pr-review-指令)
- [情境四：開分支但沒 spec（auto-trigger 安全網示範）](#情境四開分支但沒-specauto-trigger-安全網示範)
- [情境五：Brownfield — 既有功能的基準線補寫（P007a）](#情境五brownfield--既有功能的基準線補寫p007a)
- [Skill 觸發速查表（更新版）](#skill-觸發速查表更新版)

---

## 事前準備：安裝 Skill 到你的專案

### 0. 先讀 DDD 觀念指引（10-15 分鐘）

如果你的團隊對 DDD 還不熟，建議每位開發者先讀一次
**[給資深工程師的 DDD 觀念翻轉指南](DDD_MINDSET_SHIFT_tw.md)**。

### 1. 放置 Skill 檔案

將 `sdd-ddd-workflow/` 資料夾放到你的專案根目錄：

```
MyWebFormsProject/
├── sdd-ddd-workflow/          ← Skill 檔案
│   ├── SKILL.md
│   ├── references/
│   └── templates/
├── src/
├── specs/                     ← 第一次使用時建立
└── CLAUDE.md                  ← 第一次使用時建立
```

### 2. 建立初始目錄結構

第一次使用時，請 Claude Code 幫你建立 specs 目錄和 CLAUDE.md：

```
❯ claude

你：請讀取 sdd-ddd-workflow/SKILL.md 和 templates/CLAUDE.md，
   幫我建立這個 WebForms 專案的初始 specs 目錄結構和 CLAUDE.md。
   系統名稱是「費用報表系統」。
```

Claude Code 會根據 SKILL.md 中定義的專案結構，一次建好所有目錄和初始檔案。

### 3. 之後的每次使用

Skill 檔案在專案目錄中，Claude Code 會自動在適當時機參考。你可以用：
- **指令**（推薦）：`/dflow:new-feature`、`/dflow:bug-fix` 等直接進入流程
- **自然語言**：照平常說話即可，auto-trigger 會判斷並要求你背書

---

## 情境一：修 Bug — 費用計算四捨五入不一致（`/dflow:bug-fix` 指令）

> **觸發的 Skill 檔案**：`modify-existing-flow.md`、`lightweight-spec.md`
>
> **背景**：QA 回報費用報表的合計金額，在「費用明細頁」和「費用總覽頁」顯示不同數字。
>
> **📌 對比原版**：原版是自然語言描述 → AI 默默判斷「這是計算邏輯的 bug」；新版可**直接用指令明確進入**。

### 開發者用指令明確進入流程

```
❯ claude

你：/dflow:bug-fix 費用報表有個 bug，明細頁和總覽頁的合計金額不一樣，
   明細頁是 1,234 但總覽頁是 1,233。
```

### Claude Code 告知流程結構，開始 Step 1

```
Claude：收到，進入 bug-fix 流程。

       ┌─── Bug Fix Flow ───┐
       │ Step 1: 評估變更 ← 現在這裡
       │ Step 2: 捕捉現有行為（輕量規格或 Delta）
       │ Step 3: 分析根因（含可選抽離）
       │ Step 4: 實作修復
       │ --- Phase gate ---
       │ Step 5: 完成 checklist（5.1 AI / 5.2 Dev / 5.3 Docs / 5.4 Archive）
       └────────────────────┘

       先釐清幾件事：
       1. 這個差異是在什麼幣別下發生的？所有幣別都有問題還是特定幣別？
```

```
你：台幣，金額有小數的時候就會不一樣。
```

```
Claude：了解，很可能是四捨五入方式不一致。讓我先看有沒有相關規格...

       > 搜尋 specs/domain/ 和 specs/features/completed/
       > 沒有找到關於四捨五入的規格

       目前沒有文件記錄四捨五入規則。在修之前，先用輕量規格記下來。
       這只需要幾分鐘，但能確保修完後有跡可循。

       正確的四捨五入規則是什麼？依會計標準，台幣應該四捨五入到整數，對嗎？
```

```
你：對，四捨五入到整數。
```

```
Claude：Step 1 完成，進入 Step 2：捕捉現有行為。
       （注意：step 內轉換只告知，不需要你確認——你的回答就是隱性確認）

       > 建立 specs/features/active/BUG-042-rounding-inconsistency.md
```

Claude Code 會根據 `templates/lightweight-spec.md` 產生：

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
```

### 隨時可以 `/dflow:status`

假設此時你被同事打斷去處理別的事，隔 20 分鐘回來。原本的你會困惑「我剛剛進行到哪？」，現在可以直接問：

```
你：/dflow:status
```

```
Claude：目前流程：bug-fix（BUG-042 四捨五入不一致）
       目前 step：Step 3 — 分析根因（含可選抽離）

       已完成：
       - [x] Step 1: 評估變更 — 確認是計算邏輯 bug、TWD 幣別受影響
       - [x] Step 2: 捕捉現有行為 — BUG-042 lightweight spec 已建立

       進行中：
       - [ ] Step 3: 分析根因 — 剛問完你「要不要抽離到 Domain 層」，等你決定

       待完成：
       - [ ] Step 4: 實作修復
       - [ ] Step 5: 更新文件與歸檔（glossary、models、rules、tech-debt）

       等你回覆抽離意願。
```

### Claude Code 引導抽離到 Domain 層（Step 3）

```
Claude：這個 bug 的根因是四捨五入邏輯分散在 Code-Behind 裡。
       既然我們已經要改這段程式碼了，這是很好的機會
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

### Phase gate：實作完成 → 進入文件更新

Step 4 結束時遇到 phase gate（主要里程碑），AI 會**告知並提供選項**：

```
Claude：Step 4 完成（兩個頁面都改好了）。

       --- Phase gate: Step 4 → Step 5 ---

       即將進入 Step 5：更新相關文件（glossary、models、rules、tech-debt）
       並把 spec 歸檔到 completed/。

       → 回覆「好」/`/dflow:next` 繼續
       → 或 `/dflow:cancel` 先暫停（例如想先跑測試驗證）
       → 或 `/dflow:status` 看剩多少項
```

```
你：我先跑一下測試 /dflow:cancel
```

```
Claude：已中止 bug-fix 流程（未完成 Step 5 文件更新）。
       spec 保持在 in-progress 狀態，測試完後可用 `/dflow:bug-fix` 或自然語言繼續。
```

**測試通過，回來繼續**：

```
你：/dflow:bug-fix 測試過了，接續 BUG-042
```

```
Claude：偵測到 BUG-042-rounding-inconsistency.md 為 in-progress，
       接續進度。目前在 Step 5 的入口。依四層完成 checklist 逐段跑：

       ─── 5.1 AI 可獨立驗證（一次秀完 ✓/✗）───
       ✓ Delta 段（輕量規格裡對應「預期行為」）每條都有實作或測試覆蓋
       ✓ 修復在正確的層（Domain 層：Money.Round()）
       ✓ 沒有業務邏輯洩漏回 Code-Behind
       ✓ 實作任務段：本次為輕量規格，N/A
       （標 *(post-5.3)* 的 behavior.md anchor 檢查延後到 5.3 後再跑）

       ─── 5.2 需開發者確認（一次問一項）───
       - 這次修復有忠實表達四捨五入規則嗎？（AI 列出 Money.Round() 對 BR-R01 的對應）

你：對
       Claude：—— 有沒有發現需要記錄的技術債？

你：另一個頁面 ExpenseApproval.aspx 好像也用了 Math.Floor，但我先不改
       Claude：好，這條記到 tech-debt.md

       ─── 5.3 文件更新 ───
       ✅ specs/domain/glossary.md — 新增「Money」和「Currency」術語
       ✅ specs/domain/expense/models.md — 新增 Money Value Object 定義
       ✅ specs/domain/expense/rules.md — 新增四捨五入規則 BR-R01（宣告索引）
       ✅ specs/domain/expense/behavior.md — 新增 BR-R01 的情境 section anchor（A+C 結構）
       ✅ specs/migration/tech-debt.md — 記錄 ExpenseApproval.aspx 的 Math.Floor 殘留

       ─── 5.1 post-5.3 補驗（剛剛標 *(post-5.3)* 的項）───
       ✓ behavior.md 對 BR-R01 有 section anchor
       ✓ behavior.md 的 last-updated（2026-02-12）晚於 spec 的 created 日期

       ─── 5.4 歸檔 ───
       ✅ 規格狀態改為 completed，檔案移到 specs/features/completed/

       Bug-fix 流程完成。如需再次確認 spec 與實作沒漂移，可用 `/dflow:verify`。
```

### 這個情境的產出物

| 產出物 | 路徑 |
|---|---|
| 輕量規格 | `specs/features/completed/BUG-042-rounding-inconsistency.md` |
| Domain 程式碼 | `src/Domain/Expense/ValueObjects/Money.cs` |
| 術語表更新 | `specs/domain/glossary.md`（+2 筆） |
| 模型文件更新 | `specs/domain/expense/models.md` |
| 規則宣告（A 側） | `specs/domain/expense/rules.md`（+BR-R01） |
| 行為情境（C 側） | `specs/domain/expense/behavior.md`（+BR-R01 section anchor） |
| 技術債記錄 | `specs/migration/tech-debt.md`（+3 筆） |

### 重點回顧

P001–P008 在這情境帶來四個改變：
1. **指令明確**：`/dflow:bug-fix` 讓進入流程不用靠 AI 猜測（P001）。
2. **可中斷可續作**：`/dflow:cancel` 讓你可以跳出去跑測試，回來 `/dflow:bug-fix` 接續進度（P001）。
3. **進度透明**：`/dflow:status` 隨時可查；Step 5 的 5.1–5.4 分層避免 AI「一口氣倒完」（P005a/b）。
4. **A+C 結構自動維護**：`rules.md` 和 `behavior.md` 同步，`behavior.md` 的 anchor 和 `last-updated` 由 5.1 機械檢查（P003 / P005b）。

> 💡 這裡用到了兩個 DDD 觀念：**行為放在 Entity 裡**（Money.Round() 而不是到處寫 Math.Round）和 **Value Object 不只是 DTO**（Money 在建構時就確保合法狀態）。詳見 [DDD 觀念指引](DDD_MINDSET_SHIFT_tw.md) 的觀念一和觀念三。

---

## 情境二：新功能 — 支援日幣報銷（自然語言 + auto-trigger 安全網）

> **觸發的 Skill 檔案**：`new-feature-flow.md`、`feature-spec.md`、`context-definition.md`
>
> **背景**：公司開始有日本出差的需求，費用報表系統需要支援日幣報銷和匯率轉換。
>
> **📌 對比原版**：原版 AI 直接判斷「好的，支援日幣報銷」就開始問問題；新版 AI 會**先判斷並停下來要確認**，避免走錯流程。

### 開發者用自然語言描述（沒打指令）

```
❯ claude

你：我要加一個新功能，讓費用報表支援日幣報銷，
   需要能輸入日幣金額然後自動轉換成台幣。
```

### Auto-trigger 啟動，判斷後停下要確認（這是新行為）

```
Claude：我判斷這是 **new-feature-flow** 的任務（新增幣別支援、涉及領域
       概念擴展：Currency、ExchangeRate）。

       你可以：
       - `/dflow:new-feature` 明確啟動
       - 回覆「好」/「繼續」確認用這個流程
       - 或告訴我判斷錯了（其實是 bug 修復？修改既有功能？）
```

```
你：好
```

```
Claude：進入 new-feature-flow。

       ┌─── New Feature Flow ───┐
       │ Step 1: 需求理解 ← 現在這裡
       │ Step 2: 識別 Bounded Context
       │ Step 3: 領域概念發掘
       │ Step 4: 撰寫規格（含 Phase markers + 實作任務段）
       │ --- Phase gate ---
       │ Step 5: 實作規劃
       │ Step 6: 開 Git 分支
       │ --- Phase gate ---
       │ Step 7: 實作
       │ --- Phase gate ---
       │ Step 8: 完成 checklist
       │   8.1 AI 可獨立驗證
       │   8.2 需開發者確認
       │   8.3 文件更新（glossary / models / rules / behavior / tech-debt）
       │   8.4 歸檔
       └─────────────────────────┘

       讓我先了解背景：這個需求是誰提出的？
```

```
你：財務部提出的，因為開始有日本出差的費用需要報銷。
```

```
Claude：了解。讓我看看現有的資產...

       > 搜尋 specs/domain/expense/
       > 找到 models.md 中有 Money Value Object（在修 BUG-042 時建立的）
       > 找到 glossary.md 中有 Money、Currency 術語

       很好，之前修 Bug 時已經建立 Money 和 Currency。
       這次的新功能可以在這個基礎上擴展。

       Step 1 完成，進入 Step 2。目前 Currency 只支援 TWD，對嗎？
```

```
你：對，目前只有台幣。
```

（注意：上面這兩次的 step 轉換——Step 1→2、Step 2→3——AI 只用一行告知，不需要等你 `/dflow:next`，因為你繼續提供資訊就是隱性確認。這就是**三級透明化**中最輕的「Step 內部推進」。）

### 持續深入領域概念（Step 2-3）

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

```
Claude：這裡出現了一個新的領域概念：「匯率」。
       讓我確認它屬於哪個 Bounded Context。

       匯率是由財務部管理的，跟費用報銷的核心流程不太一樣。
       但它比較像是一個查詢資料——報銷時去查「當月的 JPY→TWD 匯率是多少」。

       我建議把它放在 Expense Context 裡，因為：
       - 目前規模還不大（只有 JPY→TWD 一種）
       - 它的存在是為了服務費用報銷

       等未來幣別多了，再考慮拆出獨立的 Currency Context。你覺得呢？
```

```
你：同意，先放 Expense 裡。
```

```
Claude：Step 2 完成（歸屬 Expense），進入 Step 3：領域概念發掘。

       新概念（需要加入術語表）：
       - ExchangeRate（匯率）：兩個幣別之間的轉換比率
       - ReportDate（報表日期）：用來決定適用哪個月的匯率

       擴展的概念：
       - Currency：新增 JPY，目前有 TWD、JPY
       - Money：需要支援 ConvertTo() 方法

       需要的新介面：
       - IExchangeRateRepository：查詢匯率用

       這些我先更新到術語表和 models.md 裡。Step 3 完成，進入 Step 4：撰寫規格。
```

### 撰寫規格（Step 4）

```
Claude：現在來寫功能規格。我幫你建立：

       > 建立 specs/features/active/EXP-001-jpy-currency-support.md

       先走過主要成功情境。使用者在報銷時選擇日幣，
       Given 使用者正在填寫費用報銷單，
       When 他選擇幣別為 JPY 並輸入金額 10,000，
       Then 會發生什麼？
```

```
你：系統自動查出當月匯率，換算成台幣顯示出來。
```

```
Claude：好，是即時換算顯示。再往下想：如果那個月的匯率還沒設定呢？
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

最終產出的規格摘錄（P004 的 Phase markers 會標示目前進度，Step 5 結束後會補上 `實作任務` 段）：

```markdown
---
id: EXP-001
title: 支援日幣報銷
status: in-progress
phase: Phase 3 — Spec 定稿（通過 Step 4 → 5 phase gate）
bounded-context: Expense
created: 2026-02-15
---

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

## 實作任務（Step 5 規劃後由 AI 自動生成）

- [ ] DOMAIN-01：擴展 `Currency` 加入 JPY、定義 DecimalPlaces
- [ ] DOMAIN-02：新增 `ExchangeRate` Value Object（不可變、建構時驗證）
- [ ] DOMAIN-03：`Money.ConvertTo(targetCurrency, rate)` 方法 + 單元測試
- [ ] DOMAIN-04：`IExchangeRateRepository` 介面
- [ ] DATA-01：`ExchangeRate` EF Fluent 對應 + Repository 實作
- [ ] PAGE-01：`ExpenseEntry.aspx` 幣別下拉、換算顯示
- [ ] PAGE-02：匯率未設定時的錯誤訊息
- [ ] TEST-01：整合測試涵蓋 BR-01 ~ BR-03 + EC-01 ~ EC-03
```

### Phase gate：spec 完成 → 實作規劃

```
Claude：規格 EXP-001-jpy-currency-support.md 寫好了，包含：
       - 2 個 Gherkin 情境（主要成功 + 匯率未設定）
       - 3 條業務規則（BR-01 ~ BR-03）
       - 3 個邊界情況（EC-01 ~ EC-03）

       --- Phase gate: Step 4 → Step 5 ---

       即將進入 Step 5：實作規劃。要繼續嗎？
       → 回覆「好」/`/dflow:next` 繼續
       → 或 `/dflow:cancel` 暫停（例如想先給同事 review spec）
       → 或 `/dflow:status` 檢視整體進度
```

```
你：先讓 Alice review 一下 spec /dflow:cancel
```

```
Claude：已中止 new-feature-flow。spec 保持在 in-progress 狀態，之後可
       用 `/dflow:new-feature` 或自然語言繼續。
```

### 隔天回來接續

```
❯ claude

你：/dflow:new-feature 繼續之前那個日幣報銷功能
```

```
Claude：偵測到 specs/features/active/EXP-001-jpy-currency-support.md 已存在
       且狀態 in-progress（上次停在 Step 4 → Step 5 的 phase gate）。

       要接續先前進度嗎？
       → 回覆「好」接續（進入 Step 5 實作規劃）
       → 或「從頭」重新開始
```

```
你：好
```

### Claude Code 引導實作規劃（Step 5）

```
Claude：Domain 層需要：
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

       --- Phase gate: Step 5 → Step 6 ---

       規劃完成，即將進入 Step 6：開 Git 分支。要繼續嗎？
```

```
你：好
```

```
Claude：分支名稱依照規範：

       > git checkout -b feature/EXP-001-jpy-currency-support

       Spec ID（EXP-001）對應到規格文件，
       之後所有 commit 都用 [EXP-001] 開頭。

       --- Phase gate: Step 6 → Step 7 ---

       分支開好了，即將進入 Step 7：實作。要現在開始寫程式嗎？
```

```
你：好，開始
```

### Step 7 完成 → Step 8：四層完成 checklist

實作完成後進入 Step 8。AI 會依四層分工跑 checklist，不可略過：

```
Claude：實作完成，所有 DOMAIN-01 ~ TEST-01 任務都已勾選。

       --- Phase gate: Step 7 → Step 8 ---

       進入完成 checklist。先跑 8.1（AI 可獨立驗證）：

       ─── 8.1 AI 可獨立驗證（一次秀完 ✓/✗）───
       ✓ 每個 BR / EC 都有實作或測試覆蓋
       ✓ Domain 層零 NuGet 依賴（檢查 `Expense.Domain.csproj`）
       ✓ Code-Behind 沒有業務邏輯（三件事：parse → call Domain → display）
       ✓ 新增 Domain Event 在實作裡都有 raise（本次無 Domain Event，N/A）
       ✓ 實作任務段：全部勾選
       （*(post-8.3)* 項：behavior.md anchor 檢查與 last-updated 比對，等 8.3 完成後補跑）

       ─── 8.2 需開發者確認（一次問一項）───
       Claude：— 合併進 `behavior.md` 的情境有忠實表達 BR-01 ~ BR-03 的預期行為嗎？

你：有
       Claude：— 實作過程有發現需要記下的技術債嗎？

你：ExpenseList.aspx 還有一段硬編碼的匯率計算，目前沒改
       Claude：好，記到 tech-debt.md

       Claude：— 本次是完整 spec，`實作任務` 段要摺疊 / 移除嗎？

你：保留，方便日後查
       Claude：好。

       ─── 8.3 文件更新 ───
       ✅ specs/domain/glossary.md — 新增 ExchangeRate、ReportDate
       ✅ specs/domain/expense/models.md — 擴展 Money、新增 ExchangeRate
       ✅ specs/domain/expense/rules.md — 新增 BR-01 ~ BR-03（宣告索引）
       ✅ specs/domain/expense/behavior.md — 把 Phase 3 draft 段落轉為正式段落；
          對應 rules.md anchor 的 last-updated 更新為 2026-02-17
       ✅ specs/migration/tech-debt.md — 新增 ExpenseList.aspx 硬編碼匯率

       ─── 8.1 post-8.3 補驗 ───
       ✓ behavior.md 對 BR-01 / BR-02 / BR-03 都有 section anchor
       ✓ behavior.md 的 last-updated（2026-02-17）晚於 spec 的 created（2026-02-15）

       ─── 8.4 歸檔 ───
       ✅ spec 狀態 → completed；移到 specs/features/completed/

       new-feature-flow 完成。
```

### Step 8 後用 `/dflow:verify` 做漂移複檢（P008）

```
你：/dflow:verify
```

```
Claude：執行 drift verification（機械層檢查）：

       ✓ specs/features/completed/EXP-001 對應的 BR-* 在 rules.md 全部存在
       ✓ rules.md 的每個 BR 都在 behavior.md 有對應 section anchor
       ✓ behavior.md last-updated（2026-02-17）晚於所有引用 spec 的 created
       ✓ 實作碼 (src/Domain/Expense/) 沒有出現 rules.md 沒宣告的 BR-* 標籤

       全部通過。如有需要，可把本次結果貼到 PR 留言。
```

### 這個情境的產出物

| 產出物 | 路徑 |
|---|---|
| 完整功能規格（含 Phase markers + 實作任務段） | `specs/features/completed/EXP-001-jpy-currency-support.md` |
| 術語表更新 | `specs/domain/glossary.md`（+2 筆：ExchangeRate、ReportDate） |
| 模型文件更新 | `specs/domain/expense/models.md`（+ExchangeRate VO、擴展 Money） |
| 規則宣告（A 側） | `specs/domain/expense/rules.md`（+BR-01~03，含 last-updated） |
| 行為情境（C 側） | `specs/domain/expense/behavior.md`（+3 個 section anchor） |
| Domain 程式碼 | `src/Domain/Expense/ValueObjects/ExchangeRate.cs` |
| Domain 程式碼 | `src/Domain/Expense/Interfaces/IExchangeRateRepository.cs` |
| Domain 程式碼 | 擴展 `src/Domain/Expense/ValueObjects/Money.cs`（+ConvertTo） |
| 技術債記錄 | `specs/migration/tech-debt.md`（+ExpenseList.aspx 硬編碼匯率） |
| Git 分支 | `feature/EXP-001-jpy-currency-support` |

### 重點回顧

P001–P008 在這情境帶來六個改變：
1. **Auto-trigger 不再冒失前進**：AI 判斷出「new-feature-flow」後停下來讓你背書（P001）。
2. **Step 內轉換輕量化 + phase gate 在對的地方**：Step 1→2、2→3 只告知；Step 4→5、6→7、7→8 才等確認（P001）。
3. **規格內建 Phase markers + 實作任務段**：spec 的 `phase` 欄位會跟著進度推進；Step 5 規劃後自動生成 `實作任務` 段，Step 7 勾選追蹤，Step 8 統計完成率（P004）。
4. **A+C 結構**：`rules.md` 宣告 + `behavior.md` 情境同步，避免重複/遺漏（P003）。
5. **四層完成 checklist**：8.1 AI 獨立跑、8.2 一題一題問你、8.3 動文件、8.4 歸檔，分工清楚（P005a/b）。
6. **機械 drift 防線**：`/dflow:verify` 在完成後補驗（P008）。

---

## 情境三：PR Review — 審查同事的程式碼（`/dflow:pr-review` 指令）

> **觸發的 Skill 檔案**：`pr-review-checklist.md`
>
> **背景**：同事完成了 EXP-001 日幣報銷功能的實作，發了 PR 請你 review。

### 開發者用指令明確進入

```
❯ claude

你：/dflow:pr-review EXP-001 日幣報銷功能
```

```
Claude：進入 pr-review 流程。

       ┌─── PR Review ───┐
       │ Step 0: Spec Intent（先讀 spec 理解意圖）
       │ Step 1: 規格符合度
       │ Step 2: Domain 層品質
       │ Step 3: Code-Behind 厚度
       │ Step 4: 術語一致性
       │ Step 5: 技術債
       │ Step 6: 遷移準備度評分
       └──────────────────┘

       > 讀取 specs/features/active/EXP-001-jpy-currency-support.md

       先理解意圖：這個 PR 要讓費用報表支援日幣報銷（依費用發生日查月匯率、
       未設定時阻擋送出、換算後依 TWD 四捨五入）。

       理解對嗎？回覆確認後開始逐項 review。
```

```
你：對
```

### Claude Code 開始逐項檢查

```
Claude：Step 1: 規格符合度

       > 讀取 specs/features/active/EXP-001-jpy-currency-support.md
       > 比對實作程式碼

       ✅ 規格存在
       ✅ 主要成功情境已實作（日幣輸入 → 匯率查詢 → 台幣換算）
       ✅ BR-01 已實作（依費用發生日查匯率）
       ✅ BR-02 已實作（匯率未設定時阻擋送出）
       ⚠️ BR-03 部分實作 — 換算後有四捨五入，但沒有用 Money.Round()，
          而是直接寫了 Math.Round()。應該呼叫我們在 BUG-042 建立的 Money.Round()。

       Step 2: Domain 層品質

       > 掃描 src/Domain/ 中的程式碼

       ✅ 沒有 System.Web 引用
       ✅ 沒有直接資料庫存取
       ⚠️ ExchangeRate.cs 有一行 HttpContext.Current.Cache —— 這是 Domain 層不允許的！
          快取機制應該在 Infrastructure 層實作，不是在 Domain 層。

       Step 3: Code-Behind 厚度

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

       Step 4: 術語一致性 + A+C 結構對齊

       ⚠️ 程式碼中使用 `exchangeRate` 但 glossary.md 定義的是 `ExchangeRate`（PascalCase）
          變數命名符合 C# 慣例，但類別名稱確認一致 ✅
       ⚠️ rules.md 有 BR-03，但 behavior.md 找不到對應的 section anchor
          —— spec 合併後忘了同步 C 側，`/dflow:verify` 會攔截到

       Step 5: 技術債

       ⚠️ 發現 ExpenseList.aspx.cs 裡有另一段硬編碼的匯率計算
          （看起來是之前寫死的測試用途），但沒有記錄到 tech-debt.md。

       --- Phase gate: Step 5 → Step 6 ---

       即將給出遷移準備度評分。要繼續嗎？
```

```
你：好
```

```
Claude：Step 6: 遷移準備度評分 — C 級

       好的部分：有 spec、Domain 層結構正確、Code-Behind 大致精簡。
       需要改進：
       1. Domain 層有 HttpContext.Current.Cache（嚴重：違反零依賴原則）
       2. 匯率範圍檢查邏輯在 Code-Behind 而不是 Domain 層
       3. 沒用現有的 Money.Round() 而是重寫了一遍
       4. A+C 結構未對齊：rules.md 新增 BR-03 但 behavior.md 缺對應 anchor

       修完這四項就能到 A 級。要現在把建議貼到 PR 留言，還是先記到 tech-debt？
       （提示：在 PR 被合併前，可請作者先跑 `/dflow:verify` 再複檢一次）
```

### 這個情境的產出物

| 產出物 | 說明 |
|---|---|
| Review 結果 | 逐項檢查報告，含具體程式碼位置和修改建議 |
| 遷移準備度評分 | C 級，附改善到 A 級的具體步驟 |
| 新發現的技術債 | ExpenseList.aspx.cs 的硬編碼匯率 |
| A+C 結構警示 | behavior.md 缺 BR-03 anchor（可用 `/dflow:verify` 機械覆檢） |

### 重點回顧

P001–P008 在這情境帶來的改變：
- **Step 0 Spec Intent** 明確列為第一步（P007b）——先讀 spec 理解意圖，而不是直接跳進程式碼。
- 六個 review step 清楚條列，你知道 AI 會系統性查哪些面向。
- Step 4 擴充到包含 **A+C 結構對齊**（rules.md 和 behavior.md 同步）——reviewer 不用自己比對。
- Phase gate（評分前）讓你可以先暫停討論某一項，再進入評分。
- 評分後可用 **`/dflow:verify`** 做機械層覆檢，避免漏網之魚。

> 💡 Review 中發現的 HttpContext.Current.Cache 違規，就是 [DDD 觀念指引](DDD_MINDSET_SHIFT_tw.md) 觀念四所講的「Domain 層不依賴任何外部東西」。

---

## 情境四：開分支但沒 spec（auto-trigger 安全網示範）

> **觸發的 Skill 檔案**：`git-flow-integration.md`
>
> **背景**：開發者直接要建分支開始寫程式，但還沒有規格。
>
> **📌 對比原版**：原版靠「git-flow-integration 閘門」攔截。新版把「閘門」變成 auto-trigger 的一部分——開發者即使沒打指令，Skill 仍會判斷並要求背書，且提供明確的**離開路徑**（`/dflow:cancel`）。

### 開發者想直接開分支

```
❯ claude

你：我要開一個分支來做請假單的簽核流程。
```

### Auto-trigger 判斷並給出明確選項

```
Claude：我判斷這是新功能需求（「請假簽核」是一個尚未存在的 feature），
       應該進入 **new-feature-flow** 而不是直接開分支。

       > 搜尋 specs/features/ 中沒有找到請假簽核的規格

       根據流程，feature 分支需要在 spec 存在之後才能建立。
       但我想先確認我的判斷對不對，再決定下一步：

       → `/dflow:new-feature` 或「好」進入完整流程（保護 SDD 防線）
       → `/dflow:cancel` 離開（如果你只是想練習 git 指令）
       → 告訴我我判斷錯了（其實這個功能已經有 spec？叫別的名字？）
```

```
你：好吧，但我趕時間
```

```
Claude：進入 new-feature-flow（精簡模式）。只抓最核心的部分：

       這個請假簽核流程，最基本的情境是什麼？
       例如：員工送出請假單 → 主管核准 → 扣假？
```

```
你：對，就是這樣。但超過三天的假要再加一層副總核准。
```

```
Claude：好，這樣就有兩個核心情境了。讓我快速建立 spec...

       > 建立 specs/features/active/LV-001-leave-approval-workflow.md

       規格的關鍵部分已經有了：
       - 兩個 Given/When/Then 情境
       - 一條核心業務規則（三天以上需副總核准）
       - 識別到 Leave Bounded Context

       --- Phase gate: Step 4 → Step 6（跳過 Step 5 實作規劃，精簡模式）---

       現在可以建立分支了：

       > git checkout -b feature/LV-001-leave-approval-workflow

       你可以開始寫程式了。剩下的 spec 細節（邊界情況、Domain 層設計）
       可以邊做邊補。如果想先中止寫程式的引導，就用 `/dflow:cancel`。
```

### 對比：如果開發者其實只是想練 git

```
❯ claude

你：我要開一個分支來做請假單的簽核流程。
```

```
Claude：[同樣的 auto-trigger 判斷結果，給出三個選項]
```

```
你：/dflow:cancel 我只是想先試試 feature 分支命名，不是真的要寫程式
```

```
Claude：已取消 new-feature-flow 判斷。回到自由對話。
       （如果之後要正式開始，記得用 `/dflow:new-feature` 或描述需求。）

       要試的分支命名是這種格式嗎？
         feature/LV-001-leave-approval-workflow
```

### 這個情境的產出物

| 產出物 | 路徑 |
|---|---|
| 功能規格（草稿） | `specs/features/active/LV-001-leave-approval-workflow.md` |
| 術語表更新 | `specs/domain/glossary.md`（如有新術語） |
| Git 分支 | `feature/LV-001-leave-approval-workflow` |

### 重點回顧

P001 Hybrid 設計在這情境的價值最明顯：
- **Auto-trigger 仍是守衛**：開發者沒打指令，Skill 還是會介入保護 SDD 防線。
- **但不會強推**：提供 `/dflow:cancel` 讓「只想練 git」的情況能優雅離開，不用編造理由說服 AI。
- **Ceremony Scaling 仍有效**：趕時間時 AI 會用精簡模式（合併 Step、跳過 Step 5）。

---

## 情境五：Brownfield — 既有功能的基準線補寫（P007a）

> **觸發的 Skill 檔案**：`modify-existing-flow.md` 的「Brownfield 基準線補寫」分支
>
> **背景**：舊系統裡「請假單退回」的邏輯散落在多個 aspx 裡，沒人寫過 spec；這次有需求要調整退回規則，但動手前必須先把「目前行為」記錄下來，否則 Delta 無法對照。
>
> **為什麼不是直接 `/dflow:modify-existing`？** 因為要修改的東西沒有對應的 spec / models.md / rules.md。P007a 在 `modify-existing-flow` 的前面加入「基準線補寫路徑」，引導開發者系統性地從程式碼反向文件化。

### 開發者用自然語言描述

```
❯ claude

你：請假單退回的規則要改，現在只允許主管退回，要加上 HR 也能退回。
   但我們從來沒寫過請假簽核的 spec，程式碼散在好幾個地方。
```

### Auto-trigger 判斷為 Brownfield，提出先補基準線

```
Claude：我判斷這是 **modify-existing-flow** 的任務，但偵測到兩個 Brownfield 信號：
       1. `specs/features/completed/` 和 `specs/domain/leave/` 都沒有請假簽核的紀錄
       2. 你自己也說邏輯散在多個地方

       P007a 的 Brownfield 路徑：先做「基準線補寫」再改。流程是：

       ┌─── Brownfield Baseline Capture ───┐
       │ Step B1: 定位 — 相關程式碼在哪些檔案 / 哪些分支
       │ Step B2: 抽取 — 逐檔讀取，整理出「目前行為」的 Given/When/Then
       │ Step B3: 結構化 — 產出 rules.md（宣告）+ behavior.md（情境）草稿
       │ Step B4: 開發者審閱 — 基準線確認後，作為「MODIFIED 的 BEFORE 側」
       └────────────────────────────────────┘

       基準線補寫完成後，才進入正常的 modify-existing-flow（Step 2 起用 Delta 格式記錄本次變更）。

       要進 Brownfield 路徑嗎？預估 20–30 分鐘。
       → 回覆「好」/`/dflow:modify-existing` 進入
       → 或 `/dflow:cancel` 離開（但沒 spec 就無法有效做 Delta）
```

```
你：好
```

### Claude Code 執行 B1/B2：定位並抽取現有行為

```
Claude：B1 定位：

       > grep -r "退回" src/
       > grep -r "RejectLeave" src/

       找到 3 個點：
       - `LeaveRequestDetail.aspx.cs` 第 245–280 行（主管退回按鈕邏輯）
       - `LeaveRequestService.asmx.cs` 第 88–112 行（API 退回 endpoint）
       - `LeaveStatusMachine.vb` 第 56–74 行（狀態機過渡規則）

       B2 抽取：讓我逐檔讀取，整理目前行為...

       > 讀取三個檔案

       抽取到的行為：

       ​```gherkin
       Scenario: 主管退回已送出的請假單
         Given 一張「已送出」的請假單
         And 目前使用者是該員工的直屬主管
         When 點擊「退回」按鈕並填寫退回原因
         Then 請假單狀態變為「已退回」
         And 請假單保留退回原因
         And 發出退回通知給員工

       Scenario: 非主管嘗試退回（目前會被擋）
         Given 一張「已送出」的請假單
         And 目前使用者不是該員工的直屬主管
         When 嘗試退回
         Then 顯示錯誤「僅限主管退回」，狀態不變
       ​```

       這對嗎？或我漏掉什麼情境？
```

```
你：對，就這樣。副總以上可以退回任何人的，我差點忘了這條。
```

### B3/B4：結構化並確認基準線

```
Claude：好，把副總條款補進來：

       ​```gherkin
       Scenario: 副總以上可退回任何人的請假單
         Given 一張「已送出」的請假單
         And 目前使用者職級為副總以上
         When 嘗試退回
         Then 等同主管退回的處理（狀態變為已退回 + 通知 + 保留原因）
       ​```

       產出基準線文件（標註 `source: baseline-capture` 讓未來知道這批情境是反向補寫的）：

       > 建立 specs/domain/leave/rules.md（宣告索引）
       >   BR-LV-01 只有主管或副總以上可退回請假單
       >   BR-LV-02 退回時必須填寫原因
       >   BR-LV-03 退回後狀態不可再修改，必須由員工重送

       > 建立 specs/domain/leave/behavior.md（情境細節）
       >   每個 BR 對應一個 section anchor（A+C 結構）

       B4 審閱點：這份基準線對嗎？確認後會鎖定為「MODIFIED 的 BEFORE 側」，
       接下來進入正常 modify-existing-flow 用 Delta 記錄「HR 也能退回」的變更。
```

```
你：對，可以
```

### 接續進入正常 modify-existing-flow

```
Claude：基準線鎖定。進入 modify-existing-flow Step 2：用 Delta 格式記錄本次變更。

       > 建立 specs/features/active/LV-008-hr-can-reject-leave.md

       ​```markdown
       ## 行為變更（Delta）

       ### MODIFIED — 修改的行為
       #### 規則：BR-LV-01 誰可以退回請假單
       **原本**：只有「主管」或「副總以上」可退回
       **改為**：「主管」、「副總以上」、或「HR」皆可退回
       **原因**：人資部希望在員工離職 / 轉職流程中能主動退回未處理的請假單
       ​```

       從這裡開始，後續 step 跟一般 modify-existing-flow 一樣：
       Step 3 評估 DDD 影響 → Step 4 實作 → Step 5 完成 checklist（5.1 ~ 5.4）。
```

### 這個情境的產出物

| 產出物 | 路徑 | 備註 |
|---|---|---|
| 基準線宣告（A 側） | `specs/domain/leave/rules.md` | source: baseline-capture |
| 基準線情境（C 側） | `specs/domain/leave/behavior.md` | 3 個 section anchor |
| 術語表更新 | `specs/domain/glossary.md` | Leave、LeaveStatus 等 |
| 本次變更 spec（Delta）| `specs/features/active/LV-008-hr-can-reject-leave.md` | MODIFIED BR-LV-01 |

### 重點回顧

P007a 對 WebForms 團隊特別有價值：
- **舊系統常態**：大半功能都沒 spec，直接 Delta 改會沒基準線可對照。
- **系統化反向補寫**：B1–B4 四步，不是「隨便寫一下」而是定位 → 抽取 → 結構化 → 確認。
- **補完即融入主流程**：基準線鎖定後接回正常 modify-existing-flow，後續 Delta / verify / PR Review 都能正常運作。

> 💡 這是 SDD 防線在既有系統的延伸——不是強迫一次性補完整個系統的 spec，而是**在觸及某段邏輯時順手補該段的基準線**，逐步把系統從「沒文件」推向「有文件」。

---

## Skill 觸發速查表（更新版）

### 明確指令（推薦）

| 指令 | 觸發的流程 | 主要 Skill 檔案 |
|---|---|---|
| `/dflow:new-feature` | 新功能流程（含 Phase markers + 實作任務段） | `new-feature-flow.md` + `feature-spec.md` |
| `/dflow:modify-existing` | 修改現有流程（Delta 格式 / Brownfield 基準線分支） | `modify-existing-flow.md` + `feature-spec.md` |
| `/dflow:bug-fix` | Bug 修復流程 | `modify-existing-flow.md` + `lightweight-spec.md` |
| `/dflow:pr-review` | PR 審查（Step 0 Spec Intent 開場） | `pr-review-checklist.md` |
| `/dflow:verify` | drift 機械檢查（spec / rules / behavior / 實作碼對齊） | `SKILL.md` § Drift Verification |
| `/dflow:status` | 回報目前 flow / step / 未完項 | — |
| `/dflow:next` | 確認進入下一階段 | — |
| `/dflow:cancel` | 中止目前流程 | — |

### 自然語言（auto-trigger 安全網）

| 你說的話 | AI 判斷 | AI 行為 |
|---|---|---|
| 「我要加一個新功能」 | new-feature-flow | 判斷後停下，要求你背書 |
| 「有個 bug 要修」 | bug-fix | 判斷後停下，要求你背書 |
| 「幫我 review 這個 PR」 | pr-review | 判斷後停下，要求你背書 |
| 「這段沒 spec 但我要改」 | Brownfield 基準線補寫 | 先走 B1–B4，再進 modify-existing |
| 「我要開分支」但沒 spec | 攔截到正確 flow | 判斷為 new-feature，引導先補 spec |
| 「幫我檢查 spec 和實作有沒有脫鉤」 | drift verification | 叫用 `/dflow:verify` |
| 「X 是什麼意思？」 | 術語查詢 | 查 `glossary.md`，沒有就建議新增 |
| 「接下來做什麼？」 | Sprint 規劃 | 檢查 `specs/features/backlog/` |

### Ceremony Scaling 速查

| 改什麼 | Spec 層級 | 格式 | 要抽 Domain？ | 記技術債？ |
|---|---|---|---|---|
| 新功能 | 完整 spec | feature-spec（Phase + 實作任務） | 必須 | 視情況 |
| 業務規則變更 | 標準 spec | Delta（MODIFIED） | 必須 | 必須 |
| 新增並存行為 | 標準 spec | Delta（ADDED） | 必須 | 視情況 |
| 移除既有行為 | 標準 spec | Delta（REMOVED） | 必須 | 必須 |
| 既有但無 spec 的功能 | Brownfield 基準線 + 標準 spec | 基準線 rules/behavior + Delta | 視情況 | 必須 |
| Bug 修復（邏輯） | 輕量 spec | lightweight-spec | 評估 | 有發現就記 |
| 純 UI 修改 | 不需要 | — | 不需要 | 不需要 |
| 設定/環境 | 不需要 | — | 不需要 | 不需要 |
| 效能修復 | 輕量 spec | lightweight-spec | 評估 | 必須 |

---

## 為什麼這個改動值得採用（說服同事的三個理由）

### 1. 可預測性

原版：「我這句話會觸發什麼？AI 會自己判斷還是等我說？」
新版：**你說什麼，AI 就做什麼**。指令 = 明確意圖；自然語言 = 會先判斷並讓你背書。

### 2. 可中斷

原版：一旦進入流程，中途想暫停沒有明確出口，只能靠「不回應」或「換話題」。
新版：**`/dflow:cancel` 一鍵退出**，spec 保持 in-progress，之後 `/dflow:new-feature` 接續。

### 3. 可驗證

原版：做到一半不知道自己在哪，要靠猜。
新版：**`/dflow:status` 隨時可查**，還有 phase gate 明確告知主要里程碑。

---

## 接下來

如果你的下一個專案是用 ASP.NET Core 建新系統，請參考 **Core 版使用教學（Hybrid）**，
那裡的情境會展示 Clean Architecture 四層結構、Aggregate 設計、Domain Events 等進階概念，
同樣搭配 P001 的指令化設計。
