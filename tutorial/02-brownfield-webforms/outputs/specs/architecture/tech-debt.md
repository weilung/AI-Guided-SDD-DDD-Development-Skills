<!-- Template maintained by Dflow. See proposals/PROPOSAL-013 for origin. -->

# Migration Tech Debt

> WebForms migration debt backlog discovered during SDD/DDD work on OrderManager.

## Debt Items

| Item | Location | Description | Severity | Migration impact | Status |
|---|---|---|---|---|---|
| 業務邏輯散在 Code-Behind | `OrderManager.Web/Pages/Order/OrderEntry.aspx.cs` and similar pages | `btnSubmit_Click` 等 event handler 內混合 UI 讀值、查詢、金額計算、狀態判斷與資料寫入，難以測試也難以搬遷。 | High | 阻礙 Domain logic 抽離；未來 .NET Core migration 若不先整理規則，會變成逐行翻譯 legacy 行為。 | open |
| 缺少 unit test coverage | `tests/OrderManager.IntegrationTests/` | 目前只有少量 integration tests，主要業務規則沒有快速、可隔離的 unit tests。 | High | 每次修改都依賴人工 regression；抽離 `src/Domain/` 前缺少保護網。 | open |
| Stored Procedures 重 join 難維護 | `OrderManager.DataAccess/StoredProcedures/` / SQL Server | 多個訂單、庫存、發票查詢把資料組合與業務判斷混在 SQL 裡，規則來源不清楚。 | Medium | 需要逐步把已確認的 business rules 移到 spec 與 Domain model；短期不強制重寫所有 SP。 | open |
| .NET Core migration long-term goal 未拆小步 | repository-wide | 團隊想遷移到 .NET Core，但目前沒有以規格與 Domain extraction 為單位的 incremental roadmap。 | Medium | 若直接啟動重寫，容易與現役需求衝突；Dflow 應累積可遷移 source of truth。 | open |
| 無 Dependency Injection 組態 | `OrderManager.Web/` / `App_Code/` | 多數 helper、repository 與 service 由頁面直接 new 或使用 static helper，難以替換與測試。 | Medium | 抽離 Domain service / repository interface 時需要先建立 seam；否則新 Domain code 仍會被 WebForms infrastructure 綁住。 | open |
| Order 折扣規則分散在多個頁面 | `OrderManager.Web/Pages/Order/OrderList.aspx.cs` / `OrderDetail.aspx.cs` | 初步檢查顯示 Order BC 其他頁面也可能使用相同折扣規則，但各自實作金額摘要或顯示邏輯。 | High | 若只修 `OrderEntry.aspx.cs`，查詢與明細頁可能仍顯示舊算法；本 phase 先不擴張，後續 baseline capture 應跨頁確認。 | open |
| OrderEntry event handler 仍混合資料存取與流程控制 | `OrderManager.Web/Pages/Order/OrderEntry.aspx.cs` | 折扣計算抽離後，`btnSubmit_Click` 仍約有 50 行 EF query、UI parsing、狀態設定與 DB 寫回邏輯尚未抽離。 | Medium | WebForms adapter 仍偏厚；未來遷移到 ASP.NET Core 時需再拆 application-facing adapter / repository seam。 | open |
| DiscountPolicy 結構可能需要演進 | `src/Domain/Order/DiscountPolicy` | 本 phase 只處理滿額折扣與 `Senior` 客戶折扣；若後續新增促銷、品項級折扣或通路折扣，單一 policy 可能過大。 | Low | 後續可評估是否拆成 PolicyChain 或 Strategy pattern；目前不為未確認需求過度設計。 | open |

## Follow-up Notes

- Day-0 baseline 只記錄已知 debt，不代表立即重構。
- 每個 `/dflow:modify-existing` 完成時，若看見新的 migration risk，應更新本檔。
- 優先處理能支援 `src/Domain/` 純 C# 抽離與 unit testing 的 debt。
