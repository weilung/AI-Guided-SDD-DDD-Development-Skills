<!-- Template maintained by Dflow. See proposals/PROPOSAL-013 for origin. -->

# Business Rules

> Order bounded context 的 declarative BR-ID index。

<!-- dflow:section business-rules -->
## Rule Index

| BR-ID | Rule summary | Behavior anchor | Status | Last updated |
|---|---|---|---|---|
| BR-001 | 訂單折扣前總金額等於所有 `OrderLine.UnitPrice * Quantity` 的加總。 | [BR-001](./behavior.md#br-001-pre-discount-total) | active | 2026-04-30 |
| BR-002 | 訂單折扣前總金額大於或等於 NT$50,000 時，套用滿額折扣率 10% off。 | [BR-002](./behavior.md#br-002-full-threshold-discount) | active | 2026-04-30 |
| BR-003 | `CustomerTier = 'Senior'` 的老客戶可額外套用客戶等級折扣率 5% off。 | [BR-003](./behavior.md#br-003-senior-customer-discount) | active | 2026-04-30 |
| BR-004 | 多個折扣率以乘法累積，順序為先套滿額折扣、再套客戶等級折扣。 | [BR-004](./behavior.md#br-004-compound-discount-accumulation) | active | 2026-04-30 |

## Status Legend

| Status | Meaning |
|---|---|
| draft | 規則已識別，但尚未完整驗證。 |
| active | 規則已驗證，預期由系統執行。 |
| deprecated | 規則保留作為歷史紀錄，但不再 active。 |

## Open Questions

- Tax calculation 與 invoice rounding 尚未納入這些 BR。
- 後續確認 `Senior` 以外的 customer tiers 是否需要 generalized discount table 或 policy strategy。
