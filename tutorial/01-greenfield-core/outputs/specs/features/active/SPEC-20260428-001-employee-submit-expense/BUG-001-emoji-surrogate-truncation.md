---
id: BUG-001
title: Emoji surrogate truncation in reject reason
status: in-progress
bounded-context: Expense
created: 2026-05-04
branch: feature/SPEC-20260428-001-employee-submit-expense
host-feature: SPEC-20260428-001-employee-submit-expense
tier: T2
bug_number: 001
reported_date: 2026-05-04
reported_by: Carol
slug: emoji-surrogate-truncation
---

# Emoji surrogate truncation in reject reason

## Problem

2026-05-04 早上，財務部主管 Carol 回報：她在 2026-05-03 週日加班時想 reject 一張差旅單，退回原因輸入「金額對不上👍」。前端 counter 顯示「6 字 ✓」，但送出後 API 回應「reject reason 不合法」。

Alice 用 browser dev tools 重現後確認：React form 在限制 reject reason 長度時使用 `value.substring(0, maxLen)`，在 emoji 的 UTF-16 surrogate pair 中間截斷。送到 API 的字串包含 invalid surrogate，Domain layer 的 `ApprovalReason` Value Object 在字數計算路徑中拋出 generic error，導致一個符合 BR-007 意圖的 reason 被拒絕。

這是 bug-fix，不是新的審核需求。BR-007 的 rule wording 維持不變；修正範圍是 Presentation layer 的截字邏輯，以及 Domain layer 對 malformed Unicode input 的防衛與錯誤訊息。

## Reason for Change

主管在小範圍試用中輸入的 reject reason 符合目前 BR-007：「至少 5 個中文字或至少 10 個英數字；空白不計，半形 / 全形視覺等價，emoji 算字。」系統卻因前端截斷 emoji surrogate pair 而拒絕。修正目標是讓 UI counter、送出 payload、Domain validation 對同一個有效 reason 得到一致結果。

## Behavior Delta

### ADDED - BR / behavior added

_(none)_

### MODIFIED - implementation behavior modified in this fix

#### Rule: BR-007 Reject 必須附註原因

**Before**: Given 一份 ExpenseReport 處於 Submitted 狀態 And `ApproverId != SubmitterId` When 主管在 React reject form 輸入 `金額對不上👍` And 前端 counter 顯示 `6 字 ✓` And form 用 `substring(0, maxLen)` 截斷 value Then payload 可能包含 invalid surrogate And `ApprovalReason` 在字數計算時拋出 generic error And reject 被拒絕。

**After**: Given 同樣輸入 `金額對不上👍` When React reject form 需要限制長度 Then Presentation layer 使用 grapheme-aware 截斷，不會切開 surrogate pair And payload 保持 valid Unicode string And `ApprovalReason` normalize 後依 BR-007 接受該 reason，reject 可繼續完成。

**Reason**: BR-007 本身已允許中文短語與 emoji visual characters；錯誤在 implementation-level Unicode handling，而不是 business rule wording。

### REMOVED - BR removed

_(none)_

### RENAMED - BR renamed

_(none)_

### UNCHANGED - explicitly unaffected

- BR-001 提交 ExpenseReport 時必須至少含 1 個 ExpenseItem。
- BR-002 Submitted 後不可編輯，但 Rejected 可重編並再次 Submit。
- BR-003 ExpenseItem 的 Money.Amount 必須 > 0。
- BR-004 同一 ExpenseReport 內，相同 ReceiptReference 不允許重複加入。
- BR-005 主管不可審核自己提交的 ExpenseReport。
- BR-006 只有 Submitted 的 ExpenseReport 能被 Approve / Reject。
- BR-007 wording unchanged — root cause is implementation-level, not BR-level. Reject reason 仍是「至少 5 個中文字或至少 10 個英數字；空白不計，半形 / 全形視覺等價，emoji 算字」。

## Root Cause

React reject form 使用 UTF-16 code unit-based `value.substring(0, maxLen)` 做輸入截斷。當使用者輸入 emoji 且截斷點落在 surrogate pair 中間時，前端會產生包含 unpaired surrogate 的 invalid string。

API 收到 malformed payload 後仍進入 Domain validation。`ApprovalReason` 的字數計算路徑假設 input 是 well-formed Unicode，對 invalid surrogate 沒有先做 detection，因此拋出 generic calculation error。結果是前端 counter 顯示可送出，但 Domain layer 拒絕同一段文字。

## Fix Approach

Primary fix in **Presentation layer**:

- Replace `value.substring(0, maxLen)` with grapheme-aware truncation.
- Prefer `Intl.Segmenter` for visual-character segmentation when available.
- Use `Array.from(str)` as minimum fallback so surrogate pairs are not split, while full grapheme-cluster strategy remains tracked as tech debt.
- Ensure the visible counter and truncated value use the same counting strategy for reject reason input.

Secondary guard in **Domain layer**:

- Normalize `ApprovalReason` input to Unicode NFC before length checks.
- Detect invalid / unpaired surrogate before counting characters.
- Throw a specific `InvalidApprovalReasonException` with a clear message for malformed Unicode input instead of leaking a generic calculation error.
- Keep BR-007 thresholds unchanged; do not add BR-008.

No Application contract, API route, database schema, Aggregate boundary, or Domain Event changes.

<!-- dflow:section implementation-tasks -->
## Implementation Tasks

> Keep T2 Light tasks concise. If the fix scope starts to expand, AI should pause and ask the developer whether to keep this as T2 or upgrade it to T1. Do not auto-upgrade based on task count alone.
>
> Recommended layer tags (Core): `DOMAIN` / `APP` / `INFRA` / `API` / `TEST` / `DOC`

- [ ] PRESENTATION-1: Replace reject reason `substring(0, maxLen)` truncation with grapheme-aware truncation (`Intl.Segmenter` preferred; `Array.from(str)` fallback must not split surrogate pairs).
- [ ] PRESENTATION-2: Make the reject reason counter use the same counting strategy as the truncation logic.
- [ ] DOMAIN-1: Normalize `ApprovalReason` input to NFC before BR-007 length checks.
- [ ] DOMAIN-2: Detect invalid / unpaired surrogate before length calculation and throw `InvalidApprovalReasonException` with a clear message.
- [ ] TEST-1: Add Presentation test that `金額對不上👍` remains a valid string after truncation and the counter still shows `6 字 ✓`.
- [ ] TEST-2: Add Domain unit test that `ApprovalReason("金額對不上👍")` passes BR-007.
- [ ] TEST-3: Add regression test that manually constructed half-surrogate input raises `InvalidApprovalReasonException`, not a generic calculation error.
- [ ] TEST-4: Add integration/API test that reject with `金額對不上👍` reaches the normal reject path.
- [ ] DOC-1: Update `_index.md` Lightweight Changes and `specs/architecture/tech-debt.md`; do not regenerate Current BR Snapshot because BR wording is unchanged.

Layer tag list above is the recommended set; this bug-fix adds `PRESENTATION` because the primary defect is in the React form.

<!-- dflow:section open-questions -->
## Open Questions

- Resolved 2026-05-04: Do not add BR-008 for invalid surrogate / invisible character counting. Invalid surrogate is malformed input sanitization, not a business rule.
- Deferred: full i18n character counting strategy for all user-facing length limits. See `Unicode character counting strategy under i18n` in [specs/architecture/tech-debt.md](../../../architecture/tech-debt.md).
- Deferred: adopting ICU or a shared i18n library is outside this T2 bug-fix.

## Tech Debt Discovered (if any)

Added `Unicode character counting strategy under i18n` to [specs/architecture/tech-debt.md](../../../architecture/tech-debt.md), reported 2026-05-04 from BUG-001.
