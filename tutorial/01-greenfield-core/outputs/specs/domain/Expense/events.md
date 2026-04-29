<!-- Template maintained by Dflow. See proposals/PROPOSAL-013 for origin. -->

# Domain Events

> Domain event catalog for one bounded context.

## Event Catalog

| Event name | Producer | Trigger | Payload | Consumers | Delivery expectation |
|---|---|---|---|---|---|
| ExpenseReportSubmitted | ExpenseReport (Aggregate) | `ExpenseReport.Submit()` 成功時，於狀態變為 Submitted 後 raise | `(ExpenseReportId, EmployeeId, SubmittedAt, TotalAmount)` | (尚無 consumer；MVP 階段空 handler) | in-process（MediatR `INotification`），同 process 同 transaction 內 dispatch |

## Event Flow Notes

- MVP 階段 ExpenseReportSubmitted 事件雖然 raise 了但暫無 consumer，這是刻意的：先讓 Aggregate 把行為表達完整，等 phase 2 的 Approval handler 出現時直接訂閱即可，不必回頭改 Aggregate。
- Payload 不含 ExpenseItem 細節（避免 event 太重）；consumer 若需要明細，自行透過 `IExpenseReportRepository.GetById` 重新讀取。
- 同 process / 同 transaction 內 dispatch：`SaveChanges` 後由 outbox 或 MediatR pipeline behavior 統一發。MVP 先用最簡單的「SaveChanges 成功後手動 Publish」，phase 3 再評估 outbox。

## Open Questions

- 是否需要 integration event 版本（給 BI / 報表系統訂閱）？等業務需求出現再說。
- Payload 是否要加 SubmitterIp / RequestId 等 audit 欄位？等 phase 2 審計需求釐清。
