## PROPOSAL-005: 完成流程強化（Checklist + 驗證）

**狀態**: `draft`

**提出日期**: 2026-04-11

**優先級**: `高`

**來源**: OpenSpec 比較分析（S4-1 完成 checklist + S4-2 完成驗證合併）

---

### 問題描述

1. **完成步驟不夠明確**。new-feature-flow 的 Step 8（Completion）列了幾個動作，但不是可勾選的 checklist，容易遺漏。特別是 glossary 和 tech-debt 的更新經常被跳過。

2. **沒有「實作 vs spec 對照驗證」**。Feature 做完時，沒有明確的步驟讓 AI 回頭對照 spec 的 Given/When/Then，確認每個情境都被實作覆蓋了。OpenSpec 有 `/opsx:verify` 做這件事。

### 提議的解法

#### 1. 在 new-feature-flow 加入結構化的完成 Checklist

```markdown
## 完成流程 Checklist

### 驗證（AI 主動執行）
- [ ] 對照 spec 的每個 Given/When/Then，確認實作覆蓋
- [ ] 對照 spec 的每個 BR-*（業務規則），確認實作覆蓋
- [ ] 對照 spec 的每個 EC-*（邊界情況），確認有處理
- [ ] Domain 層無 System.Web 引用（WebForms）/ 無外部 NuGet（Core）

### 文件更新
- [ ] glossary.md — 新術語已加入
- [ ] models.md — 模型定義已更新
- [ ] rules.md — 業務規則已更新
- [ ] events.md — Domain Events 已更新（Core 版）
- [ ] behavior.md — 行為規格已合併（見 P003）
- [ ] tech-debt.md — 發現的技術債已記錄

### 歸檔
- [ ] Spec 狀態改為 completed
- [ ] Spec 搬移到 completed/
- [ ] Tasks 清單全部勾選完成（見 P004）
```

#### 2. 在 SKILL.md 加入完成信號偵測

當開發者說「做完了」、「可以 merge 了」、「PR ready」等完成信號時，AI 應該：
1. 先執行驗證 checklist（自動對照 spec）
2. 列出未完成的項目
3. 引導開發者逐項完成
4. 全部完成後才確認「可以進入 review」

#### 3. 在 modify-existing-flow 也加入精簡版完成 checklist

聚焦在「文件更新」部分。

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `references/new-feature-flow.md`（兩版） | 修改 | Step 8 改為結構化 checklist |
| `references/modify-existing-flow.md`（兩版） | 修改 | Step 6 加入完成 checklist |
| SKILL.md（兩版） | 修改 | 加入「完成信號偵測 + 驗證引導」的 AI 行為指引 |

### 預估工作量

小

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-003 | 依賴 | Checklist 包含「更新 behavior.md」，需要 P003 先定義這個檔案 |
| PROPOSAL-004 | 依賴 | Checklist 包含「Tasks 清單全部勾選」，需要 P004 先加入 tasks 段 |

### 評估紀錄（整合評估時填寫）

**評估日期**:

**結論**:

**理由**:
