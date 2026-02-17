# Git Flow 與 SDD/DDD 整合

本文件定義 Git Flow 分支如何對應到 SDD 工作流程的各個階段，以及每個轉換點應執行的檢查。

## 分支與流程對應關係

```
main（正式環境）
  │
  └─ release/{版本號}           ← AI 檢查：所有 spec 已歸檔到 completed/
      │
      └─ develop                ← AI 檢查：spec 已審查、tech-debt 已更新
          │
          ├─ feature/{SPEC-ID}-{名稱}    ← 完整 SDD 流程
          │   閘門：第一次 commit 之前 spec 必須存在
          │
          ├─ bugfix/{BUG-ID}-{名稱}      ← 輕量 SDD 流程
          │   閘門：至少要有輕量 spec
          │
          └─ hotfix/{HOT-ID}-{名稱}      ← 最精簡：修完再補文件
              閘門：24 小時內補建 spec
```

## 分支命名規範

```
feature/{CONTEXT-ID}-{簡短描述}
  範例：
    feature/EXP-001-jpy-currency-support
    feature/HR-003-leave-approval-workflow
    feature/SHARED-002-audit-logging

bugfix/{BUG-ID}-{簡短描述}
  範例：
    bugfix/BUG-042-rounding-inconsistency
    bugfix/BUG-051-exchange-rate-cache

hotfix/{HOT-ID}-{簡短描述}
  範例：
    hotfix/HOT-007-production-calc-error
```

ID 前綴將分支連結到其規格文件。這就是可追溯鏈：
```
Git 分支 → 規格文件 → 領域概念 → 程式碼實作 → 測試
```

## 各分支類型的閘門檢查

### feature/ 分支 — 建立之前

AI 應確認：
- [ ] 規格存在於 `specs/features/active/{SPEC-ID}-*.md`
- [ ] 規格狀態為 `draft` 或 `in-progress`
- [ ] 已識別 Bounded Context
- [ ] 至少定義了一個 Given/When/Then 情境
- [ ] 已識別領域概念（即使尚未加入 `models.md`）

如有缺漏，在建分支之前引導開發者完成：

```
「在建分支之前，我們先把規格準備好。
我看這會動到 Expense context。讓我幫你草擬規格
——應該只需要幾分鐘，而且能幫助我們在實作時保持聚焦。」
```

### feature/ 分支 — 合併到 develop 之前

AI 應確認：
- [ ] 規格狀態已更新為 `completed`
- [ ] 所有新的業務邏輯都在 `src/Domain/`（不只是在 Code-Behind）
- [ ] 新術語已加入 `glossary.md`
- [ ] `rules.md` 和 `models.md` 已更新（如適用）
- [ ] 任何捷徑都已記錄為技術債
- [ ] Domain 層程式碼沒有 System.Web 引用

### bugfix/ 分支 — 建立之前

AI 應確認：
- [ ] 輕量規格已存在，或在本次會話中建立
- [ ] 根因已記錄
- [ ] 修復方式已記載

### bugfix/ 分支 — 合併到 develop 之前

- [ ] 規格中已記錄修復內容
- [ ] 如果底層問題更廣泛，已記錄技術債
- [ ] 如果動到業務邏輯，已評估 Domain 層抽離

### hotfix/ 分支 — 加速流程

Hotfix 是緊急的。不要阻擋開發者：
1. 先幫忙修好問題
2. 修復合併後再補建規格
3. 如果這個 hotfix 暴露了系統性問題，記錄到 `tech-debt.md`

```
「我們先修正式環境的問題。部署完成後，
我會幫你建一份規格，記錄發生了什麼事以及我們改了什麼。」
```

### release/ 分支 — 發布檢查清單

從 develop 建立 release 分支之前：
- [ ] `active/` 中所有規格狀態為 `completed`，或已移到 `backlog/`
- [ ] `specs/migration/tech-debt.md` 已審查並排定優先順序
- [ ] 沒有孤立的無規格分支
- [ ] `glossary.md` 與程式碼一致

## Commit Message 規範

將 commit 連結到規格：

```
[SPEC-ID] 簡短描述

範例：
[EXP-001] 新增日幣支援到 Money value object
[EXP-001] 將匯率邏輯抽離到 Domain service
[BUG-042] 修正捨入不一致，抽離到 Money.Round()
[HOT-007] 緊急修正正式環境的計算錯誤
```

## 每日開發流程

```
1. 開發者：「我要開始做 [功能/Bug]」
   AI：檢查有沒有規格 → 如果沒有就引導建立
       → 根據 Spec ID 建議分支名稱

2. 開發者建立分支
   AI：確認分支名稱符合規範
       → 提醒：「業務邏輯放到 src/Domain/」

3. 開發過程中
   AI：參考 specs/domain/ 的知識回答問題
       → 如果業務邏輯正在寫進 Code-Behind，提出警示
       → 在適當時機建議 Domain 層的設計模式
       → 幫助維持精簡的 Code-Behind

4. PR/合併之前
   AI：執行合併檢查清單
       → 協助更新規格、術語表、技術債
       → 建議將已完成的規格移到 completed/

5. 合併之後
   AI：確認所有文件資產已更新
       → 如相關，建議 backlog/ 中的下一個項目
```

## 與 CI/CD 整合（未來強化方向）

這些檢查未來可以自動化到 CI 中：
- 驗證 `src/Domain/` 目錄中沒有 `using System.Web`
- 驗證 feature/ 或 bugfix/ 前綴的分支都有對應的規格文件
- 驗證 Domain/ 檔案有變更時 `glossary.md` 和 `rules.md` 也有更新
- 檢查 commit message 是否符合 Spec ID 格式

目前由 AI 在開發過程中以對話方式執行這些檢查。
