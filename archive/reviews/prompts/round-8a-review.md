# Round 8a Review Prompt — PROPOSAL-012（Draft Review，給 Cursor 使用）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Cursor。
> **產出位置**：Reviewer 會寫入 `reviews/round-8a-report.md`。
> **禁止事項**：本輪不得修改任何檔（包含 proposal、skill 檔、planning 檔皆不動），只產出 review 報告。
> **R8a 與 R7 的差異**：R7 一次審 3 份相互依賴的 draft proposal（交叉一致性是核心）；R8a 只審 PROPOSAL-012 一份，scope 小（路徑英文化），沒有交叉一致性 dimension。重點放在「影響範圍是否完整」與「邊界是否守住」。
> **R8a 與 R8b 的關係**：R8a 處理 PROPOSAL-012（路徑英文化，前置）；R8b 將另案處理 PROPOSAL-013（樣板覆蓋，依賴 P012 路徑）。R8a 不審 P013，但需確認 P012 不踩進 P013 的範圍。
> **範圍規模**：1 份 draft proposal 約 11 KB，加 context 與現況檔約 30 KB；Finding 預估 2-6 項。
> **模型選擇**：使用者於 Cursor 自選；任何 frontier 模型皆適用，本 prompt 不設模型偏好。

---BEGIN PROMPT---

你是 Dflow Round 8a Review 的審查者。本輪範圍為 1 份 draft proposal（PROPOSAL-012：目錄與檔名英文化的社群散佈預備調整）的設計品質與實施前準確性，**不審任何實作檔**（本輪還沒實施）。

## 第一步：讀入以下檔案

### A. 審查標的（必讀，逐段細讀）

1. `proposals/PROPOSAL-012-distribution-friendly-paths.md`

### B. 邊界與脈絡檔（必讀，理解 P012 的職責邊界）

2. `planning/public-distribution-backlog.md` — npm 前才處理的議題集中地；P012 多次引用此檔劃出 out-of-scope 邊界，需確認引用準確
3. `planning/project-review-brief.md` — 系統文件結構觀（裡面已用前瞻性寫法提到 `specs/_共用/` → `specs/shared/`）
4. `proposals/PROPOSAL-013-system-document-template-coverage.md` — 後續 wave 將處理；只需理解其範圍以避免 P012 越界，**不審 P013 本身**
5. `reviews/cross-round-notes.md` — 議題 3 是 P012 的源頭背景；不需要特別比對，但讀過有助理解 P012 的設計動機
6. `proposals/review-workflow.md` — R1-R6 流程規範骨幹；R8a 沿用其嚴重度分類、檔名結構、三階段分工，但審查維度因本輪審 single draft 而簡化（本提示詞會覆蓋）

### C. 現況對照檔（必讀，檢查 P012 影響範圍的準確性）

對照 P012 §影響範圍表，逐檔確認「該檔目前是否真的有 `_共用` 引用、是否該被列入」：

7. `sdd-ddd-webforms-skill/SKILL.md`
8. `sdd-ddd-core-skill/SKILL.md`
9. `sdd-ddd-webforms-skill/references/init-project-flow.md`
10. `sdd-ddd-core-skill/references/init-project-flow.md`
11. `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md`
12. `sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md`
13. `sdd-ddd-webforms-skill/templates/CLAUDE.md`
14. `sdd-ddd-core-skill/templates/CLAUDE.md`
15. `README.md`
16. `CHANGELOG.md`（不需要全讀，理解 P012 將新增 changelog 段即可）

不需要全讀每個檔——逐檔搜尋 `_共用` 字串、定位出現位置即可。

### D. 不要讀的東西

- 任何繁中版檔（`sdd-ddd-*-skill-tw/`）— 繁中版同步延到 Closeout C1；本輪**不做**英文 vs 繁中對照
- `tutorial/` 目錄 — 屬 Closeout C2 範圍，與 P012 無關
- R1-R7 的歷史 review 報告（`reviews/round-1` 到 `reviews/round-7-*`）— 歷史檔，凍結不動
- `E:\工作區\SDD-Starter-Kit\`（V2）— P012 不依賴 V2 設計，不需要 V2 背景
- PROPOSAL-001 ~ PROPOSAL-011 內容 — 已 implemented；除非 P012 的「關聯的 Proposal」段宣稱依賴某項，否則不需閱讀

## 第二步：審查維度（5 個 lens）

R8a 簡化版審查（單一 proposal、無交叉一致性 dimension）：

### Dimension 1 — 問題描述具體性與必要性

- 問題描述是否具體（為什麼現狀有問題？誰受影響？影響有多大？）
- P012 提出的「machine-facing paths default to English」原則是否有充分依據（不只是審美偏好）
- 是否區分清楚「現在要做的」（路徑改名）與「未來要做的」（內容語言、npm package 等延到 backlog）

### Dimension 2 — 解法的具體性與內部一致性

- 提議的解法是否落到檔案層級的動作（無懸置 TBD）
- 各分節（§1 政策定義 / §2 路徑改名 / §3 init-project-flow 更新 / §4 skill 與 scaffolding 引用 / §5 out-of-scope / §6 與 P009 slug 規則相容）之間是否一致無矛盾
- 「`shared/` 而非 `_shared/`」的選擇理由是否充分（與既有 `features/`、`domain/` 等英文目錄風格一致）
- §6 對 P009 slug 規則的相容說明是否準確（P009 決議 8：中文 slug 跟隨討論語言）

### Dimension 3 — 影響範圍完整性（本輪重點）

對照第一步 §C 的現況檔，檢查：

**D3-a · 已列檔的準確性**：P012 §影響範圍表列的每個檔，是否真的有 `_共用` 引用？修改描述是否準確？

**D3-b · 未列檔的遺漏**：對 §C 全部現況檔執行 `_共用` 搜尋，是否有檔案出現 `_共用` 引用但**未**被 P012 §影響範圍列入？特別注意：

- `planning/project-review-brief.md` — 已知有 3 處 `_共用` 引用（line 51, 59, 111），寫法是前瞻性的「P012 後預期改名」。P012 落地後此寫法會過時。是否該納入影響範圍？或該如何處理？
- 其他可能漏列的檔（自行 grep `_共用` 確認）

**D3-c · 「不納入影響範圍」表的合理性**：P012 §不納入影響範圍 列出的項目（繁中版、tutorial、template 中文 heading、npm、根目錄命名），每一項的延後理由是否充分？

**D3-d · `templates/CLAUDE.md` 列入的必要性**：P012 將兩版 `templates/CLAUDE.md` 列入「修改」，但這兩個檔目前可能不含 `_共用` 引用。檢查現況，判斷該列為「修改」、「檢查」（無變動但需確認）、還是該移出影響範圍。

### Dimension 4 — 邊界守住（與 P013 / backlog 的職責切分）

- P012 是否完整守住「只改 machine-facing paths，不改文件內容語言」的邊界？是否有任何條目偷渡了內容語言策略？
- P012 §不納入影響範圍 對 P013 領域（樣板覆蓋、canonical English headings、template language glossary）的劃線是否清楚？
- P012 對 `planning/public-distribution-backlog.md` 的引用是否準確（確實有對應條目）？
- P012 是否有任何條目應該屬於 backlog 但被誤納入 P012？反之亦然？

### Dimension 5 — 風險與假設

- 「不向後相容讀舊 `_共用/` 路徑」（已 out of scope，延到 backlog § 5）的風險評估是否合理？對既有 Obts / 公司內部專案的影響說明是否足夠？
- 「P012 不影響 `templates/_index.md`、`templates/phase-spec.md` 等的中文 heading」（明示 out of scope）是否會在實務上產生「路徑英文化但相同層級的 template heading 仍中文」的不一致觀感？是否需要在 P012 中至少加一句說明？
- 預估工作量「小」是否合理？

## 第三步：Finding 格式

每個 Finding 採以下格式寫入報告：

```markdown
### F-NN {一句話問題摘要}

**嚴重度**: Critical | Major | Minor | Question
**對照**: {擇一或混合標示}
  - Proposal 內部：`PROPOSAL-012 § 段落名`
  - 現況對照：`{檔案路徑}:{行號}`
  - 邊界對照：`planning/public-distribution-backlog.md § N` 或 `PROPOSAL-013 § N`
**維度**: Dimension 1 / 2 / 3 / 4 / 5（單選）
**現況**: {實際讀到什麼}
**問題**: {為什麼是問題 —— 遺漏 / 矛盾 / 不可行 / 假設薄弱 / 風險未覆蓋 / 邊界越界}
**建議**: {具體怎麼改 P012；若 Question 類型則寫「需釐清：...」}
```

### 嚴重度定義（沿用 R7 慣例）

- **Critical**：proposal 設計根本缺陷，會導致**無法實施**或**破壞既有 skill 結構**。例如：解法自相矛盾、依賴不存在的前提、實施後會讓既有機制失效。**嚴格使用，不濫用**。
- **Major**：內部邏輯矛盾、影響範圍漏列關鍵檔案、邊界明顯越界、關鍵假設無依據。實施前必須修正。
- **Minor**：措辭不夠精確、列表遺漏次要項、標題不一致、可讀性問題。不影響功能。
- **Question**：Reviewer 不確定 proposal 意圖，需設計者釐清後才能判斷。**若無法確認是否為 Critical，優先標 Question，不要擅自升為 Critical**。

## 第四步：產出格式

寫入 `reviews/round-8a-report.md`，結構如下：

```markdown
# Round 8a Review Report — Draft Proposal Review (PROPOSAL-012)

**審查範圍**: PROPOSAL-012-distribution-friendly-paths（draft）
**審查類型**: Draft proposal review（無實作對照；單一 proposal、focus 在影響範圍與邊界）
**審查日期**: YYYY-MM-DD
**審查者**: {Cursor 模型名稱}

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| {總數} | {數} | {數} | {數} | {數} |

**建議下一步**: approved / revise-and-resubmit / needs-clarification

> 「建議下一步」是 Reviewer 的初判；最終 draft → approved 決定由 R8a-Approve session 做，本輪不定。

## Findings

### F-01 ...

（按嚴重度排序：Critical → Major → Minor → Question；同嚴重度內按 Dimension → 段落順序）

## 影響範圍對照表

對 P012 §影響範圍 列的每個檔執行 `_共用` 字串檢查，回填表格：

| 檔案 | P012 列入？ | 現況 `_共用` 出現次數 | 實際出現位置（行號） | 一致？ | 對應 Finding |
|---|---|---|---|---|---|
| `sdd-ddd-webforms-skill/SKILL.md` | ✓ | N | line N, N, ... | ✓ / ✗ / ⚠ | F-NN |
| `sdd-ddd-core-skill/SKILL.md` | ✓ | N | line N, N, ... | | |
| `sdd-ddd-webforms-skill/references/init-project-flow.md` | ✓ | N | line N, N, ... | | |
| `sdd-ddd-core-skill/references/init-project-flow.md` | ✓ | N | line N, N, ... | | |
| `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md` | ✓ | N | line N, N, ... | | |
| `sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md` | ✓ | N | line N, N, ... | | |
| `sdd-ddd-webforms-skill/templates/CLAUDE.md` | ✓ | N | line N, N, ... | | |
| `sdd-ddd-core-skill/templates/CLAUDE.md` | ✓ | N | line N, N, ... | | |
| `README.md` | ✓（"可能只需確認"） | N | line N, N, ... | | |
| `planning/project-review-brief.md` | ✗ | 3 | line 51, 59, 111 | ⚠ | F-NN |
| {其他經 grep 發現的檔} | ✗ | N | line N, ... | ✗ | F-NN |

> ✓ = 一致；✗ = 應列入但未列；⚠ = 列入但描述需修正 / 列為「修改」但實際只需「檢查」

## 邊界對照備註

| 邊界檢查 | P012 描述位置 | 對方檔說法 | 一致？ | 對應 Finding |
|---|---|---|---|---|
| `_共用` 內容語言策略延到 backlog | P012 §5、§不納入影響範圍 | `planning/public-distribution-backlog.md § 1, § 4` | ✓ / ✗ / ⚠ | |
| 根目錄命名（`specs/` vs `dflow/`）延後 | P012 §不納入影響範圍 | `planning/public-distribution-backlog.md § 6` | | |
| 樣板覆蓋與 canonical English 由 P013 處理 | P012 §關聯的 Proposal | `PROPOSAL-013 § 提議的解法` | | |
| Slug 跟隨討論語言（P009 決議 8）保留 | P012 §6 | `PROPOSAL-009 § H 決議 8`（已 implemented） | | |

## 審查備註

- （任何需要補充的觀察、審查流程中的限制等）

---

**跨輪議題**：若審查過程順手看到其他 proposal（P001-P011）或其他議題的問題，記入 `reviews/cross-round-notes.md`，**不佔本輪 Finding 數量**。
```

## 第五步：重要規則

- **不要修改任何檔案**。包括 proposal 檔、skill 檔、planning 檔、review-workflow.md。只產出 `reviews/round-8a-report.md`。
- **不要 review PROPOSAL-013**。P013 是後續 wave 的 target；本輪只需確認 P012 不踩進 P013 範圍，不評論 P013 本身品質。
- **不要比對英文 skill vs 繁中 skill**。繁中延到 Closeout C1；本輪**不做**中英對照。
- **影響範圍對照表必填**。第四步的「影響範圍對照表」每一列都要實際 grep 後填入行號，不能只回 ✓ 而不附證據。
- **嚴重度從嚴**：Critical 保留給「無法實施 / 破壞既有結構」級別；不確定就標 Question。
- **預估工作時間**：1 份 proposal 11 KB + context 30 KB，預期審查需 30-60 分鐘。

開始審查。完成後只產出 `reviews/round-8a-report.md`。

---END PROMPT---
