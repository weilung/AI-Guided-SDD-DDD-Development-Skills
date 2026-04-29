---
spec-id: SPEC-20260428-001
slug: employee-submit-expense
status: in-progress
created: 2026-04-28
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
| 2 | 2026-04-29 | supervisor-approval | in-progress | [phase-spec-2026-04-29-supervisor-approval.md](./phase-spec-2026-04-29-supervisor-approval.md) |

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

## Open Questions

- 要不要支援「批次 Approve」？phase 2 不做。保留到後續 phase 或實際使用後再評估。

## Resume Pointer

> 一句話：目前進展到哪？下一個動作是什麼？
> 開新對話接續工作時，從這裡讀起。

**Current Progress**: phase-2 (supervisor-approval) is in progress; `approval-reason-bilingual-length` has been implemented for trial use; BUG-001 (`emoji-surrogate-truncation`) is documented as a T2 bug-fix. Current BR Snapshot remains unchanged because BR-007 wording did not change.

**Next Action**: Implement BUG-001: replace Presentation truncation with grapheme-aware logic, add `ApprovalReason` invalid surrogate guard, and add regression tests from `BUG-001-emoji-surrogate-truncation.md`.
