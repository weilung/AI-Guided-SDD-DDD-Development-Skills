# Order — Behavior Specification

> **Purpose**: Order context current behavior 的 consolidated source of truth。
> 不同於 `specs/features/completed/` 的 historical archive，本檔永遠反映
> accepted specs 之後系統應該遵守的 current behavior。
>
> **Maintenance**: AI 在 completion flow（Step 8.3 / Step 6.3）更新本檔。
> feature completed 時，將 Given/When/Then scenarios merge 到這裡。
> behavior modified 時，用 Delta result 更新對應 section；不要保留 Delta markup。

---

<!-- dflow:section behavior-scenarios -->
## Order Discount Calculation

### BR-001: Pre-discount total

Given 訂單有一筆或多筆 OrderLine items
When 計算 order pre-discount total
Then 結果為每一筆 `OrderLine.UnitPrice * Quantity` 的加總

### BR-002: Full-threshold discount

Given order pre-discount total 大於或等於 NT$50,000
When DiscountPolicy evaluates the order
Then 套用 10% full-threshold discount

### BR-003: Senior customer discount

Given customer 的 `CustomerTier = 'Senior'`
When DiscountPolicy evaluates the order
Then 額外套用 5% customer-tier discount

### BR-004: Compound discount accumulation

Given order 同時符合 full-threshold discount 與 Senior customer discount
When DiscountPolicy calculates the final discount
Then 先套用 full-threshold discount
And 再套用 customer-tier discount
And total discount rate 為 `1 - (1 - fullThresholdRate) * (1 - customerTierRate)`
And NT$60,000 Senior customer order 在稅務處理前的 final amount 為 NT$51,300

#### Edge cases

- EC-001: Given Order 沒有 OrderLine items When 建立 Order Then 拒絕 construction。
- EC-002: Given OrderLine 有 zero 或 negative UnitPrice When 建立 OrderLine Then 拒絕 construction。
- EC-003: Given DiscountRate 超出 `[0.0, 1.0]` When 建立 DiscountRate Then 拒絕 construction。
- EC-004: Given order total 剛好 NT$50,000 When DiscountPolicy evaluates it Then 套用 full-threshold discount。

---

<!-- 
Maintenance notes:
- 依 feature area 組織，不依 spec ID 組織。
- Keep BR-IDs in sync with rules.md.
- 本檔記錄 accepted behavior；phase-spec 記錄舊的 buggy contrast。
-->
