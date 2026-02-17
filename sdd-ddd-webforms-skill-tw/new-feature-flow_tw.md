# 新功能開發流程

當開發者說「我要新增一個功能」時的逐步引導。

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

## 步驟 6：建立 Git 分支

規格撰寫並審查完成後：

```
分支命名：feature/{SPEC-ID}-{簡短描述}
範例：feature/EXP-001-jpy-currency-support
```

Spec ID 將分支連結到其規格文件。

## 步驟 7：實作

實作過程中，持續確認：
- [ ] 業務邏輯是否放進 `src/Domain/`（而不是只在 Code-Behind）？
- [ ] 新術語是否已加入術語表？
- [ ] Code-Behind 是否只做 UI 綁定？
- [ ] 外部依賴是否使用介面？
- [ ] 有沒有發現技術債？→ 記錄到 `tech-debt.md`

## 步驟 8：完成

功能完成時：
1. 更新規格狀態為 `completed`
2. 將規格從 `active/` 移到 `completed/`
3. 確認所有新術語都在術語表中
4. 確認 `models.md` 和 `rules.md` 已更新
5. 記錄實作過程中發現的技術債
