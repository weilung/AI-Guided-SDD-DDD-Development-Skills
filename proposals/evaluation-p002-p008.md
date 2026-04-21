# SDD/DDD Skill Proposal 整合評估（P002-P008）

**評估日期**: 2026-04-15

**評估對象**: PROPOSAL-002 至 PROPOSAL-007（原 draft），以及本次新增的 PROPOSAL-008

**評估者背景**: 團隊剛開始導入 DDD，評估者自身為 DDD 學習中。評估採「情境式推演」進行——不先給結論，先演一次實際情境才判斷 proposal 的取捨。

**與 P001 的關係**: P001 已於 2026-04-14 定稿為 Hybrid 設計（`/dflow:` 前綴、Flow entry / Phase gate / Step-internal 三級透明化）。本評估的所有判斷都建立在 P001 已實施的前提上。

---

## 一、評估背景與方法

### 為什麼要做這次評估

P002-P007 原本是在 P001 定稿前提出的，P001 引入 slash command 指令化入口 + auto-trigger 安全網後，部分 proposal 的設計假設需要重新審視。例如：

- P005 的「完成信號偵測」原本要靠 AI 分析自然語言，但 P001 的 `/dflow:next` Phase Gate 已提供明確觸發點
- P004 的「Phase 標註」原本要靠模板重構，但 P001 的 `/dflow:status` 已經需要 phase 資訊

此外，原 proposals 中有幾份捆綁了多個彼此無關的改動（P006、P007），適合拆分重新審視。

### 評估方法

1. **重讀 P002-P007** + `comparison-analysis.md`
2. **情境推演**：針對每個 proposal 想像實際使用情境，判斷成本效益
3. **識別盲點**：對每個決定標註關鍵假設，供雙盲對照驗證
4. **跨提案依賴分析**：識別哪些 proposal 必須先做、哪些可獨立進行

---

## 二、Executive Summary

### 所有決定一覽

| Proposal | 決定 | 關鍵調整 |
|---|---|---|
| **P002** Delta 格式 | 核准 + 澄清 + 追加 | Delta 標記只用在變更 spec，不累積到 consolidated behavior.md；追加 RENAMED + 可選 UNCHANGED |
| **P003** behavior.md | 核准 + A+C 結構 + pilot + 防護 | rules.md 當索引、behavior.md 當情境內容；先 pilot 一個 BC 再決定全面 rollout；加入中途同步與 last-updated 標記 |
| **P004** 模板重構 | 精簡版 | 只加 tasks 清單 + HTML comment phase 標記；不整體重構、不拆檔 |
| **P005** 完成流程 | 拆分為 P005a/P005b + 簡化 + 驗證分層 | P005a 提前到 Wave A、P005b 留 Wave B；完成信號偵測靠 P001 Phase Gate；checklist 分為 AI 可獨立驗證 vs 需開發者確認 |
| **P006** Ceremony 擴展 | 拆分 | P006a（DDD 深度欄）核准；P006-2（漸進導入）defer |
| **P007** Brownfield+Review | 拆分為三，P007b 提前 | P007a / P007c 留 Wave C；**P007b（PR Review Spec Intent）提前到 Wave A**（SDD 回饋迴路關鍵） |
| **P008**（新增）| 新提案 | Drift Verification 指令 `/dflow:verify [<bc>]`，先做機械層 |

### 實施順序（Wave A→D）

```
Wave A（基礎結構 + 低成本高價值，可並行）
  P001（已定稿）→ P002（Delta 格式 + RENAMED + UNCHANGED）
  → P004 精簡（tasks + phase 標記）
  → P005a（驗證 + 完成信號偵測，不依賴 P003/P004 的部分）
  → P007b（PR Review Spec Intent，零成本完成 SDD 回饋迴路）

  ── Checkpoint A ──
  確認團隊實際使用驗證 checklist 和 Spec Intent Review。
  若連這些低成本改進都沒被採用，後續 Wave 需調整策略。

Wave B（源於 Wave A）
  P003 pilot 單一 BC（含中途同步、last-updated 標記）
  → P005b（補完 checklist：behavior.md + Tasks 全勾）

  ── Checkpoint B ──
  確認 Delta Spec 接受度、Phase 標記實效、behavior.md 維護成本。
  若團隊連基本完成 checklist 都做不到，behavior.md 不應強推。

Wave C（獨立微調，可並行）
  P006a（DDD 深度欄）、P007a（Brownfield）、P007c（CLAUDE.md 分段）、
  P008（Drift Verification）

  ── Checkpoint C ──
  累積複雜度審視，是否啟動 Wave D 的精簡化審查。

Wave D（實證後再決定）
  P003 全面 rollout（pilot 跑完 2-3 個 feature 後）
  P006-2 漸進導入（累積 2-3 個月使用數據後，選方案 α/β）
  Skill 精簡化審查（檢查冗餘指引、重複規則、不一致之處）
  P008 語意層驗證（若機械層數據顯示需要升級）
```

---

## 二之二、雙盲對照後的修正

### 對照來源

獨立 Claude.AI session（不參與本評估討論）對 P001-P007 進行的平行評估，記錄於 `EVALUATION-RESULT.md`（評估日期 2026-04-13）。該評估基於 P001 **原始版本**進行（未含本 repo 已定稿的 Hybrid 設計）。

### 雙盲比對的目的

確認本評估是否有「在情境推演中沒考慮到」的盲點。對照基礎是兩份評估都看相同的 7 份 proposal 文件，但分析路徑與設計脈絡不同。

### 採納的盲點修正（B1-B7）

| 編號 | 來源建議 | 修正內容 | 原因 |
|---|---|---|---|
| B1 | P002 加 RENAMED 操作 | P002 加入 RENAMED 段落定義 | brownfield 改名（「簽核」→「審批」）常見，避免 behavior.md 留下孤立條目 |
| B2 | P002 加可選 UNCHANGED 段落 | P002 加入 UNCHANGED 為「建議但非必填」 | reviewer 常問「其他規則有沒有被影響」，明確表達範圍 |
| B3 | P003 中途同步（行為規格 Phase 3 確認後就先更新 behavior.md 草稿）| P003 加入「中途同步機制」段落 | 完成流程經常被跳過，至少保留行為分析的產出 |
| B4 | P003 區塊層級 last-updated 標記 | P003 範例加入 `last-updated:` 註記規範 | 過時段落明確標示，避免拖累整份文件可信度 |
| B5 | P005 拆分為 P005a + P005b | P005 重整：P005a 移到 Wave A、P005b 留 Wave B | 不依賴 P003/P004 的驗證項（如 Domain 純淨度、Given/When/Then 對照）可立即做 |
| B6 | P005 驗證分層（AI 獨立驗證 vs 需開發者確認）| P005 加入「驗證項目分類表」 | AI 可先跑可獨立的部分，引導開發者確認剩餘項目，提升效率 |
| B7 | Wave 之間加 milestone checkpoint | Executive Summary Wave 圖加入 Checkpoint A/B/C | 避免堆疊式失敗——前一波沒被採用，後一波不該硬推 |

### 採納的優先級調整（D1）

| 編號 | 來源建議 | 我們原本 | 修正為 |
|---|---|---|---|
| D1 | P007b（PR Review Spec Intent）提前到第一批 | Wave C | **Wave A** |

**理由**：原評估認為 P007b 工作量小，所以放 Wave C 跟其他小改動一起。但對照評估指出，P007b 的價值不在工作量大小，而在於它**完成了 SDD 流程的回饋迴路**——如果 reviewer 不先看 spec，前面所有規格工作就失去了驗證機制。即使還沒有 P002 的 delta 格式，「先讀 spec 再 review code」本身就有價值。

### 考慮但不採納（D2）

| 編號 | 來源建議 | 我們的處理 |
|---|---|---|
| D2 | P006-2 漸進導入改為「條件觸發」（不綁定時間）| 仍維持 defer（方案 γ），但記錄此替代方案以備 Wave D 決策參考 |

**為什麼不採納**：條件觸發本身是好建議（避免綁定具體週數），但不解決我們識別的核心問題——P006-1（Ceremony Scaling 加 DDD 深度欄）已大致涵蓋「按任務調整嚴謹度」，再加 Stage 分段會造成「同一週兩個建議衝突」。對照評估沒考慮 P006-1/P006-2 重疊問題。

但**條件式 Stage 1-4 比時間綁定 Stage 1-4 更合理**——若 Wave D 評估後決定採用 P006-2，採條件式比時間式好。記錄於 P006-2 段落。

### 我們在這些地方走得比對照評估深（S1-S4，僅供記錄）

| 編號 | 我們的觀察 | 對照評估的處理 |
|---|---|---|
| S1 | 質疑 P004 五段式重構的前提（spec 是對話填寫，不是空白模板自填）| 保留五段式並建議追加段落依賴標注、層級 Tasks |
| S2 | P003 採 A+C 結構 + 衍生 P008 drift verification 作為信任預設下的安全網 | 採 A+B 思維（rules.md 與 behavior.md 並存，靠 PR Review 防護）|
| S3 | P005 完成信號偵測簡化為靠 P001 `/dflow:next` Phase Gate | P005 仍保留自然語言偵測 |
| S4 | 衍生 P008 drift verification 新提案 | 未提出 |

這四項是兩份評估的設計路徑差異，不是盲點。記錄供日後回顧時對照思路。

### Wave D 新增「Skill 精簡化審查」

對照評估指出：**七個提案全部實施後，累積複雜度風險**——Claude Code context window 壓力、規則間微妙矛盾、維護成本超載。建議在 Wave C 之後做一次精簡化審查。

採納為 Wave D 新增項目。詳見第六節。

---

## 三、各 Proposal 詳細評估

### P002: 引入 Delta Spec 變更描述格式

#### 原提案摘要

在 modify-existing-flow 引入結構化 Delta Spec 格式，用 ADDED / MODIFIED / REMOVED 標記業務規則變更。MODIFIED 保留「原本 vs 改為」對照。

#### 評估結論

**核准，附一個重要澄清 + 兩個操作類型追加（B1、B2）**。

#### 關鍵澄清

**Delta 標記只用在「變更 spec」，不累積到 consolidated source of truth（P003 的 behavior.md）**。

**理由**：
- 如果 delta 標記永久保留在 consolidated 文件中，`MODIFIED` 對照(「原本 X，改為 Y」)會不斷堆疊變成歷史雜訊
- consolidated 文件應該反映「系統現在的完整行為」，不是「所有歷史變更的軌跡」
- git 本身就保留歷史，consolidated 文件只需保留當前真相

#### 追加 B1：RENAMED 操作

在 ADDED / MODIFIED / REMOVED 之外加入 RENAMED，用於追蹤業務概念改名：

```markdown
### RENAMED — 重新命名
#### 規則：{舊名稱} → {新名稱}
**原因**：{為什麼改名}
```

**使用情境**：brownfield 場景中常見的概念演化，例如「簽核」→「審批」、「客戶」→「帳戶持有人」。

**價值**：
- 保持追蹤鏈，PR reviewer 能看到「這不是新規則、也不是刪除舊規則，而是同一條規則換了名字」
- 避免 behavior.md（P003）中留下孤立條目（舊名稱的情境還在、新名稱又多一份）
- 對 P007b（PR Review Spec Intent）Step 0 讀 delta 時提供清晰語義

#### 追加 B2：可選的 UNCHANGED 段落

在 delta 區段末尾加入**可選**的 UNCHANGED 段，列出不受影響的規則名稱（不需完整描述）：

```markdown
### UNCHANGED — 明確不受影響（可選）
- BR-003 金額上限
- BR-005 提交後不可修改
```

**為什麼需要**：一個功能有多條規則、但只改了其中一兩條時，reviewer 常問「其他規則有沒有被影響？」。UNCHANGED 段主動回答這個問題，明確劃出變更範圍。

**標註為「建議但非必填」**：避免增加過多 ceremony。開發者可在 MODIFIED 規則較多時省略；在 regression 風險高的 refactor 類變更主動加上。

#### 與其他 Proposal 互動

- P003 的 behavior.md 更新機制會讀取 delta spec、**merge 成當前狀態**後寫入（不是直接複製 delta 格式）
- P007b（PR Review Spec Intent）的 Step 0 會讀 delta 格式了解變更意圖，包含 RENAMED / UNCHANGED 的語義

#### 關鍵假設

- 假設 1：Delta 格式比自由文字描述的效益大於格式負擔
- 假設 2（新）：RENAMED 的使用頻率足夠支持學習成本。若一年用不到兩次，可能成為冗餘格式
- 假設 3（新）：UNCHANGED「建議非必填」的定位不會造成「建議但沒人填」的空轉
- 驗證點：實施後觀察開發者是否願意填寫完整的 Given/When/Then，還是會退化成「改了 X」的單行描述

---

### P003: 建立系統現狀 Source of Truth

#### 原提案摘要

每個 Bounded Context 下新增 `behavior.md`，作為該 BC 行為規格的 consolidated 來源。每次 feature 完成時合併進來。

#### 評估結論

**核准，附四個重要調整（含 B3、B4）**：

1. **採 A+C 混合結構**：rules.md 當索引（表格 + 指向 behavior.md 的 anchor）、behavior.md 當情境內容（完整的 Given/When/Then）
2. **採 pilot 策略**：不強制所有 BC 都建 behavior.md，等第一個新 feature 所屬的 BC 自然建立；跑過 2-3 個 feature 後再決定是否全面 rollout
3. **（B3）中途同步機制**：行為規格（Phase 3）確認後就先更新 behavior.md 草稿，不等 Phase 7 完成流程
4. **（B4）區塊層級 last-updated 標記**：behavior.md 中每個功能區塊標注最後更新日期

#### A+C 結構範例

**`rules.md`**（索引）：
```markdown
| ID | 規則 | 詳細情境 |
|---|---|---|
| BR-001 | 金額必須為正數 | behavior.md#提交報銷單 |
| BR-002 | 單筆上限 NT$100,000 | behavior.md#金額上限 |
```

**`behavior.md`**（內容）：
```markdown
## 提交報銷單（last-updated: 2026-04-10）

### 情境：金額驗證
Given 員工填寫報銷單
When 金額 <= 0
Then 系統拒絕提交（BR-001）
```

#### 追加 B3：中途同步機制

**問題**：原提案假設在 Phase 7「完成時」更新 behavior.md，但實務上完成流程經常被跳過（忘了、延期、中途放棄都會導致）。

**解法**：借鏡 OpenSpec 的 `/opsx:sync` 概念，在 Phase 3（行為規格確認後）就先更新 behavior.md 的草稿：

- **Phase 3 結束**：AI 將本次 spec 的 Given/When/Then 整理後寫入 behavior.md（標記為 draft、或放在明確的 `## 提案中變更` 段落）
- **Phase 7 完成流程**：再做最終確認，把 draft 轉為正式段落、更新 last-updated 日期

**價值**：即使開發者中途放棄或延期，至少行為分析的產出被保留了、rules.md 的 BR-* 有對應 anchor，不會出現 P008 drift verification 就跳錯誤的情況。

**風險**：如果 Phase 3 寫入但後續實作大改，會造成 behavior.md 與實際實作脫節。緩解：Phase 7 完成流程必須重新對照 spec 和 behavior.md。

#### 追加 B4：區塊層級 last-updated 標記

**每個功能區塊**都標注最後更新日期，而非整份文件一個日期：

```markdown
## 使用者登入（last-updated: 2026-04-10）
### 情境：正常登入
...

## 權限檢查（last-updated: 2025-11-03）
### 情境：...
```

**價值**：
- 過時段落一目了然——看到 2025-11-03 的段落，reviewer 和 AI 都能知道這裡可能不準
- 避免「整份文件的可信度被個別過時段落拖累」
- 配合 P008 drift verification：機械層可加檢查「anchor 指向的段落 last-updated 是否早於程式碼最後修改日期」

**寫入時機**：
- Phase 3 草稿寫入時：標記 `(draft)`
- Phase 7 完成時：改為實際日期 `YYYY-MM-DD`
- 既有段落被修改時：同步更新日期

#### 為什麼是 A+C 而不是 A、B、C 其他組合

評估時考慮三個選項：
- **A（宣告式 + 情境式並存）**：重複但各有用處。drift 風險明顯
- **B（只留 behavior.md）**：單一真相但「快速瀏覽規則清單」需求被犧牲
- **C（rules.md 是索引、behavior.md 是內容）**：乾淨分工，drift 風險低

最終 **A+C 的意思**：結構用 C（分工明確），但**加上 P008 drift verification 的機制**作為信任預設下的驗證安全網。

這回答了 A 的 drift 焦慮，同時保留 C 的簡潔。

#### 為什麼是 pilot 而非全面 rollout

- 團隊剛學 DDD，寫 behavior.md 的品質和效率還是未知數
- 單點試驗失敗只要修一個 BC；全面 rollout 失敗要修所有 BC
- 2-3 個 feature 的使用數據足以判斷結構是否順暢

#### 與其他 Proposal 互動

- **依賴 P002**：behavior.md 的更新需要 P002 的 delta 格式作為 input 源
- **被 P005b 依賴**：完成 checklist 需要知道「behavior.md 草稿轉正式」這一項
- **配合 P007b**：PR Review Step 0 可檢查「behavior.md 是否與本次變更同步」，作為 B3 中途同步的 review 閘門
- **觸發 P008**：為了彌補 A+C 結構的 drift 風險，催生了 P008 drift verification 指令；last-updated 標記（B4）為 P008 機械層提供額外驗證線索

#### 關鍵假設

- 假設 1：rules.md 索引 + behavior.md 內容的分工在實務上好維護
- 假設 2：pilot 一個 BC 的經驗能 generalize 到其他 BC
- 假設 3：DDD 新手團隊能順利撰寫情境式 behavior.md
- 假設 4（新，B3）：Phase 3 草稿寫入不會造成「草稿 vs 實作」脫節問題（靠 Phase 7 最終確認緩解）
- 假設 5（新，B4）：開發者會實際維護 last-updated 日期（或由 AI 在修改時自動更新）

---

### P004: Spec 模板重構

#### 原提案摘要

把 feature-spec 模板重構為五段式（Proposal → 領域分析 → 行為規格 → 技術設計 → 實作任務），每段標註「Phase N 填寫」；加入 tasks 清單。

#### 評估結論

**採精簡版**：只加 tasks 清單 + 在既有段落加 HTML comment phase 標記；**不做整體重構、不拆檔**。

#### 為什麼砍掉整體重構

**關鍵洞察**：`/dflow:new-feature` 後的實際流程是**對話驅動的協作填寫**（AI 透過 Step 1-4 逐段引導問答、逐段填入 spec），不是「開發者打開空白模板自填」。

這意味著：
- 「看到空白模板會卻步」的問題在協作模式下本來就小
- 五段式重構的核心價值是 Phase 標記，這個目的**只加 HTML comment 也達得到**
- 整體重構的成本（改模板、改 SKILL.md、改 Guiding Questions、兩版同步）和收益不成正比

#### 精簡版的具體形式

```markdown
## 行為規格 <!-- 填寫時機：Phase 3 -->
...

## 實作計畫（逐層）<!-- 填寫時機：Phase 4 -->
...

## 實作任務 <!-- 填寫時機：Phase 4 完成後由 AI 產生 -->
- [ ] DOMAIN-1：建立 Money VO 含幣別驗證
- [ ] DOMAIN-2：ExpenseReport 加 Submit() 方法
- [ ] APP-1：SubmitExpenseReportCommandHandler
```

#### 為什麼不拆成多檔案（像 OpenSpec 的 proposal.md / design.md / tasks.md / specs/）

評估時考慮了 OpenSpec 的分拆模式。結論：**DDD 驅動的領域功能需要語義緊耦合**。

- OpenSpec 拆檔合理是因為它不碰 DDD，proposal 和 tasks 天然不共享概念
- 我們的 spec 一整條語義鏈：領域概念 → 行為規格 → Aggregate 不變條件 → 層級實作，拆開會讓 reviewer 跳檔拼湊
- `/opsx:new` wizard 的 UX 效果在**單檔 + HTML comment phase 標記 + `/dflow:status`** 組合下也能達成，不用拆檔

未來若單檔真的過度膨脹，再考慮新增 `/dflow:continue` wizard 指令（不拆檔，只是互動強化）——**列為 Wave D 延後評估項**。

#### 與其他 Proposal 互動

- **被 P005 依賴**：完成 checklist 需要知道「tasks 全部勾選」這一項
- **配合 P001**：HTML comment 讓 `/dflow:status` 能精準回報 Phase 進度

#### 關鍵假設

- 假設 1：HTML comment 不會被 AI 忽略（Claude Code 解析 markdown 時會讀到）
- 假設 2：對話驅動的填寫模式是主流，不會有大量開發者要求「給我空白模板我自己填」

---

### P005: 完成流程強化（拆分為 P005a + P005b）

#### 原提案摘要

1. 在 new-feature-flow Step 8 加入結構化完成 checklist（驗證、文件更新、歸檔）
2. 在 SKILL.md 加入「完成信號偵測」（當開發者說「做完了」、「PR ready」時觸發驗證）
3. 在 modify-existing-flow 加入精簡版完成 checklist

#### 評估結論

**拆分為 P005a / P005b + 一個簡化 + 一個驗證分層**。

#### B5 拆分：P005a（Wave A）+ P005b（Wave B）

**原判斷**：P005 依賴 P003 + P004，全部放 Wave B。

**修正判斷**：checklist 項目裡有一部分**不依賴 P003/P004**（如 Domain 層純淨度、Given/When/Then 覆蓋對照），這些可提前到 Wave A。全部等到 Wave B 才做，會讓「驗證文化」的建立晚半拍。

**P005a（Wave A，不依賴其他 proposal）**：
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
```

**P005b（Wave B，P003 + P004 完成後補上）**：
```markdown
- [ ] behavior.md — 行為規格已合併（P003，B3 的 draft 轉正式）
- [ ] Tasks 清單全部勾選完成（P004）
- [ ] Tasks 段已摺疊或移除（P004，歸檔前清理）
```

#### 關鍵簡化

**「完成信號偵測」直接靠 P001 的 `/dflow:next` Phase Gate 觸發，不用另做自然語言偵測**。

原 proposal 要讓 AI 偵測「做完了」、「PR ready」等自然語言信號。但 P001 實施後，Step 7→8（實作完成 → 進入 completion）是明確的 Phase Gate——開發者打 `/dflow:next` 或說「好」的時候，AI 應該停下來跑完成 checklist，這就是觸發點。

**收益**：
- 不用 SKILL.md 維護自然語言信號詞表
- 觸發精準（明確指令或確認回覆）
- 開發者控制感強（知道這一刻在做什麼）
- 簡化 AI 實作邏輯

#### B6 追加：驗證分層

checklist 項目區分為兩類，AI 執行時依序處理：

| 類別 | 說明 | 項目 |
|---|---|---|
| **AI 可獨立驗證** | AI 不需詢問開發者，可自己對照檢查 | Domain 層純淨度、Given/When/Then 覆蓋對照、Tasks 全部勾選、glossary/models/rules 是否有更新、behavior.md 是否有對應 BR-* |
| **需開發者確認** | AI 列出發現，由開發者判斷 | 業務規則的實作是否符合**意圖**、邊界情況的處理方式是否**恰當**、tech-debt 是否有遺漏、behavior.md 情境是否完整表達預期行為 |

**AI 執行順序**：
1. 先跑「可獨立驗證」項目，報出結果（✓/✗ 列表）
2. 若全數通過，主動引導開發者確認「需開發者確認」項目（一次問一項，不一次倒一大串）
3. 全部通過後，才進入歸檔步驟

**價值**：
- 避免 AI 偽裝驗證（AI 無法判斷意圖，但可能硬說「已驗證」）
- 開發者清楚知道哪些是 AI 把關、哪些需要自己看
- 配合 P008 drift verification：P008 主要是 AI 獨立驗證，P005 的驗證分層給出更完整的分類基準

#### 與其他 Proposal 互動

- **P005a 不依賴**：可在 Wave A 獨立實施
- **P005b 依賴 P003**：checklist 包含「behavior.md 草稿轉正式」
- **P005b 依賴 P004**：checklist 包含「tasks 全部勾選、歸檔前清理」
- **依賴 P001**：完成觸發點使用 Phase Gate（P005a 和 P005b 都需要）

因此 P005a 可和 P002 / P004 精簡並行；P005b 等 Wave A 結束後接 Wave B。

#### 關鍵假設

- 假設 1：`/dflow:next` 的 Phase Gate 足以替代所有「開發者表達完成意圖」的情境
- 假設 2（新，B6）：AI 可獨立驗證的項目清單實際上 AI 能可靠執行（需在 Wave A pilot 時觀察失敗率）
- 風險：如果開發者跳過 Phase Gate 直接 commit & push，完成 checklist 不會觸發
- 緩解：P001 auto-trigger 安全網 + Git Flow 整合仍會攔截（但不是本 proposal 的職責）

---

### P006: Ceremony Scaling 擴展（拆分）

#### 原提案摘要

1. Ceremony Scaling 表格加「DDD 建模深度」欄
2. 建立「漸進導入指引」（Stage 1-4 時間分段）

#### 評估結論

**拆分為 P006a（核准）+ P006-2（defer 方案 γ，暫不建檔）**。

#### 為什麼拆分

兩個子項**完全不共享實作**，只共享「幫助 DDD 新手」主題：
- P006a：Core SKILL.md 的一張表格加一欄
- P006-2：獨立的漸進導入 playbook

捆綁會讓「核准 P006」變成「同時接受兩者」的全有全無，阻礙 P006a 這個輕量改善。

#### P006a: DDD 深度欄（核准）

**變動**：Core 版 Ceremony Scaling 表格加入「DDD 建模深度」欄

| 變更類型 | Spec 層級 | DDD 建模深度 | Tech Debt |
|---|---|---|---|
| 新 Aggregate / BC | Full spec | 完整：Aggregate 設計 + Events + Context Map | — |
| 新功能（現有 BC 內） | Full spec | 標準：確認 Aggregate 歸屬 + 新 VO/Event | 如有 |
| 業務規則變更 | Standard spec | 標準：更新 rules.md + 檢查 invariants | 是 |
| Bug fix（邏輯錯誤） | Lightweight | 輕量：確認修在正確的層 | 如有 |
| API / UI 變更 | Lightweight | 跳過 | 跳過 |

#### P006-2: 漸進導入（defer，方案 γ）

**為什麼 defer**：

情境推演發現 P006-1（DDD 深度欄）已大致涵蓋 P006-2 要解的問題（按任務類型動態調整嚴謹度）。P006-2 的「時間分段」反而會和實際工作類型衝突：

| 週 | 小明做的事 | P006-1 建議嚴謹度 | P006-2 建議嚴謹度 |
|---|---|---|---|
| W2 | 新增匯出 Excel 功能 | Full Spec（新功能）| Lightweight（Stage 1）|
| W3 | 修 bug | Lightweight | Full Spec（Stage 2）|

→ 小明不知該聽哪個。

**現在不做，等 2-3 個月實證後決定**：
- **方案 α**：P006-2 合併進 P006-1（認定 Ceremony Scaling 自動涵蓋新手友善）
- **方案 β**：P006-2 重新定位為「Team Lead 觀察清單」，移到 `docs/team-adoption-playbook.md`（不是 Skill 內容）
- **方案 γ**（選定）：暫不處理，等數據才決定 α 還是 β

**關鍵假設**：Ceremony Scaling 加上 team lead 的手動介入就夠照顧新手，不需要制式的時間分段指引。

#### D2 備註：條件式 Stage（考慮但不採納）

對照評估（`EVALUATION-RESULT.md`）建議將 P006-2 的 Stage 1-4 改為**條件觸發**（非時間綁定）：

```
Stage 1：當團隊開始使用 → 只要求 Lightweight Spec
Stage 2：當團隊能自然產出 Lightweight Spec → 加入 Full Spec + Glossary
Stage 3：當團隊熟悉 spec 流程 → 引入 Domain Extraction
Stage 4：當團隊理解 Domain 層的價值 → 完整 SDD + DDD
```

**為什麼不立即採納**：條件式 Stage 本身是好建議（避免團隊節奏被綁死），但它不解決我們識別的核心問題——P006-1（Ceremony Scaling 加 DDD 深度欄）已大致涵蓋「按任務調整嚴謹度」，Stage 分段仍會造成同一週兩個建議衝突的情境。

**但若 Wave D 決策時選 β（Team Lead playbook）或 α（合併回 P006-1）**，**採條件式優於時間式**。記錄此備註以供當時判斷。

#### 關鍵假設

- 假設：新手的學習曲線是任務驅動的（做到什麼學到什麼），不是時間驅動的
- 驗證點：實施 Wave A-B 後觀察新成員有沒有卡關點，再決定是否需要 α 或 β

---

### P007: Brownfield + Review（拆分為三）

#### 原提案摘要

三個低優先小改善綁在一起：
1. WebForms modify-existing-flow 加「系統性 baseline capture」指引
2. PR Review Checklist 加 Step 0「先讀 spec 再看 code」
3. CLAUDE.md 標題分段強化

#### 評估結論

**拆分為 P007a / P007b / P007c 各自獨立可核准**。

#### 為什麼拆分

三者影響的檔案、概念、時機都不相同：

| 子項 | 影響檔案 | 和 P001 關係 | Wave |
|---|---|---|---|
| P007a Brownfield | `modify-existing-flow.md`（WebForms）| 低：和流程控制無關 | Wave C |
| P007b PR Review Spec Intent | `pr-review-checklist.md`（兩版）| **高**：`/dflow:pr-review` 進入點 | **Wave A**（D1 修正）|
| P007c CLAUDE.md 分段 | `templates/CLAUDE.md`（兩版）| 低：和 AI 互動無關 | Wave C |

**P007b 值得獨立且提前到 Wave A（D1 修正）**：

- 原排程放 Wave C 是因為工作量小，但價值判斷錯誤
- 真正價值：**完成 SDD 流程的回饋迴路**——如果 reviewer 不先看 spec，前面所有規格工作（寫 spec、Delta、behavior.md）就失去了驗證機制
- 零成本：pr-review-checklist 開頭加 4 行 Step 0 指引
- 即使還沒有 P002 的 delta 格式，「先讀 spec 再 review code」本身就有價值（可讀任何現有 spec 內容）
- 和 P001 的 `/dflow:pr-review` 指令高度配對

#### P007a: Brownfield 強化

**變動**：WebForms 版 modify-existing-flow 在 Step 2 和 Step 3 之間加入「系統性 baseline capture」指引——當 AI 發現被修改的功能沒有現有 spec 時，主動：
1. 讀取相關 Code-Behind
2. 抽取業務規則
3. 識別領域概念
4. 檢查跨頁面重複邏輯
5. 記錄到 domain docs 和 tech-debt.md

這是「趁修改時順便做」的機會主義策略，不是每次都完整掃描。

#### P007b: PR Review Spec Intent（Wave A，D1 修正）

**變動**：PR Review Checklist 開頭加 Step 0：

```markdown
## Step 0: 理解變更意圖（先於 Code Review）
- [ ] 閱讀本次 PR 對應的 spec
- [ ] 如果是修改既有功能，閱讀「行為變更（Delta）」段落
- [ ] 確認你理解：這個 PR 想解決什麼問題、預期行為是什麼
- [ ] 然後才進入程式碼 review
```

**與 P002 的配對**：Step 0 讀 delta 格式的「行為變更」（包含 RENAMED / UNCHANGED）。
**與 P001 的配對**：`/dflow:pr-review` 進入時第一步就是 Step 0。
**與 P003 的配對**：Step 0 可延伸檢查「behavior.md 是否與本次變更同步」（B3 中途同步的 review 閘門）。

**為什麼提前到 Wave A（非 Wave C）**：SDD 流程的回饋迴路。如果 reviewer 不先看 spec，前面的規格工作（寫 spec、Delta、behavior.md）就失去驗證機制。零成本高價值。

#### P007c: CLAUDE.md 分段強化

**變動**：`templates/CLAUDE.md` 用更明確的標題分離：

```markdown
## 系統脈絡（What is this system?）
> 技術棧、業務領域、團隊結構

## 開發流程（How do we work?）
> SDD 流程、Git Flow、Domain 層規範
```

目前的 CLAUDE.md 模板已經有類似分段，只需標題更明確。工作量極小。

#### 關鍵假設

- P007a 假設：「趁修改時 baseline」能累積出完整文件（而非只是散點）
- P007b 假設：reviewer 願意遵循 Step 0，不會跳過直接 review code
- P007c 假設：標題明確化能實際影響 AI 讀取策略（需要觀察）

---

### P008（新增）: Drift Verification

#### 提案起源

在評估 P003 時，採用 A+C 結構（rules.md 索引 + behavior.md 內容）。為了**在信任預設下提供驗證安全網**，評估者（配合使用者提議）衍生出這個新 proposal。

#### 核心設計

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

**理由**：
- 機械層已能抓大部分 drift
- 語意層耗 token 且判斷結果需要人類複核
- 等用過一陣子再決定是否需要（列為未來延伸）

#### 影響範圍

| 檔案 | 變更類型 |
|---|---|
| `sdd-ddd-core-skill/SKILL.md` | 修改：加入 `/dflow:verify` 指令說明和觸發規則 |
| `sdd-ddd-webforms-skill/SKILL.md` | 同上 |
| `sdd-ddd-core-skill/references/drift-verification.md` | 新增：驗證邏輯的完整規範 |
| `sdd-ddd-webforms-skill/references/drift-verification.md` | 同上 |

#### 與其他 Proposal 互動

- **依賴 P003**：必須先有 A+C 結構（rules.md 索引格式、behavior.md anchor 規範）才能驗證
- **配合 P001**：`/dflow:verify` 使用 P001 的 `/dflow:` 前綴

#### 關鍵假設

- 假設 1：大部分 drift 是機械層可偵測的（BR-ID 遺漏、anchor 失效），而非語意矛盾
- 假設 2：開發者會在關鍵時機（PR 前、release 前、重構後）主動執行 `/dflow:verify`
- 驗證點：跑過 10+ 次 verify 後觀察「找到的問題」類型分布——如果大部分是語意矛盾，需要升級到語意層

---

## 四、跨面向觀察

### 依賴圖

```
P002 (Delta 格式)
  ├─→ P003 (behavior.md 更新的 input)
  └─→ P007b (PR Review Spec Intent 讀 delta；P002 未實施前仍可用舊 spec)

P003 (behavior.md)
  ├─→ P005b (completion checklist 包含 behavior.md 草稿轉正式)
  └─→ P008 (drift verification 的驗證對象)

P004 精簡 (tasks + phase 標記)
  └─→ P005b (completion checklist 包含「tasks 全部勾選」)

P001 (Hybrid, 已定稿)
  ├─→ P005a / P005b (completion 觸發點使用 /dflow:next Phase Gate)
  ├─→ P007b (/dflow:pr-review 進入 PR Review Step 0)
  └─→ P008 (/dflow:verify 使用 /dflow: 前綴)

獨立無依賴：P005a（AI 可獨立驗證的部分）、P006a、P007a、P007b（不硬依賴 P002）、P007c
```

### 為什麼 Wave A 是 P002 + P004 精簡 + P005a + P007b

- P002 為 P003 的 behavior.md 更新提供格式基礎
- P004 精簡為 P005b 的 checklist 提供「tasks 勾選」基礎
- P005a 的驗證項（Domain 純淨度、Given/When/Then 覆蓋對照）不依賴其他 proposal，可先建立「驗證文化」
- P007b 完成 SDD 回饋迴路，零成本高價值
- 四者無互依或弱依賴，可並行做

### 為什麼 P006a / P007a / P007c / P008 在 Wave C

- 這四項彼此無依賴、都是獨立小改動
- P008 依賴 P003 的 A+C 結構已落地，因此在 Wave B 之後

### 為什麼 P003 全面 rollout 在 Wave D

- Wave B 的 pilot 實施後需要 2-3 個 feature 的使用數據
- 數據點包括：behavior.md 撰寫效率、A+C 結構維護成本、drift 發生頻率（`/dflow:verify` 結果）
- 基於數據再決定「全面 rollout」或「調整結構」

### 為什麼 P006-2 在 Wave D

- 需要 2-3 個月（而非 2-3 個 feature）的新成員加入經驗
- 評估 Ceremony Scaling（P006a）是否足以照顧新手
- 決定 α（合併）/ β（Team Lead playbook）/ 繼續 defer

---

## 五、關鍵假設清單（供盲點檢測）

以下是本評估中**不能百分之百確定的假設**，如果某一項不成立，對應的決定需要調整。這些是最可能被獨立評估者點出的潛在盲點。

| 假設編號 | 假設內容 | 如果假設不成立，影響哪個決定 |
|---|---|---|
| A1 | Delta 格式只在變更 spec 使用、不累積，能避免歷史雜訊同時保留 review 價值 | P002 核准 |
| A2 | rules.md 索引 + behavior.md 內容的 A+C 結構在實務上好維護 | P003 全面 rollout |
| A3 | pilot 一個 BC 的經驗能 generalize 到其他 BC | P003 全面 rollout 判斷 |
| A4 | DDD 新手團隊能順利撰寫情境式 behavior.md | P003 pilot 可行性 |
| A5 | HTML comment phase 標記會被 Claude Code 可靠讀取 | P004 精簡 |
| A6 | 對話驅動的填寫是主流，不會有大量「我要空白模板自填」需求 | P004 不拆檔 |
| A7 | `/dflow:next` Phase Gate 足以替代自然語言完成信號偵測 | P005 簡化 |
| A8 | 新手的學習曲線是任務驅動的（Ceremony Scaling 夠用），不是時間驅動的 | P006-2 defer |
| A9 | 大部分 drift 是機械層可偵測的，而非語意矛盾 | P008 只做機械層 |
| A10 | 單檔 spec + HTML comment 能達到 OpenSpec `/opsx:new` wizard 的 UX 效果 | P004 不拆檔、不做 wizard |
| A11 | RENAMED 的使用頻率足夠支持學習成本（B1）| P002 追加 RENAMED |
| A12 | UNCHANGED「建議非必填」不會造成「建議但沒人填」的空轉（B2）| P002 追加 UNCHANGED |
| A13 | Phase 3 草稿寫入不會造成「草稿 vs 實作」嚴重脫節（靠 Phase 7 最終確認緩解）（B3）| P003 中途同步 |
| A14 | 開發者或 AI 會實際維護 last-updated 日期（B4）| P003 last-updated 標記的可信度 |
| A15 | P005a 驗證項可在 AI 獨立執行層面可靠判斷（B6）| P005a 提前到 Wave A 可行性 |
| A16 | P007b 即使沒有 P002 的 delta 格式仍有價值（D1）| P007b 提前到 Wave A 判斷 |

### 評估者自我檢視的潛在盲點

1. **過度依賴 P001 的 Phase Gate 假設其永遠成立**——如果實施 P001 後發現 Phase Gate 的頻率不對（例如開發者覺得吵），簡化後的 P005 也會連帶失效
2. **A+C 結構假設 rules.md 和 behavior.md 的分工清晰，但在實務中「什麼放 rules.md、什麼放 behavior.md」可能模糊**——例如「多條件組合的規則」是展開到情境，還是宣告在 rules.md？
3. **P004 砍掉五段式重構可能讓 Phase 進度回報變得模糊**——HTML comment 方案沒有經過實測驗證，如果 Claude Code 對 comment 的處理不如預期，`/dflow:status` 可能無法精準判斷 Phase
4. **P006-2 defer 可能留下團隊導入時期的真空**——沒有 onboarding 指引的情況下，新成員可能因為缺乏指導而放棄或走偏

---

## 六、實施順序（Wave A→D）完整清單

### Wave A — 基礎結構 + 低成本高價值（可並行）

| 任務 | 影響檔案 | 前置條件 |
|---|---|---|
| **P001 實施** | 6 個檔案（SKILL.md × 2 + flow × 4）| 已完成定稿 |
| **P002 實施**（含 RENAMED + UNCHANGED）| modify-existing-flow × 2, lightweight-spec × 2 | — |
| **P004 精簡實施** | feature-spec × 2, SKILL.md × 2, new-feature-flow × 2 | — |
| **P005a 實施**（驗證 + 文件更新 + 歸檔 checklist）| new-feature-flow × 2, modify-existing-flow × 2, SKILL.md × 2 | P001 |
| **P007b PR Review Spec Intent**（D1）| pr-review-checklist × 2 | P001（P002 非硬依賴）|

#### ── Checkpoint A（Wave A 結束後） ──

進入 Wave B 前確認：
- 團隊是否實際執行 P005a 的驗證 checklist？（抽查最近 3 個 feature 的 spec 歸檔）
- `/dflow:pr-review` 進入時，reviewer 是否先看 spec？（觀察 PR 評論內容）
- P002 的 Delta 格式是否被接受？（觀察 modify-existing 類 spec 的填寫品質）

**判斷原則**：如果這些低成本改進都沒被採用，Wave B 的 P003（維護成本最高）不應強推。若未採用，先排查原因——可能是 Skill 指引不清、可能是工具鏈限制、可能是團隊優先級問題，對症處理。

---

### Wave B — 源於 Wave A

| 任務 | 影響檔案 | 前置條件 |
|---|---|---|
| **P003 pilot 實施**（A+C 結構 + 中途同步 + last-updated）| SKILL.md × 2, new-feature-flow × 2, modify-existing-flow × 2, context-definition × 2, behavior.md 模板 × 2 | P002, Checkpoint A 通過 |
| **P005b 實施**（補完 checklist：behavior.md + Tasks）| new-feature-flow × 2, modify-existing-flow × 2 | P003, P004, P005a |

#### ── Checkpoint B（Wave B 結束後） ──

進入 Wave C 前確認：
- pilot BC 的 behavior.md 是否順利維護？（觀察 Phase 3 草稿寫入、Phase 7 轉正式的實行率）
- A+C 結構（rules.md 索引 + behavior.md 情境）實務上清不清楚？
- last-updated 日期是否有被更新？（抽查 behavior.md 隨機段落）

**判斷原則**：若 behavior.md 維護成本超預期，Wave C 可延後 P008（drift verification 依賴 behavior.md），其他項目（P006a / P007a / P007c）仍可推進。

---

### Wave C — 獨立微調（可並行）

| 任務 | 影響檔案 | 前置條件 |
|---|---|---|
| **P006a** | Core SKILL.md | — |
| **P007a Brownfield** | WebForms modify-existing-flow | — |
| **P007c CLAUDE.md 分段** | templates/CLAUDE.md × 2 | — |
| **P008 Drift Verification** | SKILL.md × 2, drift-verification.md × 2（新增）| P003, Checkpoint B 通過 |

#### ── Checkpoint C（Wave C 結束後） ──

進入 Wave D 前評估：
- 七個 proposal 全實施後，Skill 檔案的**累積複雜度**如何？（規則數、指引數、互相衝突點）
- Context window 是否承受得住？（觀察 `/dflow:` 指令執行時的 token 使用量）
- 團隊回饋是否反映「規則太多、讀不過來」？

**判斷原則**：若累積複雜度超載，**Wave D 的第一優先是 Skill 精簡化審查**（見下），其次才是 P003 全面 rollout 等決策。

---

### Wave D — 實證後決定

| 任務 | 觸發條件 | 決定內容 |
|---|---|---|
| **Skill 精簡化審查** | Wave C 結束 Checkpoint C | 通讀所有 Skill 檔案，檢查冗餘指引、重複規則、不一致之處；砍掉 / 合併 / 重組 |
| **P003 全面 rollout** | 2-3 個 feature 使用 pilot BC 之後 | 全面推廣 / 調整結構 / 回滾 |
| **P006-2 處理** | 2-3 個月新成員加入數據之後 | 方案 α（併入 P006a）/ β（Team Lead playbook）/ 繼續 defer；若採 α 或 β，**使用條件式 Stage 而非時間綁定**（見 P006-2 D2 備註）|
| **P008 語意層驗證** | 機械層跑過 10+ 次後的問題類型分布 | 是否升級到語意層 |
| **`/dflow:continue` wizard** | 如果單檔 spec 膨脹到難管理 | 新增 P009（假設） |

**Wave D 執行順序建議**：先做 Skill 精簡化審查（清理後再做其他 Wave D 決策，避免在膨脹的 Skill 上繼續堆新東西）。

---

## 七、後續行動（本評估後的下一步）

1. **雙盲比對**（✓ 完成）：與獨立 Claude.AI session（`EVALUATION-RESULT.md`，2026-04-13）比對
2. **盲點修正**（✓ 完成）：已納入 B1-B7、D1-D2，詳見第二之二節
3. **R1–R6 審查階段已完成**（✓ 完成，2026-04-21）：P001–P008 實作的六輪審查（Review + Approve + Implement）全部完成，最後一輪 R6 於 2026-04-21 定案，同日完成全域一致性 sweep（見 `reviews/global-sweep-report.md`）。各輪決議文件見 `reviews/round-{1..6}-decisions.md`；R1/R2/R4 有 accept 並實施，R3/R5/R6 為 0 Finding。下一步進入 Post-Review Closeout 階段（繁中同步 + Tutorial 重建，見 `proposals/review-workflow.md` 第七節 7.2）。
4. **實體拆分 proposal 檔案**（下一步待執行）：
   - 建立 `PROPOSAL-006a-ddd-depth-column.md`
   - 建立 `PROPOSAL-007a-brownfield-reverse-documentation.md`
   - 建立 `PROPOSAL-007b-pr-review-spec-intent.md`（標記 Wave A 優先）
   - 建立 `PROPOSAL-007c-claude-md-segmentation.md`
   - 建立 `PROPOSAL-008-drift-verification.md`
   - 舊 P006、P007 保留並加註 `superseded-by` 欄位
5. **更新 comparison-analysis.md**：
   - 納入 P001
   - 更新 proposal 清單（P005 拆分、P006/P007 拆分、P008 新增）
   - 更新實施順序為 Wave A-D（含 Checkpoint A/B/C）
6. **既有 P002/P003/P004/P005 內文調整**：延後到 Wave A/B 實施時順便處理（納入 B1-B6 的具體內容）

---

## 八、術語對照（供讀者參考）

| 術語 | 意義 |
|---|---|
| **Bounded Context (BC)** | DDD 的領域邊界，邊界內的詞彙有專屬意義 |
| **Aggregate** | DDD 的一致性邊界，單一交易只修改一個 Aggregate |
| **Delta Spec** | 變更描述格式，用 ADDED/MODIFIED/REMOVED 標記業務規則變化 |
| **Source of Truth** | 系統現狀的權威文件，反映當前真相（不是歷史軌跡）|
| **Ceremony Scaling** | 依變更影響動態調整流程嚴謹度 |
| **Phase Gate** | P001 定義的「主要里程碑前停下來告知/確認」的時機 |
| **Drift** | rules.md 和 behavior.md 之間失去一致性的狀態 |
| **Pilot** | 小規模先試，驗證後再全面推廣 |
