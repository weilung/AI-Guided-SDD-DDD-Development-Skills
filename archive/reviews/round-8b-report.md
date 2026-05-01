# Round 8b Review Report — Draft Proposal Review (PROPOSAL-013)

**審查範圍**: PROPOSAL-013-system-document-template-coverage（draft）
**審查類型**: Draft proposal review（無實作對照；單一 proposal、focus 在影響範圍 / 設計決策 / 既有機制相容性）
**審查日期**: 2026-04-27
**審查者**: Cursor Claude Opus 4.7

---

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| 17 | 0 | 7 | 5 | 5 |

**建議下一步**: revise-and-resubmit

> 「建議下一步」是 Reviewer 的初判；最終 draft → approved 決定由 R8b-Approve session 做，本輪不定。

整體評價：P013 問題描述清楚、解法方向合理，且大量呼應 P003 / P004 / P008 / P009 / P010 已建立的機制；但目前 §影響範圍 漏列了**會直接受 canonical English 結構標題改名波及**的 reference 檔（pr-review-checklist.md、finish-feature-flow.md 標題重命名段、SKILL.md 描述段）與一份模式上完全等價的現有樣板（`templates/CLAUDE.md`），且 Coverage Matrix 也漏列了三份既有 templates（`context-definition.md`、`aggregate-design.md`、`CLAUDE.md`）。另外有兩處設計決策（anchor 機制 vs `SECTION-ANCHORS.md` 二擇一未定、`架構決策 README` 是否為 P010 既有 mandatory baseline）需要設計者釐清。沒有發現會破壞 P001-P012 既有機制的設計缺陷，因此不需 Critical；但上述 7 個 Major 在實施前必須補正，否則會出現 template 改了名、reference 還在用舊中文標題引用的破窗狀況。

---

## Findings

（按嚴重度排序：Critical → Major → Minor → Question；同嚴重度內按 Dimension → 段落順序）

### F-01 `templates/CLAUDE.md` 同樣含 Chinese 結構性標題卻未列入 §影響範圍

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-013 §1`（canonical English structural language 原則）、`§影響範圍`
  - 現況對照：`sdd-ddd-webforms-skill/templates/CLAUDE.md:7`、`:54`、`:58`（`系統脈絡` / `開發流程` / `核心原則` / `目錄結構` 等 H2 / H3 標題；同檔多處引用 `_index.md` 的 `接續入口` / `輕量修改紀錄`）
**維度**: Dimension 3
**現況**: P013 §1 明確要求「Dflow templates and scaffolding use English as the canonical structural language」，並把 `scaffolding/CLAUDE-md-snippet.md` 列為「修改 / 檢查 — 結構性中文詞彙改為 canonical English」。但 `templates/CLAUDE.md`（兩版皆有）含大量 Chinese 結構性標題（`系統脈絡`、`開發流程`、`核心原則`、`目錄結構`）與 Chinese section 引用，卻完全沒出現在 §影響範圍 表中。
**問題**: §影響範圍 對 `scaffolding/CLAUDE-md-snippet.md` 與 `templates/CLAUDE.md` 處理不一致。兩者本質上都是「init 階段引導使用者建立 / 合併 `CLAUDE.md`」的 template/scaffolding 來源；若 snippet 要英文化、template 不英文化，未來 init 把這兩者拼在一起時會產生「snippet 段是英文標題、template 包覆段是中文標題」的內部不一致，反而讓 AI 更難定位。也與 §1 提出的覆蓋原則正面衝突。
**建議**:
- 將 `sdd-ddd-webforms-skill/templates/CLAUDE.md` 與 `sdd-ddd-core-skill/templates/CLAUDE.md` 補入 §影響範圍，分類為「修改 / 檢查」。
- 如果故意排除（例如要與既有用戶 `CLAUDE.md` 的 H2 navigation 契約保持向後相容），請在 §1 或 §不納入影響範圍 明確寫出例外條款並指出 P007c 是依據；不要讓讀者以為是漏列。
- 若採例外條款方案，`scaffolding/CLAUDE-md-snippet.md` 也要同步**保留** `系統脈絡` / `開發流程` 兩個 Chinese H2，並改成局部英文化（內層字段 / 指引文字），以維持 navigation 契約。否則 snippet vs 既有用戶檔會破窗。

---

### F-02 TEMPLATE-COVERAGE.md 矩陣漏列 3 份既有 templates

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-013 §4` 矩陣
  - 現況對照：`sdd-ddd-webforms-skill/templates/context-definition.md`、`sdd-ddd-core-skill/templates/context-definition.md`、`sdd-ddd-core-skill/templates/aggregate-design.md`、`sdd-ddd-{webforms,core}-skill/templates/CLAUDE.md`
**維度**: Dimension 3
**現況**: §4 自述「本矩陣只處理 `templates/` 與 `scaffolding/` 來源檔的覆蓋與兩版對照」，並提供 11 列 logical document。但下列既有 template 完全沒出現：
- `context-definition.md`（兩版皆有；workflow 會建立 / 維護）
- `aggregate-design.md`（Core only）
- `CLAUDE.md`（兩版皆有）
**問題**: 「覆蓋」應該是全集；若是物理樣板就要在 matrix 上出現，否則「review 檢查物理上分開、邏輯上同步」這個矩陣定位失效。`context-definition.md` 與 `aggregate-design.md` 屬於 DDD 戰術 worksheet template，恰好是 WebForms / Core 差異的核心來源（Core 多 `aggregate-design.md`），更應該放進 matrix 比對。`CLAUDE.md` 則是兩版都有的根目錄 template，讓 review 一眼看到兩版是否同步。
**建議**: §4 矩陣補入：
- `Bounded Context definition` → `context-definition.md` / `context-definition.md`，Parity「結構同」、Allowed differences「Core 段落可引用 Aggregate / Domain Service / Repository Interface 等 DDD 概念」。
- `Aggregate worksheet` → `n/a` / `aggregate-design.md`，Allowed differences「Core only — WebForms 不適用」。
- `Project AI guide` → `templates/CLAUDE.md` / `templates/CLAUDE.md`，Parity「H2 結構（`系統脈絡` / `開發流程`...）一致」、Allowed differences「Core 包含 Aggregate / Architecture Decisions 描述」。視 F-01 結論調整 H2 是否英文化。

---

### F-03 `pr-review-checklist.md` 未列入 §影響範圍，但會被 `_index.md` 標題英文化波及

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-013 §影響範圍`（templates/_index.md 修改 / 檢查 → canonical English）
  - 現況對照：`sdd-ddd-webforms-skill/references/pr-review-checklist.md:19`、`:28`、`:30`、`:51`、`:73`（`接續入口`、`輕量修改紀錄`、`行為變更（Delta）`、`Current BR Snapshot`）
**維度**: Dimension 3
**現況**: `pr-review-checklist.md` 直接以 Chinese section name 引用 `_index.md` 與 `phase-spec.md` / `lightweight-spec.md` 的章節（如 `_index.md` 輕量修改紀錄、`接續入口`、`行為變更（Delta）`），用以 review 時定位。P013 計畫把這三個 template 的結構性標題統一為 canonical English，但 `pr-review-checklist.md` 完全不在 §影響範圍 表內。
**問題**: P013 實施後，`_index.md` 的 `輕量修改紀錄` 改成 `Lightweight Changes`、`接續入口` 改成 `Resume Pointer`（或類似）、`行為變更（Delta）` 改成 `Behavior Delta`，但 `pr-review-checklist.md` 仍用舊中文 label。AI 跑 `/dflow:pr-review` 時會找不到 checklist 指的段落，整個 PR review 流程會破窗。
**建議**: §影響範圍 補入兩版 `references/pr-review-checklist.md`，分類為「修改」，說明「同步引用的 template section name 改為 canonical English（與 §1 glossary 對齊）」。

---

### F-04 `finish-feature-flow.md` Integration Summary 段同樣使用 Chinese section labels，未涵蓋

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-013 §影響範圍`（finish-feature-flow.md 修改 — 僅標 BC sync 前缺檔時用 template 建立）
  - 現況對照：`sdd-ddd-webforms-skill/references/finish-feature-flow.md:45`（`接續入口`）、`:82`（`接續入口（Resume Pointer）`）、`:189`（`Feature 目標：{1-2 sentences from _index.md 目標與範圍}`）、`:191`（`變動範圍`）、`:196`（`關聯 BR-ID`）
**維度**: Dimension 3
**現況**: §影響範圍 對 `finish-feature-flow.md` 只描述「BC sync 前缺檔時用 template 建立」，但實際檔案內含 Integration Summary 模板，使用 `Feature 目標` / `變動範圍` / `關聯 BR-ID` / `_index.md 目標與範圍` / `接續入口（Resume Pointer）` 等中文結構 label，且其中 `變動範圍`、`關聯 BR-ID`、`Lightweight Change` 已出現在 §1.1 glossary。
**問題**: 與 F-03 同類型——template 結構標題英文化後，`finish-feature-flow.md` 引用舊 Chinese label 會找不到對應段落；Integration Summary 自身使用的 Chinese labels（`Feature 目標` / `變動範圍` / `關聯 BR-ID`）依 P013 §1 判準（「會被 AI 或開發者當作固定結構 / 欄位 / 狀態 / 類型理解的詞」）也屬於 canonical English 應覆蓋的範圍，卻沒被涵蓋。
**建議**: §影響範圍 把 `finish-feature-flow.md` 的修改項擴充為兩件：
1. BC sync 前缺檔時用 template 建立（已寫）。
2. Integration Summary 段的 Chinese labels（`Feature 目標` / `變動範圍` / `關聯 BR-ID` / `Phase 清單` 等）改為 canonical English；引用 `_index.md` 的 section name 也同步更新。並把這些術語補入 `TEMPLATE-LANGUAGE-GLOSSARY.md`。

---

### F-05 `SKILL.md` Templates & Scaffolding 段落硬編碼 Chinese section names

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-013 §影響範圍`（SKILL.md 修改 — 僅標 Templates 表新增 living document templates；說明 Template Coverage Matrix）
  - 現況對照：`sdd-ddd-webforms-skill/SKILL.md`（Templates & Scaffolding 段，描述 `_index.md` 含 `目標與範圍`、`輕量修改紀錄`、`接續入口` 等 section）
**維度**: Dimension 3
**現況**: P013 §影響範圍 對 SKILL.md 的修改描述只涵蓋「新增 living document templates；說明 Template Coverage Matrix」。但 SKILL.md 的 Templates & Scaffolding 段同時包含「`_index.md` 含 `目標與範圍` / `輕量修改紀錄` / `接續入口` 等 section」這類硬編碼的 Chinese 描述；scaffolding/CLAUDE-md-snippet.md 的描述也提到「保留 `系統脈絡` / `開發流程` H2」。
**問題**: 一旦 template 結構標題改為 canonical English，SKILL.md 描述變成 outdated；對 AI 來說，SKILL.md 是 entry point，描述漂移會誤導讀檔流。
**建議**: §影響範圍 把 SKILL.md 修改描述擴充為三件：
1. Templates 表新增 living document templates（已寫）。
2. 說明 Template Coverage Matrix（已寫）。
3. 同步更新 Templates & Scaffolding 段內所有引用的 template section name 為 canonical English；CLAUDE-md-snippet 描述視 F-01 的決定調整。

---

### F-06 §影響範圍 對 `SECTION-ANCHORS.md` 與 `TEMPLATE-COVERAGE.md` anchor 欄位「擇一」未定，導致實施階段沒有單一決策來源

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-013 §影響範圍` 第 3 列：`SECTION-ANCHORS.md` 或 `TEMPLATE-COVERAGE.md` 內 anchor 欄位 — 新增 / 擇一
**維度**: Dimension 2
**現況**: §影響範圍 表第 3 列同時提到「新增 `SECTION-ANCHORS.md`」與「在 `TEMPLATE-COVERAGE.md` 加 anchor 欄位」，標示為「擇一」。
**問題**: Implement Review 階段需要一個明確的目標檔結構；目前 P013 留二擇一、實施者得自行決定，會在 R8b 已通過後再次發生「設計者未定的小決策」，與 P013 自身對於「Dflow-managed `.md` 必有 source template」的精神矛盾（連 P013 自己的維護檔結構都還沒定）。也讓 §6 對應引用無法寫死指向（要指向 `SECTION-ANCHORS.md` 還是 `TEMPLATE-COVERAGE.md` anchors 欄位？）。
**建議**: P013 在 approve 前就把這個二擇一收斂。Reviewer 個人傾向：併入 `TEMPLATE-COVERAGE.md` 加一欄 `Section anchors`（避免再開一份維護檔，且 anchor 與 template 物理上同源）。但無論結論為何，§影響範圍 第 3 列改為單一條目。

---

### F-07 §5 Core mandatory baseline 列出 `architecture/decisions/README.md`，可能與 P010 既有 baseline 清單存在語意差距

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-013 §5`（Core baseline 列出 `specs/architecture/decisions/README.md ← scaffolding/architecture-decisions-README.md`）
  - 既有機制：`PROPOSAL-010 §1 Step 4` · C-06；P010 Step 4 對 Core 列出「`specs/architecture/decisions/`（空目錄）」
**維度**: Dimension 5
**現況**: P010 Step 4 明確列 `specs/architecture/decisions/`「空目錄」為 Core mandatory baseline，沒有指定要建立 README.md。`planning/project-review-brief.md` § 8 雖把 `architecture/decisions/README.md` 列為「P013 候選」，但它屬於 brief 提的補洞清單，不是 P010 的決策。P013 §5 直接把 README.md 加到 baseline 行內。
**問題**: 這同時是「P013 是否擴大 P010 mandatory baseline 範圍」的判斷題：
- 若 P013 視之為「P010 漏列的補洞」（因為空目錄沒有 README.md 不易使用），則應該在 §關聯 Proposal 段或 §影響範圍 明確標出「修補 P010 baseline」。
- 若 P013 視之為純粹補 template source（README.md 仍可選），則 §5 不應寫成 mandatory baseline 行格式（會讓讀者誤以為 P010 早已強制建立 README.md）。
**建議**: §5 對 README.md 列項加註「P010 Step 4 原列為空目錄；本 proposal 補上 README.md 範本來源並列為 baseline 推薦輸出」，或在 §關聯 Proposal 把 P010 修補項講清楚。同時 P010 後續若不重新校正，會與 P013 baseline 描述產生不一致；需要決定是 P013 蓋過 P010、或 P013 只說「init 已建立的 decisions/ 應同時放 README.md」。

---

### F-08 §1 anchor 名稱未對 `behavior.md` / `rules.md` 已用的 markdown anchor 規範作檢查，可能與 P008 drift-verification 的 link 解析格式衝突

**嚴重度**: Question（傾向 Major）
**對照**:
  - Proposal 內部：`PROPOSAL-013 §1`（`<!-- dflow:section X -->` HTML comment anchor）
  - 既有機制：`PROPOSAL-008 §核心構件`（drift-verification 演算法依賴 `rules.md` 內 `[BR-001](./behavior.md#br-001-rule-name)` 樣式 anchor link）· C-03
**維度**: Dimension 5
**現況**: P008 drift-verification 已建立的 anchor 解析依賴**Markdown auto-id**（heading 段落自動產生 fragment id，例如 `behavior.md#br-001-rule-name`）。P013 §1 新增 `<!-- dflow:section X -->` HTML comment anchor，定位語意是「給 AI 找段落用」。兩者一個是給 link 解析、一個是給段落定位，理論上不衝突。但 P013 §6 對 `drift-verification.md` 的修改描述只提到「rules.md 缺失時指向 templates/rules.md」，沒提如何處理新 anchor 與既有 markdown anchor 的並存。
**問題**: 需要釐清：
- 新 `dflow:section` anchor 在 `behavior.md` 內出現時，drift-verification 的 anchor link 演算法是否要把它列入 valid anchor pool？還是繼續只認 markdown auto-id？
- 新 `templates/rules.md` 的 `Behavior anchor` 欄位（§3 表）建議格式為何？是 `[behavior.md#br-001](...)` 還是 `<!-- dflow:section behavior-scenarios -->` 內的 BR-001 子段？
**建議**: P013 §6 對 `drift-verification.md` 的修改加一條：「新 `dflow:section` anchor 不取代 BR-ID markdown anchor link；drift-verification 仍以 BR-ID + markdown auto-id 為主索引，`dflow:section` 僅作為段落定位輔助」。並在 §3 給 `templates/rules.md` 建議一個具體的 `Behavior anchor` 範例值（例如 `[BR-001](./behavior.md#br-001-rule-name)`），讓 P008 既有演算法可直接套用。

---

### F-09 §影響範圍 未涵蓋 `templates/CLAUDE.md` 已殘留的 P010-pre 路徑（與 P012 處理範圍交集，但 P012 也未必涵蓋）

**嚴重度**: Minor
**對照**:
  - Proposal 內部：`PROPOSAL-013 §影響範圍`
  - 現況對照：`sdd-ddd-webforms-skill/templates/CLAUDE.md`（其目錄結構描述使用 P010 之前的 `specs/_overview.md` / `specs/_conventions.md` 平鋪結構，已經與 P010 後 `_共用/` 結構與 P012 後 `shared/` 結構都不一致）
  - 邊界對照：`PROPOSAL-012 §2`
**維度**: Dimension 4
**現況**: `templates/CLAUDE.md` 內的目錄結構區塊仍寫舊式 `specs/_overview.md` / `specs/_conventions.md`（直接放 `specs/` 下），與目前 `_共用/` 結構與未來 P012 的 `shared/` 都不對齊。P012 §影響範圍把 `templates/CLAUDE.md` 列入處理；P013 沒列。
**問題**: 若 P013 視 `templates/CLAUDE.md` 為「P012 處理範圍，不再重複」，這是合理的邊界切割；但 §影響範圍 完全沒 mention，使得 reviewer 不易判斷。又因為 F-01 提示 `templates/CLAUDE.md` 也存在 canonical English 議題，P012 / P013 可能都要碰到此檔。
**建議**: §不納入影響範圍 加一列：「`templates/CLAUDE.md` 目錄路徑同步交由 PROPOSAL-012 處理；canonical English 結構性標題視 F-01 決議再決定是否納入本 proposal」，避免讀者誤以為漏列。

---

### F-10 §1 Canonical English structural language 與 `planning/public-distribution-backlog.md § 1` 邊界重疊，但 P013 未明確宣告接管

**嚴重度**: Question
**對照**:
  - Proposal 內部：`PROPOSAL-013 §1`（canonical English structural language 原則）、`§不納入影響範圍`（雙語 template set 不建立）
  - 邊界對照：`planning/public-distribution-backlog.md § 1`（文件內容語言策略）、`§ 4`（Template Language Glossary）
  - 既有機制：`PROPOSAL-012 §不納入影響範圍`（明確說「文件內容語言策略應延後到 npm / public distribution 前再整體決策」）
**維度**: Dimension 4
**現況**: backlog § 1「文件內容語言策略」原規劃延後到 npm 前處理，且 P012 也明確把語言策略推給 backlog。P013 §1 提出 canonical English structural language（區分「結構性術語」vs「使用者可填寫內容」），實質上採取了 backlog § 1 描述的「單一 canonical English template + 對照表」這條路線——這跟 backlog § 1 / § 4 的路線之一一致。
**問題**: 需要釐清 backlog 與 P013 的關係：
- P013 是否已經接管 backlog § 1 的「結構性語言策略」部分（content 語言仍由開發者 / 團隊決定）？
- backlog § 4 的 Template Language Glossary 是否完全等同於 P013 §1.1 的 `TEMPLATE-LANGUAGE-GLOSSARY.md`？
- 若是，backlog 是否該在 P013 approved 後將 § 1 / § 4 標為「partially fulfilled by P013」？
**建議**: P013 §關聯 Proposal 段 加一條 backlog 對應，明確說：「P013 § 1 接管 backlog § 1 的『結構性語言策略』部分（content 語言策略仍延後）；P013 §1.1 接管 backlog § 4 的 Template Language Glossary」。實施完成後在 backlog 對應段加註「partially fulfilled by P013」。

---

### F-11 `TEMPLATE-COVERAGE.md` 與 `TEMPLATE-LANGUAGE-GLOSSARY.md` 的存放位置未指定

**嚴重度**: Minor
**對照**:
  - Proposal 內部：`PROPOSAL-013 §1.1`、`§4`、`§影響範圍`
**維度**: Dimension 6
**現況**: 兩份新增維護檔在 §影響範圍 表只寫檔名，未指定路徑。
**問題**: repo 根目錄 vs `proposals/` vs `references/`（per skill）vs 兩版各自一份 vs 共用一份——這些都會影響後續引用與維護負擔。Implement 階段才決定容易反覆修改。
**建議**: §影響範圍 把兩檔路徑明確化。Reviewer 建議：兩檔放 repo 根目錄、為兩版 skill 共用維護契約（與 `CHANGELOG.md` / `README.md` 同層）。

---

### F-12 §7 lightweight-spec Implementation Tasks 範例的 `DOC-1` 行硬編碼 `Lightweight Changes` / `Current BR Snapshot` 英文 section 名

**嚴重度**: Minor
**對照**:
  - Proposal 內部：`PROPOSAL-013 §7`（template 範例：`DOC-1: Update _index.md Lightweight Changes and Current BR Snapshot`）
  - 既有機制：`PROPOSAL-009 §H` · C-04（`_index.md` 既有 `輕量修改紀錄` section）
**維度**: Dimension 5
**現況**: §7 Implementation Tasks 樣板的 DOC-1 範例直接使用 `Lightweight Changes` 與 `Current BR Snapshot` 這兩個 canonical English section name。`Current BR Snapshot` 是 P009 既有名稱（已英文）；`Lightweight Changes` 對應 P009 既有的 `輕量修改紀錄`（中文，待 P013 改為英文）。
**問題**: §7 假設了「`_index.md` 已英文化」這件事的實施結果，但 §影響範圍 的 `_index.md` 改名並沒有明確說 `輕量修改紀錄` → `Lightweight Changes`（只說「結構標題、欄位、placeholder 統一為 canonical English」）。若實施時 `_index.md` 採用其他英文名（例如 `Light Modification Log`），§7 範例會跟 `_index.md` 不對齊。
**建議**: 在 §1.1 glossary 加一列確定 `Lightweight Changes`（中文 `輕量修改紀錄`）為 canonical name；或在 §7 加註「DOC-1 段中的 `Lightweight Changes` 與 _index.md canonical English heading 必須一致；以 §1.1 glossary 為準」。
（§1.1 glossary 已列 `Lightweight Change`，但精確名稱 `Lightweight Changes`（複數，section heading 用法）建議補上。）

---

### F-13 `core-skill/references/ddd-modeling-guide.md` 提及 `specs/domain/context-map.md` 但未補上指向新 template 的引導

**嚴重度**: Minor
**對照**:
  - Proposal 內部：`PROPOSAL-013 §3`（新增 `templates/context-map.md` Core 版）、`§6 new-feature-flow / modify-existing-flow / finish-feature-flow`
  - 現況對照：`sdd-ddd-core-skill/references/ddd-modeling-guide.md:272`（`Document in 'specs/domain/context-map.md'`）
**維度**: Dimension 3
**現況**: `ddd-modeling-guide.md` 在 Bounded Context Relationships 段直接告訴開發者把資訊寫到 `specs/domain/context-map.md`，但沒指出「該檔不存在時用 `templates/context-map.md` 建立」。P013 §6 對其他三份 reference flow 都加了缺檔時用 template 建立的指引，唯獨 `ddd-modeling-guide.md` 沒列入。
**問題**: 一致性問題；雖然 ddd-modeling-guide 是「開發者學 DDD」的引導文，不是 workflow flow，但因為它指向 context-map.md，補一句「若不存在，使用 `templates/context-map.md`」可避免破窗。
**建議**: §影響範圍 補入 `sdd-ddd-core-skill/references/ddd-modeling-guide.md`（修改 — 補 `context-map.md` 缺檔時的 template 引用）。或在 §6 加一句說「ddd-modeling-guide 的 context-map 引用同步更新」。

---

### F-14 `Git-principles-{gitflow,trunk}.md` 影響描述「結構性中文詞彙改為 canonical English」可能高估實際工作量

**嚴重度**: Minor
**對照**:
  - Proposal 內部：`PROPOSAL-013 §影響範圍`
  - 現況對照：`sdd-ddd-webforms-skill/scaffolding/Git-principles-gitflow.md`（檔內主體已是英文，僅「Integration Commit Message Conventions」一段內的 fixed labels 為中文 — `Feature 目標` / `變動範圍` / `關聯 BR-ID` / `Phase 數` / `輕量修改`）
**維度**: Dimension 3
**現況**: §影響範圍 對 Git-principles-* 兩檔（兩版共 4 個檔）標「結構性中文詞彙改為 canonical English」，描述像是大規模翻譯。但實際上檔案主體已是英文，只有 commit message convention 段含 5-6 個中文 label。
**問題**: 描述精度不足。讀者讀到時可能誤以為 4 個檔需大幅改寫，但實際只是局部文字替換，工作量遠低於描述暗示。也讓總體工作量「中高」估算來源不清。
**建議**: §影響範圍 把這 4 列描述改為「Integration Commit Message Conventions 段內的固定 labels（`Feature 目標` / `變動範圍` / `關聯 BR-ID` / `Phase 數` / `輕量修改` 等）改為 canonical English；其他段落已英文，僅檢查」。

---

### F-15 `dflow:section` anchor 命名規範未討論 namespacing / versioning / 與 markdown anchor id 衝突邊界

**嚴重度**: Question
**對照**:
  - Proposal 內部：`PROPOSAL-013 §1`（`<!-- dflow:section {section-id} -->`）
**維度**: Dimension 6
**現況**: §1 給出 6 個初始 anchor（`implementation-tasks`、`current-br-snapshot`、`lightweight-changes`、`behavior-scenarios`、`business-rules`、`open-questions`），命名格式 `dflow:section {section-id}`，沒進一步討論。
**問題**: 兩個 future-proofing 疑慮：
- **namespacing**：`dflow:section` 已有命名空間，但同一 section-id 在不同 template 中是否共用（例如 `business-rules` 同時在 `rules.md` 與 `_index.md`）？P013 §1 anchor 表格列出 `business-rules` 對應 `rules.md`, `_index.md` — 顯示是同 id 共用。這代表 anchor 是 template-scope 還是 doc-instance-scope？
- **versioning**：未來若需要重命名某 anchor（例如 `current-br-snapshot` → `br-snapshot`），如何處理已產生的 specs？目前無 deprecation strategy。
**建議**: §1 加一段「Anchor naming rules」釐清：
- anchor id 採全 repo 共用語意（同 id 在不同 template 表示同類段落）。
- 若需要重命名 anchor，新增新 id 並保留舊 id 於 template 內 6 個月，以 `<!-- dflow:section new-id (was old-id) -->` 過渡。
（或標 Question 留待設計者判斷。Reviewer 標 Question 不擅自升級。）

---

### F-16 §5 Core baseline 與 P010 既有 baseline 描述細節差異未列入比對

**嚴重度**: Question
**對照**:
  - Proposal 內部：`PROPOSAL-013 §5`
  - 既有機制：`PROPOSAL-010 §1 Step 4` Core baseline 清單 · C-06
**維度**: Dimension 5
**現況**: P010 Step 4 對 Core 列出 4 個 .md（`glossary.md`、`context-map.md`、`tech-debt.md`、空目錄 `decisions/`）。P013 §5 列出 4 個 .md + 1 個 README.md（含 `decisions/README.md`）。差異點同 F-07，但 F-07 焦點在 README.md；此 finding 焦點在「P013 是否還隱含改了其他 baseline 項目」。
**問題**: Reviewer 比對後沒看到 P010 既有 4 項被改動或刪除；P013 §5 只「新增」README.md。但 §5 沒有明確比對表，閱讀時必須兩邊互查才能確認「沒改 P010 既有項」。
**建議**: §5 補一段「baseline 比對」表，列出 P010 baseline 與 P013 baseline，讓 review 一眼可見「只新增 README.md，其餘不變」。或在 §關聯 Proposal P010 列加註此細節。

---

### F-17 §影響範圍 對 references/git-integration.md 未列入；若 §1.1 glossary 把 `Change Scope` / `Lightweight Change` 列為 canonical 名，相關 reference 應同步檢查

**嚴重度**: Question
**對照**:
  - Proposal 內部：`PROPOSAL-013 §1.1` glossary（含 `Change Scope` 對應 `變動範圍`、`Lightweight Change` 對應 `輕量修改`）
  - 現況對照：`sdd-ddd-{webforms,core}-skill/references/git-integration.md`（git-integration.md 是 P011 git 解耦原則文，已大致英文，但與 §1.1 列舉的 `Change Scope` / `Lightweight Change` 概念有交集）
**維度**: Dimension 3
**現況**: §1.1 glossary 包含 `Change Scope`、`Lightweight Change`，但這兩個術語主要出現在 commit message convention（`Git-principles-*.md`）與 spec templates。git-integration.md 自身較少 Chinese 結構性術語，但仍可能包含 branch/commit 相關描述。
**問題**: Reviewer 沒在 git-integration.md 中找到大量 Chinese 結構性詞彙；但既然 §1.1 將 `Change Scope` / `Lightweight Change` 標為 canonical 名，理應檢查所有引用此概念的 reference 檔。git-integration.md 是否有需要同步更新的小段落，留待設計者實際開檔審查。
**建議**: §影響範圍 加一列 `references/git-integration.md`（兩版）— 標「檢查」，描述為「確認與 §1.1 glossary 列出的 canonical 名（`Change Scope` / `Lightweight Change`）一致；無變更也明列」。

---

## 既有機制相容性對照表（Dimension 5）

| 檢查點 | P013 段落 | 既有機制段落 | 一致？ | 對應 Finding |
|---|---|---|---|---|
| C-01 · rules / behavior anchor | P013 §1 | PROPOSAL-003（rules.md / behavior.md source-of-truth） | ⚠ | F-08 |
| C-02 · lightweight task guard | P013 §7 | PROPOSAL-004（lightweight-spec 設計理念） | ✓ | — |
| C-03 · drift-verification anchor 與新 anchor 機制並存 | P013 §1, §6 | PROPOSAL-008（drift-verification） | ⚠ | F-08 |
| C-04 · § H Lightweight Changes vs § 7 Implementation Tasks 雙層紀錄 | P013 §7 | PROPOSAL-009 § H | ✓ | F-12（小範圍，非衝突） |
| C-05 · _index.md 英文化是否破壞 P009 § D 既有結構識別 | P013 §影響範圍 | PROPOSAL-009 § D | ⚠ | F-03、F-04、F-05 cascade（template 改名後 reference 需同步） |
| C-06 · mandatory baseline 一致性（Core 是否新增 README.md） | P013 §5 | PROPOSAL-010 §1 Step 4（init-project Core baseline） | ⚠ | F-07、F-16 |
| C-07 · scaffolding 邊界（glossary 從 scaffolding 重分類到 templates） | P013 §1 | PROPOSAL-010 scaffolding 定位 | ✓ | — |
| C-08 · 路徑使用 post-P012 | P013 §影響範圍 | PROPOSAL-012 §2 | ✓ | F-09（紀錄問題小） |

> ⚠ = 部分一致 / 存在歧義
>
> C-02 / C-04 解釋：P013 §7 task guard（不以 task 數自動升 T1，AI 應主動詢問）與 P004 lightweight 精神相容；DOC-1 強制 update `_index.md` Lightweight Changes 與 Current BR Snapshot，避免「task list 在 lightweight-spec」與「event 在 _index.md」雙頭紀錄不一致。F-12 是命名同步問題，非機制衝突。
>
> C-05 / C-06 解釋：P013 不破壞 P009 § D 6 個 section 的存在與順序，只改 label。但 reference 檔同步是 P013 必須一併處理的，否則 break。F-03 / F-04 / F-05 屬於這類 cascade 漏列。
>
> C-07 解釋：glossary.md 從未實際存在於 `scaffolding/`（init 時直接建立空檔）；P013 把它分類在 `templates/`，與 P010 對 scaffolding 的「init 寫入後由專案持有」定位不衝突。

---

## 設計決策評估表（Dimension 6）

| 決策 | 設計者主張 | Reviewer 評估 | 建議 |
|---|---|---|---|
| Canonical English structural language | P013 §1 第 6 點判準（「會被 AI 或開發者當作固定結構 / 欄位 / 狀態 / 類型理解的詞」） | ⚠ 灰色地帶大但方向合理 | 接受方向；要求實施時對所有現有 templates 列「應英文化的結構詞 → canonical 名」一覽（即 §1.1 glossary 必須隨實施擴充）。F-12、F-14、F-15 為相關 finding。 |
| `<!-- dflow:section X -->` anchor | P013 §1「Important Dflow-updated sections」段（6 個初始 anchor，僅高頻段落） | ⚠ 命名規範不完整但機制本身合理 | 接受機制；F-15 要求補 namespacing / versioning 規範或標 Question 由設計者收斂。 |
| TEMPLATE-COVERAGE.md | P013 §4（11 列 logical document） | ⚠ 列表不全 | F-02：補 3 列既有 templates。F-11：明確路徑。F-06：吸收 anchor 欄位即可省一份維護檔。 |
| TEMPLATE-LANGUAGE-GLOSSARY.md | P013 §1.1（7 列初始 glossary） | ✓ 機制合理 | F-11：明確路徑。F-10：與 backlog § 4 關係明示。F-12 / F-14：實施時擴充至涵蓋所有結構性中文詞彙。 |

---

## 影響範圍對照表

| 檔案 | P013 列入？ | 修改類型 | 實際工作量評估 | 一致？ | 對應 Finding |
|---|---|---|---|---|---|
| 新增 `TEMPLATE-COVERAGE.md` / `TEMPLATE-LANGUAGE-GLOSSARY.md` / `SECTION-ANCHORS.md`（擇一） | ✓ | 新增 | 中（11+ 行 matrix；持續維護） | ⚠ | F-06、F-11 |
| 新增 `templates/glossary.md` / `models.md` / `rules.md` / `tech-debt.md`（兩版皆 4 份） | ✓ | 新增 | 中（每份範本要設計欄位） | ✓ | — |
| 新增 `templates/context-map.md`（兩版） | ✓ | 新增 | 小 | ✓ | — |
| 新增 `templates/events.md`（Core only） | ✓ | 新增 | 小 | ✓ | — |
| 新增 `scaffolding/architecture-decisions-README.md`（Core only） | ✓ | 新增 | 小 | ⚠（baseline 行為） | F-07、F-16 |
| 既有 `templates/_index.md`（兩版） | ✓ | 修改 / 檢查 | 中（4 個結構標題改名 + reference cascade） | ⚠ | F-03、F-04、F-05、F-12 |
| 既有 `templates/phase-spec.md`（兩版） | ✓ | 修改 / 檢查 | 中-大（9 段全部 Chinese 結構標題；P013 §1.1 glossary 需同步擴充） | ⚠ | （工作量低估，見 D7） |
| 既有 `templates/lightweight-spec.md`（兩版） | ✓ | 修改 + 新增 task 段 | 中（5 段 Chinese 標題 + 新增 Implementation Tasks） | ✓ | F-12 |
| 既有 `templates/behavior.md`（兩版） | ✓ | 修改 / 檢查 | 小（檔內已大致英文） | ✓ | — |
| 既有 `templates/context-definition.md`（兩版） | ✓ | 修改 / 檢查 | 中（6 段 Chinese 結構標題） | ⚠ | F-02（Coverage Matrix 漏列） |
| 既有 `templates/aggregate-design.md`（Core only） | ✓ | 修改 / 檢查 | 中（9 段含部分 Chinese） | ⚠ | F-02 |
| 既有 `templates/CLAUDE.md`（兩版） | **✗ 漏列** | — | 中（H2 navigation 契約 + Chinese sections） | ✗ | F-01、F-09 |
| 既有 `scaffolding/_overview.md` / `_conventions.md`（兩版） | ✓ | 檢查 | 小（多為英文） | ✓ | — |
| 既有 `scaffolding/Git-principles-{gitflow,trunk}.md`（兩版共 4 檔） | ✓ | 修改 / 檢查 | 小（局部 commit convention labels） | ⚠（描述偏大） | F-14 |
| 既有 `scaffolding/CLAUDE-md-snippet.md`（兩版） | ✓ | 修改 / 檢查 | 中（與 P007c navigation 契約交集） | ⚠ | F-01 cascade |
| 既有 `references/init-project-flow.md`（兩版） | ✓ | 修改 | 小 | ✓ | — |
| 既有 `references/new-feature-flow.md`（兩版） | ✓ | 修改 | 小 | ✓ | — |
| 既有 `references/modify-existing-flow.md`（兩版） | ✓ | 修改 | 中（含 T2 task checklist 流程更新） | ✓ | — |
| 既有 `references/finish-feature-flow.md`（兩版） | ✓ | 修改（僅 BC sync 缺檔處理） | 中（Integration Summary 段 Chinese labels 待併入） | ⚠ | F-04 |
| 既有 `references/drift-verification.md`（兩版） | ✓ | 修改 | 小 | ⚠ | F-08 |
| 既有 `references/pr-review-checklist.md`（兩版） | **✗ 漏列** | — | 中（多處引用 _index.md / phase-spec / lightweight-spec section name） | ✗ | F-03 |
| 既有 `references/git-integration.md`（兩版） | **✗ 漏列** | — | 小（檢查 glossary canonical 名一致性） | ⚠ | F-17 |
| 既有 `references/ddd-modeling-guide.md`（Core only） | **✗ 漏列** | — | 小（context-map.md 缺檔時用 template） | ⚠ | F-13 |
| 既有 `SKILL.md`（兩版） | ✓ | 修改 | 中（Templates 描述 + Coverage Matrix 指引 + 引用 cascade） | ⚠ | F-05 |

---

## 邊界對照備註

| 邊界檢查 | P013 描述位置 | 對方檔說法 | 一致？ | 對應 Finding |
|---|---|---|---|---|
| 內容語言策略延到 backlog § 1 | P013 §1 第 4-6 點、§不納入影響範圍（雙語 set 不建立） | `planning/public-distribution-backlog.md § 1`（描述「單一 canonical English template + 對照表」為候選路線） | ⚠ | F-10 |
| Template Language Glossary 前移處理 | P013 §1.1 | `planning/public-distribution-backlog.md § 4`（glossary 規劃延後處理） | ⚠ | F-10 |
| 路徑使用 post-P012 | P013 §影響範圍（未顯式聲明，但表內無 `_共用/`） | `PROPOSAL-012 §2` | ✓ | F-09 |
| 雙語 template set 不建立 | P013 §不納入影響範圍 | `planning/public-distribution-backlog.md`（npm 前再評估雙語 set） | ✓ | — |
| ADR template 暫不新增 | P013 §3 末段 | （無對方檔；reviewer 自行判斷） | ✓ | — Reviewer 認為合理：無 `/dflow:new-adr` workflow 時，先補 directory README，等 ADR workflow 成型再補 ADR template，是漸進式作法 |
| Git-principles canonical English 與 P011 | P013 §影響範圍 | PROPOSAL-011 git 解耦原則 | ✓ | F-14（描述偏大，非衝突） |

---

## 工作量評估與切分建議（Dimension 7）

- P013 預估「中高」評價：⚠ 略低估
- 建議是否切 sub-waves：是
- 理由：
  - 12 個新 template + 11 份既有 templates / scaffolding 結構英文化 + 5+ 份 references cascade（含本報告補列的 4 份漏列檔）+ SKILL.md + 2-3 份維護契約檔，總計約 40+ 檔。
  - 既有 templates 的英文化牽動的不是「換幾個標題」，而是 `phase-spec.md` 9 段、`context-definition.md` 6 段、`aggregate-design.md` 9 段、`_index.md` 4 段、`lightweight-spec.md` 5 段，全部結構標題重命名，且每次重命名都會 cascade 到 `pr-review-checklist.md` / `finish-feature-flow.md` / `SKILL.md` 等引用處。
  - 一次性實施會出現「邊改 template、邊改 reference、邊更新 glossary」三線並行，回歸風險高。

建議切分：

| Wave | 範圍 | 依賴 | 預估規模 |
|---|---|---|---|
| Wave 1 | 新增 12 份 templates / scaffolding（含 README）及兩份維護契約檔（`TEMPLATE-COVERAGE.md`、`TEMPLATE-LANGUAGE-GLOSSARY.md`）；此階段不改既有檔 | 無 | 中 — 純新增 |
| Wave 2 | 既有 templates / scaffolding canonical English（`_index.md`、`phase-spec.md`、`lightweight-spec.md`、`context-definition.md`、`aggregate-design.md`、`CLAUDE.md`、`CLAUDE-md-snippet.md`、`Git-principles-*.md`）；同步更新 `TEMPLATE-LANGUAGE-GLOSSARY.md` | 依賴 Wave 1 提供 glossary 起點 | 大 — 結構性改名集中爆發 |
| Wave 3 | references cascade 更新（`init-project-flow.md`、`new-feature-flow.md`、`modify-existing-flow.md`、`finish-feature-flow.md`、`drift-verification.md`、`pr-review-checklist.md`、`git-integration.md`、`ddd-modeling-guide.md` Core）+ SKILL.md cascade | 必須 Wave 2 結束（依賴 canonical names） | 中 |
| Wave 4 | `lightweight-spec.md` 加 Implementation Tasks（含 modify-existing-flow.md task checklist 流程更新）；可與 Wave 3 平行也可緊跟 | Wave 2 | 小 |

- Implement Review 階段：Wave 2 / Wave 3 各自獨立 review session 可較好控制 cascade 漏網。Wave 1 / Wave 4 可合併 review。

---

## 審查備註

- 本輪以單份 draft proposal review 為範圍，未對任何實作檔做格式 / 命名審查，只做設計決策、影響範圍準確性、與 P001-P012 機制相容性。實際英文化結果（哪個 Chinese 標題對應哪個 canonical English 名）必須在 R8b-Approve / Implement Review 階段由設計者確認。
- F-08 標 Question 而非 Major：drift-verification 既有演算法是否會因 `dflow:section` 出現而誤判，需設計者實作 `templates/rules.md` 的 `Behavior anchor` 欄位範例後才能判定，本輪不擅自升級。
- F-15 標 Question：anchor versioning 屬於遠期維護議題，非當下實施障礙，由設計者決定是否在 P013 階段一併解決。
- F-10 / F-13 / F-16 / F-17 標 Question：屬於文件交叉確認題，需設計者確認意圖（接管 backlog § 1 / § 4？要不要把 ddd-modeling-guide / git-integration 列入？P010 baseline 修補意圖？），不適合 reviewer 單方判定。
- 未發現 Critical：P013 未破壞任何既有 P001-P012 機制；anchor 機制與 BR-ID drift-verification 是並存而非取代；mandatory baseline 修補可能擴大 P010 但不破壞 P010；canonical English 結構性語言原則與 backlog § 1 / § 4 路線重疊（F-10）但內容方向一致。

---

**跨輪議題**：
- 審查過程中觀察到的兩條跨輪議題已寫入 `reviews/cross-round-notes.md`（**注意**：reviewer 本輪不修改任何檔；以下為**建議**新增內容，待 R8b-Approve session 或下一輪 reviewer 統合後再寫入）：
  - 議題候選 A：`templates/CLAUDE.md` 與 `scaffolding/CLAUDE-md-snippet.md` 的 H2 navigation 契約（P007c）vs canonical English structural language（P013）兩者張力，未來若做 npm 散佈、與既有用戶 `CLAUDE.md` 合併時會再次浮現。
  - 議題候選 B：references 引用 template section name 的 cascade 維護（pr-review-checklist / finish-feature-flow / SKILL.md / ddd-modeling-guide），是否該設一份「reference ↔ template section name 索引表」避免未來再次漏列？此屬於 P013 後的長期維護議題。
