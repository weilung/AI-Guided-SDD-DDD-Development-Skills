---
spec-id: SPEC-20260428-001
slug: employee-submit-expense
status: completed
created: 2026-04-28
completed_date: 2026-05-07
branch: feature/SPEC-20260428-001-employee-submit-expense
---

# Employee Submit Expense Report

## Goals & Scope

讓員工能在差旅或公務結束後，建立並提交一份完整的費用申報單給主管審核。本 feature 是 ExpenseTracker 的第一個 feature，phase 1 先完成「員工端提交」，phase 2 補上「主管端 Approve / Reject」。

涉及 Bounded Context：**Expense**（首個 BC，本 feature 同步建立）。

涉及 Aggregates：
- **ExpenseReport**（Aggregate Root，內含 ExpenseItem entities）
- **ApprovalDecision**（Aggregate Root；phase 2 新增，一次 Submit 對應一筆審核決定）

邊界：本 feature 結束後系統能讓員工新增 / 編輯 / 提交 ExpenseReport，主管能對 Submitted 的 ExpenseReport 做 Approve / Reject，並保留每次 Submit 對應的一筆 ApprovalDecision audit trail。通知 email、SLA timer、財務匯款與批次審核不在 phase 2。

## Phase Specs

| Phase | Date | Slug | Status | File Link |
|---|---|---|---|---|
| 1 | 2026-04-28 | mvp | completed | [phase-spec-2026-04-28-mvp.md](./phase-spec-2026-04-28-mvp.md) |
| 2 | 2026-04-29 | supervisor-approval | completed | [phase-spec-2026-04-29-supervisor-approval.md](./phase-spec-2026-04-29-supervisor-approval.md) |

<!-- dflow:section current-br-snapshot -->
## Current BR Snapshot

> Feature 層的 BR 當前狀態（不是歷史）。AI 在以下時機 regenerate 本表：
> - `/dflow:new-phase` 進入時
> - 完成一份 phase-spec 時
> - T2 lightweight spec 定稿時

| BR-ID | Current Rule | First Seen (phase) | Last Updated (phase) | Status |
|---|---|---|---|---|
| BR-001 | 提交 ExpenseReport 時必須至少含 1 個 ExpenseItem，否則拒絕。 | phase-1 (mvp) | phase-1 (mvp) | active |
| BR-002 | ExpenseReport 提交成功後狀態變為 Submitted，不可再被編輯；唯一例外是被 Reject 後可重新編輯並再次 Submit（會建立新的 ApprovalDecision）。 | phase-1 (mvp) | phase-2 (supervisor-approval) | active |
| BR-003 | ExpenseItem 的 Money.Amount 必須 > 0。 | phase-1 (mvp) | phase-1 (mvp) | active |
| BR-004 | 同一 ExpenseReport 內，相同 ReceiptReference 不允許重複加入。 | phase-1 (mvp) | phase-1 (mvp) | active |
| BR-005 | 主管不可審核自己提交的 ExpenseReport；`SubmitterId != ApproverId` 必須由 Domain 層強制。 | phase-2 (supervisor-approval) | phase-2 (supervisor-approval) | active |
| BR-006 | 只有 Status = Submitted 的 ExpenseReport 能被 Approve / Reject；其他狀態一律 raise DomainException。 | phase-2 (supervisor-approval) | phase-2 (supervisor-approval) | active |
| BR-007 | Reject 必須附註原因；ApprovalReason 至少 5 個中文字或至少 10 個英數字，否則 raise DomainException；空白不計，半形 / 全形視覺等價，emoji 算字。 | phase-2 (supervisor-approval) | lightweight-2026-04-30 | active |

> 2026-05-04 BUG-001 note: Current BR Snapshot intentionally not regenerated. BR-007 wording is unchanged; the root cause is implementation-level Unicode truncation / sanitization, not a BR-level delta.

<!-- dflow:section lightweight-changes -->
## Lightweight Changes

> T2 行：描述含「見 `lightweight-{date}-{slug}.md`」外連
> T3 行：inline 完整描述一句話 + 標籤；T3 不產獨立 spec 檔

| Date | Tier | Description | Commit |
|---|---|---|---|
| 2026-04-30 | T2 | Reject reason 從至少 10 字元放寬為 5 中文字 OR 10 英數字。見 [lightweight-2026-04-30-approval-reason-bilingual-length.md](./lightweight-2026-04-30-approval-reason-bilingual-length.md) | `{pending}` |
| 2026-05-04 | T2 | Bug-fix: 前端 substring 截斷 emoji surrogate pair 導致 reject reason 被拒。見 [BUG-001-emoji-surrogate-truncation.md](./BUG-001-emoji-surrogate-truncation.md) | `{pending}` |

## Resume Pointer

> 一句話：目前進展到哪？下一個動作是什麼？
> 開新對話接續工作時，從這裡讀起。

**Current Progress**: Feature completed 2026-05-07; see Integration Summary in features-index.md or below.

**Next Action**: Treat future changes as follow-up features via `/dflow:modify-existing`; do not append T2/T3 changes directly to this completed feature directory.

## Integration Summary

### Feature Overview

`SPEC-20260428-001-employee-submit-expense` delivered ExpenseTracker's first end-to-end feature: employees can create, edit, and submit expense reports, and supervisors can approve or reject submitted reports with an auditable approval decision. It establishes the initial Expense Bounded Context and validates the first complete Domain-driven path from Aggregate design through trial feedback.

Audience: internal engineering team, product owner, trial supervisors, and stakeholders who need to understand what reached stable trial status. This summary is git-strategy-neutral reference material; it is not a commit message and does not assume merge commit, squash, rebase, or fast-forward.

### Change Scope

| Area | Final State |
|---|---|
| Bounded Context | Expense |
| Phase Count | 2 phase specs: phase 1 MVP (2026-04-28), phase 2 supervisor approval (2026-04-29) |
| Lightweight Changes | 2 T2 lightweight specs, 0 T3 inline changes |
| Aggregates Introduced | ExpenseReport, ApprovalDecision |
| Domain Events Introduced | ExpenseReportSubmitted, ExpenseReportApproved, ExpenseReportRejected |
| Feature Status | Completed on 2026-05-07 after 3 stable trial days |

### Phase Summary

| Phase | Summary |
|---|---|
| phase 1 mvp | Established ExpenseReport, ExpenseItem, Money, ReceiptReference, ExpenseCategory, submit flow, and `ExpenseReportSubmitted`. |
| phase 2 supervisor-approval | Added supervisor Approve / Reject, ApprovalDecision, ApprovalReason, `ExpenseReportApproved`, `ExpenseReportRejected`, and Rejected rework behavior. |

### Lightweight Changes Summary

| Date | Type | Summary |
|---|---|---|
| 2026-04-30 | T2 modify | Relaxed BR-007 from a single 10-character threshold to bilingual length validation: at least 5 Chinese / emoji visual characters or at least 10 alphanumeric characters. |
| 2026-05-04 | T2 bug-fix | Fixed emoji surrogate truncation in reject reason handling; Presentation truncation and Domain malformed-input guard now align with BR-007. |

### Aggregates Introduced

- **ExpenseReport**: Owns report lifecycle, items, submit attempt number, and status transitions Draft / Submitted / Approved / Rejected.
- **ApprovalDecision**: Owns the audit record for one supervisor decision against one submit attempt; protects self-approval and reject reason invariants.

### Domain Events Introduced

- **ExpenseReportSubmitted**: Raised when a Draft ExpenseReport is submitted successfully.
- **ExpenseReportApproved**: Raised when a Submitted ExpenseReport is approved by a non-submitting approver.
- **ExpenseReportRejected**: Raised when a Submitted ExpenseReport is rejected with a valid ApprovalReason.

### BR Final State

| BR-ID | Final Rule | Status |
|---|---|---|
| BR-001 | 提交 ExpenseReport 時必須至少含 1 個 ExpenseItem，否則拒絕。 | active |
| BR-002 | ExpenseReport 提交成功後狀態變為 Submitted，不可再被編輯；唯一例外是被 Reject 後可重新編輯並再次 Submit（會建立新的 ApprovalDecision）。 | active |
| BR-003 | ExpenseItem 的 Money.Amount 必須 > 0。 | active |
| BR-004 | 同一 ExpenseReport 內，相同 ReceiptReference 不允許重複加入。 | active |
| BR-005 | 主管不可審核自己提交的 ExpenseReport；`SubmitterId != ApproverId` 必須由 Domain 層強制。 | active |
| BR-006 | 只有 Status = Submitted 的 ExpenseReport 能被 Approve / Reject；其他狀態一律 raise DomainException。 | active |
| BR-007 | Reject 必須附註原因；ApprovalReason 至少 5 個中文字或至少 10 個英數字，否則 raise DomainException；空白不計，半形 / 全形視覺等價，emoji 算字。 | active |

### Tech Debt Outstanding

- **Unicode character counting strategy under i18n**: status remains `open` in `specs/architecture/tech-debt.md`. BUG-001 fixed reject reason truncation and malformed-input handling, but the broader product-wide strategy for grapheme clusters, code points, UTF-16 units, `Intl.Segmenter`, and ICU support remains a separate architecture follow-up.

### Outstanding / Deferred

- Batch approval: deferred from phase 2; if revived, create a follow-up feature under `features/backlog/` rather than reopening this completed feature.
- Approval notification email: out of scope for phase 2; likely Notification concern or Application-layer integration.
- Approval SLA timer / escalation: out of scope until real policy complexity appears.
- Reimbursement BC: planned future context after approval is stable; likely consumes approved report information from Expense.

### Sign-off

- Alice verified implementation completion and all regression tests before closeout on 2026-05-07.
- Carol verified the original reject path with `金額對不上👍` now works.
- Two additional trial supervisors verified normal reject flow during the 3-day stable trial window.

## Outstanding / Future Considerations

- **Batch approval: deferred from phase 2**: phase 2 intentionally shipped single-report Approve / Reject only. If trial feedback proves batch approval is needed, create a follow-up feature under `specs/features/backlog/` and link it back to this completed feature; do not append new T2/T3 work directly here.
- **Approval notification email**: deferred because phase 2 kept notification concerns out of the Expense Domain model.
- **Approval SLA timer / escalation**: deferred until policy complexity is known; may imply a future Approval policy model or separate BC review.
- **Reimbursement BC**: deferred until finance reimbursement workflow starts; likely follows `ExpenseReportApproved`.
