## PROPOSAL-001: Skill 指令化入口 + 流程透明化機制（Hybrid）

**狀態**: `draft`

**提出日期**: 2026-04-11

**優先級**: `高`

---

### 問題描述

目前兩套 Skill 採用**純 auto-trigger** 設計——開發者用自然語言描述需求，Claude Code 根據 SKILL.md 的決策樹自動判斷該走哪個流程。這個設計的摩擦低，但有三種失敗模式：

1. **沒觸發**：開發者說「幫我改一下那個頁面的按鈕顏色」，聽起來像 UI 修改，Skill 判斷「不需要 ceremony」直接跳過，但背後其實牽動業務邏輯。

2. **觸發錯的流程**：開發者說「我要處理那個報銷的問題」，Skill 猜不出是 bug 還是功能變更，可能走 new-feature-flow 而不是 modify-existing-flow。

3. **開發者不知道自己在哪**：做到一半不確定 Skill 是否還在引導，還是已經脫離流程自由發揮。

**根本原因**：純 auto-trigger 讓 Skill 成為黑盒，開發者無法驗證、無法修正。而當前 Claude Code 生態的主流做法已偏向 **slash command 指令化**——讓 Skill 的觸發和狀態對開發者完全可見。

但完全指令化又會犧牲「workflow guardian」精神：開發者沒打指令就直接寫 code，SDD 防線被繞過。

### 提議的解法

採用 **Hybrid 設計**：**指令化為主入口、auto-trigger 為安全網、透明化為支撐機制**。

#### 1. 指令化入口（Slash Commands）

在 SKILL.md 決策樹加入指令分支，開發者可明確呼叫流程。

**流程入口指令**
- `/new-feature` — 進入 new-feature-flow
- `/modify-existing` — 進入 modify-existing-flow
- `/bug-fix` — 輕量 spec + 修復流程（modify-existing-flow 的簡化版）
- `/pr-review` — 進入 PR review checklist

**通用控制指令**
- `/status` — 回報目前流程、所在 step、已完成與待完成項
- `/next` — 確認進入下一階段（等價於自然語言的「好」/「繼續」/「確認」）
- `/cancel` — 中止目前流程，回到自由對話

#### 2. Auto-trigger 作為安全網

當開發者未打指令、直接用自然語言描述需求時（例如「我要加一個功能」），Skill 的 `description` 欄位仍會觸發。**但不再自行前進**，而是判斷後停下來要確認：

```
我判斷這是 new-feature-flow 任務。你可以：
- 輸入 `/new-feature` 明確啟動
- 回覆「好」/「繼續」確認用這個流程
- 或告訴我正確的流程（bug fix？修改既有？）
```

這是三個失敗模式中「沒觸發」和「觸發錯的流程」的防線——Skill 不再默默判斷，而是**大聲說出判斷、等開發者背書**。

#### 3. 三級流程透明化

定義 AI 在 workflow 中三種「發言時機」，避免嘮叨也避免失聯：

| 級別 | 觸發點 | AI 行為 |
|---|---|---|
| **Flow 入口（必確認）** | 判斷流程後 | 停下來要確認（指令或 NL 皆可） |
| **Phase gate（告知+可選確認）** | 主要里程碑前 | 告知即將進入；若開發者直接給資料則視同繼續 |
| **Step 內部推進（只告知）** | Step N → N+1 | 單向宣告「Step N 完成，進入 Step N+1」，不等確認 |

**Phase gate 的位置**

new-feature-flow（8 steps）：
- Step 4 → 5（spec 完成 → 規劃實作）
- Step 6 → 7（分支開好 → 開始實作）
- Step 7 → 8（實作完成 → 進入 completion）

modify-existing-flow（6 steps）：
- Step 2 → 3（baseline 捕捉 → 分析 code-behind）
- Step 4 → 5（抽離決策 → 開始實作）
- Step 5 → 6（實作完成 → 更新文件）

#### 4. NL 與指令的等價規則

SKILL.md 明確定義確認訊號，讓 AI 知道哪些輸入等於「繼續」：

```
確認進入下一階段的訊號（擇一即可）：
- 指令：/next
- 中文：好 / 對 / 繼續 / 可以 / OK / 沒問題
- 隱性確認：開發者直接提供下階段所需資訊
  （例：AI 問「BC 是哪個？」開發者答 BC 名稱 = 視同確認繼續）
```

隱性確認規則很重要——避免變成「每句話都要先說『好』」的儀式感。

#### 5. `/status` 的回應格式

當開發者輸入 `/status`（或自然語言詢問「現在在哪？」「目前狀態？」），AI 應回報：

```
目前流程：new-feature-flow
目前 step：Step 3 — Domain Concept Discovery

已完成：
- [x] Step 1: Intake — 需求已理解
- [x] Step 2: Identify BC — 歸屬 Expense context

進行中：
- [ ] Step 3: Domain Concept Discovery — 正在識別領域概念

待完成：
- [ ] Step 4: Write Spec
- [ ] Step 5: Plan Implementation
- [ ] Step 6: Git Branch
- [ ] Step 7: Implementation
- [ ] Step 8: Completion
```

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `sdd-ddd-webforms-skill/SKILL.md` | 修改 | 決策樹加入 slash command 分支、新增 Workflow Transparency 段落、新增 `/status`/`/next`/`/cancel` 處理規則 |
| `sdd-ddd-webforms-skill/references/new-feature-flow.md` | 修改 | 加入三個 phase gate（Step 4→5、6→7、7→8）的確認規則；step 內轉換加一行宣告 |
| `sdd-ddd-webforms-skill/references/modify-existing-flow.md` | 修改 | 加入三個 phase gate（Step 2→3、4→5、5→6）的確認規則；step 內轉換加一行宣告 |
| `sdd-ddd-core-skill/SKILL.md` | 修改 | 同 WebForms 版的修改 |
| `sdd-ddd-core-skill/references/new-feature-flow.md` | 修改 | 同 WebForms 版的修改 |
| `sdd-ddd-core-skill/references/modify-existing-flow.md` | 修改 | 同 WebForms 版的修改 |

### 預估工作量

中

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-005 | 互補 | P005 的「完成信號偵測」可透過 `/next` 在 Step 7→8 的 phase gate 明確觸發，減少自然語言誤判 |
| PROPOSAL-006 | 互補 | 漸進導入 Stage 1-2 可只教 `/bug-fix` 和 `/status` 等少量指令，降低 onboarding 門檻 |
| PROPOSAL-007 | 互補 | `/pr-review` 指令進入 PR review checklist；與 P007 的「Spec Intent 優先」流程銜接 |

### 評估紀錄（整合評估時填寫）

**評估日期**:

**結論**:

**理由**:
