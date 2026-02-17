# 修改既有功能流程 — ASP.NET Core

修改或修正既有功能的逐步引導。

## 步驟 1：評估變更

1. **要改什麼？** 取得具體內容。
2. **為什麼？** Bug 修復、新需求、還是行為變更？
3. **受影響的是哪一層？**
   - Domain 不變條件被破壞 → Domain 修復 + 完整規格
   - Application 編排問題 → Application 修復 + 輕量規格
   - Infrastructure Bug（DB、外部服務） → Infrastructure 修復 + 輕量規格
   - API 合約變更 → Presentation 修復 + 如果行為有變就寫規格

## 步驟 2：檢查文件

- `specs/features/completed/` 有沒有規格？
- `specs/domain/{context}/models.md` 有沒有模型定義？
- `rules.md` 有沒有業務規則？
- `events.md` 有沒有 Domain Events？

如果沒有文件，在修改之前先記錄目前行為：

```markdown
## 目前行為
Given [目前的狀態]
When [操作]
Then [目前的結果]

## 變更後行為（本次 PR）
Given [狀態]
When [操作]
Then [新的結果]

## 變更原因
[為什麼要改]
```

## 步驟 3：評估 DDD 影響

### Aggregate 設計是否仍然正確？

需要重新設計 Aggregate 的情況：
- 新的不變條件橫跨了目前在不同 Aggregate 中的物件
- 太大的 Aggregate 造成效能問題
- 太大的 Aggregate 造成並行衝突
- 業務規則現在跨越了 Aggregate 邊界

```
「這次變更影響了 [不變條件]。目前的 Aggregate
邊界還合理嗎？還是需要拆分/合併？」
```

### 需不需要新的 Domain Events？

如果行為變更意味著系統的其他部分需要有不同的反應：
- 新增事件
- 修改事件 payload（注意：向後相容性）
- 新增事件處理器

### Value Objects 是否仍然有效？

如果限制條件有變更：
- 更新 Value Object 驗證
- 檢查 Value Object 的所有使用處

## 步驟 4：實作

遵循層的順序：Domain → Application → Infrastructure → Presentation。

即使是 Bug 修復，也要確認：
- [ ] 修復在正確的層
- [ ] Aggregate 不變條件仍然成立
- [ ] Domain Events 仍然正確觸發
- [ ] 測試已更新，涵蓋此修復
- [ ] 沒有業務邏輯洩漏到錯誤的層

## 步驟 5：更新文件

1. 更新或建立規格
2. 如果 Aggregate 結構有變更，更新 `models.md`
3. 如果業務規則有變更，更新 `rules.md`
4. 如果事件有變更，更新 `events.md`
5. 如果術語有變更，更新 `glossary.md`
6. 如果有捷徑，記錄技術債
