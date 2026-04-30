---
spec-id: SPEC-20260430-001
slug: order-discount-calculation
status: in-progress
created: 2026-04-30
branch: feature/SPEC-20260430-001-order-discount-calculation
---

<!-- dflow:section metadata -->
# Order Discount Calculation

<!-- dflow:section goals-scope -->
## Goals & Scope

本 feature 建立 Order BC 的第一個正式修改入口，處理經銷商「華昕貿易」回報的訂單折扣計算錯誤：滿 NT$50,000 的 9 折與老客戶額外 5% off 必須依業務期望累積，而不是只套用滿額折扣。

Phase 1 `baseline-and-fix` 同時包含三件事：先 baseline capture `OrderEntry.aspx.cs` 的折扣行為、修正折扣累積 bug、把折扣計算抽出為可測試的 `src/Domain/Order/` Domain logic。

本 feature 的邊界刻意限制在 `OrderEntry.aspx.cs` 的折扣計算路徑。`OrderList.aspx.cs`、`OrderDetail.aspx.cs` 或其他頁面若也有相同規則，先記為 tech debt，不在本 phase 擴張。

<!-- dflow:section phase-specs -->
## Phase Specs

| Phase | Date | Slug | Status | File Link |
|---|---|---|---|---|
| 1 | 2026-04-30 | baseline-and-fix | in-progress | [phase-spec-2026-04-30-baseline-and-fix.md](./phase-spec-2026-04-30-baseline-and-fix.md) |

<!-- dflow:section current-br-snapshot -->
## Current BR Snapshot

| BR-ID | Current Rule | First Seen (phase) | Last Updated (phase) | Status |
|---|---|---|---|---|
| BR-001 | 訂單折扣前總金額等於所有 `OrderLine.UnitPrice * Quantity` 的加總。 | phase-1 | phase-1 | active |
| BR-002 | 訂單折扣前總金額大於或等於 NT$50,000 時，套用滿額折扣率 10% off（price multiplier 0.9）。 | phase-1 | phase-1 | active |
| BR-003 | `CustomerTier = 'Senior'` 的老客戶可額外套用客戶等級折扣率 5% off（price multiplier 0.95）。 | phase-1 | phase-1 | active |
| BR-004 | 多個折扣率以乘法累積，順序為先套滿額折扣、再套客戶等級折扣；總折扣率 = `1 - (1 - 滿額折扣率) * (1 - 客戶等級折扣率)`。 | phase-1 | phase-1 | active |

<!-- dflow:section lightweight-changes -->
## Lightweight Changes

| Date | Type | Description | Commit |
|---|---|---|---|
| 2026-05-04 | baseline-capture | Baseline-only capture：已補 `OrderList.aspx.cs` 與 `OrderDetail.aspx.cs` 的跨頁 confirmed behavior，詳見 [`behavior.md`](../../../domain/Order/behavior.md#confirmed-across-pages-baseline-capture-2026-05-04)；新發現的 rounding / `isVip` debt 已記錄於 [`tech-debt.md`](../../../architecture/tech-debt.md)。本 row 無對應 spec 檔。 | n/a - spec capture only |

<!-- dflow:section resume-pointer -->
## Resume Pointer

**Current Progress**: phase 1 `baseline-and-fix` Domain extraction 待實作；2026-05-04 已完成 `OrderList.aspx.cs` / `OrderDetail.aspx.cs` baseline capture，另有 2 條 tech-debt 保持 open，待後續處理。

**Next Action**: 開始 phase 1 implementation：建立 `Order`、`OrderLine`、`Money`、`Quantity`、`DiscountRate`、`DiscountPolicy`；本週確認 rounding consistency 要納入 phase 1 或拆成獨立 lightweight change，並向業務確認 `isVip` multiplier `0.93` 是 legacy promotion、active VIP policy，還是可移除的 dead code。
