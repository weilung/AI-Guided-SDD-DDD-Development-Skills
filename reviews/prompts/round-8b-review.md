# Round 8b Review Prompt — PROPOSAL-013（Draft Review，給 Cursor 使用）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Cursor。
> **產出位置**：Reviewer 會寫入 `reviews/round-8b-report.md`。
> **禁止事項**：本輪不得修改任何檔（包含 proposal、skill 檔、planning 檔皆不動），只產出 review 報告。
> **R8a 與 R8b 的關係**：R8a 處理 PROPOSAL-012（路徑英文化，前置）；R8b 處理 PROPOSAL-013（樣板覆蓋，依賴 P012 路徑決策）。R8b 假設 P012 將被通過，但**不審 P012 本身**——只需確認 P013 對 P012 的依賴宣稱準確。
> **R8b 的特殊性**：P013 範圍大（~40 檔影響），引入多項新概念（canonical English structural language、`<!-- dflow:section X -->` anchor、TEMPLATE-COVERAGE.md、TEMPLATE-LANGUAGE-GLOSSARY.md）。除了影響範圍準確性，本輪重點在「設計決策合理性」與「對既有 P001-P012 機制的相容性」。
> **範圍規模**：1 份 draft proposal 約 17 KB，加 context 與現況檔約 80 KB；Finding 預估 8-20 項。
> **模型選擇**：使用者於 Cursor 自選；建議使用 frontier 模型（範圍大、需要設計判斷）。

---BEGIN PROMPT---

你是 Dflow Round 8b Review 的審查者。本輪範圍為 1 份 draft proposal（PROPOSAL-013：系統文件樣板覆蓋與 Template Coverage Matrix）的設計品質、影響範圍準確性、以及對既有 P001-P012 機制的相容性，**不審任何實作檔**（本輪還沒實施）。

## 第一步：讀入以下檔案

### A. 審查標的（必讀，逐段細讀）

1. `proposals/PROPOSAL-013-system-document-template-coverage.md`

### B. 邊界與脈絡檔（必讀，理解 P013 的職責邊界與依賴）

2. `proposals/PROPOSAL-012-distribution-friendly-paths.md` — P013 的路徑前提；只讀以理解依賴，**不審 P012 本身**
3. `planning/public-distribution-backlog.md` — npm 前才處理的議題；P013 多次引用此檔劃出 out-of-scope 邊界（特別是 § 1 內容語言策略、§ 4 Template Language Glossary）
4. `planning/project-review-brief.md` — 系統文件結構觀，幫助理解 P013 的「Dflow-managed `.md`」涵蓋範圍
5. `reviews/cross-round-notes.md` — 議題 1（lightweight-spec 缺 tasks）是 P013 § 7 的源頭背景
6. `proposals/review-workflow.md` — 流程規範骨幹

### C. P001-P012 既有機制檢查（必讀，重點檢查 P013 是否破壞既有設計）

P013 § 影響範圍涉及 BC layer docs / drift-verification / lightweight-spec / init-project，這些都是 P003 / P004 / P005 / P008 / P009 / P010 已建立的機制。逐一確認 P013 的修改不會讓既有設計失效：

7. `proposals/PROPOSAL-003-*.md`（rules.md / behavior.md source-of-truth）
8. `proposals/PROPOSAL-004-*.md`（lightweight-spec 設計理念）
9. `proposals/PROPOSAL-008-*.md`（drift-verification 機制）
10. `proposals/PROPOSAL-009-feature-directory-and-phases.md`（特別關注 § H 「Lightweight Changes」段是否與 P013 § 7 衝突；§ D `_index.md` 設計是否被 P013 §影響範圍 列出的「修改 / 檢查」破壞）
11. `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md`（特別關注 mandatory baseline 清單與 P013 § 5 是否一致；scaffolding 範圍邊界與 P013 § 1 的「templates / scaffolding 二分」是否一致）

不需要全讀每份 proposal——focus 在被 P013 觸碰到的段落即可。

### D. 現況對照檔（必讀，檢查 P013 影響範圍的準確性）

對照 P013 §影響範圍表，逐檔抽樣確認「該檔目前狀態 vs P013 計畫的修改」：

**現有 templates**（P013 標為「修改 / 檢查」）：

12. `sdd-ddd-webforms-skill/templates/_index.md`
13. `sdd-ddd-core-skill/templates/_index.md`
14. `sdd-ddd-webforms-skill/templates/phase-spec.md`
15. `sdd-ddd-core-skill/templates/phase-spec.md`
16. `sdd-ddd-webforms-skill/templates/lightweight-spec.md`
17. `sdd-ddd-core-skill/templates/lightweight-spec.md`
18. `sdd-ddd-webforms-skill/templates/behavior.md`
19. `sdd-ddd-core-skill/templates/behavior.md`
20. `sdd-ddd-webforms-skill/templates/context-definition.md`
21. `sdd-ddd-core-skill/templates/context-definition.md`
22. `sdd-ddd-core-skill/templates/aggregate-design.md`（Core only）

**現有 scaffolding**（P013 標為「修改 / 檢查」）：

23. `sdd-ddd-webforms-skill/scaffolding/_overview.md`
24. `sdd-ddd-core-skill/scaffolding/_overview.md`
25. `sdd-ddd-webforms-skill/scaffolding/_conventions.md`
26. `sdd-ddd-core-skill/scaffolding/_conventions.md`
27. `sdd-ddd-webforms-skill/scaffolding/Git-principles-gitflow.md`
28. `sdd-ddd-core-skill/scaffolding/Git-principles-gitflow.md`
29. `sdd-ddd-webforms-skill/scaffolding/Git-principles-trunk.md`
30. `sdd-ddd-core-skill/scaffolding/Git-principles-trunk.md`
31. `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md`
32. `sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md`

**現有 references**（P013 計畫修改）：

33. `sdd-ddd-webforms-skill/references/init-project-flow.md`
34. `sdd-ddd-core-skill/references/init-project-flow.md`
35. `sdd-ddd-webforms-skill/references/new-feature-flow.md`
36. `sdd-ddd-core-skill/references/new-feature-flow.md`
37. `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
38. `sdd-ddd-core-skill/references/modify-existing-flow.md`
39. `sdd-ddd-webforms-skill/references/finish-feature-flow.md`
40. `sdd-ddd-core-skill/references/finish-feature-flow.md`
41. `sdd-ddd-webforms-skill/references/drift-verification.md`
42. `sdd-ddd-core-skill/references/drift-verification.md`

**SKILL.md**：

43. `sdd-ddd-webforms-skill/SKILL.md`
44. `sdd-ddd-core-skill/SKILL.md`

不需要每檔都精讀全文——對 templates 取樣（每個獨立類型的範本讀一份），對 references 確認 P013 §6 描述的修改點存在，對 SKILL.md 確認 Templates & Scaffolding 段落結構即可。

### E. 不要讀的東西

- 任何繁中版檔（`sdd-ddd-*-skill-tw/`）— 繁中版同步延到 Closeout C1；本輪**不做**英文 vs 繁中對照
- `tutorial/` 目錄 — 屬 Closeout C2 範圍
- R1-R7 的歷史 review 報告（`reviews/round-1` 到 `reviews/round-7-*`）— 歷史檔，凍結不動
- `E:\工作區\SDD-Starter-Kit\`（V2）— P013 不依賴 V2 範本；`scaffolding/` 已從 V2 蒸餾過了
- `reviews/round-8a-report.md`（如已存在）— R8a 是平行 wave，本輪不依賴其結論

## 第二步：審查維度（7 個 lens）

P013 範圍大、引入新概念多，採完整 7 個維度審查：

### Dimension 1 — 問題描述具體性與必要性

- 6 個問題（格式漂移 / 流程描述與實作來源斷裂 / rules ↔ behavior 樣板缺一半 / 兩版缺對照契約 / T2 缺 task checklist / 結構語言混用降低 AI 定位穩定性）每個是否都有具體現況證據？
- 「Any persistent `.md` file that Dflow creates, initializes, or requires AI to maintain should have a corresponding source template」此原則的範圍是否清楚？是否有邊界灰色地帶？

### Dimension 2 — 解法的具體性與內部一致性

- §1 樣板覆蓋原則 / canonical English language 原則 / templates / scaffolding 二分 / section anchors / TEMPLATE-LANGUAGE-GLOSSARY 五項是否互相支持、無矛盾？
- §2 / §3 新增 templates 清單與「主要章節 / 欄位」是否具體到能直接寫範本（無模糊「TBD」、「之後再定」）？
- §4 TEMPLATE-COVERAGE.md 的矩陣欄位設計是否能涵蓋兩版差異？「Parity requirement」「Allowed differences」兩欄的判準是否可操作？
- §6 references 修改清單是否能追溯到 §2 / §3 新增的 templates？（避免「補了 template 但 references 沒指向」）

### Dimension 3 — 影響範圍完整性（重點）

對照第一步 §D 的現況檔，檢查：

**D3-a · 已列檔的修改範圍是否合理**：P013 §影響範圍表標「修改 / 檢查」的檔，實際打開來看，修改幅度是「換幾個標題」還是「整體重構」？P013 描述是否誠實反映實際工作量？

**D3-b · 未列檔的遺漏**：
- `sdd-ddd-*-skill/references/pr-review-checklist.md` 是否會因樣板新增 / heading 英文化而需要更新？
- `sdd-ddd-*-skill/references/git-integration.md` 是否會因 `Change Scope` 等術語英文化而受影響？
- `sdd-ddd-core-skill/references/ddd-modeling-guide.md` 是否會因為 Core 新增 events.md / context-map.md 範本而需要更新？
- `sdd-ddd-*-skill/templates/CLAUDE.md` 為什麼**沒**列入修改？（P013 改動 scaffolding/CLAUDE-md-snippet.md，但 templates/CLAUDE.md 也存在；兩者是否該一致處理？）
- 其他可能漏列的檔

**D3-c · 「不納入影響範圍」表的合理性**：每一項延後理由是否充分？特別檢查「`templates/adr.md` 不新增」是否會與 §3 新增 `architecture-decisions-README.md` 形成「有目錄導引但無 ADR 範本」的尷尬？

**D3-d · TEMPLATE-COVERAGE.md 矩陣的逐列檢查**：每一列的「WebForms template」與「Core template」欄是否對應 §2 / §3 實際提案新增的 template？是否有矩陣列出的 template 在 §2 / §3 沒新增？反之亦然？

### Dimension 4 — 邊界守住（與 P012 / backlog / 未來工作的職責切分）

**D4-a · P012 依賴**：P013 § 影響範圍是否一致使用 post-P012 路徑（`specs/shared/`）？是否有殘留 `specs/_共用/` 引用？

**D4-b · backlog § 1 內容語言策略**：P013 § 1 提出「canonical English structural language」，明確區分「結構性術語英文 vs 內容仍可中文」。這個區分是否清楚？是否會被誤解為踩進 backlog § 1（內容語言策略）的範圍？P013 規範與 backlog § 1 的職責分工是否說明充分？

**D4-c · backlog § 4 Template Language Glossary**：P013 § 1.1 新增 TEMPLATE-LANGUAGE-GLOSSARY.md，但 backlog § 4 也提及「維護一份 template 英文術語對照表」。兩者是否重複？P013 將其前移處理是否合理（vs 留到 npm 前）？

**D4-d · 對 P011 git-integration.md / scaffolding/Git-principles-*.md 的影響**：P013 § 影響範圍 標 Git-principles-{gitflow,trunk}.md 為「修改 / 檢查」（結構性中文詞彙改英文）。這是否會讓 P011 剛建立的 Git Flow Decoupling 內容受影響？是否有條目實際屬於 backlog 的 § 2「npm / CLI 散佈策略」？

### Dimension 5 — 與既有 P001-P012 機制的相容性（重點）

**C-01 · P003 rules.md / behavior.md source-of-truth**：P013 §1 引入 `<!-- dflow:section business-rules -->` anchor。這個 anchor 是否會改變 P003 已建立的 BR-ID 結構？或只是在不破壞既有結構的前提下新增定位輔助？

**C-02 · P004 lightweight-spec 設計理念**：P004 當初設計 lightweight-spec 為「輕量、不要太多 ceremony」。P013 § 7 新增 Implementation Tasks 段，附帶「若 task list 看起來超出 short fix checklist，AI 應詢問開發者是否升級為 T1」的 guard。這個 guard 是否足夠避免 lightweight-spec 變得不再 lightweight？

**C-03 · P008 drift-verification**：P008 掃描 rules.md ↔ behavior.md 的 BR-ID 對齊。P013 §1 新增的 anchor 機制是否需要 drift-verification 對應更新？P013 §6 提到「rules.md 缺失時指向 templates/rules.md」，但沒明確說 anchor 機制是否影響 drift-verification 演算法。

**C-04 · P009 § H Lightweight Changes vs P013 § 7 Implementation Tasks**：
- P009 § H 在 `_index.md` 加「Lightweight Changes」段（feature 層級的 T2/T3 修改紀錄）
- P013 § 7 在 `lightweight-spec.md` 加「Implementation Tasks」段（單一 lightweight-spec 內的任務清單）
- 兩者是不同層級的記錄，但 reviewer 需確認：T2 工作流程下，這兩段如何協作？是否會造成「同樣的內容寫兩次」？

**C-05 · P009 _index.md 設計**：P013 §影響範圍 標 `templates/_index.md` 為「修改 / 檢查」（將結構標題、欄位、placeholder 統一為 canonical English）。檢查 P009 § D 定義的 6 個 _index.md sections 是否會被英文化破壞識別性？特別是「Current BR Snapshot」「Lightweight Changes」「接續入口」等已有中文名的 section。

**C-06 · P010 mandatory baseline 清單**：P010 `/dflow:init-project` 已定義 mandatory baseline 清單（兩版各自）。P013 § 5 重列 baseline 並加上「← templates/{xxx}.md」來源指引。比對 P013 與 P010 的 baseline 清單是否完全一致？P013 是否新增 / 移除任何 baseline 項目（這應該是 P013 的 scope 嗎）？

**C-07 · P010 scaffolding 定位**：P010 將 scaffolding 定位為「專案治理文件，init 寫入後由專案持有」。P013 § 1 將 glossary.md 從 scaffolding 重新分類到 templates（「living document」），這個重分類是否會破壞 P010 的 scaffolding 邊界？

**C-08 · P012 路徑決策**：見 Dimension 4 D4-a。

### Dimension 6 — 設計決策的合理性（新概念引入）

P013 引入的 4 項新概念，逐項評估：

**D6-a · Canonical English structural language**：
- 此原則的長期維護成本（每次新增 template / heading 都要先查 glossary）vs 好處（AI 定位穩定性、降低跨工具誤判）的 trade-off 是否值得？
- 「結構性術語 vs 使用者可填寫內容」的區分判準是否可操作？P013 § 1 第 6 點寫「判斷標準以『會被 AI 或開發者當作固定結構 / 欄位 / 狀態 / 類型理解的詞』為主」——此判準在實作時是否會出現大量灰色地帶？
- 此決策是否需要設計者額外確認（vs 直接 reviewer 通過）？若需要，標 Question。

**D6-b · `<!-- dflow:section X -->` anchor 機制**：
- 引入 HTML comment anchor 的必要性是否充分？AI 已能用 heading 定位段落；新增 anchor 的邊際好處是否足以抵銷維護成本？
- P013 § 1 列的 6 個初始 anchor 是否涵蓋實際高頻需求？是否會因為 anchor 不足而造成「有些重要段落用 heading 定位、有些用 anchor 定位」的不一致？
- anchor 命名規範（`dflow:section {section-id}`）是否需要 namespacing / versioning 考慮？

**D6-c · TEMPLATE-COVERAGE.md 維護負擔**：
- 新增一份維護矩陣，每次新增 / 修改 template 都要同步更新 matrix。這個維護負擔是否值得？
- 矩陣放在 repo root vs 放在 `proposals/` / `references/` 的位置決策是否合理？

**D6-d · TEMPLATE-LANGUAGE-GLOSSARY.md 重複性**：
- backlog § 4 已提及維護 glossary，P013 § 1.1 前移處理。位置（repo root vs 其他）的選擇是否說明清楚？
- glossary 與 TEMPLATE-COVERAGE.md 是否該合併為一份維護文件？

### Dimension 7 — 工作量評估與切分建議

- P013 預估工作量「中高」是否合理？對照 ~40 檔影響、12 個新 template、所有現有 template 與 scaffolding 英文化，這個級別是否低估？
- 是否建議切 sub-waves（例如 Wave 1 新增 templates / Wave 2 既有 templates 英文化 / Wave 3 references 更新 / Wave 4 新增 COVERAGE / GLOSSARY 維護文件）？若建議，wave 之間的依賴順序如何？
- Implement Review 階段的工作量是否需要對應放大？

## 第三步：Finding 格式

每個 Finding 採以下格式寫入報告：

```markdown
### F-NN {一句話問題摘要}

**嚴重度**: Critical | Major | Minor | Question
**對照**: {擇一或混合標示}
  - Proposal 內部：`PROPOSAL-013 § 段落名`
  - 現況對照：`{檔案路徑}:{行號}`
  - 邊界對照：`planning/public-distribution-backlog.md § N` 或 `PROPOSAL-012 § N`
  - 既有機制：`PROPOSAL-00X § 段落名 · C-NN`（對應 Dimension 5 的檢查點編號）
**維度**: Dimension 1 / 2 / 3 / 4 / 5 / 6 / 7（單選）
**現況**: {實際讀到什麼}
**問題**: {為什麼是問題 —— 遺漏 / 矛盾 / 不可行 / 假設薄弱 / 風險未覆蓋 / 邊界越界 / 既有機制衝突}
**建議**: {具體怎麼改 P013；若 Question 類型則寫「需釐清：...」}
```

### 嚴重度定義（沿用 R7 / R8a 慣例）

- **Critical**：proposal 設計根本缺陷，會導致**無法實施**或**破壞既有 P001-P012 機制**。例如：解法自相矛盾、依賴不存在的前提、實施後讓 drift-verification / 既有 _index.md 結構失效。**嚴格使用，不濫用**。
- **Major**：內部邏輯矛盾、影響範圍漏列關鍵檔案、邊界明顯越界、既有機制相容性破壞、關鍵假設無依據。實施前必須修正。
- **Minor**：措辭不夠精確、列表遺漏次要項、標題不一致、可讀性問題。不影響功能。
- **Question**：Reviewer 不確定 proposal 意圖，需設計者釐清後才能判斷。**若無法確認是否為 Critical，優先標 Question，不要擅自升為 Critical**。

## 第四步：產出格式

寫入 `reviews/round-8b-report.md`，結構如下：

```markdown
# Round 8b Review Report — Draft Proposal Review (PROPOSAL-013)

**審查範圍**: PROPOSAL-013-system-document-template-coverage（draft）
**審查類型**: Draft proposal review（無實作對照；單一 proposal、focus 在影響範圍 / 設計決策 / 既有機制相容性）
**審查日期**: YYYY-MM-DD
**審查者**: {Cursor 模型名稱}

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| {總數} | {數} | {數} | {數} | {數} |

**建議下一步**: approved / revise-and-resubmit / needs-clarification

> 「建議下一步」是 Reviewer 的初判；最終 draft → approved 決定由 R8b-Approve session 做，本輪不定。

## Findings

### F-01 ...

（按嚴重度排序：Critical → Major → Minor → Question；同嚴重度內按 Dimension → 段落順序）

## 既有機制相容性對照表（Dimension 5）

| 檢查點 | P013 段落 | 既有機制段落 | 一致？ | 對應 Finding |
|---|---|---|---|---|
| C-01 · rules / behavior anchor | P013 §1 | PROPOSAL-003 | ✓ / ✗ / ⚠ | F-NN |
| C-02 · lightweight task guard | P013 §7 | PROPOSAL-004 | | |
| C-03 · drift-verification anchor | P013 §1, §6 | PROPOSAL-008 | | |
| C-04 · § H Lightweight Changes vs § 7 Implementation Tasks | P013 §7 | PROPOSAL-009 § H | | |
| C-05 · _index.md 英文化 | P013 §影響範圍 | PROPOSAL-009 § D | | |
| C-06 · mandatory baseline 一致性 | P013 §5 | PROPOSAL-010 §init-project | | |
| C-07 · scaffolding 邊界 | P013 §1 | PROPOSAL-010 scaffolding 定位 | | |
| C-08 · 路徑使用 post-P012 | P013 §影響範圍 | PROPOSAL-012 §2 | | |

> ⚠ = 部分一致 / 存在歧義

## 設計決策評估表（Dimension 6）

| 決策 | 設計者主張 | Reviewer 評估 | 建議 |
|---|---|---|---|
| Canonical English structural language | P013 §1 第 6 點判準 | ✓ 合理 / ⚠ 灰色地帶大 / ✗ 需重新設計 | {具體建議} |
| `<!-- dflow:section X -->` anchor | P013 §1 「Important Dflow-updated sections」段 | | |
| TEMPLATE-COVERAGE.md | P013 §4 | | |
| TEMPLATE-LANGUAGE-GLOSSARY.md | P013 §1.1 | | |

## 影響範圍對照表

| 檔案 | P013 列入？ | 修改類型 | 實際工作量評估 | 一致？ | 對應 Finding |
|---|---|---|---|---|---|
| {新增 templates 列表} | ✓ | 新增 | 中 / 大 | ✓ | |
| {既有 templates 修改 / 檢查} | ✓ | 修改 | 小 / 中 / 大 | | |
| {scaffolding 修改 / 檢查} | ✓ | 修改 | 小 / 中 / 大 | | |
| {references 修改} | ✓ | 修改 | 小 / 中 / 大 | | |
| {可能漏列檔} | ✗ | — | — | ✗ | F-NN |

> 表格可分組或精簡；重點是讓 reviewer 對「是否完整、是否準確」表態。

## 邊界對照備註

| 邊界檢查 | P013 描述位置 | 對方檔說法 | 一致？ | 對應 Finding |
|---|---|---|---|---|
| 內容語言策略延到 backlog § 1 | P013 §1 第 4 點、§不納入影響範圍 | `planning/public-distribution-backlog.md § 1` | ✓ / ✗ / ⚠ | |
| Template Language Glossary 前移處理 | P013 §1.1 | `planning/public-distribution-backlog.md § 4` | | |
| 路徑使用 post-P012 | P013 §影響範圍 | `PROPOSAL-012 §2` | | |
| 雙語 template set 不建立 | P013 §不納入影響範圍 | `planning/public-distribution-backlog.md` 暫不處理段 | | |
| ADR template 暫不新增 | P013 §3 末段 | （無對方檔；reviewer 自行判斷合理性） | | |

## 工作量評估與切分建議（Dimension 7）

- P013 預估「中高」評價：✓ 合理 / ⚠ 略低估 / ✗ 嚴重低估
- 建議是否切 sub-waves：是 / 否
- 若建議切 sub-waves：
  - Wave 1: ...
  - Wave 2: ...
  - ...

## 審查備註

- （任何需要補充的觀察、審查流程中的限制等）

---

**跨輪議題**：若審查過程順手看到其他 proposal（P001-P012）或其他議題的問題，記入 `reviews/cross-round-notes.md`，**不佔本輪 Finding 數量**。
```

## 第五步：重要規則

- **不要修改任何檔案**。包括 proposal 檔、skill 檔、planning 檔、review-workflow.md。只產出 `reviews/round-8b-report.md`。
- **不要 review PROPOSAL-012**。P012 是平行 wave 的 target；本輪只需確認 P013 對 P012 的依賴宣稱準確，不評論 P012 本身品質。
- **不要比對英文 skill vs 繁中 skill**。繁中延到 Closeout C1；本輪**不做**中英對照。
- **取樣現況檔即可**。第一步 §D 列了 ~30 個現況檔，不需要每個精讀全文。Templates 取樣 1-2 份理解結構，scaffolding 同理；references 確認 P013 §6 描述的修改點存在即可。
- **既有機制相容性是本輪重點之一**。Dimension 5 的 8 個檢查點（C-01 到 C-08）每一項都要在對照表給出 ✓ / ✗ / ⚠ 判斷 + 理由（若 ✗ 或 ⚠，對應 Finding）。
- **設計決策若有疑慮，優先標 Question**。Dimension 6 的 4 項新概念（canonical English / anchors / COVERAGE / GLOSSARY）若 reviewer 認為「方向不對但不確定」，標 Question 讓設計者釐清，不要擅自升為 Critical / Major。
- **嚴重度從嚴**：Critical 保留給「無法實施 / 破壞既有 P001-P012 機制」級別。
- **預估工作時間**：1 份 proposal 17 KB + context 80 KB，預期審查需 90-150 分鐘。若超過 2.5 小時仍無法完成，可分 2 次進行（第一次聚焦 Dimension 1-4，第二次聚焦 Dimension 5-7）。

開始審查。完成後只產出 `reviews/round-8b-report.md`。

---END PROMPT---
