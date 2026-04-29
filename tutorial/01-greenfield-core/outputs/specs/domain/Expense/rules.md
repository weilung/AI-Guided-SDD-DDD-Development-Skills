<!-- Template maintained by Dflow. See proposals/PROPOSAL-013 for origin. -->

# Business Rules

> Declarative BR-ID index for one bounded context.

<!-- dflow:section business-rules -->
## Rule Index

| BR-ID | Rule summary | Behavior anchor | Aggregate | Status | Last updated |
|---|---|---|---|---|---|
| BR-001 | 提交 ExpenseReport 時必須至少含 1 個 ExpenseItem，否則拒絕。 | [BR-001](./behavior.md#br-001-submit-requires-at-least-one-item) | ExpenseReport | draft | 2026-04-28 |
| BR-002 | ExpenseReport 提交成功後狀態變為 Submitted，且不可再被編輯（包含新增 / 修改 / 刪除 Item）。 | [BR-002](./behavior.md#br-002-submitted-report-is-immutable) | ExpenseReport | draft | 2026-04-28 |
| BR-003 | ExpenseItem 的 Money.Amount 必須 > 0；金額 = 0 或負數的 Item 在加入時就拒絕，提交時亦會 fail-fast。 | [BR-003](./behavior.md#br-003-item-amount-must-be-positive) | ExpenseReport | draft | 2026-04-28 |
| BR-004 | 同一 ExpenseReport 內，相同 ReceiptReference 只允許出現一次；重複加入須警告（MVP 處理方式：拒絕並回 ValidationError，提示用戶確認）。 | [BR-004](./behavior.md#br-004-duplicate-receipt-rejected) | ExpenseReport | draft | 2026-04-28 |

<!-- BR-001 / BR-002 來自 SPEC-20260428-001 phase MVP 主場景；BR-003 / BR-004 來自同 phase 的 edge case 衍生。所有 BR status 都是 draft —— 等 phase MVP 完成（Step 8.3）並合併進 behavior.md 後升 active。behavior anchor 連結指向尚未建立的 behavior.md（per Step 8.3 才建），點開時為 dead link 屬預期；/dflow:verify 會把這個 dead link 視為「pending behavior merge」而非 error。 -->

## Status Legend

| Status | Meaning |
|---|---|
| draft | Rule is identified but not fully validated. |
| active | Rule is validated and expected to be enforced. |
| deprecated | Rule is retained for history but no longer active. |

## Open Questions

- BR-004 重複收據的處理是「拒絕」還是「警告但允許覆寫」？MVP 暫採「拒絕」最嚴格，等實際使用觀察是否需放寬。
- BR-002 的「不可編輯」是否包含「撤回到 Draft 重編」？目前 MVP 採「完全不可編輯」，後續主管退件 (Rejected) 時才開窗回 Draft，等 phase 2 主管審核時釐清。
