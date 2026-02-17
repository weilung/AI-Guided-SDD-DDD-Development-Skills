# PR 審查檢查清單

在進行 Code Review 或討論 PR 時，使用此清單確保 SDD/DDD 流程有被遵循。

## 規格合規性

- [ ] **規格存在** — `specs/features/` 中有沒有對應這次變更的規格？
- [ ] **規格與程式碼一致** — 實作是否符合 Given/When/Then 情境？
- [ ] **業務規則已涵蓋** — 規格中所有 BR-* 規則是否都已實作？
- [ ] **邊界情況已處理** — 規格中所有 EC-* 邊界情況是否都已處理？

如果規格缺漏或不完整：
```
「我注意到這個 PR 沒有對應的規格。我們補建一份吧
——這對文件記錄和未來遷移都有幫助。
你能描述一下這次變更做了什麼嗎？」
```

## Domain 層品質

- [ ] **Domain 中沒有 System.Web** — `src/Domain/` 必須零 WebForms 依賴
- [ ] **Domain 中沒有直接 DB 存取** — 所有資料存取都透過介面
- [ ] **Domain 中沒有 HttpContext/Session/ViewState** — 純業務邏輯
- [ ] **不需 Web 基礎設施即可測試** — 能不能不啟動 IIS 就做單元測試？

常見違規需標記：
```csharp
// 不好：Domain 程式碼依賴 WebForms
using System.Web;
using System.Web.UI;
HttpContext.Current.Session["key"]
Page.ViewState["key"]

// 不好：Domain 中直接存取資料庫
using System.Data.SqlClient;
new SqlConnection(connectionString)

// 好：基於介面
public class ExpenseService
{
    private readonly IExpenseRepository _repo;
    public ExpenseService(IExpenseRepository repo) => _repo = repo;
}
```

## Code-Behind 的厚度

評估 Code-Behind 檔案是否足夠精簡：

**Code-Behind 可以做的事：**
- 解析 UI 輸入（TextBox → 強型別值）
- 呼叫 Domain 層服務
- 將結果綁定到 UI 控制項
- 處理 UI 特定的事件（頁面載入、按鈕點擊）
- UI 驗證（RequiredFieldValidator 等）

**應該放在 Domain 層的事：**
- 計算（數學運算、轉換、彙總）
- 業務驗證（規則、限制、上限）
- 狀態轉換（狀態變更、流程步驟）
- 資料轉換（業務意義上的轉換，不是 UI 格式化）

如果 Code-Behind 太厚：
```
「這個 Code-Behind 有 [計算/驗證/轉換] 邏輯，
可以抽離到 src/Domain/{Context}/。
既然我們已經在 review 了，要現在抽離還是記到 tech-debt.md 以後再處理？」
```

## 術語表一致性

- [ ] **新術語已記錄** — 有沒有新的業務概念加到 `glossary.md`？
- [ ] **命名一致** — 類別名/方法名/變數名是否與術語表中的名稱一致？
- [ ] **用詞精確** — 領域特定的術語是否使用準確？

範例檢查：
```
「我看到程式碼裡用了 'reimbursement'，但術語表中用的是
'報銷（Expense Claim）'。要不要統一命名？」
```

## 領域文件更新

- [ ] **models.md 已更新** — 新的 Entity/VO/Service 有記錄嗎？
- [ ] **rules.md 已更新** — 新增或變更的業務規則有記錄嗎？
- [ ] **context.md 邊界有遵守** — 這次變更有沒有超出其 Context 的範圍？

## 技術債意識

- [ ] **新債有記錄** — 任何捷徑或妥協都記到 `tech-debt.md` 了嗎？
- [ ] **既有債沒有惡化** — 這次變更有沒有讓既有的技術債更嚴重？
- [ ] **債務有減少** — 這次變更有沒有修掉 `tech-debt.md` 中的項目？如果有就勾掉。

## 遷移準備度評分

用快速的遷移準備度量表給 PR 評分：

- **A**：新業務邏輯完全在 Domain 層，可完整測試，規格完整
- **B**：大部分在 Domain 層，有些微耦合，規格存在
- **C**：混合——有部分抽離，部分邏輯仍在 Code-Behind
- **D**：所有邏輯在 Code-Behind，但至少有規格/技術債記錄
- **F**：沒有規格、沒有抽離、沒有文件——退回

以建設性的方式分享評分：
```
「這個 PR 是紮實的 B——匯率邏輯乾淨地抽離到 Domain 了，
規格也涵蓋了主要情境。讓它變成 A 的方法：
Code-Behind 裡的日期解析邏輯可以移到一個 DateRange value object。
要現在做還是加到 tech-debt？」
```
