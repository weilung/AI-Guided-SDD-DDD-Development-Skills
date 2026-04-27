# Round 8b Decisions — Draft Proposal Review (PROPOSAL-013)

**對應 review 報告**: `reviews/round-8b-report.md`
**決議日期**: 2026-04-27
**決議者**: 使用者（Claude Opus 4.7 整理 + 當場修訂）
**Reviewer 模型**: Cursor Claude Opus 4.7（review 階段）

---

## 決議彙總

| Finding | 嚴重度 | 維度 | 決議 | 說明 |
|---|---|---|---|---|
| F-01 | Major | D3 | accept-with-choice (Path A) | templates/CLAUDE.md 全 H2/H3 英文化補入 §影響範圍；無既有用戶包袱，最簡單一致 |
| F-02 | Major | D3 | accept | §4 矩陣補 3 列（Bounded Context definition / Aggregate worksheet / Project AI guide）|
| F-03 | Major | D3 | accept | §影響範圍 補 pr-review-checklist 兩列；section name cascade 同步 |
| F-04 | Major | D3 | accept | finish-feature-flow.md Step 5 Integration Summary Chinese labels 列入修改 |
| F-05 | Major | D3 | accept | SKILL.md Templates & Scaffolding 段 cascade 列入修改 |
| F-06 | Major | D2 | accept-with-choice (Path A) | anchor 欄位併入 TEMPLATE-COVERAGE.md（不另開 SECTION-ANCHORS.md）|
| F-07 | Major | D5 | accept-with-choice (Path A) | §5 加 baseline 比對表，明列為「修補 P010」；§關聯 Proposal P010 列同步 |
| F-09 | Minor | D4 | accept | §不納入影響範圍 加註 templates/CLAUDE.md 路徑同步交由 P012 處理 |
| F-11 | Minor | D6 | accept-with-choice (Path A) | TEMPLATE-COVERAGE.md / GLOSSARY.md 放 repo 根，雙版共用 |
| F-12 | Minor | D5 | accept | §1.1 glossary 補 Lightweight Changes / Resume Pointer / Behavior Delta 三列；§7 加註指向 §1.1 |
| F-13 | Minor | D3 | accept | ddd-modeling-guide.md 補 context-map.md 缺檔處理 |
| F-14 | Minor | D3 | accept | Git-principles-* 4 列描述精化（範圍縮為 Integration Commit Message Conventions 段）|
| F-08 | Question | D5 | accept | §1 加「Anchor coexistence rule」段；§影響範圍 drift-verification 兩列描述補充 |
| F-10 | Question | D4 | accept | §關聯 Proposal backlog 列改為「邊界（部分接管）」明示接管 § 1 結構性語言 + § 4 Glossary |
| F-15 | Question | D6 | accept-with-choice (Path B) | §1 加「Anchor naming rules」段：namespacing 立刻定（全 repo 共用語意）；versioning 延後到 backlog § 7。同時補「Design intent」段釐清 anchor 啟用 localized heading 的長期動機 |
| F-16 | Question | D5 | accept（隨 F-07 完成）| F-07 Path A 修訂的 baseline 比對表已涵蓋此 finding |
| F-17 | Question | D3 | accept-with-choice (Path A) | git-integration.md 兩版列「檢查」級別；verify 確認該檔已全英無修改範圍 |

**統計**：accept 11 / accept-with-choice 6 / reject 0 / defer 0 / clarify→accept 0 / out-of-scope 0

---

## Accept / Accept-with-choice 項目的修訂紀錄

### F-01 (accept-with-choice, Path A)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §1 第 6 點補一句；§影響範圍 補兩列（webforms / core templates/CLAUDE.md）
- **選定 path 理由**: 使用者澄清「沒有既有用戶」（Dflow 開發中，npm install 後才會有實際用戶）→ 「破壞既有用戶 CLAUDE.md 合併」顧慮不成立 → 全英文化是最直接、最一致的選擇
- **改動摘要**: §1 第 6 點補「`templates/CLAUDE.md` H2 navigation 契約以英文形式延續 P007c」；§影響範圍 補入兩列詳列 H2/H3 中文標題清單（系統脈絡 / 開發流程 / 核心原則 / 目錄結構 / Ceremony 三層 / 新增功能 / Bug 修復 / Feature 收尾 / Git 整合 / Domain 層規範 / 術語表 / AI 協作注意事項 等），同檔 specs/ 路徑同步交 P012 處理
- **既有機制驗證**: P007c H2 navigation 契約改英文後仍是 navigation 段，本質契約不變

### F-02 (accept)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §4 矩陣
- **改動摘要**: 矩陣 11 列 → 14 列（補 Bounded Context definition / Aggregate worksheet / Project AI guide）；F-01 Path A 結論已反映在 Project AI guide 列的 Parity 描述

### F-03 (accept)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §影響範圍 references 區
- **改動摘要**: 補 webforms / core 兩列 references/pr-review-checklist.md（修改），明列 cascade 改名清單（`輕量修改紀錄` → `Lightweight Changes`、`接續入口` → `Resume Pointer`、`行為變更（Delta）` → `Behavior Delta`）

### F-04 (accept)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §影響範圍 兩列 finish-feature-flow.md 描述
- **改動摘要**: webforms / core 描述都從「BC sync 前缺檔時用 template 建立」擴充為兩件事（+ Step 5 Integration Summary Chinese labels 改 canonical English：`Feature 目標` / `變動範圍` / `關聯 BR-ID` / `Phase 清單` / `輕量修改` / `下一步`），新詞補入 GLOSSARY

### F-05 (accept)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §影響範圍 兩列 SKILL.md 描述
- **改動摘要**: 從原本的 (1)+(2) 擴為 (1)+(2)+(3)：補 Templates & Scaffolding 段內 template section name 引用 cascade 同步

### F-06 (accept-with-choice, Path A)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §4 矩陣（表頭 + 14 列各加第 7 欄 Section anchors）；§影響範圍 第 3 列收斂為單一條目；§4 末尾加「Section anchors 欄使用方式」說明
- **選定 path 理由**: 無既有用戶包袱 → 最少維護點優先；anchor 與 template 邏輯同源；無雙檔同步成本
- **改動摘要**: 矩陣擴為 7 欄；6 個 initial anchor 對應 mapping（Feature dashboard / Phase spec / Lightweight spec / Rules index / Behavior snapshot 各列填上對應 anchor）

### F-07 (accept-with-choice, Path A)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §5（補 baseline 比對表）；§關聯 Proposal P010 列加註修補意圖
- **選定 path 理由**: 補 README.md 為 baseline 是合理的（空目錄對新人不友好）；明列「修補 P010」邊界清楚；同時吸收 F-16
- **改動摘要**: §5 Core baseline 區塊補加「Baseline 比對」副段（per F-07 / F-16 Path A），列出 P010 4 項 baseline + decisions/ 空目錄 vs P013 4 項 + decisions/ + decisions/README.md 全部對照；明列「P010 既有不變、唯一新增是 README.md」；§關聯 Proposal P010 列改為「修補缺口（baseline 擴張）」並寫入「P013 implemented 後 P010 Step 4 加註 cross-reference」
- **既有機制驗證**: P010 § 1 Step 4 verify 確認既有 4 項（features/、glossary.md、context-map.md、tech-debt.md）+ 空目錄 decisions/，沒列 README.md → P013 修補成立

### F-09 (accept)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §不納入影響範圍 補一列
- **改動摘要**: 加「`templates/CLAUDE.md` 內 specs/ 目錄結構同步」一列，明示「由 P012 處理（P012 §影響範圍已列）；本 proposal 只負責 H2/H3 canonical English 結構標題」邊界
- **既有機制驗證**: P012 §影響範圍 line 175-176 已明確列入 templates/CLAUDE.md directory tree 修正

### F-11 (accept-with-choice, Path A)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §1.1 標題段；§4 標題段；§影響範圍 第 1, 2 列
- **選定 path 理由**: 兩份檔本質是雙版間的對照契約必須單一 source；repo 根與 README/CHANGELOG 同層的慣例；npm 散佈時 repo 根維護檔最易被使用者注意
- **改動摘要**: §1.1 / §4 開頭都明寫「置於 repo 根（per F-11 Path A），兩版 skill 共用」；§影響範圍 兩列加註 repo 根 + 共用一份

### F-12 (accept)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §1.1 glossary 表；§7 task 範例下方加註
- **改動摘要**: glossary 表補 3 列（`Lightweight Changes` / `Resume Pointer` / `Behavior Delta`）；§7 加註「DOC-1 中的 Lightweight Changes 與 Current BR Snapshot 為 §1.1 glossary 列出的 canonical heading；實施時必須與 _index.md 英文化後的 heading 完全一致」

### F-13 (accept)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §影響範圍
- **改動摘要**: 補一列 `sdd-ddd-core-skill/references/ddd-modeling-guide.md`（修改），明列 Bounded Context Relationships 段補 context-map.md 缺檔處理

### F-14 (accept)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §影響範圍 4 列 Git-principles-* 描述
- **改動摘要**: 從「結構性中文詞彙改為 canonical English」精化為「Integration Commit Message Conventions 段內的固定 labels（`Feature 目標` / `變動範圍` / `關聯 BR-ID` / `Phase 數` / `輕量修改` 等）改為 canonical English；其他段落已英文，僅檢查」

### F-08 (accept)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §1 anchor 段補「Anchor coexistence rule」段；§影響範圍 drift-verification 兩列描述補充
- **改動摘要**: §1 加段明示「`dflow:section` HTML comment anchor 與 P008 markdown auto-id heading anchor 是不同維度，並存而非取代；drift-verification 仍以 BR-ID + markdown auto-id 為主索引」；`templates/rules.md` Behavior anchor 欄位範例值仍為 `[BR-001](./behavior.md#br-001-rule-name)` 樣式
- **既有機制驗證**: P008 §核心構件 verify 確認機械驗證 #2「Anchor 有效性」依 markdown auto-id 運作，與 dflow:section 不衝突

### F-10 (accept)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §關聯 Proposal backlog 列
- **改動摘要**: 列改為「邊界（部分接管）」並明示：(1) backlog § 1 結構性語言策略由 P013 §1 接管，content 語言策略仍延後；(2) backlog § 4 Template Language Glossary 由 P013 §1.1 接管；P013 implemented 後 backlog § 1 / § 4 對應段加註「partially fulfilled by P013」

### F-15 (accept-with-choice, Path B + 額外設計動機補寫)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`、`planning/public-distribution-backlog.md`
- **修訂段落**: P013 §1 補「Anchor naming rules」段 + 「Design intent」段；backlog § 1 加註 P013 接管 + 新增 §1.1「anchor-enabled localized heading」候選方向
- **選定 path 理由**: namespacing 有當下應用場景（business-rules 跨檔出現），需立刻定；versioning 沒有實證資料（沒有既有 instance），延後到 npm 散佈後與其他升級議題一起設計屬合理 YAGNI
- **改動摘要 1**: §1 加「Anchor naming rules」段：(1) Namespacing — anchor id 全 repo 共用語意（document-type-agnostic stable identifier）；(2) Versioning 延後到 backlog § 7「Dflow Version Upgrade / Migration Strategy」範疇
- **改動摘要 2**: §1 加「Design intent」段：明示 anchor 機制不只是 heading 漂移 fail-safe，更是「啟用 localized heading 的基礎契約」——這是 R8b approve session 中使用者提供的歷史脈絡（當初寫 P013 的 AI 提過此 idea，但設計動機未被寫明）；補寫進 P013 避免後續 review / implement 時設計脈絡丟失
- **改動摘要 3**: backlog § 1 末尾補註「結構性語言策略由 P013 接管」；新增 § 1.1 候選方向「anchor-enabled localized heading（P013 後）」，含 Why / 實證條件 / 為何不在 P013 立刻 implement 三段
- **既有機制驗證**: backlog § 7 Dflow Version Upgrade 範疇 verify 包含 anchor versioning 自然落腳處

### F-16 (accept，隨 F-07 完成)
- **修訂檔案**: 無（已隨 F-07 Path A 處理）
- **改動摘要**: F-07 Path A §5 baseline 比對表已涵蓋「P010 既有 4 項 vs P013 §5 對照」需求

### F-17 (accept-with-choice, Path A)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: §影響範圍
- **選定 path 理由**: F-17 是 Question 級別 due diligence 列入；webforms / core 同層既有的 _overview.md / _conventions.md 也是「檢查」級別，多兩列一致性高；reviewer 之後不會再質疑漏列
- **改動摘要**: 補 webforms / core 兩列 references/git-integration.md（檢查），明列「R8b approve verify 顯示該檔已全英無 P013 §1 範圍內的 Chinese 結構性詞，implement 階段執行者跑一次 grep verify 並明列已 verify 無需改動」
- **verify 紀錄**: 本 session 已執行 grep `Change Scope|變動範圍|Lightweight Change|輕量修改` 對兩版 git-integration.md 確認**無任何匹配**；327 + 299 行的兩版檔所有 heading 全英文

---

## 既有機制相容性確認紀錄（Dimension 5）

| C-NN | 檢查點 | 處理結論 | 對應 Finding |
|---|---|---|---|
| C-01 · rules / behavior anchor | ⚠ → ✓（補 Anchor coexistence rule 段後）| P003 BR-ID 結構不破壞；P013 dflow:section 為輔助定位機制 | F-08（已處理）|
| C-02 · lightweight task guard | ✓ | P004 lightweight 精神相容；T2 task guard「不以 task 數自動升 T1」與 P004 設計一致 | — |
| C-03 · drift-verification anchor 與新 anchor 機制並存 | ⚠ → ✓ | P008 機械驗證仍依 markdown auto-id；dflow:section 不取代 BR-ID link | F-08（已處理）|
| C-04 · § H Lightweight Changes vs § 7 Implementation Tasks 雙層紀錄 | ✓ | P009 § H 是 feature 層（_index.md 紀錄）vs P013 §7 是 spec 層（lightweight-spec.md task list），不同層級協作不衝突 | F-12（已處理）|
| C-05 · _index.md 英文化是否破壞 P009 § D 既有結構識別 | ⚠ → ✓ | P009 § D 6 sections 順序 / 存在不變，僅 label 改英文；reference cascade 已在 F-03 / F-04 / F-05 處理 | F-03 / F-04 / F-05（已處理）|
| C-06 · mandatory baseline 一致性 | ⚠ → ✓ | P010 既有 4 項 + 空目錄 decisions/ 不變；唯一擴張是 decisions/README.md（明列為 P010 漏列補上）| F-07 / F-16（已處理）|
| C-07 · scaffolding 邊界（glossary 從 scaffolding 重分類到 templates）| ✓ | P010 scaffolding 定位「init 寫入後由專案持有」與 P013 templates 定位「workflow 期間 living document」不衝突；glossary.md 從未實際存在於 scaffolding/ | — |
| C-08 · 路徑使用 post-P012 | ✓ | P013 §影響範圍 表內無 `_共用/` 引用；F-09 已加註明確邊界 | F-09（已處理）|

---

## 設計決策拍板紀錄（Dimension 6）

| 決策 | 拍板 path | 理由 |
|---|---|---|
| **Canonical English structural language** | A（採用，scope 含 templates/CLAUDE.md H2 navigation labels）| 無既有用戶包袱；npm 散佈時 canonical 一致最乾淨；P007c H2 navigation 契約以英文形式延續，本質不變 |
| **`<!-- dflow:section X -->` anchor** | A（機制採用）+ namespacing 立刻定 + versioning 延後 | 6 個 initial anchor stable identifier 設計直覺；business-rules 跨檔出現需立刻定 namespacing；versioning 無實證資料延後到 backlog § 7；補 Design intent 段釐清 anchor 啟用 localized heading 的長期動機 |
| **TEMPLATE-COVERAGE.md** | A（含 Section anchors 欄）| anchor 與 template 邏輯同源，併欄不破壞矩陣可讀性；最少維護點；F-15 命名規範由 P013 §1 承擔，不擠矩陣；放 repo 根雙版共用 |
| **TEMPLATE-LANGUAGE-GLOSSARY.md** | A（採用，repo 根，雙版共用）| 與 backlog § 4 對應（partially fulfilled by P013）；放 repo 根與 README/CHANGELOG 同層；雙版 skill 共用一份避免同步成本 |

---

## Reject / Defer 項目備忘

無（本輪無 reject 或 defer）。

---

## Out-of-scope 項目

無（本輪無 out-of-scope finding）。

---

## Proposal 狀態推進紀錄

| Proposal | 推進前 | 推進後 | 推進理由 / 維持理由 |
|---|---|---|---|
| PROPOSAL-013 | draft | **approved** | 所有 accept / accept-with-choice finding 已修訂完成；無 clarify 未解決；無 defer-to-revise；4 項新概念設計決策全部拍板 |

**推進條件補充**：PROPOSAL-012 仍 draft（R8a 未完成）。**P013 已 approved 但 implement 需等 P012 implemented 完成後才能啟動**——P013 影響範圍含後 P012 的路徑（如 templates/CLAUDE.md directory tree 路徑修正交由 P012 處理；§關聯 Proposal P012 列前置依賴）；P012 路徑未敲定前，P013 部分修訂內容（特別是引用路徑的 reference cascade）無法 implement。

---

## R8b Implement Sub-wave 切分建議

基於 reviewer 在 Dimension 7 的建議與本 session 處理過程的工作量觀察，建議 R8b Implement 切分為 4 個 sub-wave：

| Sub-wave | 範圍 | 依賴 | 預估規模 |
|---|---|---|---|
| **Sub-wave 1** | 新增 12 份 templates / scaffolding（含 README）+ 2 份維護契約檔（`<repo root>/TEMPLATE-COVERAGE.md` 含 Section anchors 欄、`<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md`）；此階段不改既有檔 | P012 implemented | 中 — 純新增 |
| **Sub-wave 2** | 既有 templates / scaffolding canonical English（`_index.md`、`phase-spec.md`、`lightweight-spec.md`、`context-definition.md`、`aggregate-design.md`、`templates/CLAUDE.md`、`scaffolding/CLAUDE-md-snippet.md`、`Git-principles-{gitflow,trunk}.md`）+ 同步擴充 `TEMPLATE-LANGUAGE-GLOSSARY.md` + 全部既有 templates 加 `<!-- dflow:section X -->` anchor | Sub-wave 1 提供 glossary / matrix 起點 | **大** — 結構性改名集中爆發；本輪是工作量最大的 sub-wave |
| **Sub-wave 3** | references cascade 更新（`init-project-flow.md`、`new-feature-flow.md`、`modify-existing-flow.md`、`finish-feature-flow.md`、`drift-verification.md`、`pr-review-checklist.md`、`git-integration.md`、`ddd-modeling-guide.md` Core）+ SKILL.md cascade（兩版）| 必須 Sub-wave 2 結束（依賴 canonical names 定案）| 中 |
| **Sub-wave 4** | `lightweight-spec.md` 加 Implementation Tasks 段（含 modify-existing-flow.md task checklist 流程更新）；CHANGELOG.md 紀錄 | Sub-wave 2 | 小 |

**切分備註**：
- 若 Codex 一次包不完，可作為斷點 checkpoint
- Sub-wave 1 與 Sub-wave 4 規模較小可合併 review；Sub-wave 2 / 3 各自獨立 review session 可較好控制 cascade 漏網
- Sub-wave 2 是高風險 wave（結構標題重命名集中爆發 + cascade 風險）；建議單獨成 commit / PR

**Implement 啟動前置條件**：
1. PROPOSAL-012 implemented 完成（路徑前提，特別 templates/CLAUDE.md directory tree、init-project-flow.md `_共用/` → `shared/` cascade 都需 P012 先確認）
2. 撰寫 `reviews/prompts/round-8b-implement.md`（仿 R7 pattern；目標執行者 Codex；切分 sub-wave 落實到 prompt）

---

## 下一步

1. **R8a 完成 → P012 implement → P012 implemented**
2. **撰寫 `reviews/prompts/round-8b-implement.md`**：依本文件 Sub-wave 切分建議撰寫；目標執行者 Codex（cascade 範圍大，Codex 一次大批包效率較好）
3. **R8b Implement 階段**：執行 PROPOSAL-013 的影響範圍修改（建議由 Codex 一次包完，若 token 不足則 Cursor 接手剩餘 sub-waves）
4. **R8b Implement Review**：仿 R7 Implement Review pattern；4 個 sub-wave 可考慮分 2 次 Review（Sub-wave 1+4 / Sub-wave 2+3）

---

## 跨輪發現備忘

**議題候選 A**（reviewer 報告末段提示，本輪未轉記）：`templates/CLAUDE.md` 與 `scaffolding/CLAUDE-md-snippet.md` 的 H2 navigation 契約（P007c）vs canonical English structural language（P013）兩者張力。
- **本輪處理結論**：因「沒有既有用戶」（per project memory），F-01 採 Path A 全英文化解決此張力；P007c H2 navigation 契約以英文形式延續，不再有張力。
- **是否轉記 cross-round-notes**：不需要——已被 F-01 Path A 解決。

**議題候選 B**（reviewer 報告末段提示）：references 引用 template section name 的 cascade 維護（pr-review-checklist / finish-feature-flow / SKILL.md / ddd-modeling-guide），是否該設一份「reference ↔ template section name 索引表」避免未來再次漏列。
- **本輪處理結論**：未在 P013 一次解決——未來需要再評估。
- **建議**：本輪不轉記 cross-round-notes；待 R8b Implement 完成後評估實際維護負擔，若再出現 reference cascade 漏網再開 proposal 處理（屬於 P013 的 § 不納入影響範圍 中「`references/*.md` WebForms / Core 流程同步矩陣」的範疇延伸）。

**新增發現（R8b approve session 過程中浮現）**：
- **anchor 機制的設計動機本來沒寫明**：使用者澄清當初寫 P013 的 AI 確實提過 anchor 概念，且設計動機就是「讓有 anchor 的段落可以中文/本地化標題」——但 P013 §1 原版只寫了「Headings are for humans; anchors are for AI/tool positioning」這個淺層 framing，沒寫出深層動機。本 session 已在 P013 §1 補「Design intent」段說明 anchor 啟用 localized heading 的長期目的，並在 backlog § 1 補新增 §1.1「anchor-enabled localized heading（P013 後）」候選方向。屬本輪 finding 處理副產物。
