# 修改既有功能流程 — ASP.NET Core

修改或修正既有功能的逐步引導。

當開發者打 `/dflow:modify-existing` 或 `/dflow:bug-fix`（或自然語言表達修改意圖、經安全網確認後）時觸發——詳見 `SKILL_tw.md` § 工作流程透明度。

**本流程的 Phase Gate 位置**（跨越前要停下來等確認）：
- Step 2 → Step 3（基準線記錄完 → 評估 DDD 影響）
- Step 3 → Step 4（DDD 影響決策 → 實作）
- Step 4 → Step 5（實作完成 → 更新文件）

其餘 step 間的轉換都是 **step-internal**：只宣告「Step N 完成，進入 Step N+1」，不等確認。完整透明度規則見 `SKILL_tw.md` § 工作流程透明度。

**步驟數說明**：Core 版只有 5 步（WebForms 版有 6 步），因為 Clean Architecture 的分層結構已經把關注點分開了——不需要「從 Code-Behind 抽離」這個步驟。

**`/dflow:bug-fix` 的 ceremony 調整**：走輕量路線——用輕量規格模板（見 `templates/lightweight-spec.md`）取代完整 feature-spec；Step 3 預設「沒有 DDD 影響，原地修復」，除非 bug 本身就在 Domain 邏輯裡。

## 步驟 1：評估變更

1. **要改什麼？** 取得具體內容。
2. **為什麼？** Bug 修復、新需求、還是行為變更？
3. **受影響的是哪一層？**
   - Domain 不變條件被破壞 → Domain 修復 + 完整規格
   - Application 編排問題 → Application 修復 + 輕量規格
   - Infrastructure Bug（DB、外部服務） → Infrastructure 修復 + 輕量規格
   - API 合約變更 → Presentation 修復 + 如果行為有變就寫規格

**→ Step-internal 轉換**：Step 1 完成。宣告「Step 1 完成（變更已評估，受影響的層已識別），進入 Step 2：檢查文件」後繼續。

## 步驟 2：檢查文件

- `specs/features/completed/` 有沒有規格？
- `specs/domain/{context}/models.md` 有沒有模型定義？
- `rules.md` 有沒有業務規則？
- `events.md` 有沒有 Domain Events？

如果沒有文件，在修改之前先記錄目前行為——用下面的 **Delta 格式**。

### Delta 規格格式（修改用）

用 ADDED / MODIFIED / REMOVED / RENAMED 加一個可選的 UNCHANGED 段。每條規則都保留 Given/When/Then；Delta 段只存在於本次 spec 裡，**不**累積到 `specs/domain/{context}/behavior.md`（git 歷史已經覆蓋變更軌跡）。

```markdown
## 行為變更（Delta）

### ADDED — 新增的行為
#### 規則：BR-NN 規則名稱
Given {Aggregate 初始狀態}
When {呼叫的 Command 或 Aggregate 方法}
Then {新的 Aggregate 狀態}
And {產生的 Domain Event}

### MODIFIED — 修改的行為
#### 規則：BR-NN 規則名稱
**原本**：Given … When … Then {舊結果} / {舊事件}
**改為**：Given … When … Then {新結果} / {新事件}
**原因**：{為什麼改}

### REMOVED — 移除的行為
#### 規則：BR-NN 規則名稱
**原因**：{為什麼移除}

### RENAMED — 重新命名
#### 規則：{舊名稱} → {新名稱}
**原因**：{為什麼改名，例如術語演化 / Aggregate 拆分 / 對齊 glossary}

### UNCHANGED — 明確不受影響（可選）
- BR-003 金額上限
- BR-005 提交後不可修改
```

**段落規則**：
- 每個行為變更都要用 **ADDED / MODIFIED / REMOVED / RENAMED** 分類；沒條目的子段就省略。
- `MODIFIED` 必填「原本 / 改為」對照，讓 reviewer 不用猜 before/after。
- `RENAMED` 只處理命名（例如「簽核」→「審批」、「Order → CustomerOrder」）。如果行為也變了，拆成 RENAMED + MODIFIED 兩條。
- `UNCHANGED` **建議但非必填**；regression 風險高或 MODIFIED 條目很多時主動填。
- 一定要搭配 `## 變更原因`（為什麼有這個 PR——ticket / 利害關係人需求）。
- Aggregate 狀態轉換和 Domain Events 要寫進 Given/When/Then 裡——`/dflow:pr-review` Step 0 會靠這些讀出變更語義。

**→ Phase Gate：Step 2 → Step 3**

向開發者宣告：
> 「基準線已備妥——既有文件已檢查，目前行為和擬議變更都記錄好了。要開始評估 DDD 影響（Aggregate 設計、Domain Events、Value Objects）嗎？打 `/dflow:next` 或回「好」繼續。」

等確認後才進 Step 3。

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

### 產生實作任務清單（僅限完整 spec）

如果這次修改用的是完整 feature-spec 模板（不是輕量規格），AI 在計畫定稿後產生具體任務清單，寫入 spec 的「實作任務」段。格式與 new-feature-flow Step 5 一致：`[LAYER]-[NUMBER]：描述`（DOMAIN / APP / INFRA / API / TEST）。bug-fix 走輕量規格時跳過這步。

**→ Phase Gate：Step 3 → Step 4**

向開發者宣告：
> 「DDD 影響分析完成——{Aggregate 邊界 OK / 需要重新設計}，{不需要新事件 / 需要新事件}。要開始實作了嗎？打 `/dflow:next` 前進，或先調整設計。」

等確認後才進 Step 4。

## 步驟 4：實作

遵循層的順序：Domain → Application → Infrastructure → Presentation。

即使是 Bug 修復，也要確認：
- [ ] 修復在正確的層
- [ ] Aggregate 不變條件仍然成立
- [ ] Domain Events 仍然正確觸發
- [ ] 測試已更新，涵蓋此修復
- [ ] 沒有業務邏輯洩漏到錯誤的層

**→ Phase Gate：Step 4 → Step 5**

向開發者宣告：
> 「實作看起來完成了。要開始跑完成流程 checklist（更新 spec / models / rules / events / glossary / tech-debt）嗎？打 `/dflow:next` 前進。」

等確認後才進 Step 5。這個 Phase Gate 是完成流程 checklist 的觸發點——不可略過。

## 步驟 5：更新文件

由 Step 4 → Step 5 Phase Gate 觸發。AI 依下面順序跑 checklist，**不可**略過任何一段。bug-fix 走輕量規格時，標註「（僅限完整 spec）」的項目可以跳過。

### 5.1 驗證 — AI 可獨立執行

AI 一次檢查完所有項目，用 ✓/✗ 一份列表回報後才開始動文件。標註 *(5.3 後)* 的項目要等 5.3 合併動作完成後再驗證：

- [ ] Delta 段的每條 ADDED / MODIFIED / REMOVED / RENAMED 都有實作或測試覆蓋
- [ ] 修復在正確的層（Domain / Application / Infrastructure / Presentation）
- [ ] Domain 層專案沒有外部 NuGet 依賴（檢查 `*.Domain.csproj`）
- [ ] Aggregate invariants 仍然成立；狀態變更都透過方法
- [ ] 新增或修改的 Domain Events 在實作裡都有 raise
- [ ] EF Core 設定只用 Fluent API（Domain 實體上沒有屬性標註）
- [ ] `實作任務` 段：全部勾選，或未勾選項目明確標註為 follow-up *（僅限完整 spec）*
- [ ] *(5.3 後)* `specs/domain/{context}/behavior.md` 對 ADDED / MODIFIED 條目的每個 `BR-*` 都有對應 section anchor；REMOVED 條目的 anchor 已刪除（`/dflow:verify` 的機械輸入）
- [ ] *(5.3 後)* `specs/domain/{context}/behavior.md` 的 `last-updated` 晚於本 spec 的 `created` 日期（機械 drift 防線）

任何一項失敗就回報差距並暫停——不要往下進 5.2。

### 5.2 驗證 — 需開發者確認

AI 一次列一項、等開發者確認後再列下一項：

- [ ] 修改有沒有**忠實表達** Delta 條目的意圖？（AI 列 delta → 實作位置，開發者判斷）
- [ ] 這次變更後 Aggregate 邊界還正確嗎？（特別注意 MODIFIED / RENAMED 條目）
- [ ] Domain Event 的 payload 與 handler 擺放位置還正確嗎？
- [ ] 有沒有漏掉的技術債？
- [ ] 合併進 `behavior.md` 的情境（含 Aggregate 轉換 + Events）是否**忠實表達 Delta 的最終行為**？（AI 列出更新後的 anchor，開發者判斷）
- [ ] 完成後要不要把 spec 的 `實作任務` 段**摺疊 / 移除**？*（僅限完整 spec——看團隊慣例）*

一次問一項，不要一次倒六個。

### 5.3 文件更新

- [ ] 更新或建立 feature / bug spec；`status` 改為 `completed`
- [ ] `specs/domain/{context}/models.md` — Aggregate 結構更新
- [ ] `specs/domain/{context}/rules.md` — 業務規則更新
- [ ] `specs/domain/{context}/events.md` — Domain Events 更新
- [ ] `specs/domain/glossary.md` — 新術語/重新命名已加入（Delta 的 RENAMED 條目這裡要對應更新）
- [ ] `specs/domain/{context}/behavior.md` — 依 Delta 結果更新行為情境（合併最終狀態，不是 Delta 標記本身）。子步驟：
      - 把 Phase 3 draft 段落（B3 中途同步）轉為正式段落
      - 更新對應 `rules.md` anchor 的 `last-updated` 日期（B4）
- [ ] `behavior.md` 草稿清理 — 若 Delta 中途放棄，保留 `## 提案中變更` 段的歷史，或明確 REMOVE
- [ ] `specs/domain/context-map.md` — 如果跨 context 互動有變更則更新
- [ ] `specs/architecture/tech-debt.md` — 實作中發現的技術債已記錄

### 5.4 歸檔

- [ ] spec 檔案搬到 `specs/features/completed/`（如果本次流程有開 spec）

只有 5.4 做完才能宣告「變更完成」。
