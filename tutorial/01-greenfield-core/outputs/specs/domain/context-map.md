<!-- Template maintained by Dflow. See proposals/PROPOSAL-013 for origin. -->

# Context Map

> Bounded context relationship map for the ExpenseTracker Core architecture.

## Contexts

| Bounded Context | Responsibility | Owner / Team | Primary Module | Notes |
|---|---|---|---|---|
| Expense | 員工的費用申報資料生命週期：建立 / 編輯 / 提交 / 審核（規劃中）/ 核銷（規劃中）的 ExpenseReport 主體。 | 差旅費用平台小組 | `ExpenseTracker.Domain.Expense` | 由 SPEC-20260428-001 建立；MVP 只覆蓋 Draft → Submitted |

<!-- 第一個 BC 由 SPEC-20260428-001-employee-submit-expense 建立。後續 phase 預期會加入 Approval / Reimbursement BC（或繼續留在 Expense BC 內，視審核流程複雜度而定）—— 等實際 phase 規劃時再決定 BC 邊界。 -->

## Relationships

| Upstream | Downstream | Relationship Type | Published Language | ACL | Events |
|---|---|---|---|---|---|
| (尚無跨 context 關係) | — | — | — | — | — |

<!-- 目前只有一個 BC，無關係可填。Phase 2 引入 Approval BC 後，預期 Expense → Approval 為 Customer/Supplier，published language 為 ExpenseReportSubmitted event。 -->

## Integration Notes

- 目前單一 BC 不需 integration design。Phase 2 起若拆出 Approval BC，預期透過 ExpenseReportSubmitted Domain Event 同 process 內 dispatch。

## Open Questions

- 主管審核（Approval）要不要拆成獨立 BC，還是留在 Expense BC 內當另一個 Aggregate？等 phase 2 的 domain modeling 階段決定。
- Reimbursement（財務匯款）跨組織邊界（差旅平台小組 → 財務系統），預期是獨立 BC 並透過 integration event 整合，但細節未定。
