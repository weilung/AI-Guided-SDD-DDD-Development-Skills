# PROPOSAL-001 至 PROPOSAL-007 整合評估結果

> **評估日期**：2026-04-13
> **評估基礎**：重新研究 OpenSpec 1.0+（OPSX 架構）一手文件後進行
> **範圍**：7 份 draft 狀態的 Proposal + 跨提案的整體考量
> **產出**：逐項結論 + 修正建議 + 實施順序 + 風險管理

---

## 背景

本評估針對 SDD/DDD Workflow Skill 的 7 份改進提案進行整合分析。這些提案主要來自兩個方向：

1. **P001**：解決 Skill 自然語言驅動設計帶來的流程不透明問題（獨立於 OpenSpec 比較）
2. **P002–P007**：從 OpenSpec 比較分析中識別的改進機會，借鏡其變更管理機制

評估時遵循的原則：
- **融合而非取代**：借鏡 OpenSpec 的變更管理優勢，不動搖 DDD 引導和架構規範的核心優勢
- **可落地優先**：團隊 DDD 經驗尚淺，簡單明確比靈活複雜更重要
- **向下相容**：WebForms 版和 Core 版的共通部分保持一致

---

## 依賴關係

```
P001 (Workflow Transparency)     ← 完全獨立，無前置依賴

P002 (Delta Spec Format)         ← 基礎層，後續提案的前提
  ↓
P003 (Source of Truth)           ← 依賴 P002（delta 格式用於累積更新）
  ↓
P005 (Completion Flow)           ← 依賴 P003（behavior.md）+ P004（tasks）
  ↑
P004 (Spec Template Restructure) ← 與 P002 互補，可同批作業

P006 (Ceremony Scaling)          ← 與 P004 互補（段落跳過對照需要 P004 的五段結構）
P007 (Brownfield & Review)       ← 與 P002/P003 互補，但無硬依賴
```

各提案之間**無衝突**。依賴鏈為線性：P002 → P003 → P005，P004 可平行插入。

---

## 逐項評估

### P001 — Workflow Transparency

**結論：`approved`（需修正實施方式）**

**原始提案摘要**：在三個時機揭示 AI 內部狀態——流程辨識後確認、每個 Step 轉換時報進度、支援主動詢問。

**評估意見**：

方向完全正確。三種失敗模式（沒觸發、觸發錯、不知道自己在哪）都是自然語言驅動設計的結構性風險，需要主動的透明化機制來補償。

**修正 1：階段轉換提示的粒度應降低。** 原始提案要求每個 Step 都報「Step N 完成，進入 Step N+1」，在 8 步流程中會產生大量儀式性輸出，干擾對話節奏。建議改為只在「大階段邊界」報進度。建議的大階段邊界：

- 流程辨識完成 →「開始領域分析」
- 領域分析完成 →「進入行為規格設計」
- 行為規格完成 →「進入技術設計」
- 技術設計完成 →「開始產生實作任務」
- 實作完成 →「進入完成驗證」

這約 5 個轉換點（vs 原始的 8 個 Step 轉換），資訊量足夠但不囉嗦。

**修正 2：透明化規則應集中在 SKILL.md，不逐一修改 flow 檔案。** 原始提案的影響範圍列了修改每個 flow 檔的每個 Step，工作量被低估且維護成本高。建議改為：在 SKILL.md 新增「Workflow Transparency」段落，作為通則寫入（含大階段邊界定義）。AI 讀到這段後，在執行任何 flow 時都會遵守，不需要每個 flow 檔案重複寫。

**保留不變**：流程辨識後的確認機制和主動詢問分支，設計良好，不需修改。

**修正後影響範圍**：

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| SKILL.md（兩版） | 修改 | 新增 Workflow Transparency 段落（含大階段邊界定義 + 主動詢問分支） |

---

### P002 — Delta Spec Format

**結論：`approved`（追加兩個設計建議）**

**原始提案摘要**：在 modify-existing-flow 中引入 ADDED / MODIFIED / REMOVED 結構化變更描述格式，維持 Gherkin 風格，MODIFIED 保留「原本/改為」對照。

**評估意見**：

核心設計良好。Gherkin 風格（Given/When/Then）比 OpenSpec 的 RFC 2119（SHALL/MUST）更適合 DDD 新手團隊。MODIFIED 的「原本/改為」對照比 OpenSpec 只寫新版本更清楚——在 brownfield 場景中，reviewer 需要知道「改了什麼」而不只是「現在是什麼」。

**追加建議 1：加入 RENAMED 操作。** OpenSpec 支援 RENAMED 來追蹤 requirement 重新命名。在 brownfield 場景中，重新命名業務概念是常見操作（例如「簽核」→「審批」、「客戶」→「帳戶持有人」），RENAMED 可以保持追蹤鏈，避免在 behavior.md（P003）中留下孤立條目。格式建議：

```markdown
### RENAMED — 重新命名
#### 規則：{舊名稱} → {新名稱}
**原因**：{為什麼改名}
```

**追加建議 2：加入可選的 UNCHANGED 段落。** 當一個功能有多條規則但只修改了部分，reviewer 常會問「其他規則有沒有被影響？」。建議在 delta 區段末尾加入可選的 UNCHANGED 段，列出不受影響的規則名稱（不需要列完整描述），明確表達「以下規則不在本次變更範圍內」。標註為「建議但非必填」，避免增加過多 ceremony。

**保留不變**：

- Gherkin 風格（不改用 RFC 2119）
- MODIFIED 的「原本/改為」對照
- 嵌入現有 spec 模板（不建額外檔案結構）
- lightweight-spec 也用精簡版 delta 格式

**修正後影響範圍**：與原始提案相同，delta 格式說明中追加 RENAMED 和 UNCHANGED 的定義即可。

---

### P003 — System Source of Truth (behavior.md)

**結論：`approved`（追加防護機制和時機調整）**

**原始提案摘要**：在每個 Bounded Context 下新增 `behavior.md`，按功能區域組織 Given/When/Then 情境，作為行為規格的 consolidated source of truth。

**評估意見**：

概念上是所有提案中最有價值的——一個隨時可讀的系統全貌。但也是最容易失敗的，因為其價值完全取決於是否被持續更新。

**追加建議 1：借鏡 OpenSpec 的 sync 概念，支援「中途更新」。** 原始提案假設在「完成時」更新 behavior.md，但實務上完成流程經常被跳過。借鏡 OpenSpec 的 `/opsx:sync`（中途同步而不歸檔），建議在行為規格（Phase 3）確認後就先更新 behavior.md 的草稿，不需要等到全部實作完成。完成流程（P005）再做最終確認。這樣即使開發者中途放棄或延期，至少行為分析的產出被保留了。

**追加建議 2：兩個防護機制。**

1. **PR Review 閘門**：在 PR Review Checklist（與 P007-PR 連動）加入「behavior.md 是否與本次變更同步？」作為 review 項目。這讓 behavior.md 的更新不只依賴開發者的自律，還有 reviewer 的把關。
2. **區塊層級的 last-updated 標記**：behavior.md 中每個功能區塊標注最後更新日期，讓過時的段落一目了然。避免整份文件的可信度被個別過時段落拖累。

```markdown
## 使用者登入（last-updated: 2026-04-10）
### 情境：正常登入
Given 使用者提供有效帳號密碼
When 提交登入表單
Then 建立 session 並導向首頁
```

**追加建議 3：明確的初始策略。** 對 WebForms 版來說，現有功能都沒有 behavior.md，不可能也不應該一次補齊。明確寫出策略：behavior.md 採「逐步累積」——只有被修改過的功能才會有條目，不要求從零補齊歷史。這與 OpenSpec 的漸進累積理念一致。

**保留不變**：

- 按 Bounded Context 組織（不是按 capability）
- 與 rules.md 互補（宣告式 vs 情境式）
- 由 AI 引導更新（不建 CLI 工具）

---

### P004 — Spec Template Restructure

**結論：`approved`（追加兩個設計建議）**

**原始提案摘要**：feature-spec 模板重構為五段式（Proposal / 領域分析 / 行為規格 / 技術設計 / 實作任務），每段標註「何時填寫」，第五段加入可勾選的 tasks 清單。

**評估意見**：

五段式結構比現在清楚很多。「何時填寫」的標註特別好——等於內建了 ceremony scaling 的指引。不拆成多個獨立檔案的決策是正確的（DDD 資訊的關聯性需要保持在同一份文件中）。

**追加建議 1：段落間加入依賴標注。** 借鏡 OpenSpec 的 Artifact State Machine 概念（不需要建狀態機，只需標注依賴），讓 AI 知道段落間的前後關係：

```markdown
## 三、行為規格（Phase 3 填寫 ← 需先完成「二、領域分析」）
```

這比單純標「Phase 3 填寫」更明確，幫助 AI 在開發者跳步時提出警告。

**追加建議 2：Tasks 段支援層級結構。** OpenSpec 的 tasks.md 支援層級任務（1.1、1.2 等），對複雜功能很有用。建議 Tasks 段不限於扁平清單，允許子任務：

```markdown
## 五、實作任務
- [ ] 1. 建立 Value Object
  - [ ] 1.1 建立 Money 類別，包含金額和幣別
  - [ ] 1.2 加入驗證（金額 > 0、幣別非空）
  - [ ] 1.3 寫單元測試
- [ ] 2. 修改 Aggregate
  - [ ] 2.1 ...
```

**追加建議 3：歸檔時清理 Tasks 段。** Tasks 的生命週期和 spec 不同——spec 是「要做什麼」，歸檔後仍有參考價值；tasks 是「怎麼做」，完成後就沒有保留價值。建議在 P005 的完成 checklist 中加入一步：歸檔前將 Tasks 段摺疊或移除，保持 `completed/` 目錄的信噪比。

---

### P005 — Completion Flow

**結論：`approved`（拆分為兩階段 + 驗證分層）**

**原始提案摘要**：在 new-feature-flow 加入結構化完成 Checklist（驗證 + 文件更新 + 歸檔），加入完成信號偵測，modify-existing-flow 也加精簡版。

**評估意見**：

這是所有提案中投入產出比最高的。完成流程的缺失是目前最大的實務問題——spec 寫了但完成時沒人驗證、文件沒更新。

**修正 1：拆分為 P005a 和 P005b。** P005 在原始依賴鏈中排在末端（依賴 P003 + P004），全部等到那時才做太晚了。拆分後：

**P005a（第一批，不依賴任何其他提案）**：
```markdown
### 驗證
- [ ] 對照 spec 的每個 Given/When/Then，確認實作覆蓋
- [ ] 對照 spec 的每個 BR-*（業務規則），確認實作覆蓋
- [ ] 對照 spec 的每個 EC-*（邊界情況），確認有處理
- [ ] Domain 層無 System.Web 引用（WebForms）/ 無外部 NuGet（Core）

### 文件更新
- [ ] glossary.md — 新術語已加入
- [ ] models.md — 模型定義已更新
- [ ] rules.md — 業務規則已更新
- [ ] events.md — Domain Events 已更新（Core 版）
- [ ] tech-debt.md — 發現的技術債已記錄

### 歸檔
- [ ] Spec 狀態改為 completed
- [ ] Spec 搬移到 completed/

### 完成信號偵測（SKILL.md 新增）
```

**P005b（第三批，P003 + P004 完成後）**：
```markdown
- [ ] behavior.md — 行為規格已合併（P003）
- [ ] Tasks 清單全部勾選完成（P004）
- [ ] Tasks 段已摺疊或移除（P004）
```

**修正 2：驗證分層。** 受 OpenSpec 的 `/opsx:verify` 啟發，將 checklist 項目區分為兩類：

| 類別 | 說明 | 項目 |
|---|---|---|
| **AI 可獨立驗證** | AI 不需要詢問開發者，可以自己對照檢查 | Domain 層純淨度、Given/When/Then 覆蓋對照、Tasks 全部勾選、glossary/models/rules 是否有更新 |
| **需開發者確認** | AI 列出發現，由開發者判斷 | 業務規則的實作是否符合意圖、邊界情況的處理方式是否恰當、tech-debt 是否有遺漏 |

AI 在偵測到完成信號時，先跑「可獨立驗證」的項目，報出結果，再引導開發者確認剩餘項目。

---

### P006 — Ceremony Scaling Expansion

**結論：`approved`（調整方向，不引入 Schema 機制）**

**原始提案摘要**：在 Ceremony Scaling 表格加入「DDD 建模深度」欄位，建立漸進導入路徑（Stage 1-4）。

**評估意見**：

OpenSpec 的 Custom Schema 揭示了比「表格加欄位」更根本的思路——可自訂的工作流組合。但我認為**我們不需要走到那麼遠**，理由：

- 我們只用 Claude Code，不需要多工具適配的 schema engine
- Skill 本身就是 AI 指令，不需要額外的 schema 來組裝指令
- 團隊 DDD 經驗尚淺，簡單明確的表格比靈活但複雜的 schema 系統更容易落地

**修正 1：DDD 建模深度欄位保留。** 這是 OpenSpec 完全不涉及的 DDD 專有需求，直接命中團隊痛點（不知道何時該做完整建模、何時可以簡化）。

**修正 2：增加「段落跳過對照」。** 借鏡 Custom Schema 的「最小 artifact 集」概念，但用更簡單的方式實現——在 Ceremony Scaling 表格旁，明確對照每個級別應該填寫 P004 五段模板中的哪些段落：

| Ceremony 級別 | 填寫段落 | 跳過段落 |
|---|---|---|
| Full | 一～五全部 | — |
| Standard | 一、三、四 | 二（領域分析）簡化、五（Tasks）可選 |
| Lightweight | 一、三（精簡） | 二、四、五 |

這比 Custom Schema 簡單得多，但達到類似的效果。

**修正 3：漸進導入改為條件觸發。** 原始的 Stage 1-4 綁定具體時間（第 1-2 週、第 3-4 週...），不同團隊節奏差異很大。改為條件式：

```
Stage 1：當團隊開始使用 → 只要求 Lightweight Spec
Stage 2：當團隊能自然產出 Lightweight Spec → 加入 Full Spec + Glossary
Stage 3：當團隊熟悉 spec 流程 → 引入 Domain Extraction
Stage 4：當團隊理解 Domain 層的價值 → 完整 SDD + DDD
```

---

### P007 — Brownfield & Review

**結論：`approved`（拆分為三個獨立項目，PR Review 提升優先級）**

**原始提案摘要**：三個改善——逆向文件化系統性強化、PR Review 加入 Spec Intent 步驟、CLAUDE.md 分段強化。

**評估意見**：

這三個項目的關聯性很弱，硬綁在一起模糊了優先級。建議拆分為三個獨立項目：

**P007-PR：PR Review 加入 Spec Intent 步驟**
- **優先級提升到中**，移入第一批實施
- 幾乎零成本（在 checklist 開頭加 4 行），但完成了 SDD 流程的回饋迴路——如果 reviewer 不先看 spec，前面所有的規格工作就失去了驗證機制
- 即使還沒有 P002 的 delta 格式，「先讀 spec 再 review code」本身就有價值

**P007-Baseline：逆向文件化強化（WebForms）**
- 維持低優先級，第三批實施
- 借鏡 OpenSpec 的 baseline change 模式，但整合進我們的 DDD 框架：
  - 在 behavior.md（P003）中為被修改但無文件的功能建立條目
  - 掃描 Code-Behind 抽取業務規則到 rules.md
  - 識別領域概念到 models.md
  - 記錄技術債到 tech-debt.md
- 定位為「趁修改時順便做」的機會主義策略，不要求完整掃描

**P007-CLAUDE：CLAUDE.md 分段強化**
- 維持低優先級，第三批實施
- 變更範圍極小（更明確的標題分離），可以在第三批其他工作中順手完成

---

## 跨提案的整體考量

### OpenSpec 的兩個能力暫不引入

**1. Explore 階段**

OpenSpec 的 `/opsx:explore` 讓開發者在承諾變更前自由探索。我們的場景中，P007-Baseline 的逆向文件化 + P001 的流程透明化已部分覆蓋了這個需求。建議等第一、二批實施後，再評估是否需要獨立的 explore 階段。

**2. Project Config 的 per-artifact 規則注入**

OpenSpec 的 `config.yaml` 允許 per-artifact 的規則注入。我們的 CLAUDE.md 承擔類似角色但結構性較弱。P007-CLAUDE 的分段強化是第一步，更深入的結構化可以作為未來的 Proposal。

### 累積複雜度風險

七個提案全部實施後，Skill 的檔案數量、規則數量、和 AI 需要遵守的行為指引都會顯著增加。風險包括：
- Claude Code 的 context window 壓力變大
- 不同指引之間出現微妙的矛盾
- 維護成本超過團隊的承受力

**緩解措施**：在第三批完成後，進行一次「精簡化審查」——通讀所有 Skill 檔案，檢查冗餘指引、重複規則、和不一致之處，做一次精簡。

---

## 實施計畫

### 第一批（立即可做，互不依賴）

| 項目 | 工作量 | 核心變更 |
|---|---|---|
| P001（簡化版） | 小 | SKILL.md 新增 Workflow Transparency 段落 |
| P005a（驗證 + 信號偵測） | 小 | flow 檔加入完成 checklist + SKILL.md 加入信號偵測 |
| P007-PR（Spec Intent Review） | 極小 | pr-review-checklist 開頭加 Step 0 |

**第一批完成後的檢查點**：確認團隊是否實際使用驗證 checklist 和 Spec Intent Review。如果連這些低成本改進都沒被採用，後續批次需要調整策略（可能是流程本身的問題，不是 checklist 不夠好）。

### 第二批（核心變更管理）

| 項目 | 工作量 | 核心變更 |
|---|---|---|
| P002（Delta Spec + RENAMED + UNCHANGED） | 小 | modify-existing-flow 加入 delta 格式 + lightweight-spec 更新 |
| P004（模板重構 + 依賴標注 + 層級 Tasks） | 中 | feature-spec 重構為五段式 + SKILL.md 更新 Guiding Questions |

**第二批完成後的檢查點**：確認 Delta Spec 格式是否被團隊接受、五段式模板是否太複雜。決定是否繼續第三批——特別是 behavior.md（P003）是所有提案中維護成本最高的，如果團隊連基本的完成 checklist 都做不到，behavior.md 不應該強推。

### 第三批（Source of Truth + 收尾）

| 項目 | 工作量 | 核心變更 |
|---|---|---|
| P003（behavior.md + 中途同步 + 防護機制） | 中 | 新增 behavior.md 模板 + flow 檔加入更新指引 |
| P005b（補完 checklist） | 極小 | 完成 checklist 加入 behavior.md 和 Tasks 項目 |
| P006（Ceremony Scaling + 段落跳過對照） | 小 | SKILL.md 表格擴展 + 漸進導入指引 |
| P007-Baseline + P007-CLAUDE | 小 | 逆向文件化指引 + CLAUDE.md 分段強化 |

**第三批完成後**：進行精簡化審查。

---

## 附錄：各 Proposal 的評估紀錄摘要

供填寫各 Proposal 檔案的「評估紀錄」段落時參考。

| Proposal | 結論 | 一句話理由 |
|---|---|---|
| P001 | approved（需修正） | 方向正確，但階段轉換提示應降低粒度，規則集中在 SKILL.md |
| P002 | approved（追加 RENAMED + UNCHANGED） | 核心設計良好，追加兩個操作類型讓 delta 格式更完整 |
| P003 | approved（追加防護 + 中途同步） | 價值最高但風險也最高，需要 PR Review 閘門和 last-updated 標記來防護 |
| P004 | approved（追加依賴標注 + 層級 Tasks） | 五段式結構解決了模板臃腫和 tasks 缺失兩個問題 |
| P005 | approved（拆分為 P005a/P005b + 驗證分層） | ROI 最高的提案，P005a 提前到第一批 |
| P006 | approved（調整方向） | 不引入 Schema 機制，用「段落跳過對照」實現類似效果 |
| P007 | approved（拆分為三項） | PR Review 提升到中優先級進第一批，其餘維持低優先級 |
