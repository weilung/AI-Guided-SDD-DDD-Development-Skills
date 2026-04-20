# Review Workflow — P001–P008 實作審查流程

> **目的**：把 P001–P008 八份提案的實作產物（已 commit 於 27c6988「所有提案實作結束」）進行系統性審查，確保實作忠於 proposal 意圖、無邏輯矛盾、WebForms/Core 兩版一致、繁中/英文同步。
>
> **設計考量**：實作工作量累積已超過單一 session 可承受的 context，因此審查採**三階段 + 多輪**的拆分結構。每輪只處理語義相關的一組 proposal，每階段只做單一模式的工作。

---

## 一、三階段角色分工

| 階段 | 工具 | 輸入 | 輸出 | 為什麼是這個工具 |
|---|---|---|---|---|
| **1. Review** | **Codex CLI** | 該輪 proposal + 實作檔 | `reviews/round-N-report.md` | 不同模型 = 不同盲點；Codex 以「對抗性審查者」角度看 Claude 產出 |
| **2. Approve** | **Claude Code**（新 session）| Codex 的 review 報告 | `reviews/round-N-decisions.md`（accept / reject / defer 標記）| Claude 熟悉設計意圖，能判斷哪些是真問題、哪些是誤判 |
| **3. Implement** | **Claude Code**（新 session）| Approved decisions | 修改實作檔 + 更新 CHANGELOG | Claude 已實作過這些 proposal，改碼一致性較好 |

### 為什麼三階段必須分開

- **不同 session 避免確認偏誤**：同一 session 裡「我剛做完」的認知會傾向自我辯護；換手能得到獨立判斷。
- **Review 與 Implement 是不同心智模式**：Review 需要懷疑，Implement 需要執行。混在一起降低兩邊品質。
- **Approve 是人類控制點**：不能把 accept/reject 決定權交給 AI。Claude 在 Approve 階段只是「整理選項」，使用者是裁判。

---

## 二、分輪策略

八份提案拆為 6 輪，每輪 1–3 個語義相關提案。輪與輪之間無依賴（但 Round 3 與 Round 1 共用 SKILL.md，排程上建議循序）。

| Round | 主題 | Proposals | 核心審查檔案 |
|---|---|---|---|
| **R1** | 流程入口與透明度 | P001, P007b | `SKILL.md` × 2 + `new-feature-flow.md` × 2 + `modify-existing-flow.md` × 2 + `pr-review-checklist.md` × 2 |
| **R2** | 變更描述格式 | P002, P004 | `modify-existing-flow.md` × 2 + `feature-spec.md` × 2 + `lightweight-spec.md` × 2 + `new-feature-flow.md` × 2（Step 5） |
| **R3** | 完成流程 Checklist | P005a, P005b | `new-feature-flow.md` × 2（Step 8）+ `modify-existing-flow.md` × 2（最終 Step）+ `SKILL.md` × 2（Completion 子節） |
| **R4** | Source of Truth 與漂移驗證 | P003, P008 | `behavior.md` × 2 + `context-definition.md` × 2 + `drift-verification.md` × 2 + `SKILL.md` × 2（/dflow:verify）+ 各 BC 的 rules.md 索引規範 |
| **R5** | Ceremony 與逆向文件化 | P006a, P007a | Core `SKILL.md`（DDD Depth 欄）+ WebForms `modify-existing-flow.md`（Brownfield 補寫） |
| **R6** | 輔助與教學 | P007c, Tutorial | `templates/CLAUDE.md` × 2 + `tutorial/TUTORIAL_*_tw_hybrid.md` × 2 |

### 繁中版審查納入策略

每一輪的英文版和繁中版**同輪審查**，不另開「繁中 review 專輪」。理由：drift 最容易出現在兩語言的同步點，分輪會讓對照中斷。

---

## 三、各階段產出格式

### Review 報告格式（Codex 產出）

每輪一份 `reviews/round-N-report.md`：

```markdown
# Round N Review Report — {主題}

**審查範圍**: P00X, P00Y
**審查日期**: YYYY-MM-DD
**審查者**: Codex

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| {總數} | {數} | {數} | {數} | {數} |

## Findings

### F-01 {問題一句話描述}
**嚴重度**: Critical | Major | Minor | Question
**位置**: `path/to/file.md:line-range`
**對照 Proposal**: P00X 的「XXX」段落
**現況**: {實際看到什麼}
**問題**: {為什麼有問題；是邏輯矛盾 / 遺漏 / 兩版不一致 / 誤解 proposal 意圖}
**建議**: {具體怎麼改；若 Question 類型則寫「需釐清：...」}

### F-02 ...

## 兩版一致性對照

| 檢查項 | WebForms 版 | Core 版 | 一致？ |
|---|---|---|---|
| ... | ... | ... | ✓ / ✗ |

## 繁中同步對照

| 檢查項 | 英文版 | 繁中版 | 一致？ |
|---|---|---|---|
| ... | ... | ... | ✓ / ✗ |
```

**嚴重度定義**：
- **Critical**：實作違背 proposal 意圖，或導致流程無法運作
- **Major**：實作不完整或兩版不一致，影響使用
- **Minor**：格式、排版、措辭不夠精確，不影響功能
- **Question**：Reviewer 不確定，需要設計者釐清

### Approval 決議格式（Claude Approve session 產出）

每輪一份 `reviews/round-N-decisions.md`：

```markdown
# Round N Decisions — {主題}

**對應 review 報告**: `reviews/round-N-report.md`
**決議日期**: YYYY-MM-DD
**決議者**: {使用者}（Claude 整理）

## 決議彙總

| Finding | 嚴重度 | 決議 | 說明 |
|---|---|---|---|
| F-01 | Critical | **accept** | 照建議改 |
| F-02 | Major | **reject** | 誤判：Codex 看到的「矛盾」其實是 P00X 刻意設計 |
| F-03 | Minor | **defer** | 等 Wave D 精簡化審查一起處理 |
| F-04 | Question | **clarify** | 澄清後歸入 accept/reject |

## Accept 項目的實作指引（交給 Implement session）

### F-01
- 修改檔案：`path/to/file.md`
- 具體改動：...
- 注意事項：...

### F-05
...

## Reject/Defer 項目備忘

- **F-02 reject 理由**：... （寫清楚給未來回顧用）
- **F-03 defer 原因**：... （何時處理）
```

### Implementation 紀錄（Claude Implement session 產出）

- 照 `round-N-decisions.md` 的 accept 項目修改檔案
- `CHANGELOG.md` 新增一段：`## YYYY-MM-DD — Round N 審查修正：{主題}`，逐項列出修改
- commit message：`Round N review fixes: {accepted findings count} items`

---

## 四、三階段 Session 提示詞模板

### 階段 1：Codex Review Session 提示詞

> 使用者在 Codex CLI 中貼這段給該輪設定。

```
你是 P00X 和 P00Y 實作的審查者。請讀以下檔案：

【Proposal 來源】
1. proposals/PROPOSAL-00X-{slug}.md
2. proposals/PROPOSAL-00Y-{slug}.md
3. proposals/evaluation-p002-p008.md 的對應段落（作為設計脈絡參考）

【實作產物】
{列出該輪「核心審查檔案」— 見 review-workflow.md 第二節 Round N 表格}

【審查重點】
1. 實作是否忠於 proposal 意圖？有沒有遺漏 proposal 明列的要求？
2. 實作是否有邏輯矛盾或自相牴觸的指引？
3. WebForms 版和 Core 版的共通部分是否一致？
4. 英文版和繁中版是否同步（用字、段落結構、範例）？
5. 實作有沒有超出 proposal 範圍的改動（可能引入未經審查的行為）？

【產出格式】
依照 proposals/review-workflow.md 第三節「Review 報告格式」寫入
reviews/round-N-report.md。

【重要規則】
- 不要修改任何實作檔，只產出 review 報告。
- 嚴重度判斷從嚴：Critical 保留給「違背 proposal 意圖」級別，不要濫用。
- 若對 proposal 意圖不確定，標為 Question，不要擅自判 Critical。
```

**需要替換的變數**：
- `{N}`：輪次編號
- `{P00X, P00Y}`：該輪的 proposal 編號
- `{列出該輪核心審查檔案}`：從 Round 表格抄下來

### 階段 2：Claude Approve Session 提示詞

> 開新 Claude Code session，第一句話貼這段。

```
這是 P00X 和 P00Y 實作的審查決議階段（Round N）。請依序讀：

1. proposals/review-workflow.md（本流程的規範）
2. proposals/PROPOSAL-00X-{slug}.md
3. proposals/PROPOSAL-00Y-{slug}.md
4. reviews/round-N-report.md（Codex 的審查報告）

讀完後，針對 review 報告的每一個 Finding，你的工作是：

1. 判讀這個 finding 是真問題、誤判、還是需釐清
2. 提出你的建議決議（accept / reject / defer / clarify）和理由
3. 一次向使用者呈現一個 finding，等使用者確認決議後才處理下一個
4. 全部決議完成後，產出 reviews/round-N-decisions.md（格式見
   review-workflow.md 第三節「Approval 決議格式」）

【重要規則】
- 你不是裁判，使用者才是。你的工作是分析選項、提供建議。
- 不要修改任何實作檔，也不要動 review 報告。
- 誤判要明確說出哪裡誤判（不要只說「這不是問題」）。
- Accept 的項目要把「具體改動」寫得夠細，讓 Implement session 可以照做、
  不需要重新閱讀 review 報告。
```

**需要替換的變數**：
- `{N}`：輪次編號
- `{P00X, P00Y}`、`{slug}`：該輪的 proposal

### 階段 3：Claude Implement Session 提示詞

> 再開新 Claude Code session，第一句話貼這段。

```
這是 P00X 和 P00Y 審查修正的實作階段（Round N）。請依序讀：

1. proposals/review-workflow.md（本流程的規範）
2. reviews/round-N-decisions.md（已核准的修改清單）
3. （如需要）reviews/round-N-report.md 和對應 proposal

你的工作：

1. 逐項實施 decisions 文件裡標為 accept 的項目
2. 每項實施完檢查：
   - 改動範圍是否符合「具體改動」描述
   - 有沒有影響到 decisions 沒提到的其他檔案
3. 全部實施完後：
   - 更新 CHANGELOG.md：新增
     「## YYYY-MM-DD — Round N 審查修正：{主題}」一段
   - 列出所有修正項目（引用 F-XX 編號和一句話說明）
   - 列出影響檔案

【重要規則】
- 不要做 decisions 沒列的改動，即使你覺得應該順手修。有新發現寫備註
  給下一輪，不要偷改。
- Reject / Defer 的項目完全不動。
- 完成後不主動 commit，只報告使用者「已完成 N 項修改，請檢視後 commit」。
```

**需要替換的變數**：
- `{N}`：輪次編號
- `{P00X, P00Y}`：該輪的 proposal

---

## 五、輪次執行順序與狀態追蹤

| Round | 主題 | 狀態 | Review 完成日 | Approve 完成日 | Implement 完成日 |
|---|---|---|---|---|---|
| R1 | 流程入口與透明度 | 未開始 | — | — | — |
| R2 | 變更描述格式 | 未開始 | — | — | — |
| R3 | 完成流程 Checklist | 未開始 | — | — | — |
| R4 | Source of Truth 與漂移驗證 | 未開始 | — | — | — |
| R5 | Ceremony 與逆向文件化 | 未開始 | — | — | — |
| R6 | 輔助與教學 | 未開始 | — | — | — |

**建議執行順序**：R1 → R2 → R3 → R4 → R5 → R6（從核心流程到輔助產物）。但各輪間無硬依賴，可依時間與精力彈性調整。

**跨輪發現事項**：某輪 review 若發現問題根源在其他輪的範圍內，記在 `reviews/cross-round-notes.md`，在進入該輪 Review 階段前讀入。

---

## 六、目錄結構建議

```
reviews/
├── round-1-report.md              ← Codex 產出
├── round-1-decisions.md           ← Claude Approve session 產出
├── round-2-report.md
├── round-2-decisions.md
├── ...
└── cross-round-notes.md           ← 跨輪發現的累積筆記
```

建立時機：第一輪 Review 開始前，由使用者手動 `mkdir reviews/`。

---

## 七、流程結束條件

六輪全部完成（Review + Approve + Implement）後：

1. 執行一次**全域一致性 sweep**（可作為 Wave D「Skill 精簡化審查」的前置步驟）：
   - 檢查各輪修正之間是否有新引入的矛盾
   - 檢查 CHANGELOG 連貫性
2. 更新 `evaluation-p002-p008.md` 第七節：標記 review 階段完成
3. 決定是否進入 Wave B 實施（P003 pilot / P005b 補完）

---

## 附註

- 本文件本身不進入 review 範圍（review 流程的規範，不是 proposal 的實作）。
- 若流程跑一輪後發現需要修改（例如報告格式調整、階段順序調整），直接改本文件；改動以註記日期的方式記錄在本節附註下方。
