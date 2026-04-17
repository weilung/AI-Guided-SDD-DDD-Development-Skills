# Drift 驗證 — rules.md ↔ behavior.md 一致性檢查

由 `/dflow:verify` 或 `/dflow:verify <bounded-context>` 觸發。

## 目的

A+C 結構（`rules.md` 作為索引 + `behavior.md` 作為情境內容）引入了 drift 風險——兩份文件可能失去同步。這個指令提供一個機械式驗證安全網，讓開發者在關鍵時機執行：PR 前、release 前、重構後。

## 範圍

### 這個指令做的事（機械層）

三項 AI 可確定性執行的字串比對檢查：

1. **BR-ID 正向檢查**：`rules.md` 宣告的每個 `BR-*` 在 `behavior.md` 中都有對應段落
2. **Anchor 有效性**：`rules.md` 連結到 `behavior.md#段落` 的 anchor 確實存在
3. **BR-ID 反向檢查**：`behavior.md` 引用的每個 `BR-*` 在 `rules.md` 中都有宣告

### 這個指令不做的事（語意層——明確排除）

語意驗證（LLM 讀 `rules.md` 的一行摘要 vs `behavior.md` 的 Given/When/Then，判斷是否矛盾）**不在範圍內**。理由：
- 機械層檢查已能抓到大部分 drift（遺漏的 ID、失效的連結）
- 語意判斷耗 token 且結論需要人類複核
- 延遲到 Wave D——累積 10+ 次 verify 後再根據實際 drift 的類型分布決定是否需要

## 用法

```
/dflow:verify            # 驗證所有 Bounded Contexts
/dflow:verify Expense    # 驗證單一 BC（建議的預設使用方式）
```

驗證所有 BC 時，逐個 Context 獨立執行，分別回報結果。

## 驗證步驟

對每個 Bounded Context：

### Step 1：找到檔案

- 找 `specs/domain/{context}/rules.md`
- 找 `specs/domain/{context}/behavior.md`
- 如果任一不存在，回報後停止該 Context 的驗證：
  ```
  ✗ Expense：rules.md 存在但 behavior.md 不存在
    → 用 templates/behavior.md 建立 behavior.md，或執行完成流程從既有的 completed specs 填入
  ```

### Step 2：從 rules.md 擷取 BR-ID

掃描 `rules.md` 找出所有 `BR-*` 識別碼。記錄每個 ID 和指向 `behavior.md` 的 anchor 連結。

### Step 3：從 behavior.md 擷取 BR-ID

掃描 `behavior.md` 找出段落標題或內文中引用的所有 `BR-*` 識別碼。

### Step 4：交叉比對

執行三項檢查：

| 檢查 | 通過條件 | 失敗訊息 |
|---|---|---|
| 正向 | rules.md 的每個 BR-ID 都出現在 behavior.md | `✗ BR-NNN 在 rules.md 中宣告但 behavior.md 中找不到` |
| Anchor | rules.md 中的每個 `behavior.md#anchor` 都能解析 | `✗ BR-NNN 連結到 behavior.md#段落 但 anchor 不存在` |
| 反向 | behavior.md 的每個 BR-ID 都出現在 rules.md | `✗ BR-NNN 在 behavior.md 中被引用但 rules.md 中沒有宣告` |

### Step 5：回報

輸出格式：

```
驗證 {Context} — rules.md ↔ behavior.md 一致性

✓ BR-001 → {段落標題} 存在且引用 BR-001
✓ BR-002 → {段落標題} 存在且引用 BR-002
✗ BR-003 → behavior.md 中找不到 BR-003 的段落
✓ BR-004 → {段落標題} 存在且引用 BR-004
✗ BR-010 → 在 behavior.md 中被引用但 rules.md 中沒有宣告

摘要：3 通過，2 個問題

問題：
1. rules.md 宣告 BR-003，但 behavior.md 沒有對應的情境
   → 可能原因：規則已實作但完成流程時沒有更新 behavior.md
   → 建議：檢查實作程式碼，然後選擇「補 behavior.md 的情境」
     或「從 rules.md 移除 BR-003（如果已廢止）」

2. behavior.md 引用 BR-010，但 rules.md 沒有宣告
   → 可能原因：情境直接加到 behavior.md 但沒有更新 rules.md
   → 建議：在 rules.md 加入 BR-010 的一行摘要，
     或從 behavior.md 移除過時的引用
```

## 建議執行時機

建議的觸發點（不強制——由開發者判斷）：
- 建 PR 之前（`/dflow:verify` 作為 pre-PR 健全性檢查）
- Release 分支切出前
- 重構後有動到多份 spec 或 domain 文件
- 初次接觸不熟悉的 Bounded Context 時（先驗證再信任文件）

## 與其他指令的互動

- `/dflow:verify` 是**獨立指令**——不需要有進行中的流程
- 可以在流程進行中執行（例如 Step 7 實作時檢查有沒有 drift）
- 發現問題時，由開發者決定要現在修還是之後修——這個指令不會阻擋其他流程
