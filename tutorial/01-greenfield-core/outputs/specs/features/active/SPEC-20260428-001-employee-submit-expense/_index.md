---
spec-id: SPEC-20260428-001
slug: employee-submit-expense
status: in-progress
created: 2026-04-28
branch: feature/SPEC-20260428-001-employee-submit-expense
---

# Employee Submit Expense Report

## Goals & Scope

讓員工能在差旅或公務結束後，建立並提交一份完整的費用申報單給主管審核。本 feature 是 ExpenseTracker 的第一個 feature，**MVP 範圍刻意收斂**到「員工端的提交動作」這一段，不含主管審核（phase 2）也不含財務核銷（phase 3）。

涉及 Bounded Context：**Expense**（首個 BC，本 feature 同步建立）。
涉及 Aggregate：**ExpenseReport**（Aggregate Root，內含 ExpenseItem entities）。

邊界：本 feature 結束後系統能讓員工新增 / 編輯 / 提交 ExpenseReport，並 raise 一個 ExpenseReportSubmitted Domain Event；該 event 在 MVP 階段沒有 consumer（保留給 phase 2）。

## Phase Specs

| Phase | Date | Slug | Status | File Link |
|---|---|---|---|---|
| 1 | 2026-04-28 | mvp | in-progress | [phase-spec-2026-04-28-mvp.md](./phase-spec-2026-04-28-mvp.md) |

<!-- dflow:section current-br-snapshot -->
## Current BR Snapshot

> Feature 層的 BR 當前狀態（不是歷史）。AI 在以下時機 regenerate 本表：
> - `/dflow:new-phase` 進入時
> - 完成一份 phase-spec 時
> - T2 lightweight spec 定稿時

| BR-ID | Current Rule | First Seen (phase) | Last Updated (phase) | Status |
|---|---|---|---|---|
| BR-001 | 提交 ExpenseReport 時必須至少含 1 個 ExpenseItem，否則拒絕。 | phase-1 (mvp) | phase-1 (mvp) | draft |
| BR-002 | ExpenseReport 提交成功後狀態變為 Submitted，且不可再被編輯。 | phase-1 (mvp) | phase-1 (mvp) | draft |
| BR-003 | ExpenseItem 的 Money.Amount 必須 > 0。 | phase-1 (mvp) | phase-1 (mvp) | draft |
| BR-004 | 同一 ExpenseReport 內，相同 ReceiptReference 不允許重複加入。 | phase-1 (mvp) | phase-1 (mvp) | draft |

<!-- dflow:section lightweight-changes -->
## Lightweight Changes

> T2 行：描述含「見 `lightweight-{date}-{slug}.md`」外連
> T3 行：inline 完整描述一句話 + 標籤；T3 不產獨立 spec 檔

| Date | Tier | Description | Commit |
|---|---|---|---|
| _(none yet)_ | — | — | — |

## Resume Pointer

> 一句話：目前進展到哪？下一個動作是什麼？
> 開新對話接續工作時，從這裡讀起。

**Current Progress**: phase-1 (mvp) phase-spec drafted; Aggregate design done; Implementation Tasks generated. 尚未進入 Step 6 git branch。

**Next Action**: 跑 `/dflow:next` 進入 Step 6 建 git branch `feature/SPEC-20260428-001-employee-submit-expense`，再進 Step 7 從 Domain layer 開始實作（DOMAIN-1 起）。
