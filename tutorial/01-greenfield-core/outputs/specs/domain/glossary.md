<!-- Template maintained by Dflow. See proposals/PROPOSAL-013 for origin. -->

# Glossary

> Ubiquitous Language for ExpenseTracker.

## Terms

| Term | Definition | Bounded Context | Code Mapping | Notes |
|---|---|---|---|---|
| ExpenseReport | 員工針對一次出差或一段差旅期間提交的整份費用申請單；內含 1..N 個 ExpenseItem，是核銷流程的主體 Aggregate Root。 | Expense | `ExpenseTracker.Domain.Expense.ExpenseReport` | 狀態：Draft / Submitted / Approved / Rejected / Reimbursed（MVP 只實作 Draft → Submitted） |
| ExpenseItem | 報銷單內單一筆費用項目（例：一張高鐵票、一筆住宿）；金額、發生日期、類別、收據編號為必要欄位。屬於 ExpenseReport 內的 Entity。 | Expense | `ExpenseTracker.Domain.Expense.ExpenseItem` | 不可獨立存在；金額必須 > 0 |
| Approver | 負責審核 ExpenseReport 的角色（通常為員工的直屬主管或部門簽核人）。MVP 階段不建模審核流程，僅記錄「Submitted 後可被 Approver 看見」的概念。 | Expense | (尚未建模) | 預留術語，phase 2+ 才落到程式 |
| Reimbursement | 核銷完成後，財務匯款給員工的動作或紀錄；ExpenseReport 從 Approved → Reimbursed 經過此事件。 | Expense | (尚未建模) | 預留術語，phase 3+ 才落到程式 |

<!-- Approver / Reimbursement 兩個 term 在 MVP phase 還沒對應的 code mapping；先入詞表是因為它們會出現在 BR 描述（BR-002 提到「可被主管看見」即指 Approver），同事讀 spec 時必須能查到定義。Code mapping 等對應 phase 落地時再回補。 -->

## Open Questions

- Approver 的選定規則目前未定（預設主管？費用金額 > 上限要二次簽核？）—— 等 phase 2「主管審核」spec 啟動時釐清
- Reimbursement 是否要建模成獨立 Aggregate（ReimbursementBatch）還是 ExpenseReport 的狀態尾端，等 phase 3 跟財務團隊確認
