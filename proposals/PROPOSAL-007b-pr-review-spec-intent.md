## PROPOSAL-007b: PR Review Spec Intent（Step 0）

**狀態**: `implemented`

**提出日期**: 2026-04-11（從 PROPOSAL-007 拆分）

**實施日期**: 2026-04-16

**優先級**: `高`（D1 優先級調整：從 Wave C 提前至 Wave A）

**來源**: PROPOSAL-007 拆分 + 雙盲對照 D1 優先級修正。原排程放 Wave C 是因為工作量小，但對照評估指出其**完成 SDD 流程回饋迴路**的關鍵價值，調整為 Wave A。

**實施排程**: Wave A（基礎結構 + 低成本高價值）

---

### 問題描述

PR Review 沒有「先看 spec 意圖」的步驟。Reviewer 直接進入 code review，沒有先透過 spec 了解「這個 PR 想改什麼」。

如果 reviewer 不先看 spec，前面所有規格工作（寫 spec、Delta、behavior.md）就失去了驗證機制——SDD 流程的回饋迴路不完整。

### 提議的解法

PR Review Checklist 開頭加入 Step 0：

```markdown
## Step 0: 理解變更意圖（先於 Code Review）
- [ ] 閱讀本次 PR 對應的 spec
- [ ] 如果是修改既有功能，閱讀「行為變更（Delta）」段落
- [ ] 確認你理解：這個 PR 想解決什麼問題、預期行為是什麼
- [ ] 然後才進入程式碼 review
```

### 為什麼提前到 Wave A（D1 修正）

- **完成 SDD 流程的回饋迴路**：如果 reviewer 不先看 spec，前面的規格工作就失去驗證機制
- **零成本**：pr-review-checklist 開頭加 4 行 Step 0 指引
- **不硬依賴 P002**：即使還沒有 Delta 格式，「先讀 spec 再 review code」本身就有價值（可讀任何現有 spec 內容）
- **與 P001 高度配對**：`/dflow:pr-review` 進入時第一步就是 Step 0

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `sdd-ddd-webforms-skill/references/pr-review-checklist.md` | 修改 | 開頭加入 Step 0: 理解變更意圖 |
| `sdd-ddd-core-skill/references/pr-review-checklist.md` | 修改 | 同上 |

### 預估工作量

小

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-001 | 互補 | `/dflow:pr-review` 進入時第一步是 Step 0 |
| PROPOSAL-002 | 互補 | Step 0 讀 delta 格式的「行為變更」（含 RENAMED / UNCHANGED）；P002 未實施前仍可用舊 spec |
| PROPOSAL-003 | 互補 | Step 0 可延伸檢查「behavior.md 是否與本次變更同步」（B3 中途同步的 review 閘門）|
| PROPOSAL-007（原） | 拆分自 | 本 proposal 是原 P007 的第二個子項 |

### 關鍵假設

- 假設（A16）：即使沒有 P002 的 delta 格式，「先讀 spec 再 review code」仍有獨立價值
- 假設：reviewer 願意遵循 Step 0，不會跳過直接 review code
- 驗證點：觀察 PR 評論內容是否引用 spec 意圖

### 評估紀錄

**評估日期**: 2026-04-15

**結論**: approved（D1 優先級調整至 Wave A）

**理由**: 零成本完成 SDD 回饋迴路。原排程放 Wave C 是工作量導向的判斷失誤——真正價值在於讓前面所有規格工作有驗證機制。雙盲對照指出此盲點後修正。

### 實施紀錄

**Wave**: A（於 P001 / P002 / P004 / P005a 完成後接上）

**實施日期**: 2026-04-16

**實際變更**:

1. **兩版 `references/pr-review-checklist.md`** 開頭新增 Step 0：
   - 指明 `/dflow:pr-review` 進入時第一步是 Step 0，不可跳過。
   - 四項 checkbox：讀 spec、讀 Delta（ADDED/MODIFIED/REMOVED/RENAMED + 可選 UNCHANGED）、用一句話陳述 PR 意圖（若寫不出來要暫停問 author）、然後才進入 code review。
   - 附若 PR 沒有 spec 的引導話術：要求先開 lightweight-spec。
   - Core 版另補：若 `behavior.md` 存在，交叉確認 Delta 是否已反映（B3 中途同步 review 閘門）；Aggregate 狀態轉換與 Domain Events 也要特別留意。

2. **不需要修改 SKILL.md**：P001 已定義 `/dflow:pr-review` 為 flow entry command 並指向 pr-review-checklist.md；Step 0 作為 checklist 第一步自動被覆蓋。

**影響檔案**（2 個）：

- `sdd-ddd-webforms-skill/references/pr-review-checklist.md`
- `sdd-ddd-core-skill/references/pr-review-checklist.md`

**待同步**：繁體中文版（`sdd-ddd-webforms-skill-tw/`、`sdd-ddd-core-skill-tw/`）尚未同步，列為後續作業。
