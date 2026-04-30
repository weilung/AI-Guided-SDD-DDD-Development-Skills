<!-- Template maintained by Dflow. See proposals/PROPOSAL-013 for origin. -->

# Glossary

> Ubiquitous Language for OrderManager.

## Terms

| Term | Definition | Bounded Context | Code Mapping | Notes |
|---|---|---|---|---|
| Order | B2B 經銷商提交的訂單主體，包含訂單狀態、客戶、訂單明細與金額資訊；是否作為 Aggregate Root 待第一個 modify-existing 確認。 | Order | `OrderManager.Web.Pages.Order.*` / `OrderManager.DataAccess.Entities.Order` | 目前規則分散在 `OrderEntry.aspx.cs` 與 Stored Procedures。 |
| OrderLine | 訂單中的單一品項明細，包含商品、數量、單價、折扣與庫存檢查所需欄位。 | Order / Inventory | `OrderManager.DataAccess.Entities.OrderLine` | Order 與 Inventory 邊界待釐清。 |
| Dealer | 透過 OrderManager 下訂單的 B2B 經銷商。 | Customer | `OrderManager.DataAccess.Entities.Customer` | 業務常稱 Customer / Dealer，後續需統一命名。 |
| CreditLimit | 客戶可用信用額度；接單或提交訂單時可能影響是否允許成立訂單。 | Customer | Stored Procedure / Customer repository | 規則可能在 Customer 與 Order 之間跨 context。 |
| AvailableToPromise | 可承諾給訂單的可售量，通常由庫存量扣除預留量後得到。 | Inventory | Stored Procedure | 目前命名與實作待確認。 |
| Shipment | 訂單成立後的出貨流程與物流狀態。 | Shipment | `OrderManager.Web.Pages.Shipment.*` | 是否與 Order 狀態共用欄位待釐清。 |
| Invoice | 與訂單或出貨結果相關的發票與應收帳款資料。 | Invoice | `OrderManager.Web.Pages.Invoice.*` | 財務規則可能由不同 owner 維護。 |

## Open Questions

- Customer 與 Dealer 是否為同一個 Ubiquitous Language？若業務部門使用 Dealer、資料庫使用 Customer，spec 與 code mapping 要明確記錄。
- Order 提交時的金額計算、信用額度檢查與庫存預留，哪些屬於 Order BC，哪些應由 Customer / Inventory 提供能力？
- Invoice 狀態是否應影響 Order 狀態，或只作為 downstream 財務流程？
