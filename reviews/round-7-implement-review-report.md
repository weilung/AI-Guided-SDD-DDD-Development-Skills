# Round 7 Implement Review Report — Wave 1-3 實作品質審查

**審查範圍**: PROPOSAL-011 / PROPOSAL-009 / PROPOSAL-010 的 Wave 1-3 實施產物
**審查類型**: Implement review（實施忠誠度 + 跨 wave 一致性 + P001-P008 regression）
**審查日期**: 2026-04-24
**審查者**: Codex（gpt-5.4 xhigh）

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| 4 | 1 | 2 | 1 | 0 |

## 按 Wave 分類

| Wave | Proposal | Finding 數 | 建議下一步 |
|---|---|---|---|
| Wave 1 | PROPOSAL-011 | 1 | needs-fix |
| Wave 2 | PROPOSAL-009 | 1 | needs-fix |
| Wave 3 | PROPOSAL-010 | 1 | needs-fix |
| Cross-wave | — | 1 | needs-fix |

> 「建議下一步」是 Reviewer 初判；最終修正決定由 R7 Implement Approve session 處理。

## Findings

### F-01 `/dflow:new-phase` 的 `_index.md` row 範例把 phase-spec 檔名寫成 feature slug，會產生失配連結

**嚴重度**: Critical
**對照**:
  - Wave：W2
  - Proposal：`PROPOSAL-009 § C 決議 9 / § H 決議 12-14`
  - Decision：`round-7-decisions.md F-01`（accept-with-choice Path A-3）
  - 實施產物：`sdd-ddd-webforms-skill/references/new-phase-flow.md:94-160`、`sdd-ddd-core-skill/references/new-phase-flow.md:101-171`
**維度**: Dimension 3
**現況**: 兩版 `new-phase-flow.md` 在 Step 3 / Step 4 一致定義新檔名為 `phase-spec-{date}-{phase-slug}.md`，但 Step 5 的 `_index.md` Phase Specs row 範例卻寫成 `[phase-spec-{date}-{slug}.md](./phase-spec-{date}-{slug}.md)`（WebForms `:160`；Core `:171`）。
**問題**: 若 AI 依文件逐字執行，會先建立 `phase-spec-{date}-{phase-slug}.md`，再把 `_index.md` 指到另一個不存在的檔名。這直接破壞 P009 Path A-3 所要求的 phase-spec 命名一致性，也會讓後續 `finish-feature-flow` / `pr-review-checklist` 依 `_index.md` 驗證 phase-spec existence 時出現假性缺檔，屬於 flow 可運作性缺陷。
**建議**: 兩版 Step 5 row 範例都改為 `[phase-spec-{date}-{phase-slug}.md](./phase-spec-{date}-{phase-slug}.md)`；同步掃描同檔其餘 row / path 範例，確保 phase 2+ 一律使用 `phase-slug`。

### F-02 Wave 1 的 `git-flow-integration.md` → `git-integration.md` 重命名沒有保留 Git rename 歷史

**嚴重度**: Major
**對照**:
  - Wave：W1
  - Proposal：`PROPOSAL-011 § 1 檔名重命名 + 重構`、`PROPOSAL-011 § 影響範圍`
  - Decision：—
  - 實施產物：`sdd-ddd-webforms-skill/references/git-integration.md:1-11`、`sdd-ddd-core-skill/references/git-integration.md:1-11`、`CHANGELOG.md:84-90`
**維度**: Dimension 1
**現況**: 新檔存在、舊檔不存在，表面上完成了 rename；但 `git diff-tree -M --summary bf5bb85` 對雙版都顯示 `delete mode .../git-flow-integration.md` + `create mode .../git-integration.md`，不是 rename。`git log --follow --oneline -- .../git-integration.md` 只回到 `bf5bb85`，沒有接回舊檔更早歷史；相對地，舊路徑仍各自保留前史。`CHANGELOG.md:89` 仍自述為「`git mv`」。
**問題**: P011 把這次 rename 明確定義為「首次實踐 `git mv` 原則」；現在 Git 視角實際是 delete/add，代表 rename trace 沒被保留。這不只違背 proposal 明列驗收項，也讓 `CHANGELOG` 的 Wave 1 自述與 Git 事實不一致。
**建議**: 在進入 approve 前把 Wave 1 rename 重做成真正可追溯的 rename（必要時重寫 / 重做該 commit，使 Git 記為 rename，讓 `git log --follow` 能穿回舊歷史）；若決定不修歷史，至少要把 CHANGELOG 改成如實描述，但這仍不滿足 P011 原驗收意圖。

### F-03 WebForms `CLAUDE-md-snippet.md` 指向 `init-project-flow` 不會產生的路徑

**嚴重度**: Major
**對照**:
  - Wave：W3
  - Proposal：`PROPOSAL-010 § 2 新增 scaffolding/ 目錄`、`PROPOSAL-010 § 4 新增 references/init-project-flow.md`
  - Decision：—
  - 實施產物：`sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md:43-45`、`sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md:111-117`、`sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md:132-138`、`sdd-ddd-webforms-skill/references/init-project-flow.md:124-132`、`sdd-ddd-webforms-skill/references/init-project-flow.md:194-198`
**維度**: Dimension 1
**現況**: WebForms `init-project-flow.md` 明確把 scaffolding 寫到 `specs/_共用/_overview.md`、`specs/_共用/_conventions.md`、`specs/_共用/Git-principles-*.md`；但 WebForms `CLAUDE-md-snippet.md` 內文仍寫「完整內容放在 `specs/_overview.md`」、「見 `specs/_conventions.md`」、「見 `specs/Git-principles-{gitflow|trunk}.md`」。
**問題**: 這會讓 `/dflow:init-project` 產出的專案 `CLAUDE.md` 指向一組實際不會被建立的路徑。Core 版同類段落已正確引用 `specs/_共用/...`，因此目前是單版錯誤且無正當理由的雙版不一致。
**建議**: 把 WebForms `CLAUDE-md-snippet.md` 內所有 `_overview` / `_conventions` / `Git-principles-*` 參考路徑統一改成 `specs/_共用/...`；修正後再 grep 同檔，避免殘留 `specs/_overview.md` / `specs/Git-principles-*.md`。

### F-04 `templates/CLAUDE.md` 仍引用不存在的 `scaffolding/Git-原則-gitflow.md`

**嚴重度**: Minor
**對照**:
  - Wave：cross-wave
  - Proposal：`PROPOSAL-011 § 5 交叉影響的檔案`、`PROPOSAL-010 § 2 新增 scaffolding/ 目錄`
  - Decision：—
  - 實施產物：`sdd-ddd-webforms-skill/templates/CLAUDE.md:113-116`、`sdd-ddd-core-skill/templates/CLAUDE.md:133-136`、`sdd-ddd-webforms-skill/scaffolding/Git-principles-gitflow.md:1`、`sdd-ddd-core-skill/scaffolding/Git-principles-gitflow.md:1`
**維度**: Dimension 2
**現況**: 兩版 `templates/CLAUDE.md` 都寫「若採用 Git Flow，可參考 `scaffolding/Git-原則-gitflow.md` 範本」；Wave 3 實際新增的檔名是 `scaffolding/Git-principles-gitflow.md`。
**問題**: Wave 1 / 2 的 Git-neutral 指引沒有隨 Wave 3 的實際 scaffolding 檔名同步，導致模板中的參考路徑無法落到真實檔案。這不影響核心 flow，但屬於明顯的 cross-wave 文檔漂移。
**建議**: 將兩版 `templates/CLAUDE.md` 的檔名統一改為 `scaffolding/Git-principles-gitflow.md`；若要保留中文敘述，應只翻中文說明，不翻檔名 token。

## 跨 Wave 一致性對照表

| 檢查點 | 主張方 | 回應方 | 一致？ | 對應 Finding |
|---|---|---|---|---|
| CW-01 · 011 placeholder ↔ 009 填齊 | W1 `git-integration.md` § branch naming / `git mv` 總則 | W2 `git-integration.md` Slug Language + `Directory Moves Must Use git mv` | ✓ | — |
| CW-02 · 009 `_index.md` ↔ 010 init-flow 引用 | W2 `templates/_index.md` | W3 `init-project-flow.md` Step 4 | ✓ | — |
| CW-03 · 009 Integration Summary ↔ 010 Git-principles 格式段 | W2 `finish-feature-flow.md` | W3 `Git-principles-{gitflow,trunk}.md` | ✓ | — |
| CW-04 · 009 三層 Ceremony ↔ 010 `_conventions.md` 引用 | W2 SKILL.md | W3 `_conventions.md` | ✓ | — |
| CW-05 · 011 Git Flow 解耦 ↔ 010 trunk.md 中立前提 | W1 `git-integration.md` | W3 `Git-principles-trunk.md` | ✓ | — |
| CW-06 · 009 完整 `git mv` 段 ↔ 011 總則 | W1 `git-integration.md` 總則 | W2 `git-integration.md` 擴增 | ✓ | — |
| CW-07 · Wave 1/2/3 CHANGELOG 完整性 | CHANGELOG.md | 實施檔 | ⚠ | F-02 |
| CW-08 · SKILL.md 雙版吸收三 wave 一致性 | W1/W2/W3 累積 | SKILL.md 雙版結構 | ✓ | — |

## R7 Approve Decisions 落實對照

| Finding | Path / 決議 | 落實狀態 | 對應實施 Finding |
|---|---|---|---|
| F-01 | accept-with-choice Path A-3 | ✗ | F-01 |
| F-02 | accept-with-choice Path C | ✓ | — |
| F-03 | accept | ✓ | — |
| F-04 | accept-with-choice Path B（跨 3 proposal）| ✓ | — |
| F-05 | accept | ✓ | — |
| F-06 | accept | ✓ | — |
| F-07 | accept | ✓ | — |

## P001-P008 Regression Check

逐項 verify（見 Dimension 4 清單）：

| Proposal | 核心機制 | 受 R7 三 wave 影響狀態 | 對應 Finding（若有） |
|---|---|---|---|
| P001 | /dflow: 前綴 + NL safety net | 擴充（新命令沿用同 pattern） | — |
| P002 / P004 | Delta 格式 | 擴充（phase-spec 新增 Delta 段，格式沿用 ADDED / MODIFIED / REMOVED / RENAMED / UNCHANGED） | — |
| P003 | rules.md / behavior.md / Step 8.3-5.3 sync | 擴充（`finish-feature` 明確重用既有 sync；核心機制未重寫） | — |
| P005 / 005a / 005b | Completion checklist 結構 | 擴充，但 `new-phase-flow` 的 F-01 會讓 `_index.md` Phase Specs row 可被寫成錯誤路徑，進而影響後續 closeout 驗證 | F-01 |
| P006 / 006a | Ceremony Scaling + DDD Depth | 擴充（T1/T2/T3 重寫完成；Core 的 DDD Depth 保留） | — |
| P007a | Brownfield baseline capture | 未動（僅被 Wave 3 正確引用為 `behavior.md` 的建立時機） | — |
| P007c | CLAUDE.md H2 分段 | 擴充（template / snippet 都保留 `系統脈絡` / `開發流程` 分段） | — |
| P008 | /dflow:verify 機械對照 | 擴充（僅補 path / scope 註記與 trigger；演算法未改） | — |

## 雙版一致性備註（WebForms / Core）

| 檢查項 | WebForms | Core | 一致？ |
|---|---|---|---|
| 三層 Ceremony 表 | `sdd-ddd-webforms-skill/SKILL.md:224-243` | `sdd-ddd-core-skill/SKILL.md:234-260` | ✓ |
| `_index.md` 範本 | `sdd-ddd-webforms-skill/templates/_index.md:1-116` | `sdd-ddd-core-skill/templates/_index.md:1-116` | ✓ |
| `phase-spec.md` Delta 段 | `sdd-ddd-webforms-skill/templates/phase-spec.md:78-124` | `sdd-ddd-core-skill/templates/phase-spec.md:78-124` | ✓ |
| `/dflow:new-phase` / `finish-feature` 定義 | `sdd-ddd-webforms-skill/SKILL.md:103-107` | `sdd-ddd-core-skill/SKILL.md:118-122` | ✓ |
| `/dflow:init-project` 定義 | `sdd-ddd-webforms-skill/SKILL.md:94-95` | `sdd-ddd-core-skill/SKILL.md:109-110` | ✓ |
| Scaffolding 5 檔存在 | `sdd-ddd-webforms-skill/SKILL.md:384-394` | `sdd-ddd-core-skill/SKILL.md:453-463` | ✓ |
| 刻意差異說明（Core 版 DDD Depth / 架構段 / Aggregate 等） | `sdd-ddd-webforms-skill/scaffolding/_overview.md:78-96`、`sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md:32-80` | `sdd-ddd-core-skill/SKILL.md:257-260`、`sdd-ddd-core-skill/scaffolding/_overview.md:34-52`、`sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md:38-90` | ✓ |

## 審查備註

- 本輪依指示只讀英文 skill、approved proposal、R7 decisions/report、CHANGELOG、review workflow；未讀 V2 / Obts / 繁中版。
- 未執行功能測試；本報告完全基於文件對照與本地 Git metadata（含 `git log --follow`、`git diff-tree -M --summary`）審查。
- `README.md` 舊段落仍保留 pre-R7 的檔名（如 `git-flow-integration.md` / `feature-spec.md`），但 Wave 3 明示「只插入新段、不改舊段」，因此本輪不把那些既有內容列為 finding。
- Wave 1 / 2 / 3 的 CHANGELOG 三段都存在，且大部分影響範圍與實際檔案吻合；唯一明確 drift 是 Wave 1 自述「`git mv`」與 Git 記錄不符（F-02）。

---

**跨輪議題**：本輪未額外寫入 `reviews/cross-round-notes.md`，因本 session 依指示只產出本報告檔。
