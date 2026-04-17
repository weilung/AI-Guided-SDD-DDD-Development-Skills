# 新功能開發流程

當開發者打 `/dflow:new-feature`（或自然語言表達新增功能意圖、經安全網確認後）的逐步引導。

**本流程的 Phase Gate 位置**（跨越前要停下來等確認）：
- Step 4 → Step 5（規格寫完 → 規劃實作）
- Step 6 → Step 7（分支建好 → 開始實作）
- Step 7 → Step 8（實作完成 → 走完成流程）

其餘 step 間的轉換都是 **step-internal**：只宣告「Step N 完成，進入 Step N+1」，不等確認。完整透明度規則見 `SKILL_tw.md` § 工作流程透明度。

## 步驟 1：需求理解

自然地詢問這些問題（不要一次丟出清單）：

1. **這個功能是什麼？** 取得白話描述。
2. **誰需要它？** 確認利害關係人或使用者角色。
3. **為什麼現在要做？** 了解優先順序和緊急程度（影響流程嚴謹度）。

然後檢查既有資產：
- 搜尋 `specs/domain/` 尋找相關概念
- 搜尋 `specs/features/` 尋找相關或重疊的功能
- 檢查 `specs/domain/glossary.md` 中的相關術語

分享發現結果：「我看到我們已經有 [X] 的文件。這個新功能似乎是在延伸它——對嗎？」

**→ Step-internal 轉換**：Step 1 完成。宣告「Step 1 完成（需求理解），進入 Step 2：識別 Bounded Context」後繼續。

## 步驟 2：識別 Bounded Context

引導開發者將功能放到正確的 Context：

```
「這個功能涉及 [概念]。看我們的領域結構，
它似乎適合放在 [Context] 這個 Bounded Context 裡。
你的理解也是這樣嗎？」
```

如果沒有匹配的 Context：
1. 提議一個新的 Context 名稱
2. 使用 context-definition 模板建立 `specs/domain/{new-context}/context.md`
3. 取得開發者確認後再繼續

**→ Step-internal 轉換**：Step 2 完成。宣告「Step 2 完成（BC 已識別），進入 Step 3：領域概念發掘」後繼續。

## 步驟 3：領域概念發掘

逐步討論這些問題：

- **關鍵名詞是什麼？** → 可能的 Entity 或 Value Object
- **關鍵動詞是什麼？** → 可能的 Domain Service 或 Entity 行為
- **規則/限制是什麼？** → 需要記錄的業務規則
- **狀態/狀態碼是什麼？** → 需要建模的狀態機
- **需要什麼外部資料？** → 需要定義的介面

對每個新概念：
1. 檢查術語表——如果沒有就新增
2. 檢查 `models.md` 中是否已存在——需要就擴充
3. 如果是全新的，新增到對應 Context 的 `models.md`

**→ Step-internal 轉換**：Step 3 完成。宣告「Step 3 完成（領域概念已捕捉），進入 Step 4：撰寫規格」後繼續。

## 步驟 4：撰寫規格

使用功能規格模板，在 `specs/features/active/{ID}-{功能名稱}.md` 建立文件。

引導開發者完成每個區段：

### 行為規格（Given/When/Then）
這是最重要的部分。幫助開發者思考各種情境：

```
「我們先走一遍主要成功情境。
Given [初始狀態], When 使用者 [執行操作], Then [應該發生什麼]？」
```

然後探索變化情境：
- 「如果 [輸入無效] 呢？」
- 「如果 [相關資料不存在] 呢？」
- 「[邊界值] 會怎樣？」
- 「有沒有權限/角色的要求？」

### 業務規則
從情境中提取明確的規則：

```
「根據你的描述，我看到這些業務規則：
BR-01: [規則]
BR-02: [規則]
這樣有正確抓到邏輯嗎？有沒有遺漏的？」
```

### 邊界情況
特別詢問：
- 空值/null 輸入
- 並行修改
- 大量資料
- 幣別/小數精度（如果涉及財務）
- 日期/時區邊界
- 字元編碼（中日韓、特殊字元）

**→ Phase Gate：Step 4 → Step 5**

向開發者宣告：
> 「規格初稿已完成——行為情境、業務規則、邊界情況都有了。準備進入實作規劃（Domain 層設計、介面、薄 Code-Behind）嗎？打 `/dflow:next` 或回「好」繼續，或先告訴我規格還要再改一輪。」

等確認（`/dflow:next`、回覆「好」/「繼續」、或隱含確認——見 `SKILL_tw.md` § 確認訊號）後才進 Step 5。

## 步驟 5：規劃實作

### Domain 層優先
確認什麼要放進 `src/Domain/`：

```csharp
// 引導範例：
// 「這個功能，我建議：
//  - 一個新的 Value Object: Money(Amount, Currency)
//  - 一個新的介面: IExchangeRateService
//  - 一個 Domain Service: ExpenseCalculationService
//  全部都是純 C#，不依賴 WebForms。」
```

### Code-Behind 保持精簡
規劃 WebForms 頁面如何呼叫 Domain 層：

```csharp
// 理想的 Code-Behind 模式：
protected void btnSubmit_Click(object sender, EventArgs e)
{
    // 1. 解析 UI 輸入
    var input = ParseFormInput();

    // 2. 呼叫 Domain 層
    var result = _domainService.Process(input);

    // 3. 顯示結果
    BindResult(result);
}
```

### 介面定義
對任何外部依賴（資料庫、API、檔案系統），在 `src/Domain/{Context}/Interfaces/` 定義介面：

```csharp
// src/Domain/Expense/Interfaces/IExpenseRepository.cs
public interface IExpenseRepository
{
    ExpenseItem GetById(int id);
    void Save(ExpenseItem item);
}
```

### 產生實作任務清單

計畫定稿後，AI 根據實作計畫產生具體任務清單，寫入 spec 的「實作任務」段（見 feature-spec 模板）。每個任務格式為 `[LAYER]-[NUMBER]：描述`：

- `DOMAIN` — Domain 類別、VO、Service、Interface
- `PAGE`    — Code-Behind / ASPX 變更
- `DATA`    — 資料表 schema 或 Repository 實作
- `TEST`    — 測試案例

範例（實際內容依功能調整）：

```markdown
- [ ] DOMAIN-1：建立 Money value object 含幣別轉換
- [ ] DOMAIN-2：定義 IExchangeRateService 介面
- [ ] PAGE-1：ExpenseForm.aspx.cs 改薄、呼叫 domain service
- [ ] DATA-1：新增 ExchangeRate 資料表與 Repository
- [ ] TEST-1：Money 相等與轉換單元測試
```

這份清單會成為 Step 7 的執行清單，也是 Step 8 完成流程 checklist 的依據。

**→ Step-internal 轉換**：Step 5 完成。宣告「Step 5 完成（實作計畫 + 任務清單備妥），進入 Step 6：建立 Git 分支」後繼續。

## 步驟 6：建立 Git 分支

規格撰寫並審查完成後：

```
分支命名：feature/{SPEC-ID}-{簡短描述}
範例：feature/EXP-001-jpy-currency-support
```

Spec ID 將分支連結到其規格文件。

**→ Phase Gate：Step 6 → Step 7**

向開發者宣告：
> 「分支 `feature/{SPEC-ID}-{描述}` 已建立。要開始實作了嗎？打 `/dflow:next` 前進，或先討論實作順序 / 範圍。」

等確認後才進 Step 7。

## 步驟 7：實作

實作過程中，持續確認：
- [ ] 業務邏輯是否放進 `src/Domain/`（而不是只在 Code-Behind）？
- [ ] 新術語是否已加入術語表？
- [ ] Code-Behind 是否只做 UI 綁定？
- [ ] 外部依賴是否使用介面？
- [ ] 有沒有發現技術債？→ 記錄到 `tech-debt.md`

**→ Phase Gate：Step 7 → Step 8**

向開發者宣告：
> 「實作看起來完成了。要開始跑完成流程 checklist（對照 spec 驗證、更新 domain 文件、歸檔 spec）嗎？打 `/dflow:next` 前進。」

等確認後才進 Step 8。這個 Phase Gate 是完成流程 checklist 的觸發點——不可略過。

## 步驟 8：完成

由 Step 7 → Step 8 Phase Gate 觸發。AI 依下面順序跑 checklist，**不可**略過任何一段。

### 8.1 驗證 — AI 可獨立執行

AI 一次檢查完所有項目，用 ✓/✗ 一份列表回報後才開始動文件。標註 *(8.3 後)* 的項目要等 8.3 合併動作完成後再驗證：

- [ ] spec 的每個 Given/When/Then 情境都有實作或測試覆蓋
- [ ] spec 的每個 BR-*（業務規則）都有實作或測試覆蓋
- [ ] spec 的每個 EC-*（邊界情況）都有處理
- [ ] Domain 層沒有 `System.Web` 引用（grep `src/Domain/`）
- [ ] `實作任務` 段：全部勾選，或未勾選項目明確標註為 follow-up（連結到 spec 或 tech-debt）
- [ ] *(8.3 後)* `specs/domain/{context}/behavior.md` 對本 spec 引入的每個 `BR-*` 都有對應 section anchor（`/dflow:verify` 的機械輸入）
- [ ] *(8.3 後)* `specs/domain/{context}/behavior.md` 的 `last-updated` 晚於本 spec 的 `created` 日期（機械 drift 防線）

任何一項失敗就回報差距並暫停——不要往下進 8.2。

### 8.2 驗證 — 需開發者確認

AI 一次列一項、等開發者確認後再列下一項：

- [ ] 每條 BR 的實作是否**忠實表達意圖**？（AI 列出 BR → 實作位置，開發者判斷）
- [ ] 邊界情況的處理方式是否**恰當**？（AI 列出 EC → 處理方式，開發者判斷）
- [ ] 有沒有漏掉的技術債？（AI 列出觀察到的，開發者補遺漏）
- [ ] 合併進 `behavior.md` 的情境是否**忠實表達預期行為**？（AI 列出合併後的 anchor，開發者判斷）
- [ ] 完成後要不要把 spec 的 `實作任務` 段**摺疊 / 移除**？（看團隊慣例，開發者決定）

一次問一項，不要一次倒五個。

### 8.3 文件更新

- [ ] `specs/domain/glossary.md` — 新術語已加入
- [ ] `specs/domain/{context}/models.md` — 模型定義已更新
- [ ] `specs/domain/{context}/rules.md` — 業務規則已更新
- [ ] `specs/domain/{context}/behavior.md` — 把完成的 spec 的 Given/When/Then 情境合併進行為彙整。子步驟：
      - 把 Phase 3 draft 段落（B3 中途同步）轉為正式段落
      - 更新對應 `rules.md` anchor 的 `last-updated` 日期（B4）
- [ ] `behavior.md` 草稿清理 — 若本 spec 中途放棄，保留 `## 提案中變更` 段的歷史，或明確 REMOVE
- [ ] `specs/migration/tech-debt.md` — 實作中發現的技術債已記錄

### 8.4 歸檔

- [ ] spec 的 `status` 欄位改為 `completed`
- [ ] spec 檔案從 `specs/features/active/` 搬到 `specs/features/completed/`

只有 8.4 做完才能宣告「功能完成」。
