# Round 8a Review Report — Draft Proposal Review (PROPOSAL-012)

**審查範圍**: PROPOSAL-012-distribution-friendly-paths（draft）
**審查類型**: Draft proposal review（無實作對照；單一 proposal、focus 在影響範圍與邊界）
**審查日期**: 2026-04-27
**審查者**: Claude Opus 4.7

---

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| 10 | 0 | 3 | 5 | 2 |

**建議下一步**: revise-and-resubmit

> 「建議下一步」是 Reviewer 的初判；最終 draft → approved 決定由 R8a-Approve session 做，本輪不定。
>
> 主要修訂訴求：
> 1. 補納 `planning/project-review-brief.md` 4 處過時引用（F-01）
> 2. `scaffolding/CLAUDE-md-snippet.md` 描述補上 directory tree 層的調整需求（F-02）
> 3. 明示對既有 `_共用/` 內部專案（Obts 等）的相容策略歸屬與當下立場（F-03）
>
> 三項都屬「描述補強 / 邊界顯式化」級別，沒有設計層面的根本問題。Critical 為 0；P012 的核心方向、§1 政策定義、§2 路徑選擇、§6 P009 相容皆穩固。

---

## Findings

### F-01 `planning/project-review-brief.md` 4 處 `_共用` 過時引用未納入影響範圍

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-012 § 影響範圍`、`§ 不納入影響範圍`
  - 現況對照：`planning/project-review-brief.md:51`、`:59`、`:111`、`:367`
**維度**: Dimension 3
**現況**:
- Line 51：「目前正式 flow 仍使用 `specs/_共用/`。P012 若通過，machine-facing 路徑會改為 `specs/shared/`；下方先用 `shared/` 表示目標方向，並在註解標明目前名稱。」
- Line 59 / 111：兩個 directory tree 註解皆寫「# 目前 flow 寫作 specs/_共用/；P012 後預期改名」
- Line 367：第 10 節 P012 候選議題段落「`specs/_共用/` 是 machine-facing path，不利於公開散佈與跨工具使用。」

這些是前瞻性筆記，刻意把 `_共用/` 當成「目前狀態」來描述、用來比對 P012 落地後的目標狀態。
**問題**: P012 一旦 implemented，line 51 / 59 / 111 的「目前正式 flow 仍使用」、「目前 flow 寫作 specs/_共用/」描述就會變成事實錯誤；line 367 的「是 machine-facing path」（現在式）也會與現況脫節。但 P012 § 影響範圍未列入此檔，§ 不納入影響範圍也未說明此檔該如何處理（保留為歷史脈絡？還是同步重寫？）。R8a 的 prompt 也明確點名此檔。
**建議**: 二擇一處理：
  1. **納入 § 影響範圍**：列為「修改」，描述「P012 落地後將 line 51 / 59 / 111 的『目前 flow 寫作 _共用/』描述改寫成『目前 flow 寫作 shared/』；line 367 P012 候選議題段可保留作為過程紀錄，加上『（已由 P012 處理）』註記」；或
  2. **納入 § 不納入影響範圍**：明示「`planning/project-review-brief.md` 是暫時性工作筆記（其檔頭已自陳為『暫時性工作筆記，不是正式 proposal』），P012 落地後其前瞻性註解自然成為歷史脈絡；不強制重寫」。

無論採哪個，必須在 P012 中對此檔有明確處置，否則 implementer grep `_共用` 時會碰到此檔卻不知道該不該改。

---

### F-02 `scaffolding/CLAUDE-md-snippet.md` 描述未涵蓋 directory tree 層的同步調整

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-012 § 影響範圍`（WebForms `scaffolding/CLAUDE-md-snippet.md` 列）、`§4 更新 skill 與 scaffolding 內的引用`
  - 現況對照：`sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md:44, 48–69, 115–117, 138`
**維度**: Dimension 3（D3-a）
**現況**:
WebForms `scaffolding/CLAUDE-md-snippet.md` 含兩類引用：
- **inline 路徑引用（5 處）**：lines 44, 115, 116, 117, 138 — 寫作 `specs/_共用/_overview.md`、`specs/_共用/Git-principles-{gitflow|trunk}.md`、`specs/_共用/_conventions.md`（這是 R7 F-03 修正後的形式）
- **directory tree 範例（lines 48–69）**：把 `_overview.md`、`_conventions.md`、`Git-principles-*.md` 列為 `specs/` 的**直接**子節點（沒有 `_共用/` 中間層），是 R7 F-03 當時沒有同步處理的殘留不一致：
  ```
  specs/
  ├── _overview.md              # 系統現況與遷移策略
  ├── _conventions.md           # 規格撰寫慣例
  ├── Git-principles-*.md       # Git 規範（gitflow 或 trunk 版）
  ```

P012 § 影響範圍對此檔的描述是「專案內引用路徑改為 `specs/shared/...`」。
**問題**:
- 「引用路徑」一詞在直覺上只涵蓋 inline 路徑引用（lines 44, 115–117, 138），不一定明示包含 directory tree 範例。
- Implementer 可能只 grep `_共用` 並替換為 `shared`，這會讓 5 處 inline 引用變成 `specs/shared/...`，但 directory tree 仍維持「`_overview.md` 為 `specs/` 直接子節點」的舊樣貌——P012 落地後依然會出現 R7 F-03 同類型的 tree-vs-actual-write-path 不一致（這次是 tree 顯示 `specs/_overview.md` vs init 實際寫入 `specs/shared/_overview.md`）。
- 對比同檔 § 影響範圍對 `SKILL.md` 與 `templates/CLAUDE.md` 的描述「如 Project Structure 範例列出 `_overview.md` / `_conventions.md`，同步調整」（明示 tree 要動），`scaffolding/CLAUDE-md-snippet.md` 的描述措辭不對等。
**建議**: § 影響範圍中 `scaffolding/CLAUDE-md-snippet.md` 的描述補上 tree 層：
  > 「inline 路徑引用（lines 44, 115–117, 138）改為 `specs/shared/...`；directory tree 範例（lines 48–69）將 `_overview.md` / `_conventions.md` / `Git-principles-*.md` 從 `specs/` 直接子節點移到 `specs/shared/` 子目錄之下，與 init-project-flow.md 實際寫入路徑一致」。

Core 版同檔僅 line 145 一處 inline 引用、無 directory tree 範例，描述「同 WebForms」需要相對應加註「Core 僅 line 145 inline 引用；tree 不適用」。

---

### F-03 對既有內部專案（Obts 等）採用 `_共用/` 的相容策略未明示歸屬

**嚴重度**: Major
**對照**:
  - Proposal 內部：`PROPOSAL-012 § 不納入影響範圍`、`§5 明確保留文件內容語言為 out of scope`
  - 邊界對照：`planning/public-distribution-backlog.md § 5 Legacy / Internal Compatibility`
**維度**: Dimension 5
**現況**:
- P012 § 5 列出 6 項 out-of-scope，但**沒有一條**處理「既有採用 `_共用/` 結構的內部專案在 P012 落地後會發生什麼事」。
- Backlog § 5 是這個議題的家，明列三個子問題（是否只讀不寫 / 是否 migration guide / 是否 init-project 偵測舊路徑），但 P012 並未在任何位置 cross-reference 至 backlog § 5。
- P012 § 預估工作量段隱含假設「path rename 風險低」，但這是站在「修改 skill 自己」的視角；對「既有用 `_共用/` 的內部專案」視角的影響沒有評估。

實務上 P012 落地後的可預期狀態：
- 新專案 `/dflow:init-project` 會寫到 `specs/shared/`
- 既有 Obts-style 專案的 `specs/_共用/` 不會被 init 覆蓋（init 設計上只填 gap）
- 但若既有專案重跑 init，因為 file-list preview 用新路徑判斷「缺什麼」，可能會在同一個專案下寫出 `specs/shared/_overview.md`、與既有 `specs/_共用/_overview.md` 並存，造成雙來源混淆
- 既有專案的 root `CLAUDE.md`（若已合併 R7 F-03 修正後的 snippet）內含的「`specs/_共用/Git-principles-*.md`」等指引也會與新 init 流程不一致
**問題**: P012 沒有顯式表達「對既有 `_共用/` 內部專案的相容性問題刻意延後到 backlog § 5」這個邊界決策，也沒有警示 implementer 此風險。實施前若不釐清，可能出現兩種不良結局：
  1. Implementer 自行加碼（例如在 init-project 加偵測 / migration），偷渡進 P012；
  2. 不加碼，但 release notes 沒提這個風險，使用者（如 Obts 團隊）踩到才發現。
**建議**: 在 P012 § 5 / § 不納入影響範圍 補一條：
  > 「**既有採用 `specs/_共用/` 的內部專案（如 Obts）相容性策略**：是否提供 read-only 讀取舊路徑、是否提供 migration guide、是否讓 `/dflow:init-project` 偵測舊路徑提示，皆延後至 `planning/public-distribution-backlog.md § 5 Legacy / Internal Compatibility` 處理。P012 落地後，既有 `_共用/` 路徑的目錄不會被 Dflow 主動讀取（Dflow 流程不在 runtime 讀 shared docs），但若該專案重跑 init 流程，可能在同一專案內並存 `_共用/` 與 `shared/`，需由該專案手動整理或等 backlog § 5 明朗後處理。」

可順手在 P012 § 預估工作量補一句：「對既有內部專案的影響評估不在本 proposal 範圍。」

---

### F-04 兩版 `templates/CLAUDE.md` 的 § 影響範圍描述用同一語句，但 Core 版實際無 tree 可動

**嚴重度**: Minor
**對照**:
  - Proposal 內部：`PROPOSAL-012 § 影響範圍`（兩列 `templates/CLAUDE.md`）
  - 現況對照：`sdd-ddd-webforms-skill/templates/CLAUDE.md:19–39`、`sdd-ddd-core-skill/templates/CLAUDE.md:36–57`
**維度**: Dimension 3（D3-d）
**現況**:
- WebForms `templates/CLAUDE.md` lines 19–39 directory tree **有**列 `_overview.md`、`_conventions.md` 為 `specs/` 直接子節點。
- Core `templates/CLAUDE.md` lines 36–57 directory tree **沒有**列 `_overview.md`、`_conventions.md`（只列 `domain/`、`features/`、`architecture/`）。
- 兩者皆無 `_共用` 字串。
- P012 § 影響範圍對 WebForms 寫「專案結構範例如列出 `_overview.md` / `_conventions.md`，改放 `specs/shared/`」，對 Core 寫「同 WebForms」。
**問題**: 「同 WebForms」隱含 Core 版會有對應修改，但 Core tree 沒有 `_overview.md` / `_conventions.md` 需要動，等於 Core 版實質上是 no-op（最多只是「檢查無事可改」）。把 Core 標為「修改」會讓 implementer 預期有東西可改，找不到時可能困惑。
**建議**: 將 Core `templates/CLAUDE.md` 一列的變更類型改為「檢查」，描述改為「Core 版 tree 不列 `_overview.md` / `_conventions.md`，預期無需修改；若實作時發現相關引用，依 WebForms 同方式調整」。

---

### F-05 § 影響範圍對 init-project-flow.md 的描述用「write rules」一詞與 §3 不對齊

**嚴重度**: Minor
**對照**:
  - Proposal 內部：`PROPOSAL-012 § 影響範圍`（`references/init-project-flow.md` 兩列）、`§3 更新 /dflow:init-project 的檔案預覽與寫入規則`
**維度**: Dimension 2
**現況**:
- § 影響範圍寫「Q5、file-list preview、**write rules**、results report 中的 `specs/_共用/` 改為 `specs/shared/`」
- §3 列出的具體段落是「Step 2 Q5 / Step 3 file-list preview / Step 4 `CLAUDE.md` special handling / Step 5 results report」
- 「write rules」對應到 §3 哪一段並不直觀（最接近的是 Step 4 CLAUDE.md special handling，但 §3 用「special handling」措辭，不叫「write rules」；標題 §3 才是「檔案預覽與寫入規則」）。
**問題**: 措辭不對齊增加 implementer 對應難度；同時可能讓 reviewer 誤以為還有「write rules」這個獨立段落需要處理。
**建議**: § 影響範圍措辭與 §3 對齊：「Step 2 Q5、Step 3 file-list preview、Step 4 `CLAUDE.md` special handling、Step 5 results report 中的 `specs/_共用/` 改為 `specs/shared/`」。

---

### F-06 § 影響範圍對 `planning/public-distribution-backlog.md` 的列入意義不清

**嚴重度**: Minor
**對照**:
  - Proposal 內部：`PROPOSAL-012 § 影響範圍`（最後一列）
  - 現況對照：`planning/public-distribution-backlog.md:61, 89`
**維度**: Dimension 3（D3-a）、Dimension 4
**現況**:
- § 影響範圍最後一列：`planning/public-distribution-backlog.md` | 變更類型「已新增 / 可能補充」 | 描述「保留 npm 前處理的內容語言與 localization 議題；本 proposal 只引用，不實作其中項目」
- 該檔現有 `_共用/` 引用兩處（lines 61、89），皆是 backlog 條目刻意保留的「legacy path」描述，不應更動。
- 描述同時說「只引用，不實作其中項目」（暗示 P012 對此檔無寫入動作）與「可能補充」（暗示有可能補東西），語義矛盾。
**問題**: § 影響範圍應該是「P012 會修改的檔案」清單，但這列的描述把 backlog 視為「邊界對照檔」、不是真的修改。把它放在 § 影響範圍會讓 implementer 困惑「我到底要不要動 backlog？」。同時 backlog § 5 已是 P012 § 不納入影響範圍 row 5（根目錄命名）的歸屬地，但本列描述把 backlog 講成「保留 localization 議題」，與 § 不納入影響範圍 row 5 的職責切分不完全對齊。
**建議**: 二擇一：
  1. 將此列移出 § 影響範圍，改於 § 關聯的 Proposal / 文件 段落以「邊界 / 引用」關係列出（事實上 P012 § 關聯的 Proposal 已有此檔，本列實屬重複）；
  2. 或保留在 § 影響範圍，但變更類型改為「不變動（僅引用）」並把描述精準成「P012 不修改此檔；引用其 § 1（內容語言）/ § 2（npm/CLI）/ § 5（legacy compat）/ § 6（根目錄命名）作為 out-of-scope 邊界」。

建議採方案 1，§ 影響範圍只列「真的會被改的檔」。

---

### F-07 § 不納入影響範圍 對 P013 與 backlog 的職責歸屬可更顯式

**嚴重度**: Minor
**對照**:
  - Proposal 內部：`PROPOSAL-012 § 不納入影響範圍` row 3、`§ 關聯的 Proposal`
  - 邊界對照：`PROPOSAL-013 § 提議的解法 §1 / §1.1`、`planning/public-distribution-backlog.md § 1, § 4`
**維度**: Dimension 4
**現況**:
- § 不納入影響範圍 row 3：「`templates/_index.md`、`templates/phase-spec.md`、`templates/lightweight-spec.md` 的中文 heading | 文件內容語言延後到 npm 前整體決策」
- 此議題實際上同時被 P013 § 1 / § 1.1（canonical English template + Template Language Glossary）承接、與 backlog § 1（文件內容語言策略）/ § 4（Template Language Glossary）對應
- P012 此列只說「延後到 npm 前整體決策」，沒有明確指向 P013 或 backlog
**問題**: Reviewer / 未來 implementer 看到這條時，無法直接知道該議題的歸屬地是哪裡——尤其 P013 已經 draft 中且明確處理 canonical English template structural language，P012 再說「延後到 npm 前」會讓人誤以為這是純 backlog 議題、不知道 P013 已在處理。
**建議**: row 3 描述補上歸屬指引，例如：
  > 「文件內容語言策略由 PROPOSAL-013（canonical English template + `TEMPLATE-LANGUAGE-GLOSSARY.md`）處理；雙語 template set 等更深的 localization 議題延後到 `planning/public-distribution-backlog.md § 1, § 4`」

---

### F-08 §1 政策表「使用者提供的 feature slug」與 §6 P009 相容說明措辭微差

**嚴重度**: Minor
**對照**:
  - Proposal 內部：`PROPOSAL-012 §1 政策表 row 4`、`§6 與 P009 slug 語言規則保持相容`
**維度**: Dimension 2
**現況**:
- §1 表 row 4：「使用者提供的 feature slug | 跟隨討論語言 | 沿用 P009 決議 8：中文討論可產生中文 slug；這是使用者語境，不是 Dflow 固定系統路徑」
- §6 列舉保留的中文 slug 範例：`specs/features/active/SPEC-20260421-001-報表調整/`、`feature/SPEC-20260421-001-報表調整`
- §6 同時提到「`SPEC-ID` 與結構詞仍是英文 / ASCII」「slug 是 feature 名稱，不是 Dflow 固定系統目錄」
**問題**:
- §1 政策表第二欄「跟隨討論語言」措辭與 P012 整體「machine-facing default English」基調有微小張力：feature 目錄路徑（`specs/features/active/{slug}/`）本身仍屬 machine-facing path，但其中 `{slug}` 段允許中文。對非中文使用者而言，這仍會在 path 中看到中文字元。這個取捨 §6 解釋了（「slug 是使用者語境」），但 §1 政策表沒有把這個取捨明示，可能讓 reader 第一眼看到表時覺得「machine-facing English」與「slug 跟隨討論語言」自相矛盾。
- 這不是設計缺陷（P009 決議 8 已 implemented，P012 刻意相容是正確的），只是表達清晰度。
**建議**: §1 政策表 row 4 的「說明」欄微調，加一句聚焦使用者選擇權：
  > 「沿用 P009 決議 8。`SPEC-ID` 與目錄結構詞仍 ASCII；slug 段是『使用者命名空間』，不是 Dflow 固定系統路徑——使用者可自選用 ASCII slug 以利國際化，但 Dflow 不強制。未來若公開散佈需要 `--ascii-slug` 選項，可另案處理（已記入 §6）。」

---

### F-09 「路徑英文化但 template 內部仍中文 heading」的轉場一致性觀感未提及

**嚴重度**: Question
**對照**:
  - Proposal 內部：`PROPOSAL-012 § 問題描述`、`§ 不納入影響範圍 row 3`
  - 邊界對照：`PROPOSAL-013 § 1.1`
**維度**: Dimension 5
**現況**:
- P012 落地後，使用者在新專案會看到 `specs/shared/` 這條英文路徑，打開 `specs/shared/_overview.md` 卻是中文標題與內容；同樣地 `specs/features/active/{SPEC-ID}-{slug}/phase-spec-*.md` 模板內部仍有「目標與範圍」「行為規格」「實作任務」等中文 heading。
- P012 問題描述 line 51 已說「本問題不等於『現在要把所有文件內容英文化』」，是有意識的取捨。
- P013 §1.1 引入 `TEMPLATE-LANGUAGE-GLOSSARY.md` 與 canonical English heading 政策，是真正解決一致性的 wave。
**問題**: P012 落地到 P013 落地之間會存在一段「path 英文 / template 內部中文」的過渡期。對既有內部團隊（已習慣中文）影響不大，但對任何在這段期間採用 Dflow 的新使用者而言會有「為什麼路徑英文，內容卻中文」的觀感落差。P012 沒有在 § 問題描述或 § 風險中提到這段過渡會存在、也沒有 cross-reference 至 P013 作為下一個 wave 處理。
**建議（需釐清）**: 需釐清——P012 是否願意在 § 問題描述末段（line 51 附近）多加一句說明：
  > 「需注意：P012 只處理路徑與檔名；落地後 `specs/shared/_overview.md` 等檔案內容仍是繁體中文 heading，這個 path 英文 / content 中文的過渡狀態屬有意延後處理，由 P013（canonical English template structural language）後續整體解決。」

如審查者認為這是「預期內的過渡狀態、不需贅述」，本 finding 可 reject；如希望 P012 對讀者更體貼，則 accept。

---

### F-10 CHANGELOG 歷史條目避免改動的規則可更顯式

**嚴重度**: Question
**對照**:
  - Proposal 內部：`PROPOSAL-012 § 影響範圍`（CHANGELOG 列）、`§ 預估工作量`
  - 現況對照：`CHANGELOG.md:17, 61`
**維度**: Dimension 3（D3-a）
**現況**:
- CHANGELOG.md 既有兩處 `_共用` 引用（line 17 R7 F-03 紀錄、line 61 P010 init-project 紀錄），都是歷史條目，不應改動。
- P012 § 預估工作量段有警示：「需仔細 grep，避免殘留 `specs/_共用/` 或錯誤地改到歷史紀錄語境」——這是泛指原則。
- § 影響範圍 CHANGELOG 列的描述只寫「實作時新增一段，說明 `specs/_共用/` → `specs/shared/` 的路徑政策變更」，沒有顯式「歷史條目保持原樣」規則。
**問題**: implementer 若做 grep-and-replace 全域替換，會把 line 17 / 61 的歷史描述（「specs/_overview.md → 加 `_共用/` 前綴」「`specs/_共用/CLAUDE-md-snippet.md`」）改成 `specs/shared/...`，破壞歷史紀錄真實性。「§ 預估工作量段已警示」的位置略偏，不在 § 影響範圍 CHANGELOG 列旁邊。
**建議（需釐清）**: 需釐清——是否在 § 影響範圍 CHANGELOG 列描述加一條顯式規則：
  > 「**歷史條目保持原樣**：CHANGELOG.md 既有 R7 / P010 條目（line 17、61）對 `specs/_共用/` 的引用是當時的事實紀錄，**不**改寫；P012 只在 CHANGELOG.md 末尾**新增**一段紀錄路徑政策變更。」

是否接納由 R8a-Approve 判斷；reviewer 認為加上去更穩妥。

---

## 影響範圍對照表

對 P012 § 影響範圍 列的每個檔執行 `_共用` 字串檢查，回填表格：

| 檔案 | P012 列入？ | 現況 `_共用` 出現次數 | 實際出現位置（行號） | 一致？ | 對應 Finding |
|---|---|---|---|---|---|
| `sdd-ddd-webforms-skill/SKILL.md` | ✓ | 1 | line 386 | ✓（描述涵蓋 line 386 + Project Structure tree lines 252–253 同步調整，正確） | — |
| `sdd-ddd-core-skill/SKILL.md` | ✓ | 1 | line 455 | ✓（描述 + Project Structure tree lines 268–269） | — |
| `sdd-ddd-webforms-skill/references/init-project-flow.md` | ✓ | 16 | line 124, 125, 131, 132, 194, 195, 196, 197, 198, 216, 217, 289, 292, 326, 327, 354, 377（含 line 376 帶引號小段；計 16 處） | ⚠（描述用「write rules」與 §3 措辭不對齊） | F-05 |
| `sdd-ddd-core-skill/references/init-project-flow.md` | ✓ | 17 | line 133, 134, 140, 141, 210, 211, 212, 213, 214, 234, 235, 307, 310, 351, 352, 382, 407 | ⚠（同上） | F-05 |
| `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md` | ✓ | 5 | line 44, 115, 116, 117, 138 | ⚠（inline 路徑覆蓋；directory tree lines 48–69 未被描述明示） | F-02 |
| `sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md` | ✓ | 1 | line 145 | ✓（單一 inline；無 directory tree 範例需動） | — |
| `sdd-ddd-webforms-skill/templates/CLAUDE.md` | ✓ | 0 | （無 `_共用` 字串；但 directory tree lines 19–39 列 `_overview.md` / `_conventions.md` 為 `specs/` 直接子節點，需移到 `shared/` 之下） | ✓（描述「如列出 `_overview.md` / `_conventions.md`，同步調整」涵蓋此情況） | — |
| `sdd-ddd-core-skill/templates/CLAUDE.md` | ✓ | 0 | （無 `_共用` 字串；directory tree lines 36–57 不列 `_overview.md` / `_conventions.md`） | ⚠（「同 WebForms」描述對 Core 不準；Core tree 無對應條目可動，實質 no-op） | F-04 |
| `README.md` | ✓（"可能只需確認"） | 0 | （無 `_共用` 字串；無 `specs/_overview` / `specs/_conventions` 等變體） | ✓（描述準確：確實無顯式路徑） | — |
| `CHANGELOG.md` | ✓ | 2 | line 17、line 61（皆歷史條目） | ⚠（描述未明示「歷史條目不改」） | F-10 |
| `planning/public-distribution-backlog.md` | ✓（"已新增 / 可能補充"） | 2 | line 61、line 89（皆 backlog 條目刻意保留為 legacy path 描述，**不應更動**） | ⚠（列在 § 影響範圍語義不清，描述同時暗示「不實作」與「可能補充」） | F-06 |
| `planning/project-review-brief.md` | ✗ | 4 | line 51、line 59、line 111、line 367 | ✗（**遺漏**：P012 落地後此檔的「目前 flow 寫作 `_共用/`」前瞻性註解全部過時） | F-01 |

> ✓ = 一致；✗ = 應列入但未列；⚠ = 列入但描述需修正 / 列為「修改」但實際只需「檢查」 / 描述未明示子情境

備註：經整檔 grep 驗證，repo 中其他出現 `_共用` 的位置皆屬「歷史 review 文件 / R7 prompts / V2 對照引用 / 繁中版 skill / tutorial」性質（如 `reviews/round-7-*`、`reviews/prompts/round-7-*`、`reviews/field-reference-candidates.md`、`tutorial/*`、`*-skill-tw/`、`reviews/prompts/round-8a-review.md` 本身），按 R8a prompt § D「不要讀的東西」與 P012 § 不納入影響範圍 邊界，皆不在本輪審查或本 proposal 處置範圍。

---

## 邊界對照備註

| 邊界檢查 | P012 描述位置 | 對方檔說法 | 一致？ | 對應 Finding |
|---|---|---|---|---|
| `_共用` 內容語言策略延到 backlog | P012 §5、§ 不納入影響範圍 row 3 | `planning/public-distribution-backlog.md § 1`（文件內容語言策略）、`§ 4`（Template Language Glossary） | ⚠（P012 未顯式 cross-reference 至 backlog § 1, § 4，亦未指向 P013 §1.1） | F-07 |
| 根目錄命名（`specs/` vs `dflow/`）延後 | P012 § 不納入影響範圍 row 5、§ 關聯的 Proposal「後續『Dflow 文件根目錄命名策略』」 | `planning/public-distribution-backlog.md § 6`（Dflow 文件根目錄命名策略） | ✓（P012 § 5 row 5 描述明確指向 backlog） | — |
| 樣板覆蓋與 canonical English 由 P013 處理 | P012 § 關聯的 Proposal「後續『System Document Template Coverage』proposal」 | `PROPOSAL-013 § 不納入影響範圍`（明確說 `specs/shared/` 路徑變更由 P012 處理）、`§ 關聯的 Proposal`（PROPOSAL-012：前置） | ✓（雙向一致） | — |
| Slug 跟隨討論語言（P009 決議 8）保留 | P012 §1 政策表 row 4、§6 | `PROPOSAL-009 § H 決議 8`（已 implemented，內部團隊實戰背書） | ✓（核心相容性正確；僅措辭可微調） | F-08 |
| 既有內部專案 `_共用/` 相容性 | （P012 未明示） | `planning/public-distribution-backlog.md § 5 Legacy / Internal Compatibility` | ✗（P012 未顯式 cross-reference） | F-03 |
| 繁中版同步延到 Closeout | P012 § 不納入影響範圍 row 1 | `proposals/review-workflow.md § 七 Post-Review Closeout C1`（繁中版同步） | ✓（路徑指引方向正確） | — |
| Tutorial 重建延到 Closeout | P012 § 不納入影響範圍 row 2 | `proposals/review-workflow.md § 七 Post-Review Closeout C2`（Tutorial 重建） | ✓ | — |

---

## 審查備註

- P012 整體設計穩固，5 個審查維度中：
  - **Dimension 1（問題具體性）**：✓ 問題描述具體（4 點理由 + 既有路徑舉例 + line 51 明確劃出與「文件內容英文化」的邊界）
  - **Dimension 2（解法具體性與內部一致性）**：✓ §1–§6 內部無矛盾；`shared/` vs `_shared/` 的選擇理由充分（與 features/ domain/ 既有英文目錄風格一致）；§6 對 P009 決議 8 的相容說明準確；唯措辭可微調（F-05、F-08）
  - **Dimension 3（影響範圍完整性）**：⚠ 主要扣分項——`planning/project-review-brief.md` 4 處過時引用未納入（F-01）；scaffolding `CLAUDE-md-snippet.md` 描述未涵蓋 directory tree 層（F-02）；Core `templates/CLAUDE.md` 描述失準（F-04）；`backlog` 列入語義不清（F-06）；CHANGELOG 歷史條目規則未顯式（F-10）
  - **Dimension 4（邊界守住）**：✓ 整體邊界乾淨（內容語言、npm、根目錄命名、繁中版、tutorial、P013 領域皆有明確劃線）；唯與 P013 / backlog 的 cross-reference 可更顯式（F-07）；對既有內部專案相容性的歸屬未提（F-03）
  - **Dimension 5（風險與假設）**：⚠ 對既有 `_共用/` 內部專案的相容性風險未評估（F-03）；過渡期「path 英文 / content 中文」的觀感落差未提（F-09）；其餘工作量「小」評估合理。

- 審查過程中**未**對照繁中版檔（`*-skill-tw/`）、tutorial、R1–R7 歷史 review 報告、V2 路徑、PROPOSAL-001 ~ PROPOSAL-011 內容，遵守 R8a prompt § D「不要讀的東西」邊界。

- 本 proposal 的 8 處需改動位置（F-01 / F-02 / F-03 必須處理；F-04 ~ F-10 視 R8a-Approve session 判斷）皆屬「描述補強 / 邊界顯式化」，不需要重構 P012 設計。修訂後再 review 一輪即可。

- 跨輪議題：審查過程順手觀察到的、不屬本輪 Finding 範圍的小問題（例如 R7 F-03 修正當時是否有同步處理 directory tree、scaffolding 與 templates/CLAUDE.md 的角色重疊度），暫不重複記錄；如需追蹤，可由本 review 另行整理進 `reviews/cross-round-notes.md`。

---

**跨輪議題**：本輪審查過程中無新發現的 P001-P011 或其他輪次議題（F-02 雖反映 R7 F-03 當時未同步處理 directory tree 的歷史殘留，但該議題自然會被 P012 的修訂所吸收，無需另記）。
