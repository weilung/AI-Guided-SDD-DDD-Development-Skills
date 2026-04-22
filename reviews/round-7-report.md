# Round 7 Review Report — Draft Proposal Review (P009 / P010 / P011)

**審查範圍**: PROPOSAL-009 / PROPOSAL-010 / PROPOSAL-011（draft）
**審查類型**: Draft proposal review（無實作對照；三份之間交叉一致性為核心）
**審查日期**: 2026-04-22
**審查者**: Codex

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| 7 | 0 | 6 | 1 | 0 |

## 按 Proposal 分類

| Proposal | Finding 數 | 建議下一步 |
|---|---|---|
| PROPOSAL-009 | 4 | revise-and-resubmit |
| PROPOSAL-010 | 3 | revise-and-resubmit |
| PROPOSAL-011 | 2 | revise-and-resubmit |

> 「建議下一步」是 Reviewer 的初判；最終 draft → approved 決定由 R7-Approve session 做，本輪不定。

## Findings

### F-01 PROPOSAL-009 對 phase-spec / feature-spec / lightweight-spec 的終局策略仍停在選項層

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-009.md § 影響範圍（Templates）`（`proposals/PROPOSAL-009-feature-directory-and-phases.md:160-168`）
  - 現況對照：`sdd-ddd-webforms-skill/SKILL.md:259,327-328`
  - 現況對照：`sdd-ddd-core-skill/SKILL.md:344,396-399`
  - 現況對照：`sdd-ddd-webforms-skill/references/new-feature-flow.md:64-67`
  - 現況對照：`sdd-ddd-core-skill/references/new-feature-flow.md:87-90`
  - 現況對照：`sdd-ddd-webforms-skill/references/modify-existing-flow.md:12,165-167`
  - 現況對照：`sdd-ddd-core-skill/references/modify-existing-flow.md:16,117`
**維度**: Dimension 1
**現況**: proposal 同時寫了 `phase-spec.md`「新增（或由 `feature-spec.md` 重命名）」、`feature-spec.md`「刪除 / 重命名」、`lightweight-spec.md`「修改或保留」，並把是否保留獨立 lightweight 模板留到「實施時評估」。
**問題**: 這不是實作細節，而是會直接決定 flow 文件、SKILL.md、模板引用、遷移指引怎麼寫的核心設計。現況 `new-feature-flow`、`modify-existing-flow`、SKILL.md 都明確綁定 `feature-spec.md` / `lightweight-spec.md`；若 proposal 不先定案，後續所有受影響檔都無法一致落稿。
**建議**: 在 proposal 內明確選一條路徑並寫成唯一終局。例如：「`feature-spec.md` 以 `git mv` 重命名為 `phase-spec.md`；`lightweight-spec.md` 保留獨立檔，不併入 `_index.md`」；若要雙軌過渡，也要寫清楚過渡期長度、引用優先序與最終移除時點。

### F-02 PROPOSAL-009 沒有定義 completed feature 再次修改時的目錄生命週期

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-009.md § 結構類 / 命令類 / 影響範圍（modify-existing-flow）`（`proposals/PROPOSAL-009-feature-directory-and-phases.md:51-59,71-85,147-148`）
  - 現況對照：`sdd-ddd-webforms-skill/references/modify-existing-flow.md:32-34,257-260`
  - 現況對照：`sdd-ddd-core-skill/references/modify-existing-flow.md:30-37,189-191`
**維度**: Dimension 1
**現況**: 現況 `modify-existing-flow` 會先檢查 `specs/features/completed/` 裡是否已有既有 spec。proposal 同時規定 feature 完成後整個目錄搬到 `completed/`，而 `/dflow:new-phase` 只適用於「已啟動的 active feature branch」。
**問題**: proposal 沒寫清楚「已完成的 feature 日後又要改」時要怎麼做：是把整個目錄從 `completed/` 搬回 `active/`、是建立新的 SPEC-ID 目錄、還是允許 completed 目錄直接加 phase。這個缺口正好落在 `/dflow:modify-existing` 的主場景，屬於可實施性問題，不是文字細節。
**建議**: 補一條明確 lifecycle 規則，至少定義：
  - 既有 completed feature 被 reopen 時的路徑移動規則
  - 是否沿用原 SPEC-ID
  - `_index.md` 如何標示 reopen / follow-up phase
  - `/dflow:finish-feature` 在 reopen 後再次收尾的對應動作

### F-03 PROPOSAL-009 把 `/dflow:verify` 當成 feature-spec 聚合器，與現況機制不符

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-009.md § 影響範圍（Git / Review / Verify）`（`proposals/PROPOSAL-009-feature-directory-and-phases.md:156-159,202,206,218-221`）
  - 現況對照：`sdd-ddd-webforms-skill/references/drift-verification.md:41-74`
  - 現況對照：`sdd-ddd-core-skill/references/drift-verification.md:41-74`
**維度**: Dimension 1
**現況**: 目前 `drift-verification.md` 只做 `rules.md` ↔ `behavior.md` 的機械一致性檢查：找兩個檔、抽 BR-ID、做 forward/anchor/reverse cross-check。它沒有讀 `specs/features/active/`，也沒有從 feature spec 擷取 BR-ID。
**問題**: proposal 把這個檔案的變更描述成「路徑假設改為 `active/{SPEC-ID}-{slug}/phase-spec-*.md`」與「BR-ID 擷取邏輯需跨多份 phase spec」，等於建立在一個現況不存在的前提上。這會把 scope 拉向與現行 verify 設計無關的新需求，也讓 proposal 的影響範圍表失真。
**建議**: 把這一列改成真正需要的影響：
  - 若只是 completion flow 改成從 feature 目錄彙整後再更新 `behavior.md`，應寫在 flow / checklist，而不是 `drift-verification.md`
  - 若真的想讓 `/dflow:verify` 讀 phase spec，需先在 proposal 的解法區新增該能力的目的、演算法與與 P008 的關係，不要只在 scope 表突然出現

### F-04 `/dflow:finish-feature` 仍產出 merge commit 訊息，與 011/010 的 Git 策略中立目標衝突

**嚴重度**: Major
**對照**:
  - 交叉一致性：`PROPOSAL-009 ↔ PROPOSAL-011 · C-08`
  - 交叉一致性：`PROPOSAL-009 ↔ PROPOSAL-010 · C-08`
  - Proposal 內部：`PROPOSAL-009.md § 命令類（決議 10）`（`proposals/PROPOSAL-009-feature-directory-and-phases.md:77-85`）
  - Proposal 內部：`PROPOSAL-011.md § 雙層區分`（`proposals/PROPOSAL-011-git-flow-decoupling.md:49-54`）
  - Proposal 內部：`PROPOSAL-010.md § Git-principles 二版本`（`proposals/PROPOSAL-010-dflow-init-and-scaffolding.md:106-110`）
**維度**: Dimension 3
**現況**: PROPOSAL-009 把 `/dflow:finish-feature` 的產出之一寫成「merge commit 訊息建議」。但 PROPOSAL-011 明確把「怎麼合」交回專案決策，PROPOSAL-010 也明寫 trunk-based 模板會提供 squash / rebase 選項。
**問題**: 這會把剛解耦掉的 Git strategy 又從另一個命令偷偷綁回來。對採 squash merge、rebase merge、甚至直接 fast-forward 的專案來說，「merge commit 訊息」不是通用產物。
**建議**: 把產出改成 Git-strategy-neutral 的描述，例如「integration summary / PR title / commit suggestion」，並明寫「格式依專案 Git-principles 選定的 merge strategy 決定」。

### F-05 PROPOSAL-010 的必產清單把 WebForms 與 Core 結構混成單一路徑，且 `behavior.md` 位置未定義

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-010.md § 1. 新增命令 /dflow:init-project`（`proposals/PROPOSAL-010-dflow-init-and-scaffolding.md:68-76`）
  - 現況對照：`sdd-ddd-webforms-skill/SKILL.md:211-231`
  - 現況對照：`sdd-ddd-core-skill/SKILL.md:228-251,331-338`
  - 現況對照：`sdd-ddd-webforms-skill/templates/CLAUDE.md:18-33`
  - 現況對照：`sdd-ddd-core-skill/templates/CLAUDE.md:35-51`
  - V2 對照：`V2: docs/specs/_overview.md`, `docs/specs/_共用/Git-原則.md`
**維度**: Dimension 4
**現況**: proposal 把 `specs/architecture/tech-debt.md`、`specs/architecture/decisions/`、`specs/domain/context-map.md`、`behavior.md` 都列成 init 的必產。現況卻是：
  - WebForms 結構使用 `specs/migration/tech-debt.md`，沒有 `architecture/`
  - Core 才使用 `specs/architecture/tech-debt.md` 與 `specs/domain/context-map.md`
  - `behavior.md` 在兩版都是 `specs/domain/{context}/behavior.md` 的 bounded-context 檔，不是 init 階段就能無條件建立的單一根檔
**問題**: 這會讓 `/dflow:init-project` 產生出與現行 skill 不相容的骨架，特別是 WebForms 版會拿到錯的 tech-debt 路徑，而 `behavior.md` 在沒有 context 名稱前也無法正確安放。這不是 wording 問題，而是實施後會直接讓 scaffold 與既有 flow/模板脫節。
**建議**: 把必產清單拆成 skill-version-specific：
  - WebForms：`specs/features/{active,completed,backlog}/`、`specs/domain/glossary.md`、`specs/migration/tech-debt.md`
  - Core：上列 + `specs/domain/context-map.md`、`specs/architecture/decisions/`、`specs/architecture/tech-debt.md`
  - `behavior.md` 改為「不在 init 階段必產；等第一個 bounded context 建立時再由 flow / template 產生」

### F-06 PROPOSAL-011 的影響範圍漏掉真正仍帶 Git Flow 語境的 `drift-verification.md`

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-011.md § 影響範圍`（`proposals/PROPOSAL-011-git-flow-decoupling.md:102-118`）
  - 現況對照：`sdd-ddd-webforms-skill/references/drift-verification.md:118-124`
  - 現況對照：`sdd-ddd-core-skill/references/drift-verification.md:128-134`
  - 現況對照：`sdd-ddd-webforms-skill/references/modify-existing-flow.md:1-16`
  - 現況對照：`sdd-ddd-core-skill/references/modify-existing-flow.md:1-16`
  - 現況對照：`sdd-ddd-webforms-skill/references/pr-review-checklist.md:1-31`
  - 現況對照：`sdd-ddd-core-skill/references/pr-review-checklist.md:1-29`
**維度**: Dimension 1
**現況**: `drift-verification.md` 在兩版都還把「Before a release branch cut」列為推薦觸發時機；這是 proposal 自己想移除的 Git Flow 語境。相反地，proposal scope 表列入的 `modify-existing-flow.md` 與 `pr-review-checklist.md`，現況並沒有 release / hotfix 流程段可刪，只是一般性的 bug-fix / spec review 指引。
**問題**: 011 的 scope 表因此同時出現「漏列真實受影響檔」與「列入缺乏對應文本的檔」。對一個標成 small 的 proposal，這種 scope 精準度不足會直接影響實施切分與 reviewer 對完成定義的判斷。
**建議**: 將 `references/drift-verification.md`（雙版）補入影響範圍，並把 `modify-existing-flow.md` / `pr-review-checklist.md` 的改動收斂到「只在實際有 Git Flow 語句時才改」，或在 proposal 內標出要改的現行句子。

### F-07 PROPOSAL-010 的 README 範例仍寫死 `.claude/skills/`，與自己的風險緩解互相抵觸

**嚴重度**: Minor
**對照**:
  - Proposal 內部：`PROPOSAL-010.md § 更新 README.md`（`proposals/PROPOSAL-010-dflow-init-and-scaffolding.md:114-121`）
  - Proposal 內部：`PROPOSAL-010.md § 預期後果（風險 6）`（`proposals/PROPOSAL-010-dflow-init-and-scaffolding.md:237-238`）
**維度**: Dimension 2
**現況**: proposal 一方面說 README 應保持「高層步驟 + link to Claude Code official docs」以避免分發機制變動失效，另一方面在範例段落又直接寫「copy ... to your `.claude/skills/`」。
**問題**: 這讓 proposal 內部對 README 應否寫死安裝路徑出現自我矛盾。雖然不是結構性阻塞，但會讓實作者不知道該遵循哪一段。
**建議**: 直接把範例改成高層措辭，例如「Install the appropriate skill using the current Claude Code skill mechanism」，並把具體路徑移到外部文件或註腳。

## 三份 Proposal 交叉一致性對照表

| 檢查點 | 主張方 | 對方回應 | 一致？ | 對應 Finding |
|---|---|---|---|---|
| C-01 · 011 留 placeholder ↔ 009 填齊 | 011 §2 | 009 §E 決議 11 | ✓ | — |
| C-02 · `git mv` 首次實踐歸屬 | 011 §1 | 009 §E | ✓ | — |
| C-03 · feature 目錄規範 ↔ init 產出 | 009 §A | 010 §1 | ✓ | — |
| C-04 · trunk-based 範本 ↔ 011 解耦 | 010 §2 | 011 §1 | ✓ | — |
| C-05 · `_index.md` 範本歸屬 | 009 §影響範圍 | 010 §scaffolding 定位 | ✓ | — |
| C-06 · 影響範圍加總 | 三份加總 | §C 現況檔 | ✗ | F-03, F-06 |
| C-07 · 依賴順序一致性 | 三份的依賴表 | field-reference-candidates.md Phase 3 | ✓ | — |
| C-08 · 命令全景自洽 | 三份的命令描述 | — | ⚠ | F-04 |

> ⚠ = 部分一致 / 存在歧義

## V2 對照備註（非 Finding，僅作理解記錄）

- PROPOSAL-009 對 V2 的「feature 目錄 + `_index.md` + 階段規格」描述基本忠於 `docs/specs/SDD-工作流程說明.md` 與 `_conventions.md`；proposal 也正確承認 V2 本身不是 filled-in field sample，實戰背書來自 `field-reference-candidates.md`。
- PROPOSAL-010 將 `_overview.md`、`_conventions.md`、`Git-原則.md`、`CLAUDE.md-範例片段.md` 重新定位為 human-facing scaffolding，這與 V2 的檔案性質相符；V2 也確實把 README / 使用說明 / 模式介紹分成 repo-onboarding 層，而非 skill 核心邏輯。
- PROPOSAL-011 把 Git Flow 指引移出 skill 核心、改放到可選 scaffolding 的方向，與 V2 `Git-原則.md` 作為專案治理文件的角色相容。

## 審查備註

- 本輪依指示只審英文 skill 現況與三份 draft proposal；未讀 Obts 專案、未比對繁中版、未對 V2 本體產生 finding。
- `review-workflow.md` 的 severity 與報告骨架已作為格式參照；R7 的判準以本提示詞覆蓋後執行。
- 本輪沒有額外寫入 `reviews/cross-round-notes.md`；未發現需要脫離 P009/P010/P011 的跨輪議題。

---

**跨輪議題**：若審查過程順手看到其他 proposal（P001-P008）或其他議題的問題，記入 `reviews/cross-round-notes.md`，**不佔本輪 Finding 數量**。
