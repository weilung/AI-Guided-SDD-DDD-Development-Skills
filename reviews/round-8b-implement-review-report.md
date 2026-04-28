# Round 8b Implement Review Report — 4 Sub-waves 實作品質審查

**審查範圍**: PROPOSAL-013 實施產物（commit chain `62640c7` → `0cd99fb` → `2aaba8b` → `61c0df4` → `12f552e`）
**審查類型**: Implement review（意圖 vs 實作對照 + 跨 sub-wave cascade + 設計決策執行品質）
**審查日期**: 2026-04-28
**審查者**: Cursor Claude Opus 4.7

---

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| 12 | 0 | 2 | 6 | 4 |

**建議下一步**: `minor-fix`

**主要觀察**：

- **整體實施品質高**：Sub-wave 1 新增 14 檔（templates / scaffolding / 維護契約）全部到位且含 Dflow marker；Sub-wave 2 既有 templates/scaffolding 的 H2/H3 結構性 heading 與 6 個初始 `<!-- dflow:section X -->` anchor 完整注入；Sub-wave 4 lightweight-spec Implementation Tasks 段內容、layer tags（WebForms `DOMAIN/PAGE/DATA/TEST/DOC`、Core `DOMAIN/APP/INFRA/API/TEST/DOC`）、anchor 緊接內容皆嚴格對應 PROPOSAL-013 §7。
- **17 個 R8b decisions 全部落地**：F-01 / F-02 / F-04 / F-05 / F-06 / F-07 / F-08 / F-11 / F-12 / F-13 / F-14 / F-15 / F-17 等全部可驗證。雙版 git-integration.md 末尾 `<!-- R8b verified: no Chinese structural terms in scope; per F-17 Path A. -->` 註記到位。
- **既有機制（P003-P012）相容性無 regression**：drift-verification.md「Anchor coexistence with `dflow:section`」段明確劃出 P008 演算法繼續以 BR-ID + markdown auto-id 為主索引；P010 mandatory baseline 4 項+1 README 對齊 P013 §5 比對表；scaffolding 內**未**注入 anchor，`specs/_共用/` 殘留為 0（C-08 不破）。
- **Cascade 漏網主要集中在 Sub-wave 3**：references 多處仍有對 templates 內已英文化 H2/H3 / column labels 的中文殘留引用（特別 `new-phase-flow.md` 對 `Business Rules` / Snapshot 表 column labels 的引用）；`templates/CLAUDE.md` 雙版 directory tree 內 inline comment 對 `_index.md` 的 section name 引用未同步。
- **設計決策 4 項執行品質均達標**，但有兩項補充意見：(a) `_index.md` 的 Resume Pointer 段內 `**目前進展**` / `**下一個動作**` placeholder labels 為設計選擇邊界（GLOSSARY 未列），需設計者釐清是否屬結構性 fields；(b) GLOSSARY 13 term 涵蓋核心 + commit-message labels，但部分高頻 structural heading（`Open Questions` / `Edge Cases` / `Goals & Scope` / `Phase Specs` 等）未列入，是 minimum viable set 還是漏列亦需釐清。

---

## Findings

（按嚴重度排序：Critical → Major → Minor → Question）

### F-01 references 對 templates canonical English heading / column labels 的中文 cascade 漏網

**嚴重度**: Major
**對照**:
- 跨 Sub-wave cascade：`Sub-wave 2 templates 英文化 heading` ↔ `Sub-wave 3 references 引用` (CW-04)
- 實施 vs R8b Decisions：`reviews/round-8b-decisions.md F-04 / F-05` ↔ 多檔 references

**維度**: Dimension 2

**現況**:

雙版 references 仍有多處引用 `templates/_index.md` Current BR Snapshot 表 columns、`templates/phase-spec.md` `Business Rules` heading、Resume Pointer placeholder fields 時保留中文：

- `sdd-ddd-{webforms,core}-skill/references/new-phase-flow.md`：
  - L57: `... left off (its 業務規則 and Delta-from-prior-phases sections in particular)` ← 應為 `Business Rules`
  - L134/143: `list NEW or MODIFIED BRs in 業務規則` ← 應為 `Business Rules`
  - L165-171/176-182: 描述 Snapshot 表更新時用「首次出現 / 最後修訂 / 現況規則」中文 column labels；對應 `_index.md` Snapshot 表的英文 column 已是 `First Seen (phase) / Last Updated (phase) / Current Rule`
  - L174/185: `"下一個動作: implement DOMAIN-1 / write spec for ..."` ← Resume Pointer placeholder 仍中文
- `sdd-ddd-{webforms,core}-skill/references/modify-existing-flow.md` L125-126/129-130: `inherited row with 首次出現 = ... 最後修訂 = ...`
- `sdd-ddd-{webforms,core}-skill/references/new-feature-flow.md` L134/160: `Resume Pointer: "phase-1 進行中：... / 下一個動作：..."`（注意此處 label 本身已英文化，但 placeholder 內容含中文 — 屬使用者填入內容範例性質）
- `sdd-ddd-{webforms,core}-skill/references/finish-feature-flow.md` L85-86/86-87: `**目前進展**：feature 已完成（{date}）...` / `**下一個動作**：merge / push（依專案 Git-principles）。`
- `sdd-ddd-{webforms,core}-skill/templates/CLAUDE.md` L32/38/75 (WebForms) / L51/57/97 (Core): directory tree inline comment + Three Ceremony Tiers 段對 `_index.md` 段名「接續入口」「輕量修改紀錄」的中文引用，與 `_index.md` 雙版已英文化的 H2 `Resume Pointer` / `Lightweight Changes` 不一致

**問題**:

CW-04 cascade 不完整。R8b approve session 處理 F-04 / F-05 已將 `_index.md` H2 結構性 heading 與 finish-feature Step 5 Integration Summary labels 英文化；但 references 內**對 templates section / column 的描述性引用**未一併 cascade。AI 讀 references 時仍會看到中文「業務規則」「現況規則」等，與 templates 英文化後的 `Business Rules` / `Current Rule` 不對齊，破壞 D5-01「結構性術語英文化」的設計動機。

L165-171 / 176-182 是最直接的影響範圍：references 描述 Snapshot 表如何更新，但用了與實際表 column 不一致的中文 label，AI 一旦根據 references 操作會找不到對應欄位。

`templates/CLAUDE.md` 的「接續入口」「輕量修改紀錄」雖位於 directory tree comment 與 prose，但這些**是對 `_index.md` H2 heading 的引用**（GLOSSARY 列出的 canonical Resume Pointer / Lightweight Changes），與 P013 §1 第 6 點「H2/H3 結構性標題與 SKILL.md / flow 對齊處同步英文化」原則衝突。

**建議**:

實施再修，cascade 補完。建議 batch sed-like 替換清單：

| 中文 | Canonical English | 影響檔 |
|---|---|---|
| `業務規則`（H2 引用） | `Business Rules` | `new-phase-flow.md` L57 / L134 / L143 |
| `現況規則`（_index.md Snapshot column）| `Current Rule` | `new-phase-flow.md` L167 / L170 / L178 / L181 |
| `最後修訂`（_index.md Snapshot column）| `Last Updated` | `new-phase-flow.md` L165 / L167-171 / L176-182；`modify-existing-flow.md` L126 / L130 |
| `首次出現`（_index.md Snapshot column）| `First Seen` | `new-phase-flow.md` L165 / L176；`modify-existing-flow.md` L125 / L129 |
| `接續入口`（_index.md H2 引用）| `Resume Pointer` | `templates/CLAUDE.md` 雙版 L32 / L51（directory tree comment）|
| `輕量修改紀錄`（_index.md H2 引用）| `Lightweight Changes` | `templates/CLAUDE.md` 雙版 L38 / L75 (WebForms) / L57 / L97 (Core) |

`finish-feature-flow.md` L85-86 的 `**目前進展**` / `**下一個動作**` 與 `_index.md` 雙版 L95/97 (WebForms) / L103/105 (Core) 同源（屬 placeholder labels）— 見 F-04 評估。

---

### F-02 Core SKILL.md Scaffolding 表未列入新增的 `architecture-decisions-README.md`

**嚴重度**: Major
**對照**:
- 跨 Sub-wave cascade：`Sub-wave 1 新增 architecture-decisions-README.md` ↔ `Sub-wave 3 SKILL.md cascade` (CW-01)
- 實施 vs R8b Decisions：`reviews/round-8b-decisions.md F-05` ↔ `sdd-ddd-core-skill/SKILL.md:466-478`

**維度**: Dimension 2

**現況**:

`sdd-ddd-core-skill/SKILL.md` L470-477 「Scaffolding (used by `/dflow:init-project`)」表只列 `_overview.md` / `_conventions.md` / `Git-principles-{gitflow,trunk}.md` / `CLAUDE-md-snippet.md` 共 5 檔，**未列**新增的 `scaffolding/architecture-decisions-README.md`。

```
| `scaffolding/_overview.md` | ... |
| `scaffolding/_conventions.md` | ... |
| `scaffolding/Git-principles-gitflow.md` | ... |
| `scaffolding/Git-principles-trunk.md` | ... |
| `scaffolding/CLAUDE-md-snippet.md` | ... |
```

對比同檔 L444-458 Templates 表已正確列入 6 個新 templates（`glossary.md` / `models.md` / `rules.md` / `context-map.md` / `tech-debt.md` / `events.md`）。

**問題**:

`architecture-decisions-README.md` 是 Sub-wave 1 在 Core 版新增的 scaffolding（per F-07 Path A 修補 P010 baseline），SKILL.md Scaffolding 表是其在 SKILL 內的權威索引點。漏列導致：

1. AI 讀 SKILL.md 找 scaffolding 來源時，無法直接認識此檔的存在與用途。
2. CW-01 SKILL.md cascade 不完整，與 R8b decisions F-05 修訂紀錄「Templates & Scaffolding 段內所有引用 cascade 同步」精神不符（雖然 F-05 顯式條目寫的是 "template section name"，但 Scaffolding 表的新檔遺漏屬同類 cascade）。

**建議**:

在 `sdd-ddd-core-skill/SKILL.md` Scaffolding 表（L477 之後）補一列：

```markdown
| `scaffolding/architecture-decisions-README.md` | ADR directory README seeded into `specs/architecture/decisions/README.md` (mandatory baseline; per PROPOSAL-013 §5 baseline 比對表) |
```

WebForms 版不需修（無 ADR 對應 scaffolding）。

---

### F-03 CHANGELOG R8b 段對 `scaffolding/_overview.md` 自述「修改」實際雙版未動

**嚴重度**: Minor
**對照**:
- 實施 vs CHANGELOG 自述：`CHANGELOG.md:25` ↔ `git diff 62640c7~1 12f552e -- sdd-ddd-{webforms,core}-skill/scaffolding/_overview.md`（兩檔皆未動）

**維度**: Dimension 1

**現況**:

`CHANGELOG.md` L25：`修改 scaffolding/_overview.md / _conventions.md（雙版共 4 檔，檢查級別）`

但 `git diff 62640c7~1 12f552e -- sdd-ddd-webforms-skill/scaffolding/_overview.md sdd-ddd-core-skill/scaffolding/_overview.md` 為空 — 兩檔在整個 R8b 期間皆未動（per resume prompt §3.1(c)，verify pass 沒動是合理的）。實際只有 `_conventions.md` 雙版各動了一處（將 "輕量修改紀錄" 改為 "Lightweight Changes"）。

**問題**:

CHANGELOG 自述失準。讀者看到「修改 ... 共 4 檔」會以為有實質變動，但實際只有 2 檔有變更。雖然 "（檢查級別）" 註記暗示可能無修改，但描述仍偏離事實。

**建議**:

`CHANGELOG.md` L25 改寫為：

```markdown
- 修改 `scaffolding/_conventions.md`（雙版，per F-04 cascade 將「輕量修改紀錄」改為 `Lightweight Changes`）
- `scaffolding/_overview.md` 雙版檢查 verify 為已英文化結構，無需修改
```

不影響 PROPOSAL 意圖落地，僅提升 CHANGELOG 真實性。

---

### F-04 `_index.md` Resume Pointer 段內 `**目前進展**` / `**下一個動作**` placeholder labels 是否屬結構性 fields

**嚴重度**: Question
**對照**:
- 設計決策：`PROPOSAL-013 §1 第 6 點「結構性術語 vs 散文段落」` ↔ `templates/_index.md` 雙版 L95/97 (WebForms) / L103/105 (Core)；`references/finish-feature-flow.md` 雙版 L85-86 / L86-87 (D5-01)

**維度**: Dimension 5

**現況**:

`templates/_index.md` 雙版 Resume Pointer 段（H2 已英文化為 `## Resume Pointer`）下的 placeholder 仍為：

```markdown
**目前進展**：{一句話}

**下一個動作**：{建議的下一個動作}
```

`finish-feature-flow.md` 雙版 Step 5 末段示範 closeout 後 `_index.md` 該段樣貌時也維持這兩個中文 bold labels：

```markdown
**目前進展**：feature 已完成（{date}），所有 phase-spec status = completed。
**下一個動作**：merge / push（依專案 Git-principles）。
```

GLOSSARY 13 term 中**未列**「目前進展」「下一個動作」，僅列 H2 級別的 `Resume Pointer`。

**問題**:

P013 §1 第 6 點：「H2/H3 結構性標題與 SKILL.md / flow 對齊處同步英文化，散文段落仍為使用者填寫範例，不在覆蓋範圍。」

「**目前進展**」「**下一個動作**」是 Resume Pointer 段內的 bold inline labels，性質介於：
- (a) 結構性 fields — AI / 工具會 grep `**目前進展**` 來定位「current progress」，類似 YAML key
- (b) 散文 placeholder — 屬於 `_index.md` 內示意該段該寫什麼樣的內容

實作端選擇 (b)（保留中文）。但 `Resume Pointer` 是 GLOSSARY 列為 canonical English 的 H2，其下 sub-labels 是否也屬「結構性語言」未在 P013 §1 拍板。

**建議**:

需釐清：

1. 設計者明確拍板：`**目前進展**` / `**下一個動作**` 屬結構性 fields（應為 `**Current Progress**` / `**Next Action**`，並補入 GLOSSARY），或屬散文範例（保留中文，與 `_index.md` Goals & Scope 段內的中文 prose 同類）
2. 若選 (b)，建議在 `_index.md` Resume Pointer 段內補一行 template note 說明「**目前進展** / **下一個動作** 為填寫範例 labels；專案可依語言改寫」
3. 若選 (a)，`finish-feature-flow.md` Step 5 範例同步英文化，並補入 GLOSSARY

---

### F-05 GLOSSARY 13 term 是 minimum viable set 還是漏列高頻 structural heading

**嚴重度**: Question
**對照**:
- 設計決策：`PROPOSAL-013 §1.1 GLOSSARY` ↔ `TEMPLATE-LANGUAGE-GLOSSARY.md` (D5-04)
- 實施 vs templates 結構性 heading：`templates/phase-spec.md` 等

**維度**: Dimension 5

**現況**:

`TEMPLATE-LANGUAGE-GLOSSARY.md` 列出 13 term：Implementation Tasks / Behavior Scenarios / Business Rules / Current BR Snapshot / Domain Models / Change Scope / Feature Goal / Related BR-IDs / Phase Count / Lightweight Change / Lightweight Changes / Resume Pointer / Behavior Delta。

實際 templates 中還有以下高頻 structural heading 未列入 GLOSSARY：

- `Goals & Scope` (`_index.md` L43)
- `Phase Specs` (`_index.md` L51) — H2 級
- `Open Questions` (`phase-spec.md` L167 / `glossary.md` / `rules.md` etc.) — 已注入 anchor `dflow:section open-questions`
- `Edge Cases` (`phase-spec.md` L122)
- `Delta from prior phases` (`phase-spec.md` L88) — 含 ADDED / MODIFIED / REMOVED / RENAMED / UNCHANGED Delta sub-types
- `Implementation Notes` / `Test Strategy` / `Data Structure Changes` (`phase-spec.md`)
- `Problem Description` / `Domain Concepts` (`phase-spec.md`)
- `Problem` / `Root Cause` / `Fix Approach` / `Tech Debt Discovered (if any)` (`lightweight-spec.md`)
- `Phase List` / `Next Steps (developer)` (`finish-feature-flow.md` Integration Summary)

**問題**:

P013 §1 末段：「判斷標準以『會被 AI 或開發者當作固定結構 / 欄位 / 狀態 / 類型理解的詞』為主」。上述 heading 都屬此類。GLOSSARY 13 term 涵蓋了核心流程關鍵 + commit-message labels，但漏列其他 structural heading 可能讓人讀文件時遇到「Phase Specs」「Open Questions」「Edge Cases」等不知道對應中文意義（GLOSSARY 的目的是人讀輔助）。

兩種解釋皆合理：
- (a) **設計選擇**：GLOSSARY 為 minimum viable set，只收「跨檔同名 / 流程關鍵 / 翻譯難度高」的 term；其他 structural heading 顯而易見不需對照
- (b) **漏列**：應一併納入

**建議**:

需釐清：設計者拍板 GLOSSARY 的「收錄門檻」（流程關鍵 only / 跨檔同名 only / 含 Delta sub-types 等），並在 `TEMPLATE-LANGUAGE-GLOSSARY.md` 開頭補一段「Inclusion criteria」說明，避免後續 review 重複質疑。

---

### F-06 P013 §1 anchor 表寫 `_index.md` 應含 `business-rules` anchor 但實作未注入

**嚴重度**: Minor
**對照**:
- 實施 vs P013：`PROPOSAL-013 §1 anchor 表 line 100`（`business-rules` 用於 `rules.md`, `_index.md`） ↔ `sdd-ddd-{webforms,core}-skill/templates/_index.md`（無 `business-rules` anchor，只有 `current-br-snapshot` / `lightweight-changes`）
- 設計決策：`D5-02 dflow:section anchor` ↔ `TEMPLATE-COVERAGE.md` row 1 (Section anchors 欄)

**維度**: Dimension 5

**現況**:

`PROPOSAL-013 §1` anchor 對照表 line 100：

```
| `dflow:section business-rules` | `Business Rules` | `rules.md`, `_index.md` | BR-ID / anchor 對應與 drift verification |
```

但實際 `templates/_index.md` 雙版只注入了：
- `<!-- dflow:section current-br-snapshot -->` (L60/68)
- `<!-- dflow:section lightweight-changes -->` (L76/84)

**沒有** `business-rules` anchor。

`TEMPLATE-COVERAGE.md` row 1 Section anchors 欄填的是 `current-br-snapshot, lightweight-changes`（也未列 `business-rules`），與實際實作對齊；但與 P013 §1 anchor 表預期不一致。

**問題**:

P013 §1 anchor 表寫「`business-rules` 用於 `rules.md`, `_index.md`」。實作端選擇 `_index.md` 用 `current-br-snapshot` anchor 對應「Current BR Snapshot」H2 而非 `business-rules`，這是合理設計：
- `_index.md` 沒有名為 `## Business Rules` 的獨立段，只有 `## Current BR Snapshot`
- 同段不重複注入兩個 anchor（避免「同段兩個 stable id」混淆 namespacing 語意）

但這個設計選擇與 P013 §1 表字面不一致。COVERAGE 矩陣與實作對齊，但 P013 §1 表未隨實作調整。

**建議**:

P013 §1 anchor 表 line 100 應修訂為：

```
| `dflow:section business-rules` | `Business Rules` | `rules.md` | BR-ID / anchor 對應與 drift verification |
```

去掉 `_index.md` 引用。並在 §1 末段或 design intent 段補一行：「`_index.md` 的 Business Rules 概念由 `current-br-snapshot` anchor 涵蓋（同段不重複注入）」。

或反過來，若設計者堅持 `_index.md` 應同時含 `business-rules` anchor，則需實際注入。

實施端建議優先採前者（修 P013 §1 表 + 加 design note），因為實作端的選擇是合理的「同段單一 stable id」原則。

---

### F-07 `lightweight-spec.md` Behavior Delta 段內 H4 sub-heading 中英混合

**嚴重度**: Minor
**對照**:
- 設計決策：`PROPOSAL-013 §1 第 6 點 canonical English structural language` ↔ `sdd-ddd-{webforms,core}-skill/templates/lightweight-spec.md:50/51` (D5-01)

**維度**: Dimension 5

**現況**:

`templates/lightweight-spec.md` 雙版 Behavior Delta 段：

```markdown
## Behavior Delta

> 精簡 delta 格式：bug fix 多數只需 MODIFIED；若確實是新增規則可改用 ADDED、移除用 REMOVED、改名用 RENAMED。多項變更時照類別列。

### MODIFIED — 修改的行為
#### 規則：BR-NN {規則名稱}
**原本**：Given {目前的狀態} When {操作} Then {目前的（錯誤）結果}
...
```

對比 `templates/phase-spec.md` 雙版 Delta from prior phases 段已英文化：

```markdown
### ADDED - BR / behavior added in this phase
#### Rule: BR-NN {規則名稱}
...
### MODIFIED - BR / behavior modified in this phase
#### Rule: BR-NN {規則名稱}
**原本**：Given … When … Then {舊結果}
**改為**：Given … When … Then {新結果}
**原因**：{為什麼改}
```

`lightweight-spec.md` 的 H3 是 `### MODIFIED — 修改的行為`（中英混合），H4 是 `#### 規則：BR-NN ...`（中文）；`phase-spec.md` 的 H3 是 `### MODIFIED - BR / behavior modified in this phase`（英文），H4 是 `#### Rule: BR-NN {規則名稱}`（英文 + 中文 placeholder）。

**問題**:

`MODIFIED` 等 Delta sub-types 是結構性術語（GLOSSARY 第 1 行已明示「Behavior Scenarios」是 canonical heading；`phase-spec.md` 已採英文 H3 / H4）。`lightweight-spec.md` 雙版的 `### MODIFIED — 修改的行為` / `#### 規則：BR-NN` 與 `phase-spec.md` 的 `### MODIFIED - BR / behavior modified in this phase` / `#### Rule: BR-NN` 不一致，cascade 內部不對齊。

注意：`lightweight-spec.md` L56 註解寫：「若需要 ADDED / REMOVED / RENAMED / UNCHANGED 請比照 references/modify-existing-flow.md 的 Delta 格式」 — 暗示應與 `modify-existing-flow.md` 同源；但 `modify-existing-flow.md` 的 Delta sub-headings 樣式本身也是中英混合，這個漂移屬於 P013 §1 第 6 點「結構性術語英文化」未完整 cascade 到 `lightweight-spec.md`。

**建議**:

`lightweight-spec.md` 雙版改為：

```markdown
### MODIFIED - behavior modified in this fix
#### Rule: BR-NN {規則名稱}
**原本**：Given {目前的狀態} When {操作} Then {目前的（錯誤）結果}
**改為**：Given {相同狀態} When {相同操作} Then {正確的結果}
**原因**：{為什麼改 — bug / 需求澄清 / 對齊 spec}
```

與 `phase-spec.md` 雙版 ADDED / MODIFIED / REMOVED / RENAMED Delta sub-headings 樣式對齊。

---

### F-08 `Behavior Delta` 段內 placeholder labels `**原本** / **改為** / **原因**` 中文未英文化

**嚴重度**: Question
**對照**:
- 設計決策：`PROPOSAL-013 §1 第 6 點 canonical English structural language` ↔ `templates/{lightweight,phase}-spec.md` (D5-01)

**維度**: Dimension 5

**現況**:

`phase-spec.md` 雙版 Delta from prior phases 段內：

```markdown
**原本**：Given … When … Then {舊結果}
**改為**：Given … When … Then {新結果}
**原因**：{為什麼改}
```

`lightweight-spec.md` 雙版同樣使用中文 bold labels `**原本**` / `**改為**` / `**原因**`。

**問題**:

`原本` / `改為` / `原因` 是 Delta scenario 的固定 fields，AI 會 grep `**原本**` 來定位「previous state」（類似 F-04 的 `**目前進展**` / `**下一個動作**` 議題）。GLOSSARY 未列。屬結構性 inline labels 還是散文範例？P013 §1 第 6 點未明確涵蓋此層級。

**建議**:

需釐清。若選結構性，建議 cascade 改為：

```markdown
**Before**: Given … When … Then {舊結果}
**After**: Given … When … Then {新結果}
**Reason**: {為什麼改}
```

並補入 GLOSSARY。若選散文，補 template note 說明「**原本** / **改為** / **原因** 為填寫範例 labels」。

---

### F-09 `templates/context-definition.md` 內中文 prose 引用 `behavior.md` 用「行為情境」而非 `Behavior Scenarios`

**嚴重度**: Minor
**對照**:
- 跨 Sub-wave cascade：`Sub-wave 2 GLOSSARY canonical Behavior Scenarios` ↔ `templates/context-definition.md` 雙版 L45 (CW-04 / CW-05)

**維度**: Dimension 2

**現況**:

`templates/context-definition.md` 雙版 L45：

```markdown
> 規則索引在 `rules.md`（BR-ID + 一行摘要），完整行為情境在 `behavior.md`（Given/When/Then）。這裡只列最重要的幾條。
```

「行為情境」在 GLOSSARY 列為「Behavior Scenarios | 行為情境」canonical English / 繁中對照。此處屬 prose 引用 `behavior.md` 內的 canonical English heading。

**問題**:

CW-04 cascade 漏網：`templates` 內部對其他 template canonical heading 的 prose 引用，應與 GLOSSARY 對齊，否則人讀文件時的中英對應感受不一致。

但屬 prose 性質，不破壞 AI 操作；屬 Minor。

**建議**:

雙版 `context-definition.md` L45 改為：

```markdown
> The rule index lives in `rules.md` (BR-ID + one-line summary); full Behavior Scenarios (Given/When/Then) live in `behavior.md`. This section only lists the most critical rules.
```

或保留中文但點出對應 canonical：

```markdown
> 規則索引在 `rules.md`（BR-ID + 一行摘要），完整 Behavior Scenarios 在 `behavior.md`（Given/When/Then）。這裡只列最重要的幾條。
```

---

### F-10 `references/finish-feature-flow.md` Step 5 範例 placeholder 內 `Next Steps (developer):` 內容仍中文

**嚴重度**: Minor
**對照**:
- 實施 vs R8b Decisions：`reviews/round-8b-decisions.md F-04` ↔ `sdd-ddd-{webforms,core}-skill/references/finish-feature-flow.md:210-213/211-215`

**維度**: Dimension 3

**現況**:

`finish-feature-flow.md` 雙版 Step 5 Integration Summary 範例：

```
Next Steps (developer):
- 依專案 Git-principles 決定 merge strategy（merge commit / squash / rebase /
  fast-forward）並執行
- 推送到遠端 / 開 PR
```

H2 / labels 已英文化（`Next Steps (developer):`）✓，但 list items 內容仍中文。

**問題**:

P013 §1 第 6 點「散文段落仍為使用者填寫範例」涵蓋此情況——範例內容用中文展示「使用者會看到什麼」屬合理。但與 Sub-wave 2 將 `Git-principles-{gitflow,trunk}.md` Integration Commit Message Conventions 段內 prose 改為英文（如 §4 example）的方向略有張力：

- `Git-principles-*.md` 的 example 已是英文 list（如 `git push origin develop`）
- `finish-feature-flow.md` Step 5 emit 的 Integration Summary 範例 list 仍中文

兩者格式語意對齊（emit ↔ commit message），但語言層級不一致。

**建議**:

需釐清是否屬於設計選擇邊界。若選一致英文化，list items 改為：

```
Next Steps (developer):
- Per the project's Git-principles, choose a merge strategy
  (merge commit / squash / rebase / fast-forward) and execute
- Push to remote / open a PR
```

若選保留（屬使用者範例），可在範例下加一行 note：「The example list above is illustrative; AI emits in the language of the discussion.」

---

### F-11 `templates/CLAUDE.md` 散文段落中對 `_index.md` H2 結構性 heading 引用混用中英

**嚴重度**: Minor
**對照**:
- 實施 vs R8b Decisions：`reviews/round-8b-decisions.md F-01 Path A` ↔ `templates/CLAUDE.md` 雙版

**維度**: Dimension 3

**現況**:

`templates/CLAUDE.md` 雙版的 H2/H3 結構性 heading 已全部英文化（System Context / Development Workflow / Background / Project Structure / Core Principles / Three Ceremony Tiers / New Feature / New Phase / Modify Existing / Bug Fix / Feature Closeout / Git Integration / Domain Layer Rules / Glossary / AI Collaboration Notes）✓。但 directory tree 內的 inline comment（屬說明性散文）出現對 `_index.md` H2 結構性 heading 的中文引用：

WebForms `templates/CLAUDE.md`:
- L32: `_index.md     # Feature dashboard：目標 / Phase Specs / BR Snapshot / 接續入口`
- L38: `_index.md 輕量修改紀錄寫一列`
- L75: `只在 _index.md 輕量修改紀錄 inline 寫一列`

Core `templates/CLAUDE.md` 雙版同類位置 L51 / L57 / L97。

**問題**:

「目標」≈ Goals & Scope（部分對應）、「接續入口」= Resume Pointer (GLOSSARY)、「輕量修改紀錄」= Lightweight Changes (GLOSSARY)。F-01 Path A 拍板「templates/CLAUDE.md H2/H3 全英文化」+「散文段落仍為使用者填寫範例」。Directory tree 內的 `# comment` 屬散文還是結構性引用？這些位置直接寫出 `_index.md` 的 section name，AI 讀此處會以為 `_index.md` 真的有「接續入口」「輕量修改紀錄」這些中文 H2，但實際上 `_index.md` 已英文化為 `Resume Pointer` / `Lightweight Changes`。屬 cascade 漏網。

**建議**:

WebForms `templates/CLAUDE.md` L32 / L38 / L75 與 Core `templates/CLAUDE.md` L51 / L57 / L97 改為：

```text
_index.md     # Feature dashboard: Goals & Scope / Phase Specs / Current BR Snapshot / Lightweight Changes / Resume Pointer
```

```text
# T3 has no independent file — just one row in _index.md Lightweight Changes
```

```markdown
- **T3 Trivial** ... → record one inline row in `_index.md` Lightweight Changes
```

注意：F-01 Path A 範圍涵蓋「H2/H3 結構性標題」；對 `_index.md` H2 的引用屬於同一邏輯範疇，建議納入 cascade 補完。

---

### F-12 GLOSSARY 13 term 列出 `Domain Models` 但 `models.md` 內 H1 是 `Domain Models`（一致）；其他 term 使用位置無漏

**嚴重度**: Question
**對照**:
- 設計決策：`D5-04 GLOSSARY 完整性` ↔ `TEMPLATE-LANGUAGE-GLOSSARY.md` + 各 templates

**維度**: Dimension 5

**現況**:

GLOSSARY 13 term 中 `Domain Models` 對應 `models.md` 使用位置。`templates/models.md` 雙版 L3 是 `# Domain Models`（H1）—— 但 H1 通常是檔案標題，不是 H2 結構性 section。其他 12 term 都對應 H2 級 section heading 或 inline label。

**問題**:

GLOSSARY 「使用位置」欄寫 `models.md`，意指「整個 models.md 檔的概念」還是「models.md 內的 `Domain Models` H1」？若是後者，需釐清為何只此一個 term 對應 H1 級別（其他 12 term 都對應 H2 / inline label）。實施端可能視為「概念名稱」收錄。

**建議**:

需釐清：GLOSSARY 收錄的「使用位置」語意是檔案級還是 section 級？建議在 `TEMPLATE-LANGUAGE-GLOSSARY.md` 開頭補一段 Inclusion / scope 說明（與 F-05 建議合併處理）。

不影響 implement 落地，僅釐清設計語義。

---

## Sub-wave 實施對照表（Dimension 1）

### Sub-wave 1 (commit `62640c7` — 純新增 14 檔)

| 檔案 | 預期狀態 | 實際狀態 | 一致？ | Finding |
|---|---|---|---|---|
| `<repo root>/TEMPLATE-COVERAGE.md` | 14 rows + Section anchors 欄 | 14 rows ✓ + Section anchors 欄已存在；row 1 Section anchors 列 `current-br-snapshot, lightweight-changes`（與 P013 §1 表「business-rules 用於 _index.md」不一致） | ⚠ | F-06 |
| `<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md` | ≥10 initial term | 13 term（含 Sub-wave 2 新增 +3：Feature Goal / Related BR-IDs / Phase Count）；3 term 涵蓋 backlog § 4 範圍 | ✓ | — |
| 雙版 `templates/glossary.md` ×2 | 含 Dflow marker | ✓ Term / Definition / Bounded Context / Code Mapping / Notes 五欄結構 | ✓ | — |
| 雙版 `templates/models.md` ×2 | 含 Dflow marker | ✓ Entities / Value Objects / Domain Services / Repository Interfaces；Core 版未含 Aggregates 表（與 COVERAGE row 7「Core has Aggregate / Specification depth」描述不對齊 — 但 Core 另有 `aggregate-design.md` 補位，可接受） | ⚠（border） | (no separate finding — note in section) |
| 雙版 `templates/rules.md` ×2 | 含 Dflow marker；含 `dflow:section business-rules` anchor；Behavior anchor 範例 `[BR-001](./behavior.md#br-001-rule-name)` | ✓ 全部到位（per F-08 既有機制相容性確認）；Core 版有 Aggregate 欄、WebForms 版無 — 對齊 COVERAGE row 6 Allowed differences | ✓ | — |
| 雙版 `templates/context-map.md` ×2 | 含 Dflow marker | ✓；WebForms 版 Notes 寫 "Optional bounded context relationship map"（對齊 COVERAGE row 11 Allowed differences）| ✓ | — |
| 雙版 `templates/tech-debt.md` ×2 | 含 Dflow marker；WebForms migration / Core architecture 焦點分開 | ✓ WebForms 版欄位 Migration impact；Core 版欄位 Layer / Decision / Impact / Follow-up | ✓ | — |
| `core/templates/events.md` | 含 Dflow marker | ✓ Event name / Producer / Trigger / Payload / Consumers / Delivery expectation 六欄結構 | ✓ | — |
| `core/scaffolding/architecture-decisions-README.md` | 含 Dflow marker；ADR Naming Convention / When To Write An ADR / Minimal ADR Fields | ✓ 全部到位 | ✓ | — |

### Sub-wave 2 (commits `0cd99fb` + `2aaba8b` — 既有 templates / scaffolding canonical English + anchors + GLOSSARY +3)

| 檔案 | 預期狀態 | 實際狀態 | 一致？ | Finding |
|---|---|---|---|---|
| 雙版 `templates/_index.md` | H2/H3 全英；6 sections 順序保留；anchors `current-br-snapshot` + `lightweight-changes` | ✓ Goals & Scope / Phase Specs / Current BR Snapshot / Lightweight Changes / Resume Pointer 全英；6 sections 順序保留；anchor 注入到位；P013 §1 表預期的 `business-rules` anchor 缺（合理設計選擇） | ⚠ | F-06 |
| 雙版 `templates/_index.md` Resume Pointer placeholder | bold labels 英文化或保留範例 | `**目前進展**` / `**下一個動作**` 仍中文（GLOSSARY 未列） | ⚠ | F-04 |
| 雙版 `templates/phase-spec.md` | H2/H3 全英；anchors `behavior-scenarios` / `open-questions` / `implementation-tasks` | ✓ Problem Description / Domain Concepts / Behavior Scenarios / Business Rules / Delta from prior phases / Edge Cases / Implementation Notes / Data Structure Changes / Test Strategy / Open Questions / Implementation Tasks 全英；ADDED / MODIFIED / REMOVED / RENAMED / UNCHANGED Delta sub-headings 全英；3 個 anchor 到位 | ✓ | — |
| 雙版 `templates/lightweight-spec.md` | H2/H3 全英；anchor `implementation-tasks` | ✓ Problem / Behavior Delta / Root Cause / Fix Approach / Implementation Tasks / Tech Debt Discovered (if any) 全英；anchor 緊接 `## Implementation Tasks`（CW-03 ✓）；H3 sub-headings `### MODIFIED — 修改的行為` 中英混合（與 phase-spec.md 不一致）+ H4 `#### 規則：BR-NN` 中文 | ⚠ | F-07 |
| 雙版 `templates/lightweight-spec.md` Behavior Delta inline labels | bold labels 英文化或保留範例 | `**原本**` / `**改為**` / `**原因**` 仍中文 | ⚠ | F-08 |
| 雙版 `templates/behavior.md` | H2/H3 全英；anchor `behavior-scenarios` | ✓ purpose 段全英；`Feature Area` 範例性 H2；anchor 注入到位 | ✓ | — |
| 雙版 `templates/context-definition.md` | H2/H3 全英 | ✓ Responsibilities / Boundaries / Core Domain Models / Interactions with Other Contexts / Key Business Rules / Code Mapping 全英；prose 段 L45 引用 `behavior.md` 用「行為情境」非 canonical `Behavior Scenarios` | ⚠ | F-09 |
| `core/templates/aggregate-design.md` | H2/H3 全英 | ✓ Purpose / Invariants / Structure / Aggregate Root / State Transition Methods / Domain Events / Referenced Aggregates / Design Decisions 全英 | ✓ | — |
| 雙版 `templates/CLAUDE.md` | H2/H3 全英；directory tree 引用 `_index.md` H2 對齊 canonical | ✓ H2/H3 全英；但 directory tree inline comment + Three Ceremony Tiers / Modify Existing 段對 `_index.md` 「接續入口」「輕量修改紀錄」中文引用未 cascade | ⚠ | F-11 |
| 雙版 `scaffolding/_overview.md` | 檢查級別已英文化 | ✓ verify pass 雙版未動 | ✓ | F-03 (CHANGELOG 措辭) |
| 雙版 `scaffolding/_conventions.md` | 已英文化（檢查級別） | ✓ 雙版各動 1 處（將 `_index.md` 內 "輕量修改紀錄" 改為 `Lightweight Changes`） | ✓ | — |
| 雙版 `scaffolding/Git-principles-{gitflow,trunk}.md` | Integration Commit labels 英文：Feature Goal / Change Scope / Related BR-IDs / Phase Count / Lightweight Changes | ✓ §4 Integration Commit Message Conventions 段全英 labels；Core 版補 Aggregate(s) touched / Domain Events 列；trunk 版 squash / rebase / fast-forward 三種 commit 格式 | ✓ | — |
| 雙版 `scaffolding/CLAUDE-md-snippet.md` | H2/H3 英文化 | ✓ System Context / Development Workflow / Background / Project Structure / Core Principles / Dflow Skill / Project-Level Supplemental Rules / Domain Layer Rules / AI Collaboration Notes 全英 | ✓ | — |
| `<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md` 擴充 | +3 term：Feature Goal / Related BR-IDs / Phase Count | ✓ 13 term 全部到位 | ✓ | F-05 / F-12（其他 structural heading 是否漏列） |

### Sub-wave 3 (commit `61c0df4` — references cascade + SKILL.md cascade)

| 檔案 | 預期狀態 | 實際狀態 | 一致？ | Finding |
|---|---|---|---|---|
| 雙版 `references/init-project-flow.md` | baseline 加 cascade source 註記 | ✓ WebForms baseline 4 項（含 `glossary.md ← templates/glossary.md`、`tech-debt.md ← templates/tech-debt.md`）；Core baseline 5 項（含 `decisions/README.md ← scaffolding/architecture-decisions-README.md`，per F-07 baseline 比對 NEW）；§3.3 file-list preview 也帶 source 註記 | ✓ | — |
| 雙版 `references/new-feature-flow.md` | 缺 domain docs 用 template 建立；Core 含 events / context-map | ✓ WebForms L65-69 列 4 個 templates；Core L89-94 列 6 個（含 events / context-map） | ✓ | — |
| 雙版 `references/modify-existing-flow.md` | task guard cascade（T2 不再跳過 task generation；不以 task 數自動升 T1）；缺檔 cascade | ✓ L283-289 task guard 完整段（per Sub-wave 4）；L162-169 缺檔 cascade；L342 / L351 / L363 completion checklist 適用 phase-spec + lightweight-spec | ✓ | — |
| 雙版 `references/finish-feature-flow.md` | Step 5 Integration Summary canonical labels | ✓ Feature Goal / Change Scope / Phase Count / Lightweight Changes / Related BR-IDs / Phase List / Next Steps (developer) labels 全英；list items 部分中文（屬使用者範例邊界） | ⚠ | F-10 |
| 雙版 `references/drift-verification.md` | Anchor coexistence 段；rules.md 缺檔 cascade | ✓ L51-59 / L53-61 「Anchor coexistence with `dflow:section`」段明示「markdown heading anchors remain primary」+「dflow:section does not replace BR-ID markdown anchors, and does not change drift-verification algorithm」；rules.md 缺檔 cascade 在 Step 1 報告中 | ✓ | — |
| 雙版 `references/pr-review-checklist.md` | section name cascade | ✓ L19-30 等引用 `Current BR Snapshot` / `Lightweight Changes` / `Resume Pointer` / `Behavior Delta` 全英 | ✓ | — |
| 雙版 `references/git-integration.md` | F-17 traceability comment | ✓ WebForms L329 / Core L301 含 `<!-- R8b verified: no Chinese structural terms in scope; per F-17 Path A. -->` | ✓ | — |
| 雙版 `references/new-phase-flow.md` | conditional cascade | ⚠ 部分英文化（Goals & Scope / Phase Specs / Current BR Snapshot / Resume Pointer / Implementation Tasks 已 cascade）；但 L57 / L134 / L143 對 `Business Rules` 仍寫「業務規則」；L165-171 / L176-182 對 _index.md Snapshot column labels 仍寫「首次出現 / 最後修訂 / 現況規則」中文 | ⚠ | F-01 |
| `core/references/ddd-modeling-guide.md` | context-map.md 缺檔處理 | ✓ L272 「If `specs/domain/context-map.md` does not exist yet, create it from `templates/context-map.md` before documenting relationships.」 | ✓ | — |
| 雙版 `SKILL.md` | Templates 表新增 + COVERAGE/GLOSSARY 段 + section name cascade | WebForms ✓（templates 表 +5 / Maintenance contracts 段 / section name 引用全英）；Core templates 表 +6 ✓ + Maintenance contracts 段 ✓；但 Core scaffolding 表**漏列** `architecture-decisions-README.md` | ⚠ | F-02 |

### Sub-wave 4 (commit `12f552e` — lightweight Implementation Tasks + task guard 補完 + CHANGELOG)

| 檔案 | 預期狀態 | 實際狀態 | 一致？ | Finding |
|---|---|---|---|---|
| 雙版 `templates/lightweight-spec.md` Implementation Tasks 段 | 內容 + 對應 layer tags + DOC-1 對齊 `_index.md` heading | ✓ WebForms `DOMAIN/PAGE/DATA/TEST/DOC` / Core `DOMAIN/APP/INFRA/API/TEST/DOC` 對版；3 個 task line（{LAYER}-1 / TEST-1 / DOC-1）；DOC-1 引用 `Lightweight Changes and Current BR Snapshot`（CW-06 對齊 _index.md heading ✓）；anchor 緊接 `## Implementation Tasks` 緊接 task content（CW-03 ✓） | ✓ | — |
| 雙版 `references/modify-existing-flow.md` task guard | bug-fix path 補 task 確認；不以項目數自動升 T1 | ✓ L12 「T2 still generates a concise Implementation Tasks checklist」；L283-289 task guard 完整段；L342 completion checklist 適用 phase-spec + lightweight-spec | ✓ | — |
| `CHANGELOG.md` | R8b 實施段（4 sub-wave 涵蓋）| ✓ L9-50 完整段；4 sub-wave 邊界明確；繁中版同步延 Closeout C1 註記到位；設計動機補寫條目；但 L25 自述「修改 _overview.md」與實際雙版未動偏離 | ⚠ | F-03 |

---

## 跨 Sub-wave Cascade 對照表（Dimension 2）

| 檢查點 | 實施狀態 | 一致？ | 對應 Finding |
|---|---|---|---|
| CW-01 · Sub-wave 1 新 templates ↔ Sub-wave 3 SKILL.md cascade | WebForms Templates 表 +5；Core Templates 表 +6；Core Scaffolding 表漏 `architecture-decisions-README.md` | ⚠ | F-02 |
| CW-02 · Sub-wave 1 新 templates ↔ Sub-wave 3 references cascade | init-project-flow / new-feature-flow / modify-existing-flow / finish-feature-flow / drift-verification / ddd-modeling-guide 全部含缺檔 cascade source 註記 | ✓ | — |
| CW-03 · Sub-wave 2 anchor ↔ Sub-wave 4 lightweight 引用緊接 | `<!-- dflow:section implementation-tasks -->` (L67) 緊接 `## Implementation Tasks` (L68) 緊接 task content (L70+)，無夾雜其他段落 | ✓ | — |
| CW-04 · Sub-wave 2 canonical English heading ↔ Sub-wave 3 references 引用 cascade | finish-feature / pr-review-checklist 對齊；new-phase-flow 對 Business Rules / Snapshot column labels 大量中文殘留；templates/CLAUDE.md 對 _index.md 接續入口 / 輕量修改紀錄殘留；context-definition.md prose 用「行為情境」 | ⚠ | F-01 / F-09 / F-11 |
| CW-05 · Sub-wave 2 GLOSSARY +3 ↔ Sub-wave 3 finish-feature-flow Integration Summary | finish-feature-flow.md Step 5 Feature Goal / Phase Count / Related BR-IDs labels 全英 | ✓ | — |
| CW-06 · Sub-wave 4 DOC-1 ↔ Sub-wave 2 _index.md heading | DOC-1 寫 `Update _index.md Lightweight Changes and Current BR Snapshot`，與 _index.md L60/76 (WebForms) / L68/84 (Core) 英文化 H2 完全一致 | ✓ | — |
| CW-07 · TEMPLATE-COVERAGE 14 rows ↔ 實際 templates / scaffolding | 14 rows 全部對應實際存在檔案；Section anchors 欄填寫與實際 anchor 注入一致；row 1 (`_index.md`) `business-rules` 預期但無實作（與 P013 §1 表不一致，與實作對齊 — 屬合理設計選擇） | ⚠ | F-06 |
| CW-08 · TEMPLATE-LANGUAGE-GLOSSARY 13 term ↔ 實際 templates 使用 | 13 term 全部有實際使用位置；但 `_index.md` Resume Pointer 段內 `**目前進展** / **下一個動作**` 與 lightweight-spec.md `**原本** / **改為** / **原因**` 屬同類 inline labels 但 GLOSSARY 未列；其他 H2 級 structural heading（Goals & Scope / Phase Specs / Open Questions / Edge Cases ...）未列 | ⚠ | F-04 / F-05 / F-08 |

---

## R8b Approve 決議落實對照表（Dimension 3）

| Finding | 決議類型 | 預期效果 | 實施狀態 | 一致？ | 對應 Implement Review Finding |
|---|---|---|---|---|---|
| F-01 (Path A) | accept-with-choice | 雙版 templates/CLAUDE.md 全 H2/H3 英文化 | H2/H3 全英 ✓；directory tree comment 引用 _index.md 段名仍中文 | ⚠ | F-11 |
| F-02 | accept | §4 矩陣 14 rows | TEMPLATE-COVERAGE.md 14 rows 全部到位 ✓ | ✓ | — |
| F-03 | accept | pr-review-checklist 雙版 cascade | section name cascade 同步 ✓ | ✓ | — |
| F-04 | accept | finish-feature-flow Step 5 canonical English | Feature Goal / Phase Count / Related BR-IDs / Lightweight Changes / Phase List / Next Steps (developer) labels 全英 ✓；list items 部分中文（合理範例邊界，標 F-10） | ⚠ | F-10 |
| F-05 | accept | SKILL.md cascade | WebForms ✓；Core 漏 architecture-decisions-README.md（標 F-02） | ⚠ | F-02 |
| F-06 (Path A) | accept-with-choice | anchor 欄併入 COVERAGE.md | TEMPLATE-COVERAGE.md row 1-14 含 Section anchors 欄 ✓；末段含 anchor 維護指引 ✓ | ✓ | — |
| F-07 (Path A) | accept-with-choice | §5 baseline 比對表 | P013 §5 baseline 比對表已存在；Core init-project-flow.md baseline 5 項（含 NEW decisions/README.md）✓ | ✓ | — |
| F-08 | accept | Anchor coexistence rule | drift-verification.md 雙版「Anchor coexistence with `dflow:section`」段到位 ✓；rules.md template Behavior anchor 範例值 `[BR-001](./behavior.md#br-001-rule-name)` ✓ | ✓ | — |
| F-09 | accept | §不納入影響範圍 加註 | P013 §不納入影響範圍 已含「templates/CLAUDE.md 內 specs/ 目錄結構同步」由 P012 處理註記 ✓ | ✓ | — |
| F-10 | accept | backlog § 1 接管邊界明示 | planning/public-distribution-backlog.md § 1 加註「P013 接管」+ §1.1「anchor-enabled localized heading」候選方向 ✓ | ✓ | — |
| F-11 (Path A) | accept-with-choice | COVERAGE / GLOSSARY 放 repo 根 | 兩檔在 repo 根 ✓；雙版 skill 共用 ✓ | ✓ | — |
| F-12 | accept | glossary +3 + §7 指向 §1.1 | GLOSSARY 含 Lightweight Changes / Resume Pointer / Behavior Delta ✓；lightweight-spec.md DOC-1 對齊 _index.md H2 ✓ | ✓ | — |
| F-13 | accept | ddd-modeling-guide context-map 缺檔 | core/references/ddd-modeling-guide.md L272 cascade 加上 ✓ | ✓ | — |
| F-14 | accept | Git-principles labels 精化 | Git-principles-{gitflow,trunk}.md §4 Integration Commit Message Conventions 段全英 labels ✓；Core 版補 Aggregate(s) / Domain Events 列 ✓ | ✓ | — |
| F-15 (Path B) | accept-with-choice | Anchor naming rules + Design intent | P013 §1 Anchor naming rules 段（namespacing + versioning 延後）+ Design intent 段（localized heading 啟用） ✓ | ✓ | — |
| F-16 | accept（隨 F-07）| F-07 Path A 涵蓋 | baseline 比對表已涵蓋 ✓ | ✓ | — |
| F-17 (Path A) | accept-with-choice | git-integration traceability comment | webforms L329 / core L301 R8b verified comment 到位 ✓ | ✓ | — |

---

## 既有機制相容性對照表（Dimension 4）

| C-NN | 檢查點 | 實施結論 | 對應 Finding |
|---|---|---|---|
| C-01 · P003 rules / behavior anchor | ✓ — drift-verification.md 雙版 Anchor coexistence 段明示「markdown heading anchors remain primary link target」+「dflow:section does not replace BR-ID markdown anchors」；rules.md template Behavior anchor 範例值維持 `[BR-001](./behavior.md#br-001-rule-name)` 與 P003 既有結構一致 | — |
| C-02 · P004 lightweight task guard | ✓ — lightweight-spec.md Implementation Tasks 段含「If the fix scope starts to expand, AI should pause and ask the developer」guard；modify-existing-flow.md L289 同步「Do not auto-upgrade based on task count alone.」與 P004 lightweight 精神相容 | — |
| C-03 · P008 drift-verification anchor | ✓ — drift-verification.md 雙版仍以 markdown auto-id 為主索引；dflow:section 明示為輔助 metadata；演算法不變 | — |
| C-04 · P009 § H Lightweight Changes vs § 7 Implementation Tasks 雙層紀錄 | ✓ — _index.md H2 `Lightweight Changes` 為 feature 層；lightweight-spec.md `Implementation Tasks` 為 spec 層；DOC-1 引用 `_index.md Lightweight Changes` 為 cross-reference 而非重複 | — |
| C-05 · P009 _index.md 6 sections 仍存在 | ✓ — Goals & Scope / Phase Specs / Current BR Snapshot / Lightweight Changes / Resume Pointer 順序 + 必要性不變；Follow-up Tracking 仍為 commented-out optional 段 | — |
| C-06 · P010 mandatory baseline 一致性 | ✓ — WebForms 4 項（features 子目錄、glossary.md、tech-debt.md、無 context-map）；Core 5 項（features 子目錄、glossary.md、context-map.md、tech-debt.md、decisions/README.md NEW）；其餘 baseline 項目無變動；P010 Step 4 cross-reference 預期 implemented 後加（屬 P010 維護動作，本 R8b 不在範圍） | — |
| C-07 · P010 scaffolding 邊界 | ✓ — glossary.md 落 `templates/`（living document）；scaffolding 仍維持「init 寫入後由專案持有」定位；scaffolding 內**未注入** dflow:section anchor（只在 templates 注入），與 P010 邊界一致 | — |
| C-08 · P012 路徑使用 specs/shared/（無 _共用 殘留）| ✓ — 雙版 skill 內 grep `_共用` 0 命中；殘留僅在 historical proposals/reviews/CHANGELOG（皆屬歷史紀錄，不屬 R8b 影響範圍）| — |

---

## 設計決策執行品質對照（Dimension 5）

| 決策 | 執行品質 | 主要觀察 |
|---|---|---|
| D5-01 · Canonical English structural language | ⚠ | H2/H3 級結構性 heading 雙版 templates / scaffolding / SKILL.md 全英 ✓；但對 `_index.md` H2 的 prose 引用（templates/CLAUDE.md / context-definition.md）+ Snapshot column labels（references/new-phase-flow.md / modify-existing-flow.md）+ inline bold labels（**目前進展** / **下一個動作** / **原本** / **改為** / **原因**）+ Delta sub-headings（lightweight-spec.md `### MODIFIED — 修改的行為` / `#### 規則：BR-NN`）cascade 不完整。「結構性術語 vs 散文」邊界在實作中部分模糊，需設計者拍板部分 inline labels 的歸類（F-04 / F-08）。雙版命名一致 ✓（無 WebForms 用 X、Core 用 Y 但語意相同的不一致） |
| D5-02 · dflow:section anchor 機制 | ✓（含 1 個設計選擇與 P013 §1 表不一致）| 6 個 initial anchor（implementation-tasks / current-br-snapshot / lightweight-changes / behavior-scenarios / business-rules / open-questions）全部到位；雙版位置一致；scaffolding **未注入** anchor（per resume prompt §3.1(b) 邊界 ✓）；Anchor coexistence rule 落實到 drift-verification.md（C-01 / C-03 ✓）；唯一不一致：P013 §1 表寫 `business-rules` 用於 `_index.md`，但實作 `_index.md` 採 `current-br-snapshot` anchor（避免同段 multi-anchor）— 屬合理設計選擇，COVERAGE 矩陣與實作對齊；建議修 P013 §1 表（F-06）|
| D5-03 · TEMPLATE-COVERAGE.md 維護契約 | ✓ | 14 rows + 7 欄（含 Section anchors）；雙版差異（Allowed differences）描述準確（如 row 6 「Core may include Aggregate column」對應實際 rules.md Core / WebForms 差異；row 11 「WebForms optional / emergent」對應實際 context-map.md 差異）；末段 Section Anchors 維護指引完整（單一維護契約點，不另開 SECTION-ANCHORS.md）|
| D5-04 · TEMPLATE-LANGUAGE-GLOSSARY.md | ✓（含 inclusion 邊界釐清 questions）| 13 term 每個都有實際使用位置 ✓；中英對照準確；放 repo 根（per F-11 Path A）；雙版 skill 共用一份；GLOSSARY 收錄門檻待釐清（F-05 / F-12 / F-04 / F-08 多項 inline / sub-heading 級 term 是否屬範圍）|

---

## 邊界守住對照（Dimension 6）

| 邊界 | 實施結果 | 對應 Finding |
|---|---|---|
| 未踩進 backlog § 1（content language）| ✓ — R8b 只動結構性 English heading + canonical labels；填入內容仍是中文（example：finish-feature-flow.md Step 5 list items；lightweight-spec.md `**原本**` / `**改為**` placeholder）|（F-04 / F-08 / F-10 是否屬 content vs 結構邊界尚需釐清）|
| 未踩進 backlog § 2（npm / CLI）| ✓ — R8b 影響範圍內無 npm / package.json / CLI 操作 | — |
| 未踩進 backlog § 6（根目錄改名）| ✓ — `specs/` 根目錄名稱保留 | — |
| 未動繁中版（`*-tw/`）| ✓ — `git diff 62640c7~1 12f552e -- sdd-ddd-{webforms,core}-skill-tw/` 為空 | — |
| 未動 tutorial / proposals / reviews | ✓ — `git diff` 範圍：`proposals/` 0 / `reviews/` 僅有 prompt 新增（屬 implement 流程一部分，不算 review 內容修訂） | — |
| Sub-wave 之間範圍邊界守住 | ✓ — Sub-wave 1 純新增（commit `62640c7` 無修改檔）；Sub-wave 2 不動 references（commit `0cd99fb` + `2aaba8b` 範圍：templates / scaffolding / GLOSSARY 擴充）；Sub-wave 3 不動 templates 結構（commit `61c0df4` 範圍：references + SKILL.md）；Sub-wave 4 不動 SKILL.md（commit `12f552e` 範圍：lightweight-spec.md + modify-existing-flow.md + CHANGELOG）— 實際 commit 邊界一致 | — |

---

## 審查備註

- **取樣範圍**：對 4 sub-wave 共 ~55 檔次依 Dimension 1 表格逐 sub-wave 抽樣 verify。重點檔（TEMPLATE-COVERAGE.md / TEMPLATE-LANGUAGE-GLOSSARY.md / drift-verification.md / lightweight-spec.md / phase-spec.md / _index.md / SKILL.md / Git-principles-gitflow.md / templates/CLAUDE.md / CLAUDE-md-snippet.md / init-project-flow.md / finish-feature-flow.md）細讀；其他 spot-check（grep + git diff hunk）。
- **未細讀**：繁中版（per 提示）、tutorial/、R1-R7 歷史 review、`E:/工作區/SDD-Starter-Kit/`、`reviews/round-8a-*.md`。
- **整體結論**：實作品質高，4 個 sub-wave 邊界紀律好，17 個 R8b decisions 全部可驗證落地，4 項設計決策皆達標。主要 cascade 漏網集中於 Sub-wave 3 references 對 templates 內已英文化 column labels / inline 引用的描述性 cascade，以及 templates/CLAUDE.md directory tree comment 對 `_index.md` H2 的中文殘留引用。其餘為設計邊界釐清類 question（inline labels 歸類、GLOSSARY 收錄門檻），不影響本輪 implement quality 評分，建議在 Closeout 前以「minor-fix」處理 F-01 / F-02 / F-09 / F-11 Major / Minor cascade 漏網，其他 Question 類交設計者一次性收斂。
- **建議 fix 次序**（若選 minor-fix）：(1) F-01 references column labels cascade 補完（new-phase-flow / modify-existing-flow 是最直接的 AI 操作影響）→ (2) F-02 Core SKILL.md Scaffolding 表補列 architecture-decisions-README.md → (3) F-09 + F-11 prose 引用 cascade → (4) F-03 CHANGELOG 措辭 → (5) F-04 / F-05 / F-06 / F-08 / F-10 / F-12 設計邊界 questions 一次釐清

---

**跨輪議題**：

本輪審查過程中順手觀察到一個跨輪議題候選——`scaffolding/_overview.md` 雙版實際在 R7 Wave 3 (PROPOSAL-010) 已落 canonical English（heading 全英），R8b 「檢查級別」verify 未動屬合理；但 R8b CHANGELOG 自述「修改」與實際不符。屬於 CHANGELOG 自述精準度議題，不上升為 finding，建議 Closeout 統一 verify CHANGELOG 描述與實際 git diff 的一致性，避免後續 review 重複質疑。已不佔本輪 Finding 數量。

無其他跨輪議題需轉記 `reviews/cross-round-notes.md`。
