# Round 8a Implement Review Report — PROPOSAL-012 實作品質審查

**審查範圍**: PROPOSAL-012 實施產物（commit `2803f27` Round 8a Implement）
**審查類型**: Implement review（意圖 vs 實作對照）
**審查日期**: 2026-04-27
**審查者**: Cursor Claude Opus 4.7

---

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| 3 | 0 | 1 | 2 | 0 |

**建議下一步**: minor-fix

> 主要 fix 訴求：
> 1. 雙版 `SKILL.md § Project Structure` 目錄樹（WebForms line 252–253 / Core line 268–269）將 `_overview.md` / `_conventions.md` 從 `specs/` 直接子節點移到 `specs/shared/` 之下，與其他文件一致（F-01）
> 2. CHANGELOG R8a 段內自指 `line 17` / `line 61` 已因新段插入頂端而失效，需更新為實際對應的行號或改為內容指引（F-02）
>
> 兩項皆為實施層面的文件一致性問題，未涉及 P012 設計或 P010/P011 既有機制；其餘 Dimension 2 / 3 / 4 / 5 全綠。Critical 為 0；P012 核心意圖（machine-facing paths default to English、`specs/_共用/` → `specs/shared/`）已落地，雙版 init flow / scaffolding / templates/CLAUDE.md tree / planning brief / CHANGELOG 新段皆按 R8a Approve 決議完成。

---

## Findings

### F-01 雙版 `SKILL.md § Project Structure` 目錄樹未將 `_overview.md` / `_conventions.md` 移到 `specs/shared/` 之下

**嚴重度**: Major
**對照**:
  - 實施 vs P012：`PROPOSAL-012 § 影響範圍 列 1`（WebForms `SKILL.md`）+ `列 2`（Core `SKILL.md`）— 描述「`Templates & Scaffolding` 段目的地由 `specs/_共用/` 改為 `specs/shared/`；**如 Project Structure 範例列出 `_overview.md` / `_conventions.md`，同步調整**」 ↔ `sdd-ddd-webforms-skill/SKILL.md:252-253` + `sdd-ddd-core-skill/SKILL.md:268-269`
  - 實施 vs R8a Report：`reviews/round-8a-report.md:241-242` 影響範圍對照表已預示「描述涵蓋 line 386 + Project Structure tree lines 252–253 同步調整，正確」「描述 + Project Structure tree lines 268–269」 ↔ 實際實施只動 line 386 / 455
  - 跨版一致性：兩版 SKILL.md 同樣漏改（WebForms `:252-253` / Core `:268-269`），缺漏對稱
  - Regression：與 R7 F-03 修正的「tree-vs-actual-write-path 不一致」屬同類型；R8a F-02 對 `scaffolding/CLAUDE-md-snippet.md` 已預防同一問題，但 SKILL.md 漏網

**維度**: Dimension 1

**現況**:

WebForms `SKILL.md`（line 248-274 區域）的 Project Structure 範例：

```254:255:sdd-ddd-webforms-skill/SKILL.md
│   ├── _overview.md                  # System status & migration strategy
│   ├── _conventions.md               # Spec writing conventions
```

— 兩檔仍列為 `specs/` 直接子節點（line 252、line 253，緊接於 `specs/` 之後），**未**置於 `specs/shared/` 之下。

Core `SKILL.md`（line 264-294 區域）的 Project Structure 範例：

```268:269:sdd-ddd-core-skill/SKILL.md
│   ├── _overview.md
│   ├── _conventions.md
```

— 同樣的問題（line 268、line 269 緊接於 `specs/` 之後）。

對照同 commit 內已正確更新的位置：
- WebForms `SKILL.md:386`（Templates & Scaffolding 段）：「seeded by `/dflow:init-project` into a project's `specs/shared/` directory」 ✓
- WebForms `templates/CLAUDE.md:19-22`：tree 顯示 `specs/shared/_overview.md` ✓
- Core `templates/CLAUDE.md:36-39`：tree 顯示 `specs/shared/_overview.md`（per F-04 Path B 補檔）✓
- 雙版 `init-project-flow.md` 全部 `specs/shared/...` ✓
- 雙版 `scaffolding/CLAUDE-md-snippet.md` inline + tree 全部 `specs/shared/...` ✓

只有兩版 `SKILL.md` 的 Project Structure tree 漏改。

**問題**:

P012 §影響範圍 row 1 / row 2 描述明確包含「如 Project Structure 範例列出 `_overview.md` / `_conventions.md`，同步調整」這一條件子句，且兩版 Project Structure tree 確實列出這兩個檔案——條件成立、應同步調整。

CHANGELOG R8a 段的實施摘要寫的是「雙版 `SKILL.md § Templates & Scaffolding` 段路徑文字更新」，**只提到 § Templates & Scaffolding 段，沒有提到 § Project Structure 段**，與實際漏改現況一致。Implementer 視同只改了 line 386 / 455 一處，未處理 line 252-253 / 268-269。

落地後果：

1. **Reader confusion**：使用者讀 SKILL.md 看到 `specs/_overview.md`、`specs/_conventions.md` 直接掛在 `specs/` 下，但 `/dflow:init-project` 實際寫入 `specs/shared/_overview.md`、`specs/shared/_conventions.md`；同檔的 § Templates & Scaffolding 段（line 386 / 455）又寫 `specs/shared/`。同一檔案內三個來源不一致。
2. **R7 F-03 同類型 regression**：R7 F-03 處理過完全相同的 pattern（`scaffolding/CLAUDE-md-snippet.md` tree 顯示 `specs/_overview.md` 但 init flow 寫到 `specs/_共用/_overview.md`）；R8a F-02 預防 P012 後 scaffolding 出現同問題；但 SKILL.md 的 Project Structure tree 屬同一風險面，本輪未涵蓋。
3. **跨版一致性**：兩版同樣漏改，並非單版 oversight；對 implementer 而言應視為同一缺口。

**建議**:

對兩版 SKILL.md 的 Project Structure tree 各做一次小修正，將 `_overview.md` 與 `_conventions.md` 從 `specs/` 直接子節點移到新的 `specs/shared/` 子節點之下，與 `templates/CLAUDE.md` 雙版、`scaffolding/CLAUDE-md-snippet.md`（WebForms 版）的 tree 結構對齊。可順手檢查相鄰的註解風格與其他範本一致。

具體建議寫法（與雙版 `templates/CLAUDE.md` tree 一致）：

```
specs/
├── shared/                            # Project-level governance docs (seeded by /dflow:init-project)
│   ├── _overview.md                   # System status & migration strategy (WebForms) / Clean Architecture overview (Core)
│   └── _conventions.md                # Spec writing conventions
├── domain/
│   ...
```

修正後可順手再做一次 `rg "_overview\.md|_conventions\.md" sdd-ddd-{webforms,core}-skill/SKILL.md` 確認 tree 內無殘留 `specs/_overview.md` / `specs/_conventions.md` 平鋪結構。

---

### F-02 CHANGELOG R8a 段內自指「line 17 / line 61」於新段插入後失效

**嚴重度**: Minor
**對照**:
  - 實施 vs P012：`PROPOSAL-012 § 影響範圍 列 10`（CHANGELOG.md）描述「**歷史條目保持原樣**：line 17（R7 F-03 紀錄）、line 61（P010 init-project 紀錄）」 ↔ `CHANGELOG.md:26`（新 R8a 段內的自指）
  - 實施 vs R8a Decisions：`reviews/round-8a-decisions.md:83` F-10 accept 摘要明示「line 17（R7 F-03 紀錄）、line 61（P010 init-project 紀錄）」 ↔ 實際 line 17 / line 61 因新段插入而位移
  - 歷史保留 verify：實際 R7 F-03 紀錄現位於 `CHANGELOG.md:54`；P010 init-project 紀錄現位於 `CHANGELOG.md:98`（皆未被改動，per F-10 hardline ✓）

**維度**: Dimension 2

**現況**:

CHANGELOG.md 行 26（R8a 新段內）：

```26:26:CHANGELOG.md
**歷史條目保持原樣**（per F-10 accept）：CHANGELOG.md 既有 line 17（R7 F-03 紀錄）、line 61（P010 init-project 紀錄）對 `specs/_共用/` 的引用是當時的事實紀錄，**不改寫**；implementer 不應對 CHANGELOG 做全域 grep-and-replace。
```

但於 commit `2803f27` 將 R8a 新段（行 9–44）插入 CHANGELOG 頂部後，原本的 line 17 / line 61 已下移：
- 實際 R7 F-03 紀錄現位於 `CHANGELOG.md:54`（"**F-03 (Major, accept)**：WebForms `CLAUDE-md-snippet.md` ... 加 `_共用/` 前綴"）
- 實際 P010 init-project 紀錄現位於 `CHANGELOG.md:98`（"`CLAUDE.md` 特別處理：已存在則不覆寫，以 `specs/_共用/CLAUDE-md-snippet.md` 形式..."）
- 而現在的 line 17 是新 R8a 段的子項目（「雙版 `SKILL.md § Templates & Scaffolding` 段路徑文字更新」），line 61 是舊 R7 Implement Review Fix 段的影響檔案行（「`sdd-ddd-{webforms|core}-skill/references/git-integration.md`（F-02，頂部加 file history note）」），與所述「R7 F-03 紀錄」「P010 init-project 紀錄」對不上。

**問題**:

R8a 段內這段文字的本意是引導未來讀者「不要動歷史條目」並指明哪兩條是必須保留的歷史。**歷史條目本身是保留的**（R7 F-03 在 line 54、P010 在 line 98 皆未被改動，F-10 硬性要求滿足 ✓），但 R8a 段內的指示性文字已自指失效——若未來 reviewer 真要 verify「line 17 / line 61 沒被動」，照行號去看會看到完全不相關的內容。

這不是歷史改寫（沒有 Critical 等級的破壞），但是 implementer 在新段中複製貼上 P012 §影響範圍 描述（其行號是 R8a 修訂期、新段尚未插入時的位置）後沒有更新行號，屬於文件自我一致性的小瑕疵。

**建議**:

二擇一：

1. **更新行號**：將 R8a 段行 26 的「line 17（R7 F-03 紀錄）、line 61（P010 init-project 紀錄）」改為「line 54（R7 F-03 紀錄）、line 98（P010 init-project 紀錄）」（依新段插入後的實際行號）。
2. **改為內容指引**：將行號式自指改為內容式自指，例如「2026-04-24 R7 Implement Review Fix 段下的 F-03 條目、2026-04-24 R7 Wave 3 段下的 P010 init-project `CLAUDE.md` 特別處理條目」。**此方案較穩健**，未來 CHANGELOG 再增段時不會再次失效。

兩種方案皆屬一行內小修，不影響其他內容。

---

### F-03 CHANGELOG R8a 段「修改檔案（10 檔）」與 P012 §影響範圍 11 列的對應關係未顯式

**嚴重度**: Minor
**對照**:
  - 實施 vs P012：`CHANGELOG.md:28-34`（R8a 段「修改檔案（10 檔）」列表）↔ `PROPOSAL-012 § 影響範圍`（11 列）
  - 實施 vs R8a Decisions：`reviews/round-8a-decisions.md` 沒有對 CHANGELOG 列表的具體形式做要求；本項屬實施自我一致性

**維度**: Dimension 2

**現況**:

P012 §影響範圍表共 11 列（WebForms SKILL.md、Core SKILL.md、雙版 init-project-flow.md、雙版 scaffolding/CLAUDE-md-snippet.md、雙版 templates/CLAUDE.md、README.md、CHANGELOG.md、planning/project-review-brief.md）。

R8a CHANGELOG 段（line 28-34）「修改檔案（10 檔）」列出：

- `sdd-ddd-{webforms|core}-skill/SKILL.md`（2 檔）
- `sdd-ddd-{webforms|core}-skill/references/init-project-flow.md`（2 檔）
- `sdd-ddd-{webforms|core}-skill/scaffolding/CLAUDE-md-snippet.md`（2 檔）
- `sdd-ddd-{webforms|core}-skill/templates/CLAUDE.md`（2 檔）
- `planning/project-review-brief.md`（4 處更新）
- `CHANGELOG.md`（本段新增）

合計 10 檔（缺 README.md，因 README.md 經 verify 無 `_共用` 引用、無需修改，line 24 已單獨記述「README.md 確認」）。

**問題**:

「10 檔」與 P012 §影響範圍 「11 列」少 1 列；少的是 README.md（驗證通過、無修改）。對未來 reviewer 而言，這個數字不一致雖可從 line 24 推得（README 是 verify 不是 modify），但 R8a 段把「修改檔案」與「§影響範圍」的對應做得不顯式，需要交叉閱讀才能對齊。

不影響任何運行語義或其他文件，只是 implementer 自報的列表結構與 §影響範圍 對應關係不完全清楚。

**建議**:

可選擇性修整 CHANGELOG R8a 段「修改檔案」標題，例如改為「實際修改檔案（10 檔；§影響範圍 11 列中 README 經 verify 無需修改）」一句帶過即可。本項屬「文件可讀性」級別，不修也不影響本輪 P012 落地完整性。

---

## 影響範圍實施對照表（Dimension 1）

對 PROPOSAL-012 §影響範圍 的 11 列逐一 verify：

| P012 §影響範圍 列 | 檔案 | P012 描述 | 實施狀態 | 一致？ | 對應 Finding |
|---|---|---|---|---|---|
| 1 | `sdd-ddd-webforms-skill/SKILL.md` | Templates & Scaffolding 段路徑由 `specs/_共用/` 改為 `specs/shared/`；如 Project Structure 範例列出 `_overview.md` / `_conventions.md`，同步調整 | line 386 inline 已改；**line 252–253 Project Structure tree 未改**（仍為 `specs/_overview.md` / `specs/_conventions.md` 平鋪） | ⚠ | F-01 |
| 2 | `sdd-ddd-core-skill/SKILL.md` | 同 WebForms | line 455 inline 已改；**line 268–269 Project Structure tree 未改**（同問題） | ⚠ | F-01 |
| 3 | `sdd-ddd-webforms-skill/references/init-project-flow.md` | Step 2 Q5、Step 3 file-list preview、Step 4 `CLAUDE.md` special handling、Step 5 results report 中的 `_共用/` → `shared/` | 全部 17 處 `_共用/` → `shared/` 已改（line 124, 125, 131, 132, 194, 195, 196, 197, 198, 216, 217, 289, 292, 326, 327, 354, 377）；Step 4.3 「special handling」段（line 282-295）正確改為 `specs/shared/CLAUDE-md-snippet.md` | ✓ | — |
| 4 | `sdd-ddd-core-skill/references/init-project-flow.md` | 同 WebForms | 全部 17 處 `_共用/` → `shared/` 已改（line 133, 134, 140, 141, 210, 211, 212, 213, 214, 234, 235, 307, 310, 351, 352, 382, 407）；Step 4.3「special handling」段（line 300-313）正確 | ✓ | — |
| 5 | `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md` | inline 引用（line 44, 115–117, 138）改為 `specs/shared/...`；directory tree（line 48–69）將 `_overview.md` / `_conventions.md` / `Git-principles-*.md` 從 `specs/` 直接子節點移到 `specs/shared/` 之下 | inline 5 處（line 44, 115, 116, 117, 138 → 現位於 line 44, 116, 117, 118, 139）已改為 `specs/shared/...`；tree（line 48–69 → 現 line 48–77）已 restructure，`shared/` 子節點下含 `_overview.md` / `_conventions.md` / `Git-principles-*.md`（line 50–53） | ✓ | — |
| 6 | `sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md` | line 145 單一 inline 引用改為 `specs/shared/...`；無 tree 範例 | line 145 已改為「詳見 `specs/shared/Git-principles-{gitflow\|trunk}.md`」；無 tree 不適用 | ✓ | — |
| 7 | `sdd-ddd-webforms-skill/templates/CLAUDE.md` | directory tree（line 19–39）將 `_overview.md` / `_conventions.md` 從 `specs/` 直接子節點移到 `specs/shared/` 之下 | tree 已 restructure：`shared/` 出現於 line 20，`_overview.md` 在 line 21 與 `_conventions.md` 在 line 22（皆於 `specs/shared/` 子節點下） | ✓ | — |
| 8 | `sdd-ddd-core-skill/templates/CLAUDE.md` | directory tree（line 36–57）目前漏列 `_overview.md` / `_conventions.md`；P012 順手補上於 `specs/shared/` 之下 | tree 已 restructure：`shared/` 補上於 line 37，`_overview.md` 在 line 38、`_conventions.md` 在 line 39（與 WebForms tree 對稱，per F-04 Path B 補檔） | ✓ | — |
| 9 | `README.md` | 若提及 `specs/_共用/` 改為 `specs/shared/`；可能只需確認 | grep verify：無 `_共用` 字串、無 `specs/_overview` / `specs/_conventions` 等變體；無需修改（per CHANGELOG line 24 單獨記述） | ✓ | — |
| 10 | `CHANGELOG.md` | 檔尾**新增**一段；歷史條目（line 17、line 61）保持原樣 | 新段已加於頂部（line 9–44）；歷史 R7 F-03 紀錄（現於 line 54）、P010 init-project 紀錄（現於 line 98）內容皆未改動，`_共用` 引用保留為當時事實紀錄 | ✓（歷史保留）；⚠（新段內自指 line 17/61 因位移失效） | F-02 |
| 11 | `planning/project-review-brief.md` | line 51, 59, 111 的「目前 flow 寫作 `_共用/`」前瞻性註解改為「目前 flow 寫作 `shared/`」；§10 P012 候選議題段（line 367）加上「（已由 P012 處理）」註記 | line 51 已改為「目前正式 flow 使用 `specs/shared/`（由 P012 確立，2026-04-27 implemented）」；line 59 / 111 directory tree 直接顯示 `shared/`（去除前瞻性註解）；line 367 已加「（已由 P012 處理）」註記 | ✓ | — |

> ✓ = 一致；⚠ = 部分一致 / 子情境漏改；✗ = 漏改 / 與描述衝突

備註：

- 證據查核採以下指令／路徑：`rg "_共用" sdd-ddd-{webforms,core}-skill`（兩目錄各 0 命中）、`rg "_overview\.md|_conventions\.md" sdd-ddd-{webforms,core}-skill`（兩目錄殘留結構皆已對齊 `shared/`，僅 SKILL.md tree 兩列例外）。
- F-01 涉及 P012 §影響範圍 列 1 + 列 2 兩列；列入同一 finding 因為兩版症狀完全一致（缺漏對稱），不視為兩個獨立 finding。

---

## R8a Approve 決議落實對照表（Dimension 2）

對 R8a Decisions 中的 accept / accept-with-choice 共 8 項逐一 verify（reject 2 項不在實施範圍）：

| Finding | 決議類型 | 預期效果 | 實施狀態 | 一致？ | 對應 Implement Review Finding |
|---|---|---|---|---|---|
| F-01 (Path A) | accept-with-choice | `planning/project-review-brief.md` line 51, 59, 111, 367 過時引用更新；§10 P012 候選議題段加註「（已由 P012 處理）」 | line 51 改為現在式（含「2026-04-27 implemented」標註）；line 59 / 111 directory tree 直接顯示 `shared/`（前瞻性註解移除）；line 367 加上註記 | ✓ | — |
| F-02 | accept | `scaffolding/CLAUDE-md-snippet.md` 描述精準化（WebForms = inline 5 處 + tree 重構；Core = 僅 line 145 inline，tree 不適用） | PROPOSAL-012 §影響範圍 列 5 / 列 6 描述已精準化（已於 R8a Approve session 完成，本輪 verify）；實際實施符合（WebForms inline + tree 都改、Core 僅 line 145 改） | ✓ | — |
| F-04 (Path B) | accept-with-choice | Core `templates/CLAUDE.md` directory tree **補上**漏列的 `_overview.md` / `_conventions.md` 於 `specs/shared/` 下；WebForms 同步「移動」 | Core templates/CLAUDE.md line 37–39 補上 `shared/` + 兩檔（原 line 36–57 範圍漏列已修正）；WebForms templates/CLAUDE.md line 20–22 將 `_overview.md` / `_conventions.md` 從 `specs/` 直接子節點移到 `shared/` 下 | ✓ | — |
| F-05 | accept | `init-project-flow.md` 的章節名與 §3 對齊（Step 2 Q5 / Step 3 file-list preview / Step 4 CLAUDE.md special handling / Step 5 results report） | PROPOSAL-012 §影響範圍 列 3 描述已對齊；雙版 init-project-flow.md 各 step 結構與 P012 §3 對應正確（Step 2 § Q5 line 119–139 / line 128–148；Step 3 file-list preview line 149–242 / line 158–258；Step 4 special handling line 282–295 / line 300–313；Step 5 results report line 311–382 / line 334–413） | ✓ | — |
| F-06 (Path A) | accept-with-choice | 從 §影響範圍 移出 `planning/public-distribution-backlog.md`；保留於 §關聯的 Proposal | PROPOSAL-012 §影響範圍 11 列中已無 `public-distribution-backlog.md`；§關聯的 Proposal 仍保留該檔的「前置 / 邊界」描述（line 209） | ✓ | — |
| F-07 | accept | §不納入影響範圍 row 3 明示歸屬（PROPOSAL-013 + backlog § 1, § 4） | PROPOSAL-012 §不納入影響範圍 row 3 描述為「文件內容語言策略由 PROPOSAL-013（canonical English template + `TEMPLATE-LANGUAGE-GLOSSARY.md`）處理；雙語 template set 等更深的 localization 議題延後至 `planning/public-distribution-backlog.md § 1, § 4`」 | ✓ | — |
| F-08 | accept | §1 政策表 row 4 明示 slug 取捨 + ASCII slug 出口 | PROPOSAL-012 §1 政策表 row 4 描述已明示「沿用 P009 決議 8。`SPEC-ID` 與目錄結構詞仍 ASCII；slug 段是『使用者命名空間』，不是 Dflow 固定系統路徑——使用者可自選 ASCII slug 以利國際化，但 Dflow 不強制。未來若公開散佈需要 `--ascii-slug` 選項，可另案處理（已記入 §6）」 | ✓ | — |
| F-10 | accept | CHANGELOG **新增段**於檔尾／頂部；既有 line 17（R7 F-03）、line 61（P010 init-project）對 `_共用/` 的歷史引用**不改寫** | 新段已加於頂部（line 9–44）；歷史 R7 F-03 紀錄（現於 line 54）內容未動：「`init-project-flow.md` 實際產出 `specs/_共用/...`；... 加 `_共用/` 前綴」原樣保留；歷史 P010 init-project 紀錄（現於 line 98）內容未動：「`specs/_共用/CLAUDE-md-snippet.md`」原樣保留 | ✓（歷史保留 hardline 滿足）；⚠（自指 line 17/61 失效，內容對但行號錯） | F-02 |

> 8 項 accept / accept-with-choice 全部落實核心訴求。F-10 的「歷史條目不改」hardline 完全滿足；F-02 是其衍生小瑕疵（描述複製過來時行號未更新），不影響歷史保留本身。

---

## 跨版一致性對照表（Dimension 3）

| 配對 | 檢查點 | 一致？ | 對應 Finding |
|---|---|---|---|
| 雙版 SKILL.md | § Templates & Scaffolding 段措辭（line 384–396 / line 453–465） | ✓（兩版描述「seeded by `/dflow:init-project` into a project's `specs/shared/` directory」措辭完全一致；scaffolding 表項目對稱） | — |
| 雙版 SKILL.md | § Project Structure tree 內 `_overview.md` / `_conventions.md` 位置 | ⚠（兩版**同樣漏改**——皆仍將 `_overview.md` / `_conventions.md` 列為 `specs/` 直接子節點；缺漏對稱，但兩版同症狀） | F-01 |
| 雙版 init-project-flow.md | 全檔 `_共用/` → `shared/` 替換完整性 | ✓（WebForms 17 處 / Core 17 處全部替換完成；殘留檢查 0 命中；雙版對稱） | — |
| 雙版 scaffolding/CLAUDE-md-snippet.md | inline 引用 + tree restructure | ✓（WebForms inline 5 處 + tree restructure / Core 1 處 inline；雙版按各自描述完成；無 tree 的 Core 版未越界） | — |
| 雙版 templates/CLAUDE.md | tree 內 `shared/` 子節點下檔案順序與註解風格 | ✓（雙版皆於 `shared/` 子節點下列 `_overview.md` + `_conventions.md`；註解文字 WebForms「規格撰寫慣例與模板」vs Core「規格撰寫慣例」屬版本特異字微差，與其他段落 WebForms 略偏闡述風格一致） | — |
| 雙版 templates/CLAUDE.md | H2 結構（系統脈絡 / 開發流程） | ✓（兩版皆保留 P007c 雙 H2 segmentation；P012 範圍只動 directory tree 圖，未動 H2） | — |

---

## Regression check（Dimension 4）

| 既有機制 | 檢查結果 | 對應 Finding |
|---|---|---|
| P010 `/dflow:init-project` flow 連貫性 | ✓ — 雙版 init-project-flow.md 全部 `shared/` 後 Step 1–5 流程語意連貫；mandatory baseline 清單（WebForms：`features/{active,completed,backlog}/`、`domain/glossary.md`、`migration/tech-debt.md` + .gitkeep；Core：另含 `domain/context-map.md`、`architecture/tech-debt.md`、`architecture/decisions/README.md`）未被改動，與 P010 §3.1 / P010 R7 F-05 決議一致；`behavior.md` 不在 init 階段產生（F-05）規則保留 | — |
| P011 Git Flow Decoupling 不受影響 | ✓ — 雙版 `references/git-integration.md` 未在本輪修改範圍；`scaffolding/Git-principles-{gitflow,trunk}.md` 路徑由 `specs/_共用/` 改為 `specs/shared/` 與 P011 既有設計（Git Flow 從 skill 本體解耦、移到可選 scaffolding）一致；P011 Integration Commit Message 慣例段未受影響 | — |
| P007c CLAUDE.md H2 navigation 結構 | ✓ — 雙版 `templates/CLAUDE.md` 仍保留兩大 H2（`系統脈絡（What is this system?）` / `開發流程（How do we work?）`），P012 只動 § 系統脈絡 → § 目錄結構 內的 directory tree 圖；H2 標題、子標題（背景／架構／核心原則／Ceremony 三層／…）皆未動 | — |
| `_共用` 全域 grep 殘留檢查（skill 目錄） | ✓ — `rg "_共用" sdd-ddd-webforms-skill/`：0 命中；`rg "_共用" sdd-ddd-core-skill/`：0 命中。skill 目錄完全清淨 | — |
| `_共用` 全域 grep（其他預期保留位置） | ✓ — 殘留 `_共用` 僅於：(a) `reviews/` 各歷史審查文件（凍結，不動）；(b) `proposals/PROPOSAL-012-distribution-friendly-paths.md`（proposal 本體，刻意保留）；(c) `proposals/PROPOSAL-013-system-document-template-coverage.md`（P013 提案，本輪不審）；(d) `planning/public-distribution-backlog.md`（line 80, 108 legacy path / migration 描述，刻意保留）；(e) `planning/project-review-brief.md:367`（§10 P012 候選議題段，已加「已由 P012 處理」註記）；(f) `CHANGELOG.md`（line 9, 15, 18, 24, 26 新段；line 54, 98 歷史條目刻意保留）。皆屬合理保留語境 | — |
| 其他 references 檔（`new-feature-flow.md` / `modify-existing-flow.md` / `pr-review-checklist.md` / `drift-verification.md` / `git-integration.md` / `new-phase-flow.md` / `finish-feature-flow.md`）內 `_共用` 殘留 | ✓ — 全部 0 命中 | — |
| `specs/_overview` / `specs/_conventions` 等舊平鋪 path 變體殘留檢查（skill 目錄） | ⚠ — 雙版 SKILL.md 的 § Project Structure tree（WebForms `:252-253` / Core `:268-269`）仍以平鋪結構列出；其餘檔案（`templates/CLAUDE.md`、`scaffolding/CLAUDE-md-snippet.md`、`scaffolding/Git-principles-*.md`、`scaffolding/_overview.md`、`scaffolding/_conventions.md`、`init-project-flow.md`）皆已對齊 `specs/shared/` 或使用相對路徑 | F-01 |

---

## 邊界守住對照（Dimension 5）

| 邊界 | 檢查結果 | 對應 Finding |
|---|---|---|
| 未踩進 P013 範圍（template heading 英文化）| ✓ — 雙版 `templates/_index.md` / `templates/phase-spec.md` / `templates/lightweight-spec.md` / `templates/context-definition.md` / `templates/behavior.md` / `templates/aggregate-design.md`（Core）heading 全保留繁中（「目標與範圍」「行為規格」「實作任務」等），未被英文化；本輪未開啟 PROPOSAL-013 範圍 | — |
| 未踩進 P013 範圍（新增 template / TEMPLATE-COVERAGE.md / TEMPLATE-LANGUAGE-GLOSSARY.md）| ✓ — 雙版 `templates/` 與 `scaffolding/` 目錄無新增檔；無 `TEMPLATE-COVERAGE.md` 或 `TEMPLATE-LANGUAGE-GLOSSARY.md` 出現 | — |
| 未踩進 P013 範圍（template anchor）| ✓ — 雙版 templates 內無新增 `<!-- dflow:section X -->` anchor；既有 `<!-- 填寫時機：Phase N -->` 等 P004 既有 comment 全保留 | — |
| 未踩進 backlog 範圍（文件根目錄改名 `specs/` → `dflow/` 等）| ✓ — 文件根目錄仍為 `specs/`；backlog § 6 未被觸碰 | — |
| 未踩進 backlog 範圍（npm / package.json / CLI）| ✓ — 無新增 `package.json` / `npm-publish.yml` / `bin/` 等發布相關產物；backlog § 2 未被觸碰 | — |
| 未踩進 backlog 範圍（雙語 template set / `templates/en/` / `templates/zh-TW/`）| ✓ — 雙版 `templates/` 結構未拆分 en / zh-TW 子目錄；backlog § 1, § 4 未被觸碰 | — |

---

## 審查備註

- 本輪嚴格遵守審查邊界：不讀繁中版（`*-skill-tw/`）、tutorial、R1–R7 歷史 review 報告、`E:\工作區\SDD-Starter-Kit\` (V2)、PROPOSAL-013 / R8b 相關文件。grep `_共用` 命中的 reviews/round-7-* / round-8b-* / round-7-implement-review-* 等檔皆**僅看檔名與行號性質**（確認屬 frozen 歷史），未進入內容詳閱。
- F-01 是本輪唯一 Major finding。其性質與 R7 F-03 屬同類「tree-vs-actual-write-path 不一致」，且 R8a Reviewer（draft review 階段）已於 §影響範圍對照表預示「描述涵蓋 Project Structure tree lines 252–253 / 268–269 同步調整」，但 Implementer 在執行階段視同只動 § Templates & Scaffolding 段。從 implementer self-report（CHANGELOG R8a 段 line 16-23）可看出此 oversight：「雙版 `SKILL.md § Templates & Scaffolding` 段路徑文字更新」明確只列 § Templates & Scaffolding，未提 § Project Structure。
- F-02 是 F-10 「歷史條目保持原樣」的衍生小瑕疵——hardline（不改歷史內容）完全滿足，但描述複製貼上時 line 17 / line 61 未隨新段插入後的位置更新。屬一行內可修。
- F-03 屬可有可無級別，列入只是揭露 implementer self-report 與 P012 §影響範圍 對應關係的小不顯式（修不修不影響落地）。
- 整體評價：P012 核心意圖（machine-facing paths default to English、`specs/_共用/` → `specs/shared/`）已穩固落地；雙版 init flow / scaffolding / templates/CLAUDE.md tree / planning brief / CHANGELOG 新段皆按 R8a Approve 8 項決議完成；唯獨雙版 SKILL.md § Project Structure tree 漏改一處。修正 F-01（雙檔 4 行小修）即可進入 R8a 收尾。

---

**跨輪議題**：本輪 review 過程中順手觀察到的、不屬 P012 範圍的小問題（不佔本輪 Finding 數量；如需追蹤可補入 `reviews/cross-round-notes.md`）：

- `README.md:55, 79` 的 `git-flow-integration.md` 引用係 P011 rename 為 `git-integration.md` 之後未同步更新的舊檔名殘留（與本輪 P012 無關）。`planning/project-review-brief.md:296` 也已自陳觀察到「`AGENTS.md` 裡仍提到 `git-flow-integration.md`」。屬 P011 後 cleanup，可在 Closeout 期一併處理。
