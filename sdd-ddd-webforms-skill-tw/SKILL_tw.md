# SDD/DDD 工作流程守衛

## 概述

這是一個 AI 引導的開發流程規範，用於 ASP.NET WebForms 專案中導入 **規格驅動開發（SDD）** 和 **領域驅動設計（DDD）**，同時為未來遷移到 ASP.NET Core 做準備。

當開發者開始任何功能開發、Bug 修復、或程式碼修改時，AI 會自動介入引導，確保每一次變更都遵循「先寫規格、再寫程式」的流程。

---

## 為什麼要這樣做

這個專案目前的問題：業務邏輯散落在 Code-Behind 檔案裡、頁面處理器中有直接的 SQL 查詢、多個頁面之間有重複的計算邏輯。每一個沒有規格就開發的功能，都會讓未來遷移更困難；反之，每一份規格文件和每一個被抽離的領域概念，都會讓遷移更容易。

AI 的角色不是說教，而是在對的時機問對的問題，讓開發者在每次變更中自然產出三樣資產：

1. **規格文件** — 未來的需求文件
2. **Domain 層程式碼** — 可直接搬到 ASP.NET Core
3. **技術債記錄** — 遷移指南

---

## 決策樹：AI 根據開發者的意圖決定流程

從 2026-04 起採 **Hybrid 雙軌**：明確指令（`/dflow:`）直接進入流程；自然語言視為安全網，AI 判斷後要等開發者確認才真正進入。

```
入口類型
    │
    ├─ 明確指令（優先）
    │   ├─ /dflow:new-feature         → 直接進新功能流程
    │   ├─ /dflow:modify-existing     → 直接進修改既有功能流程
    │   ├─ /dflow:bug-fix             → 修改既有功能流程（輕量 ceremony）
    │   ├─ /dflow:pr-review           → 進 PR Review Checklist（Step 0 起）
    │   ├─ /dflow:verify [<bc>]       → Drift 驗證：rules.md ↔ behavior.md 一致性檢查
    │   ├─ /dflow:status              → 回報目前流程與進度
    │   ├─ /dflow:next                → 確認進到下一個 Phase（= OK / 繼續）
    │   └─ /dflow:cancel              → 放棄當前流程，回到自由對話
    │
    └─ 自然語言（安全網，判斷後等確認，不自動前進）
        ├─「我要新增一個功能」        → 判斷為 new-feature；列三選項等確認
        ├─「我要修改/修正既有的…」    → 判斷為 modify-existing；列三選項等確認
        ├─「快速問一下…」/「X 是怎麼運作的？」
        │                            → 先查 specs/domain/ 回答，不進流程
        ├─「我在做 Code Review」/「看一下 PR」
        │                            → 判斷為 pr-review；列三選項等確認
        ├─「接下來要做什麼？」/ Sprint 規劃
        │                            → 檢視 specs/features/backlog/，依遷移價值建議
        ├─「我要建分支」             → 指向 git-flow-integration_tw.md；確認 spec 存在
        └─ 其他碰到業務邏輯的描述    → 判斷最像哪個流程，列三選項等確認
```

三選項格式（判斷後給開發者三條路）：
1. 打對應的 `/dflow:` 指令明確啟動
2. 回「好」/「OK」/「繼續」確認走 AI 的判斷
3. 更正（例：「不是，這是 bug 修復」）

不可以自動進流程——錯判的成本比等一秒確認高。

---

## 核心原則

1. **先規格、後程式碼** — 沒有至少一份輕量規格，就不開始實作
2. **領域抽離** — 業務邏輯屬於 `src/Domain/`，不屬於 Code-Behind
3. **統一語言** — 一致使用 `specs/domain/glossary.md` 中定義的術語
4. **遷移意識** — 每個決策都要考慮未來 ASP.NET Core 遷移
5. **務實不教條** — 緊急修復不需要 50 行的規格。流程的嚴謹度應該與影響程度成正比

---

## 流程嚴謹度對照表

不是所有變更都需要完整流程。根據影響程度調整：

| 變更類型 | 規格等級 | 領域抽離 | 技術債記錄 |
|---|---|---|---|
| 新功能 | 完整規格 | 必須 | 視情況 |
| 業務規則變更 | 標準規格 | 必須 | 是 |
| Bug 修復（邏輯錯誤） | 輕量規格 | 評估 | 有發現就記 |
| 純 UI 變更 | 跳過 | 跳過 | 跳過 |
| 設定/環境變更 | 跳過 | 跳過 | 跳過 |
| 效能修復 | 輕量規格 | 評估 | 是 |

**輕量規格** = 問題描述 + 預期行為 + 1-2 個 Given/When/Then。可以直接寫在註解中，或在 `specs/features/active/` 建一個簡短條目。

---

## 專案目錄結構

```
WebFormsProject/
├── CLAUDE.md                         # AI 工作流程規則
├── specs/
│   ├── _overview.md                  # 系統現況與遷移策略
│   ├── _conventions.md               # 規格撰寫慣例與模板
│   ├── domain/                       # 領域知識（為 DDD 做準備）
│   │   ├── glossary.md               # 統一語言術語表
│   │   └── {bounded-context}/        # 例如：expense/, hr/, leave/
│   │       ├── context.md            # Context 邊界與職責
│   │       ├── models.md             # Entity、VO、Aggregate 定義
│   │       ├── rules.md              # 業務規則索引（BR-ID + 一行摘要）
│   │       └── behavior.md           # 行為規格彙整（Given/When/Then）
│   ├── features/
│   │   ├── active/                   # 目前開發中
│   │   ├── completed/                # 已完成（文件歸檔）
│   │   └── backlog/                  # 待處理
│   └── migration/
│       └── tech-debt.md              # 新系統中需要修復的問題
├── src/
│   ├── Domain/                       # 抽離的領域邏輯（純 C#）
│   │   ├── {BoundedContext}/         # 例如：Expense/
│   │   │   ├── Entities/
│   │   │   ├── ValueObjects/
│   │   │   ├── Services/
│   │   │   └── Interfaces/           # Repository 介面等
│   │   └── SharedKernel/             # 跨 Context 共用概念
│   └── Pages/                        # 既有 WebForms 頁面
```

---

## 工作流程透明度（Workflow Transparency）

三層通知規則，讓開發者隨時知道自己在流程的哪個位置：

| 層級 | 觸發時機 | AI 行為 |
|---|---|---|
| **Flow entry（必須確認）** | 從自然語言判斷出流程後 | 停下來等開發者確認（指令、「好」、或隱含確認）才進流程 |
| **Phase gate（通知 + 可選確認）** | 跨過關鍵里程碑 | 宣告轉換；若開發者給出下一階段的輸入，視為隱含確認 |
| **Step-internal（只通知）** | 同流程內 Step N → Step N+1 | 宣告「Step N 完成，進入 Step N+1」——不等確認 |

**Phase gate 位置**

new-feature-flow（8 步）：
- Step 4 → 5（規格寫完 → 規劃實作）
- Step 6 → 7（分支建好 → 開始實作）
- Step 7 → 8（實作完成 → 走完成流程）

modify-existing-flow（6 步）：
- Step 2 → 3（現況記錄完 → 分析 Code-Behind）
- Step 4 → 5（抽離決定 → 開始實作）
- Step 5 → 6（實作完成 → 更新文件）

### 完成流程 Checklist 的執行方式

Step 7→8（new-feature）或 Step 5→6（modify-existing）的 Phase Gate 是**完成流程 checklist 的唯一觸發點**。開發者跨過這個 gate（打 `/dflow:next`、回「好」、或隱含確認）之後，AI 才開始跑 checklist；不可在開發者還在 Step 7 時就搶跑。

跑的時候嚴格照下面順序：

1. **AI 可獨立驗證**（8.1 / 6.1）：AI 一次把所有項目檢查完，用 ✓/✗ 一份列表回報。項目分兩類時機：
   - **合併前**（預設）：動文件之前就可以驗——Given/When/Then 與 BR/EC 覆蓋、Domain 純淨度（無 `System.Web`）、`實作任務` 段完整度。
   - **8.3 / 6.3 之後**（標 `*(...後)*`）：合併動作完成後再驗——`behavior.md` 對 `BR-*` 的 anchor 對應與 `last-updated` 日期（`/dflow:verify` 的機械輸入）。
   任何一項失敗就停下修復，不往下走。
2. **需開發者確認**（8.2 / 6.2）：一次問一個問題（意圖是否一致、邊界處理是否恰當、tech-debt 有沒有漏），等開發者回覆再問下一個。P005b 多加兩題：合併進 `behavior.md` 的情境是否忠實表達預期行為、以及 spec 的 `實作任務` 段要不要摺疊 / 移除（看團隊慣例）。
3. **文件更新**（8.3 / 6.3）：glossary / models / rules / tech-debt 依 checklist 項逐一更新。`behavior.md` 的合併包含兩個子步驟：把 Phase 3 draft 段落（B3 中途同步）轉為正式段落、更新對應 `rules.md` anchor 的 `last-updated` 日期（B4）。若 spec 中途放棄，要清理 `## 提案中變更` 段（保留歷史或明確 REMOVE）。
4. **歸檔**（8.4 / 6.4）：spec 狀態改 `completed`、檔案搬到 `completed/`。

只有 8.4（或 6.4）做完才能宣告「功能完成 / 修改完成」。若開發者跳過 Phase Gate 直接 commit，安全網應該先攔：「看起來你要收尾了，要先跑 Step 8 checklist 嗎？」

### 確認訊號（自然語言 ↔ 指令等價）

以下任何一種都算「前進到下一階段」，擇開發者用的那種：

- **指令**：`/dflow:next`
- **中文**：好 / 對 / 繼續 / 可以 / 沒問題
- **英文**：OK / yes / continue / go ahead / sounds good / proceed
- **隱含**：開發者直接提供下一階段要的資訊（例：AI 問「屬於哪個 Bounded Context？」開發者直接回「Expense」）→ 隱含確認

隱含確認很重要——不要把每個轉換都搞成「必須先說 OK」的 ceremony。

### `/dflow:status` 回應格式

開發者打 `/dflow:status` 或用自然語言問（「現在在哪？」、「目前進度？」）時，照這個格式回：

```
目前流程：new-feature-flow
目前 Step：Step 3 — 領域概念發掘

已完成：
- [x] Step 1：Intake — 需求已理解
- [x] Step 2：識別 BC — 歸到 Expense context

進行中：
- [ ] Step 3：領域概念發掘 — 辨識 domain 概念中

待處理：
- [ ] Step 4：撰寫規格
- [ ] Step 5：規劃實作
- [ ] Step 6：建 Git 分支
- [ ] Step 7：實作
- [ ] Step 8：完成流程
```

若沒有進行中的流程，回：「目前沒有進行中的流程。可用 `/dflow:new-feature`、`/dflow:modify-existing`、`/dflow:bug-fix`、`/dflow:pr-review` 啟動。」

---

## 行為規格 Source of Truth（rules.md + behavior.md）

每個 Bounded Context 有兩份互補的檔案，合起來描述系統目前的行為：

- **`rules.md`** — 宣告式索引：列出每個 BR-ID 和一行摘要。快速查找、方便掃讀。
- **`behavior.md`** — 情境層細節：每個 BR-ID 的完整 Given/When/Then 情境。這是「系統現在到底在做什麼」的彙整 source of truth。

`specs/features/completed/` 是歷史歸檔（個別變更記錄）。`behavior.md` 是**合併後的現狀**——功能完成時，AI 把 spec 裡的情境合併進 `behavior.md`；行為被修改時，AI 更新對應段落反映新行為（git 保留歷史）。模板見 `templates/behavior.md`。

這相當於 OpenSpec 的 `specs/` 目錄作為系統行為 source of truth 的角色，但按 Bounded Context 而非 capability 組織。

## 引導問題（按階段）

開發者開始工作時，依序引導這些問題。不要一次全部丟出來——隨著對話自然推進。

**模板 Phase 標記約定**：`templates/feature-spec.md` 的每個段落標題後都加了 HTML 註解（例如 `<!-- 填寫時機：Phase 2 -->`），對應下面的階段。`/dflow:status` 和完成流程 checklist 會用這些標記判斷進度。引導時照階段順序填，不要在 Phase 3（撰寫規格）還沒談完時就跳去 Phase 4（實作規劃）。模板末尾的 `實作任務` 段由 AI 在 Phase 4 結束後產生——詳見 `references/new-feature-flow_tw.md` Step 5 / `references/modify-existing-flow_tw.md` Step 4。

### 第一階段：理解需求（做什麼、為什麼）
- 這要解決什麼問題？誰提出的需求？
- 從使用者的角度，預期行為是什麼？
- 有沒有相關的既有規格或領域文件？

### 第二階段：領域分析（放在哪裡）
- 這屬於哪個 Bounded Context？（檢查 `specs/domain/`）
- 涉及哪些領域概念？（Entity、Value Object、Service）
- 有沒有新術語？→ 更新 `glossary.md`
- 有沒有新增或變更的業務規則？→ 記錄在 `rules.md`

### 第三階段：撰寫規格（先文件再寫程式）
- 使用模板撰寫規格（見 `templates/feature-spec.md`）
- 定義關鍵行為的 Given/When/Then 情境
- 找出邊界情況和業務規則之間的互動

### 第四階段：實作規劃（怎麼做）
- 業務邏輯能不能放在 `src/Domain/` 裡當純 C#？
- 需要哪些介面？（Repository、外部服務）
- Code-Behind 能多薄？（理想：解析輸入 → 呼叫 Domain → 顯示結果）

### 第五階段：技術債察覺（發現了什麼）
- 有沒有發現散落的業務邏輯？→ 記錄到 `tech-debt.md`
- 有沒有重複的計算？→ 記錄
- Code-Behind 裡有直接 SQL？→ 記錄
- Magic number 或未文件化的狀態碼？→ 記錄並加入術語表

---

## Domain 層規範（`src/Domain/`）

`src/Domain/` 中的程式碼必須遵守以下限制。這些是不可妥協的，因為這些程式碼將直接遷移到 ASP.NET Core：

- **不可引用 System.Web** — 零 WebForms 依賴
- **不可直接存取資料庫** — 使用介面（Repository pattern）
- **不可使用 HttpContext、Session、ViewState** — 純業務邏輯
- **不可有 UI 相關邏輯** — 不做顯示格式化、不引用 Page
- 所有公開行為都應該能在沒有任何 Web 基礎設施的情況下測試

---

## 術語表維護

`specs/domain/glossary.md` 是業務術語的唯一事實來源。

遇到業務術語時：
1. 先檢查術語表中是否已存在
2. 如果不存在，向開發者確認定義
3. 立即新增——不要拖延
4. 在規格和程式碼中一致使用術語表中的名稱（類別名、方法名、變數名）

格式：`| 術語 | 定義 | Bounded Context | 對應程式碼 |`

---

## 參考文件

需要詳細步驟時閱讀這些文件：

| 文件 | 閱讀時機 |
|---|---|
| `references/new-feature-flow_tw.md` | 開發者要新增功能時 |
| `references/modify-existing-flow_tw.md` | 開發者要修改既有功能時 |
| `references/pr-review-checklist_tw.md` | Code Review 或 PR 討論時 |
| `references/git-flow-integration_tw.md` | 建立分支、合併策略、發布流程時 |
| `references/drift-verification_tw.md` | `/dflow:verify` — rules.md ↔ behavior.md 一致性檢查 |

## 模板

| 模板 | 用途 |
|---|---|
| `templates/feature-spec.md` | 完整功能規格 |
| `templates/lightweight-spec.md` | Bug 修復/小變更的快速規格 |
| `templates/context-definition.md` | 新 Bounded Context 定義 |
| `templates/CLAUDE.md` | 放在專案根目錄的 CLAUDE.md |
