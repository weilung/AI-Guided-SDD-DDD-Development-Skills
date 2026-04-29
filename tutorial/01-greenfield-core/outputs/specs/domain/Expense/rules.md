<!-- Template maintained by Dflow. See proposals/PROPOSAL-013 for origin. -->

# Business Rules

> Declarative BR-ID index for one bounded context.

<!-- dflow:section business-rules -->
## Rule Index

| BR-ID | Rule summary | Behavior anchor | Aggregate | Status | Last updated |
|---|---|---|---|---|---|
| BR-001 | 提交 ExpenseReport 時必須至少含 1 個 ExpenseItem，否則拒絕。 | [BR-001](./behavior.md#br-001-submit-requires-at-least-one-item) | ExpenseReport | active | 2026-04-28 |
| BR-002 <!-- phase-2 MODIFIED --> | ExpenseReport 提交成功後狀態變為 Submitted，不可再被編輯；唯一例外是被 Reject 後可重新編輯並再次 Submit（會建立新的 ApprovalDecision）。 | [BR-002](./behavior.md#br-002-submitted-report-is-immutable-except-rejected-rework) | ExpenseReport | active | 2026-04-29 |
| BR-003 | ExpenseItem 的 Money.Amount 必須 > 0；金額 = 0 或負數的 Item 在加入時就拒絕，提交時亦會 fail-fast。 | [BR-003](./behavior.md#br-003-item-amount-must-be-positive) | ExpenseReport | active | 2026-04-28 |
| BR-004 | 同一 ExpenseReport 內，相同 ReceiptReference 只允許出現一次；重複加入須警告（MVP 處理方式：拒絕並回 ValidationError，提示用戶確認）。 | [BR-004](./behavior.md#br-004-duplicate-receipt-rejected) | ExpenseReport | active | 2026-04-28 |
| BR-005 <!-- phase-2 ADDED --> | 主管不可審核自己提交的 ExpenseReport；`SubmitterId != ApproverId` 必須由 Domain 層強制。 | [BR-005](./behavior.md#br-005-approver-cannot-approve-own-report) | ApprovalDecision | active | 2026-04-29 |
| BR-006 <!-- phase-2 ADDED --> | 只有 Status = Submitted 的 ExpenseReport 能被 Approve / Reject；其他狀態一律 raise DomainException。 | [BR-006](./behavior.md#br-006-only-submitted-report-can-be-approved-or-rejected) | ExpenseReport | active | 2026-04-29 |
| BR-007 <!-- phase-2 ADDED --> | Reject 必須附註原因；ApprovalReason 至少 10 字元，否則 raise DomainException。 | [BR-007](./behavior.md#br-007-reject-requires-reason) | ApprovalDecision | active | 2026-04-29 |

<!-- phase 2 Delta:
- MODIFIED BR-002：phase 1 原文「ExpenseReport 提交成功後狀態變為 Submitted，且不可再被編輯。」更新為 Rejected 可重編並再次 Submit。
- ADDED BR-005..007：主管不可自審、只有 Submitted 可審、Reject reason 必填且至少 10 字元。
behavior.md 仍由 finish-feature / Step 8.3 從 phase-spec 場景 merge；anchor 可能暫時是 pending link。
-->

## Status Legend

| Status | Meaning |
|---|---|
| draft | Rule is identified but not fully validated. |
| active | Rule is validated and expected to be enforced. |
| deprecated | Rule is retained for history but no longer active. |

## Open Questions

- 要不要支援「批次 Approve」？phase 2 不做。先收集主管實際使用回饋，再決定是否新增後續 phase。
- Approval policy 若出現金額門檻、二階主管、代理簽核或 SLA escalation，可能需要重新評估是否拆出 Approval BC。
