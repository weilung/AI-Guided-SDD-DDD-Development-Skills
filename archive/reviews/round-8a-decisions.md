# Round 8a Decisions — Draft Proposal Review (PROPOSAL-012)

**對應 review 報告**: `reviews/round-8a-report.md`
**決議日期**: 2026-04-27
**決議者**: 使用者（Claude Opus 4.7 整理 + 當場修訂）

---

## 決議彙總

| Finding | 嚴重度 | 維度 | 決議 | 說明 |
|---|---|---|---|---|
| F-01 | Major | D3 | accept-with-choice (Path A) | `planning/project-review-brief.md` line 51/59/111/367 過時引用納入 § 影響範圍 |
| F-02 | Major | D3 | accept | `scaffolding/CLAUDE-md-snippet.md` 描述明示 inline + tree 雙層、區分 WebForms / Core |
| F-03 | Major | D5 | reject | Reviewer 假設前提錯誤（Obts 用 V2 不用 Dflow，無路徑衝突） |
| F-04 | Minor | D3 | accept-with-choice (Path B) | Core templates/CLAUDE.md tree 補上漏列的 `_overview.md` / `_conventions.md`，修正 P010 之前就存在的兩版不一致 |
| F-05 | Minor | D2 | accept | § 影響範圍 init-project-flow.md 章節名與 §3 對齊（write rules → Step 4 CLAUDE.md special handling） |
| F-06 | Minor | D3/D4 | accept-with-choice (Path A) | 從 § 影響範圍 移出 `planning/public-distribution-backlog.md`（§ 關聯的 Proposal 表已有） |
| F-07 | Minor | D4 | accept | § 不納入影響範圍 row 3 明示歸屬（P013 + backlog § 1, § 4） |
| F-08 | Minor | D2 | accept | §1 政策表 row 4 明示 slug 取捨 + ASCII 選項出口 |
| F-09 | Question | D5 | reject | 過渡期觀感落差屬 phantom 顧慮（無實際使用者經歷該過渡） |
| F-10 | Question | D3 | accept | § 影響範圍 CHANGELOG 列明示「歷史條目保持原樣」+ 禁用全域 grep-and-replace |

**統計**：accept 5 / accept-with-choice 3 / reject 2 / defer 0 / clarify 0 / out-of-scope 0

---

## Accept / Accept-with-choice 項目的修訂紀錄

### F-01 (accept-with-choice, Path A)

- **修訂檔案**: `proposals/PROPOSAL-012-distribution-friendly-paths.md`
- **修訂段落**: § 影響範圍
- **改動摘要**: 在 CHANGELOG.md 列之後新增一列 `planning/project-review-brief.md`，變更類型「修改」，描述指明 line 51/59/111 前瞻性註解改為 `specs/shared/`，§10 P012 候選議題段（line 367）的「`specs/_共用/` 是 machine-facing path」描述加上「（已由 P012 處理）」註記。
- **Path A 選擇理由**: 此檔雖自陳為「暫時性工作筆記」，但仍作為 Dflow 產生系統專案目錄結構的脈絡描述，需保持可信度；4 行改動成本低。

### F-02 (accept)

- **修訂檔案**: `proposals/PROPOSAL-012-distribution-friendly-paths.md`
- **修訂段落**: § 影響範圍 兩列 `scaffolding/CLAUDE-md-snippet.md`
- **改動摘要**:
  - WebForms 列描述改為「inline 路徑引用（line 44, 115–117, 138）改為 `specs/shared/...`；directory tree 範例（line 48–69）將 `_overview.md` / `_conventions.md` / `Git-principles-*.md` 從 `specs/` 直接子節點移到 `specs/shared/` 子目錄之下，與 init-project-flow.md 實際寫入路徑一致」
  - Core 列描述改為「line 145 單一 inline 引用改為 `specs/shared/...`；此檔無 directory tree 範例，tree 層不適用」

### F-04 (accept-with-choice, Path B)

- **修訂檔案**: `proposals/PROPOSAL-012-distribution-friendly-paths.md`
- **修訂段落**: § 影響範圍 兩列 `templates/CLAUDE.md`
- **改動摘要**:
  - WebForms 列描述精準化為「directory tree（line 19–39）將 `_overview.md` / `_conventions.md` 從 `specs/` 直接子節點移到 `specs/shared/` 之下」
  - Core 列描述從「同 WebForms」改為「directory tree（line 36–57）目前漏列 `_overview.md` / `_conventions.md`（兩版 init-project-flow 皆會產生這些檔，是 P010 之前就存在的兩版 oversight）；本 proposal 順手補上於 `specs/shared/` 之下，與 WebForms tree 一致」
- **Path B 選擇理由**: 使用者指出 skill 本身內部矛盾（兩版 init-project-flow 都產生這些檔、project-review-brief.md § 3.1/§ 3.2 都呈現此結構，但 Core templates/CLAUDE.md tree 漏列），P012 順手修正符合「machine-facing path contract 要穩定且一致」的精神。

### F-05 (accept)

- **修訂檔案**: `proposals/PROPOSAL-012-distribution-friendly-paths.md`
- **修訂段落**: § 影響範圍 WebForms `references/init-project-flow.md` 列
- **改動摘要**: 「Q5、file-list preview、write rules、results report」改為「Step 2 Q5、Step 3 file-list preview、Step 4 `CLAUDE.md` special handling、Step 5 results report」，與 §3 章節列表對齊。

### F-06 (accept-with-choice, Path A)

- **修訂檔案**: `proposals/PROPOSAL-012-distribution-friendly-paths.md`
- **修訂段落**: § 影響範圍
- **改動摘要**: 刪除最後一列 `planning/public-distribution-backlog.md`（該檔原描述「已新增 / 可能補充 | 保留 npm 前處理...只引用，不實作」，語義自相矛盾）。
- **Path A 選擇理由**: § 影響範圍 應為「P012 會修改的檔」清單；§ 關聯的 Proposal 表（line 209）已有此檔的「前置 / 邊界」描述，邊界資訊不丟失。

### F-07 (accept)

- **修訂檔案**: `proposals/PROPOSAL-012-distribution-friendly-paths.md`
- **修訂段落**: § 不納入影響範圍 row 3
- **改動摘要**: 「文件內容語言延後到 npm 前整體決策」改為「文件內容語言策略由 PROPOSAL-013（canonical English template + `TEMPLATE-LANGUAGE-GLOSSARY.md`）處理；雙語 template set 等更深的 localization 議題延後至 `planning/public-distribution-backlog.md § 1, § 4`」，明示歸屬。

### F-08 (accept)

- **修訂檔案**: `proposals/PROPOSAL-012-distribution-friendly-paths.md`
- **修訂段落**: §1 政策表 row 4
- **改動摘要**: 「沿用 P009 決議 8：中文討論可產生中文 slug；這是使用者語境，不是 Dflow 固定系統路徑」改為「沿用 P009 決議 8。`SPEC-ID` 與目錄結構詞仍 ASCII；slug 段是『使用者命名空間』，不是 Dflow 固定系統路徑——使用者可自選 ASCII slug 以利國際化，但 Dflow 不強制。未來若公開散佈需要 `--ascii-slug` 選項，可另案處理（已記入 §6）」，明示取捨與未來出口。

### F-10 (accept)

- **修訂檔案**: `proposals/PROPOSAL-012-distribution-friendly-paths.md`
- **修訂段落**: § 影響範圍 CHANGELOG.md 列
- **改動摘要**: 描述補上「實作時於檔尾**新增**一段...**歷史條目保持原樣**：line 17（R7 F-03 紀錄）、line 61（P010 init-project 紀錄）對 `specs/_共用/` 的引用是當時的事實紀錄，不改寫；implementer 不應對 CHANGELOG 做全域 grep-and-replace」。

---

## Reject 項目備忘

### F-03 (reject)

**理由**：Reviewer 假設「有既有 Dflow 專案採用 `specs/_共用/`（如 Obts），P012 落地後會面臨雙存在問題」，但實際無此專案。Obts 等內部專案使用血緣相關的 SDD-Starter-Kit V2，路徑為 `docs/specs/`，不在 Dflow 路徑系統下，無路徑衝突。Backlog § 5 / § 7 是 hypothetical 未來相容性的家，與 P012「改 path」職責無直接邊界關係（不同於 § 6「根目錄命名」與 P012 有刻意劃線的邊界）。Obts 等 V2 專案未來若改採 Dflow，是專案級「檔案 cherry-pick」決定（例如 `glossary.md` 拿過來），不是 Dflow proposal 的職責。

**使用者補充脈絡**：npm 散佈後才會讓 Obts 使用 WebForms 版本，並會有小專案測試使用 Core 版本。在那之前 Dflow 無實際使用者。

### F-09 (reject)

**理由**：Reviewer 預設「P012 落地後到 P013 落地之間，新採用 Dflow 的使用者會經歷『path 英文 / heading 中文』觀感落差」，但 Dflow 目前無實際採用者，P012 → P013 的過渡期僅內部開發者經歷，且開發者完全知曉此設計順序。P012 § 關聯的 Proposal 表的「後續 System Document Template Coverage proposal | 前置」一行已隱含 P012 → P013 順序，無需在 § 問題描述加 UX 體貼註解。與 F-03 reject 邏輯一致：reviewer 兩處皆假設了實際不存在的使用者群體。

**使用者補充脈絡**：npm 散佈後才有真實使用者（Obts WebForms / 小專案 Core），屆時 P012 + P013 都已 implemented，過渡期完全不存在。

---

## Out-of-scope 項目

無。

---

## Proposal 狀態推進紀錄

| Proposal | 推進前 | 推進後 | 推進理由 |
|---|---|---|---|
| PROPOSAL-012 | draft | approved | 全部 10 個 finding 處理完畢；accept / accept-with-choice 共 8 項已實際修訂；reject 2 項已記錄理由；無 clarify / defer-to-revise / defer 阻擋條件 |

---

## 下一步

1. **R8a Implement 階段**：執行 PROPOSAL-012 的影響範圍修改（路徑改名 + Core templates/CLAUDE.md tree 補上 + project-review-brief.md 過時引用更新 + CHANGELOG 新增段）；建議由 Cursor 或 Claude Code 執行，範圍小（~10 檔），單 session 可完成。建議啟動提示詞另起 `reviews/prompts/round-8a-implement.md`。
2. **R8a Implement Review**：Implement 完成後仿 R7 pattern 做 implement review。
3. **R8b（PROPOSAL-013）**：路徑英文化（P012）是 P013 樣板覆蓋的前置，P013 review/approve/implement 為平行 wave，不擋 R8a 進度。

---

## 跨輪發現備忘

無新議題（原 reviewer 已在報告末段確認本輪無新 P001-P011 跨輪發現）。

R8a Approve 過程中的觀察補記（不新增 cross-round-notes 條目，因屬本輪正常處理範圍）：

- **F-04 揭示的兩版 oversight** 反映 P010 之前 Core templates/CLAUDE.md 的 tree 範例未充分對應 init flow 實際輸出。此問題已由 P012 順手解決（透過 F-04 Path B 的修訂指引），無需另記跨輪議題。
- **F-03 與 F-09 的共同 pattern**：兩個 finding 都建立在「假設有實際 Dflow 使用者」前提上，但 Dflow 目前仍純內部開發狀態，真實使用者要 npm 散佈後才會出現（Obts WebForms + 小專案 Core）。Reviewer 在類似 finding 上若無確認 Dflow 採用狀態，可能持續產生 phantom UX 顧慮——此 pattern 可作為未來 review prompt 設計時的補強項（例如在 review prompt 加入「先確認 Dflow 是否已有實際採用者」的脈絡），但不阻擋本輪推進。
