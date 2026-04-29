<!-- Template maintained by Dflow. See proposals/PROPOSAL-013 for origin. -->

# Domain Models

> DDD model catalog for one bounded context.

## Context

- **Bounded Context**: Expense
- **Source Code Area**: `src/ExpenseTracker.Domain/Expense/`
- **Last Updated**: 2026-04-28

## Aggregates

| Aggregate | Root Entity | Invariants | Code Mapping | Notes |
|---|---|---|---|---|
| ExpenseReport | ExpenseReport | 提交時必須 >=1 個 ExpenseItem；Submitted 後不可編輯；Items 內每筆金額 > 0 | `ExpenseTracker.Domain.Expense.ExpenseReport` | 詳細不變條件見 `aggregate-design.md`（feature 目錄內） |

## Entities

| Entity | Responsibility | Key Identity | Aggregate | Code Mapping |
|---|---|---|---|---|
| ExpenseReport | 費用申報單主體；管理狀態變遷與 Items 集合 | `ExpenseReportId` (Guid) | ExpenseReport | `ExpenseTracker.Domain.Expense.ExpenseReport` |
| ExpenseItem | 單筆費用項目；金額、日期、類別、收據參照 | `ExpenseItemId`（在 Report 內唯一） | ExpenseReport | `ExpenseTracker.Domain.Expense.ExpenseItem` |

## Value Objects

| Value Object | Responsibility | Equality Components | Code Mapping | Notes |
|---|---|---|---|---|
| Money | 金額 + 幣別 | (Amount, Currency) | `ExpenseTracker.Domain.Expense.Money` | MVP 固定 Currency = "TWD"，但用 VO 預留多幣別 |
| ReceiptReference | 收據編號或檔案參照 | (Value) | `ExpenseTracker.Domain.Expense.ReceiptReference` | 不存影像本體；Infrastructure 層存檔，Domain 只記參照 |
| ExpenseCategory | 費用類別（高鐵、住宿、餐費…） | (Code) | `ExpenseTracker.Domain.Expense.ExpenseCategory` | MVP 列舉內建類別；後續可擴充為動態管理 |

## Domain Services

| Service | Responsibility | Inputs / Outputs | Code Mapping | Notes |
|---|---|---|---|---|
| _(none in MVP)_ | — | — | — | 提交流程能由 Aggregate 自身處理，不需獨立 Service |

## Specifications

| Specification | Rule / Invariant | Used By | Code Mapping | Notes |
|---|---|---|---|---|
| _(none in MVP)_ | — | — | — | 待 phase 2 審核流程出現 reusable 規則時再考慮 |

## Repository Interfaces

| Repository | Aggregate | Query Responsibility | Code Mapping | Notes |
|---|---|---|---|---|
| IExpenseReportRepository | ExpenseReport | GetById / Add / Update（MVP 無查詢清單需求） | `ExpenseTracker.Domain.Expense.IExpenseReportRepository` | 查詢功能（如「主管的待審核清單」）等 phase 2 規劃 |
