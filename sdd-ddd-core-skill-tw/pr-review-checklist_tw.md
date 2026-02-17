# PR 審查檢查清單 — ASP.NET Core

## 規格合規性

- [ ] 這次變更有對應的規格
- [ ] 實作符合 Given/When/Then 情境
- [ ] 所有業務規則（BR-*）已實作
- [ ] 邊界情況（EC-*）已處理

## 領域層品質

- [ ] **零外部依賴** — 檢查 .csproj，除了基礎 .NET 沒有任何 NuGet
- [ ] **沒有 ORM 屬性** — Domain 類別上沒有 [Table]、[Column]、[Key]
- [ ] **沒有序列化屬性** — 沒有 [JsonProperty]、[JsonIgnore]
- [ ] **Private setter** — 狀態只能透過方法變更
- [ ] **不變條件有強制執行** — 建構式和方法拒絕無效狀態
- [ ] **Value Objects 不可變** — 使用 `record` 或 readonly 屬性
- [ ] **Domain Events 有發出** — 重要的狀態變更都有產生事件
- [ ] **其他 Aggregate 透過 ID 引用** — 不是直接物件引用

## 應用層品質

- [ ] **沒有業務邏輯** — Handler 只做編排，不做決策
- [ ] **CQRS 有遵守** — Command 處理寫操作，Query 處理讀操作
- [ ] **驗證在 Validator 中** — 不在 Handler 或 Controller 裡
- [ ] **DTO 中沒有 Domain 物件** — 各層之間有正確的映射
- [ ] **Event Handler 是冪等的** — 重複執行也安全

## 基礎設施層品質

- [ ] **EF 設定使用 Fluent API** — 不是 Domain 實體上的屬性標註
- [ ] **Repository 只針對 Aggregate Root** — 不是針對子實體
- [ ] **SQL/LINQ 中沒有業務邏輯** — 複雜過濾用 Specification
- [ ] **外部服務在介面後面** — 可以 mock 來測試

## 展示層品質

- [ ] **Controller 精簡** — 解析、派發、回應
- [ ] **沒有 Domain 物件暴露** — API 只有 DTO/ViewModel
- [ ] **正確的狀態碼** — 201 Created、404 Not Found、422 Unprocessable
- [ ] **沒有業務邏輯** — 連驗證都只做格式檢查

## 橫切關注點

- [ ] **術語表一致性** — 新術語有記錄嗎？
- [ ] **Context 邊界有遵守** — 沒有伸手進另一個 Context 的內部
- [ ] **Domain Events 有記錄** — events.md 有更新嗎？
- [ ] **測試涵蓋不變條件** — 不是只測 happy path

## 架構評分

- **A**：乾淨的層分離、Domain 優先設計、完整規格、全面測試
- **B**：大部分乾淨，輕微的層洩漏，規格存在，良好的測試覆蓋
- **C**：部分業務邏輯在錯誤的層，規格存在
- **D**：程式碼可以運作但有架構疑慮，需要重構
- **F**：業務邏輯在 Controller/Infrastructure 中，沒有規格——退回
