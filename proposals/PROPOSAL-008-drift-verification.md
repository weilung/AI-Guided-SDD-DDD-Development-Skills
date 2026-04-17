## PROPOSAL-008: Drift Verification 指令

**狀態**: `implemented`

**提出日期**: 2026-04-15

**優先級**: `中`

**來源**: P003 評估過程中衍生。採用 A+C 結構（rules.md 索引 + behavior.md 內容）後，為在信任預設下提供驗證安全網而提出。

**實施排程**: Wave C（依賴 P003 的 A+C 結構已在 Wave B 落地）

---

### 問題描述

P003 採用 A+C 結構（rules.md 當索引、behavior.md 當情境內容），分工明確但引入了 drift 風險——兩份文件可能失去一致性：

- rules.md 宣告了 BR-ID，但 behavior.md 沒有對應情境
- behavior.md 引用了 BR-ID，但 rules.md 沒有宣告
- rules.md 的 anchor 連結指向不存在的 behavior.md 段落

需要一個低成本的驗證機制，讓開發者在關鍵時機（PR 前、release 前、重構後）能快速確認一致性。

### 提議的解法

新增 slash command：

```
/dflow:verify            # 驗證所有 BC
/dflow:verify <bc>       # 驗證單一 BC（建議預設使用方式）
```

#### 機械層驗證內容（本 proposal 範圍）

AI 執行以下機械檢查：

1. **BR-ID 對應**：rules.md 列出的 BR-* 在 behavior.md 中是否有對應情境
2. **Anchor 有效性**：rules.md 指向的 `behavior.md#段落` 是否存在
3. **反向檢查**：behavior.md 中引用的 BR-ID 是否在 rules.md 中有宣告

#### 輸出範例

```
驗證 Expense BC 的 rules.md ↔ behavior.md 一致性

✓ BR-001 → 提交報銷單 段落存在且引用 BR-001
✗ BR-002 → behavior.md 中找不到「#金額上限」段落
✓ BR-003 → 多幣別支援 段落存在且引用 BR-003

發現問題：
- rules.md 宣告 BR-002 存在，但 behavior.md 沒有對應情境
  → 可能：規則已實作但沒補 behavior.md；或規則已廢止但沒刪 rules.md
  → 建議：檢查 ExpenseReport Aggregate 的程式碼，確認實際行為，
         然後選擇「補 behavior.md」或「從 rules.md 刪除 BR-002」
```

#### 明確排除：語意層驗證

語意層（LLM 讀 rules.md 一行描述 vs behavior.md 情境，判斷是否矛盾）**本 proposal 不做**。

理由：
- 機械層已能抓大部分 drift
- 語意層耗 token 且判斷結果需要人類複核
- 等用過一陣子再決定是否需要（列為 Wave D 延伸）

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `sdd-ddd-core-skill/SKILL.md` | 修改 | 加入 `/dflow:verify` 指令說明和觸發規則 |
| `sdd-ddd-webforms-skill/SKILL.md` | 修改 | 同上 |
| `sdd-ddd-core-skill/references/drift-verification.md` | 新增 | 驗證邏輯的完整規範 |
| `sdd-ddd-webforms-skill/references/drift-verification.md` | 新增 | 同上 |

### 預估工作量

中

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-003 | 依賴 | 必須先有 A+C 結構（rules.md 索引格式、behavior.md anchor 規範）才能驗證 |
| PROPOSAL-001 | 互補 | `/dflow:verify` 使用 P001 的 `/dflow:` 前綴 |

### 關鍵假設

- 假設 1（A9）：大部分 drift 是機械層可偵測的（BR-ID 遺漏、anchor 失效），而非語意矛盾
- 假設 2：開發者會在關鍵時機（PR 前、release 前、重構後）主動執行 `/dflow:verify`
- 驗證點：跑過 10+ 次 verify 後觀察「找到的問題」類型分布——如果大部分是語意矛盾，需要升級到語意層（Wave D）

### 評估紀錄

**評估日期**: 2026-04-15

**結論**: approved

**理由**: A+C 結構的 drift 風險需要安全網。機械層驗證成本低（AI 做字串比對 + anchor 檢查）、價值明確（抓遺漏和斷鏈）。語意層延後到有實證數據再決定。

### 實施紀錄

**實施日期**: 2026-04-17

**實施內容**：

1. **新增 `references/drift-verification.md`（兩版）**：5 步驟驗證流程（找檔案 → 擷取 rules.md BR-ID → 擷取 behavior.md BR-ID → 交叉比對 → 回報）。三項機械檢查（正向、anchor、反向）。Core 版額外包含 events.md 交叉引用警告。明確排除語意層。
2. **兩版 SKILL.md 更新**：`/dflow:verify [<bc>]` 加入 description frontmatter、決策樹、Slash Commands 段、Reference Files 表格。
3. **繁中同步**：兩版 SKILL_tw.md（決策樹 + Slash Commands + 參考文件表）、兩版 `drift-verification_tw.md`（新增）。

**備註**：P008 原排程 Wave C 但依賴 P003（A+C 結構）。P003 於同日提前實施後，P008 隨即進行。
