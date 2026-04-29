---
context: Expense
chinese-name: 費用申報
owner: 差旅費用平台小組
created: 2026-04-28
---

# Expense Bounded Context

## Responsibilities

負責員工差旅 / 公務費用申報資料的整個生命週期：建立草稿、加入費用項、提交給主管審核、（後續 phase）審核結果接收與核銷狀態追蹤。Expense BC 是「費用單據」這個業務概念的 source of truth。

## Boundaries

### In Scope
- 建立、編輯、提交 ExpenseReport
- 管理 ExpenseReport 內的 ExpenseItem（新增 / 修改 / 移除）
- ExpenseReport 狀態機：Draft → Submitted（MVP 範圍）
- 發布 ExpenseReportSubmitted Domain Event（給後續 Approval BC 訂閱）

### Out of Scope
- 主管審核流程（規劃中，phase 2）→ 由 Approval BC（暫定）處理
- 核銷匯款（規劃中，phase 3）→ 由 Reimbursement BC（暫定）處理
- 員工身分管理 / 部門組織 → 由 Identity BC（外部，暫不建模）處理
- 收據影像儲存 → 由 Infrastructure 層的 file storage adapter 處理，Domain 只持有 receipt reference

## Core Domain Models

> 詳細定義在 `models.md`，這裡只列出概覽。

### Aggregates
- **ExpenseReport** — 一份費用申報單，內含 1..N 個 ExpenseItem，是核銷流程的主體 Aggregate Root

### Entities
- **ExpenseItem** — ExpenseReport 內的單筆費用項目

### Value Objects
- **Money** — 金額 + 幣別（MVP 先固定 TWD，但用 VO 預留多幣別擴充）
- **ReceiptReference** — 收據編號 / 檔案參照（Domain 不存影像，只持參照）
- **ExpenseCategory** — 費用類別（高鐵、住宿、餐費…）

### Domain Services
- _(MVP 無 Domain Service；提交邏輯能由 Aggregate 自身處理)_

### Repository Interfaces
- **IExpenseReportRepository** — ExpenseReport 的持久化抽象（Domain 定義介面，Infrastructure 實作）

## Interactions with Other Contexts

| Other Context | Interaction Type | Description |
|---|---|---|
| (Approval, 規劃中) | 事件（ExpenseReportSubmitted） | MVP 階段事件已 raise，但目前無 consumer；phase 2 起 Approval BC 將訂閱此事件啟動審核流程 |

## Key Business Rules

> 規則索引在 `rules.md`（BR-ID + 一行摘要），完整 Behavior Scenarios 在 `behavior.md`（Given/When/Then）。這裡只列最重要的幾條。

1. ExpenseReport 提交時必須至少含 1 個 ExpenseItem（BR-001）
2. 提交後狀態變為 Submitted，不可再編輯（BR-002）

## Code Mapping

### Current Implementation
- Domain: `src/ExpenseTracker.Domain/Expense/`
- Application: `src/ExpenseTracker.Application/Expense/`
- Infrastructure: `src/ExpenseTracker.Infrastructure/Expense/`

### Architecture Notes
- 依 Clean Architecture dependency direction 維持 Domain 純淨。
- ExpenseReport 為唯一 Aggregate Root；ExpenseItem 不可在 Aggregate 外被獨立修改。
- Money / ReceiptReference / ExpenseCategory 為 immutable record VOs，建構式驗證。
