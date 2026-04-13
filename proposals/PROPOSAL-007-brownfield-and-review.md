## PROPOSAL-007: Brownfield 強化與 Review 改善

**狀態**: `draft`

**提出日期**: 2026-04-11

**優先級**: `低`

**來源**: OpenSpec 比較分析（S8-1 逆向文件化 + S6-1 PR Review + S2-2 CLAUDE.md 合併）

---

### 問題描述

三個低優先但相關的改善點：

1. **逆向文件化不夠系統性**。WebForms 版的 modify-existing-flow Step 2 說「capture what currently exists」，但只是記錄單一功能的行為。沒有引導 AI 主動掃描相關的 Code-Behind，系統性地抽取業務規則和領域概念。

2. **PR Review 沒有「先看 spec 意圖」的步驟**。Reviewer 直接進入 code review，沒有先透過 spec 了解「這個 PR 想改什麼」。OpenSpec 的 spec delta review 讓 reviewer 先理解意圖再看實作。

3. **CLAUDE.md 的「專案脈絡」和「流程規則」混在一起**。分開可以讓 AI 在不同情境下更有效地讀取相關資訊。

### 提議的解法

#### 1. 強化逆向文件化（WebForms 版）

在 modify-existing-flow 的 Step 2-3 之間加入「系統性 baseline capture」指引：

```
當 AI 發現被修改的功能沒有現有 spec 時：
1. 讀取相關的 Code-Behind 檔案
2. 抽取所有業務規則（if/else、計算、驗證）
3. 識別領域概念（潛在的 Entity、VO、Service）
4. 檢查是否有跨頁面的重複邏輯
5. 將發現記錄到對應的 domain docs 和 tech-debt.md
```

這不是要求每次都做完整掃描，而是「趁修改時順便做」的機會主義策略。

#### 2. PR Review 加入 Spec Intent 步驟

在 PR Review Checklist 開頭加入：

```markdown
## Step 0: 理解變更意圖（先於 Code Review）
- [ ] 閱讀本次 PR 對應的 spec
- [ ] 如果是修改既有功能，閱讀「行為變更（Delta）」段落
- [ ] 確認你理解：這個 PR 想解決什麼問題、預期行為是什麼
- [ ] 然後才進入程式碼 review
```

#### 3. CLAUDE.md 分段強化

在 CLAUDE.md 模板中，用更明確的標題分離：

```markdown
## 系統脈絡（What is this system?）
> 技術棧、業務領域、團隊結構

## 開發流程（How do we work?）
> SDD 流程、Git Flow、Domain 層規範
```

目前的 CLAUDE.md 模板已經有類似的分段，只需要讓標題更明確。

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `references/modify-existing-flow.md`（WebForms） | 修改 | 加入系統性 baseline capture 指引 |
| `references/pr-review-checklist.md`（兩版） | 修改 | 加入 Step 0: Spec Intent Review |
| `templates/CLAUDE.md`（兩版） | 修改 | 標題分段強化 |

### 預估工作量

小

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-002 | 互補 | PR Review 的 Step 0 會讀 delta 格式的變更描述 |
| PROPOSAL-003 | 互補 | 逆向文件化的結果存入 behavior.md |

### 評估紀錄（整合評估時填寫）

**評估日期**:

**結論**:

**理由**:
