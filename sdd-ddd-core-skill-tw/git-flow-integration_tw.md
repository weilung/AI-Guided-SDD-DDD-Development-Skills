# Git Flow 與 SDD/DDD 整合 — ASP.NET Core

與 WebForms 版本相同的分支策略。關鍵差異：閘門檢查現在驗證 Clean Architecture 的層規則，而非 WebForms 特定的檢查。

## 分支與流程對應關係

```
main（正式環境）
  │
  └─ release/{版本號}           ← AI 檢查：所有 spec 已歸檔、context-map 是最新的
      │
      └─ develop                ← AI 檢查：spec 已審查、tech-debt 已更新
          │
          ├─ feature/{SPEC-ID}-{名稱}    ← 完整 SDD + DDD 流程
          │   閘門：第一次 commit 之前需有 spec + Aggregate 設計
          │
          ├─ bugfix/{BUG-ID}-{名稱}      ← 輕量 SDD 流程
          │   閘門：輕量 spec 並識別受影響的層
          │
          └─ hotfix/{HOT-ID}-{名稱}      ← 最精簡：修完再補文件
              閘門：24 小時內補建 spec
```

## 分支命名規範

```
feature/{CONTEXT-ID}-{簡短描述}
bugfix/BUG-{NUMBER}-{簡短描述}
hotfix/HOT-{NUMBER}-{簡短描述}
```

## 閘門檢查

### feature/ — 建立之前
- [ ] 規格至少有 draft 狀態
- [ ] 已識別 Bounded Context
- [ ] 已記錄 Aggregate 設計（哪個 Aggregate、什麼不變條件）
- [ ] 已識別 Domain Events
- [ ] 至少一個 Given/When/Then 情境

### feature/ — 合併到 develop 之前
- [ ] 規格狀態：completed
- [ ] 領域層：零外部依賴
- [ ] Domain 層以外沒有業務邏輯
- [ ] Domain Events 已記錄在 events.md
- [ ] 術語表、models.md、rules.md 已更新
- [ ] Domain 單元測試通過
- [ ] Domain 實體上沒有 ORM/序列化屬性

### Commit Message 規範

```
[SPEC-ID] 簡短描述

[EXP-001] 定義 ExpenseReport Aggregate 及提交不變條件
[EXP-001] 新增 CreateExpenseReport command 和 handler
[EXP-001] 實作 ExpenseReport 的 EF Core 設定
[BUG-042] 修正 Money value object 的捨入問題
```

## 每日開發流程

```
1. 開始：檢查 spec → 如果沒有就建立 → 設計 Aggregate → 建分支
2. 實作：Domain 先 → Application → Infrastructure → Presentation
3. 測試：Domain 單元測試 → Application 測試 → 整合測試
4. 審查：層合規性 → 規格合規性 → 架構評分
5. 合併：更新文件 → 將 spec 移到 completed/
```
