# R8 全程總覽（R8a + R8b）

> **目的**：作為 Closeout C1 / 改名遷移 / Dflow V1 release notes 的素材來源；幫助新 session 或新貢獻者快速建立 R8 全貌的脈絡。
> **建立日期**：2026-04-28（R8 收尾後立即記錄）
> **狀態**：R8 全程結束；進入 Post-R8 階段

---

## 一句話總結

R8 同時處理兩件「Dflow 走向公開散佈」的前置事項：**R8a** 把 machine-facing 路徑英文化（`specs/_共用/` → `specs/shared/`），**R8b** 把所有 Dflow-managed `.md` 文件納入樣板覆蓋契約並導入 canonical English structural language + section anchor 機制。

---

## R8a vs R8b 範圍對比

| 項目 | R8a（PROPOSAL-012）| R8b（PROPOSAL-013）|
|---|---|---|
| 主題 | 路徑英文化 | 樣板覆蓋 + 結構性術語英化 + anchor 機制 |
| 範圍規模 | 小（10 檔）| 中高（~55 檔次）|
| 設計決策 | 0 項新概念 | 4 項新概念落地 |
| Implement 結構 | 單一 wave | 4 sub-waves |
| Review findings | 3 (1M / 2m / 0Q) | 12 (0C / 2M / 6m / 4Q) |
| 主要 implementer | Claude Opus 4.7 (本主 session) | Codex gpt-5.4 + Cursor Codex 5.3 / Claude Opus 4.7 |
| 主要 reviewer | Cursor Claude Opus 4.7 | Cursor Claude Opus 4.7 |

---

## R8 commit chain（時序由舊到新）

```
2803f27  Round 8a Implement                                 ← R8a 起點
f5df981  Round 8a/8b Implement prompts
fb763a2  Round 8a Implement Review Fix (re-commit)
═══════════════════════ R8a 收尾 ═══════════════════════
62640c7  Round 8b Sub-wave 1 COMPLETE (新增 14 檔)
0cd99fb  Round 8b Sub-wave 2 PARTIAL                        ← Codex gpt-5.4 中斷
74b53f3  Round 8b Sub-wave 2 resume prompt
2aaba8b  Round 8b Sub-wave 2 COMPLETE                       ← Cursor 接續完成
ccda7d2  Round 8b Sub-wave 3 wrapper prompt
61c0df4  Round 8b Sub-wave 3 COMPLETE
12f552e  Round 8b Sub-wave 4 COMPLETE
882a41e  Round 8b Implement Review prompt
9288742  Round 8b Implement Review report + Approve prompt
ef0bac2  Round 8b Implement Review Fix (12 findings)        ← R8 終點
```

R8a 之前的 R8 啟動 commits（draft → review → approve）位於 `c9480f8` 之前（`Round 8a與b Approve完成`）。

---

## R8a 全程概要

### 範圍

**Path policy 落地**：將 `/dflow:init-project` 產生的專案級 scaffolding 目的地由 `specs/_共用/` 改為 `specs/shared/`，符合 P012 §1「machine-facing paths and filenames default to English」政策。

### 修改檔（共 12 檔，2 個 commits）

| Commit | 檔數 | 主要內容 |
|---|---|---|
| `2803f27` | 10 | 雙版 SKILL.md + init-project-flow.md + scaffolding/CLAUDE-md-snippet.md + templates/CLAUDE.md + planning/project-review-brief.md + CHANGELOG |
| `fb763a2` | 6 | Implement Review Fix 3 findings：雙版 SKILL.md Project Structure tree + CHANGELOG R8a 段內容式自指 + cross-round-notes 議題 4 |

### Implement Review

**3 findings**（1 Major / 2 Minor / 0 Question）全部 accept inline 處理：

- **F-01 (Major)**：雙版 SKILL.md § Project Structure tree 將 `_overview.md` / `_conventions.md` 移到 `specs/shared/` 之下（implementer 漏改的條件子句）
- **F-02 (Minor)**：CHANGELOG R8a 段內 line 17/61 自指因新段插入後位移失效 → 改為內容式自指（Path B：「2026-04-24 R7 Implement Review Fix 段下的 F-03 條目」）
- **F-03 (Minor)**：CHANGELOG「修改檔案 (10 檔)」標題加註 P012 §影響範圍 11 列中 README 經 verify 無需修改

### 跨輪議題（轉記 cross-round-notes.md 議題 4）

`README.md:55/79` 引用舊檔名 `git-flow-integration.md`（P011 rename 後未同步殘留）→ 延到 Closeout 處理。

---

## R8b 全程概要

### 範圍

**樣板覆蓋契約 + canonical English structural language + section anchor 機制**：

1. 補齊所有 Dflow-managed `.md` 的 source template（Sub-wave 1 新增 12 檔）
2. 將既有 templates / scaffolding 結構性術語英文化（Sub-wave 2）
3. references / SKILL.md cascade 同步（Sub-wave 3）
4. lightweight-spec Implementation Tasks + task guard 細節（Sub-wave 4）

### 4 個 Sub-wave 範圍與 commits

| Sub-wave | 範圍 | 修改檔數 | Commits |
|---|---|---|---|
| **Sub-wave 1** | 純新增：12 templates + 1 scaffolding（Core ADR README）+ 2 維護契約檔（COVERAGE / GLOSSARY） | 14 | `62640c7` |
| **Sub-wave 2** | 既有 templates / scaffolding canonical English + 6 個 dflow:section anchor 注入 + GLOSSARY +3 term | 17 | `0cd99fb` (PARTIAL) + `2aaba8b` (COMPLETE) |
| **Sub-wave 3** | references cascade + SKILL.md cascade（雙版 references 各 9 檔 + 雙版 SKILL.md + Core ddd-modeling-guide）| 19 | `61c0df4` |
| **Sub-wave 4** | lightweight-spec.md Implementation Tasks 段內容 + modify-existing-flow.md task guard 細節 + CHANGELOG | 5 | `12f552e` |
| **R8b Implement 小計** | | ~55 | 5 個 implement commits |

### 4 項設計決策（R8b approve session 拍板，Path A 全採用）

| 決策 | 落地形式 |
|---|---|
| **Canonical English structural language** | 雙版 templates / scaffolding / SKILL.md / references 內 H2/H3 結構性 heading + 固定 labels 全英；Implement Review Fix 後連帶 inline bold labels 也英化（DD-A Path A） |
| **`<!-- dflow:section X -->` anchor 機制** | 6 個 initial anchor 注入到對應 templates；scaffolding 不加；P008 drift-verification 加「Anchor coexistence rule」段明示 markdown auto-id 仍為 primary index |
| **TEMPLATE-COVERAGE.md 維護契約** | repo 根單一檔；14 rows × 7 columns（含 Section anchors 欄）；雙版 skill 共用一份 |
| **TEMPLATE-LANGUAGE-GLOSSARY.md** | repo 根單一檔；Implement Review Fix 後共 18 term + Inclusion Criteria 段（4 條收錄條件 + 3 條排除條件） |

### Implement Review

**12 findings**（0 Critical / 2 Major / 6 Minor / 4 Question）全部 accept：

- **2 Major**：F-01 references 對 templates 已英文化 column / heading 中文殘留 cascade 補完；F-02 Core SKILL.md Scaffolding 表漏列 architecture-decisions-README.md
- **6 Minor**：F-03 CHANGELOG 措辭 / F-06 P013 §1 anchor 表修訂 / F-07 lightweight-spec H3/H4 cascade / F-09 context-definition prose 引用 / F-10 finish-feature-flow Step 5 list items / F-11 templates/CLAUDE.md directory tree comment cascade
- **4 Question 集中於 2 個設計議題**：
  - **DD-A** (inline bold labels 歸類) → Path A 全英化，連帶 F-04 / F-08 / F-10
  - **DD-B** (GLOSSARY 收錄門檻) → Path A 補 Inclusion Criteria 段，不擴 term，連帶 F-05 / F-12

### Implement Review Fix 修改檔（22 檔 + 1 新增）

涵蓋雙版 templates / references / SKILL.md / TEMPLATE-LANGUAGE-GLOSSARY.md 增 5 term + Inclusion Criteria 段 / PROPOSAL-013 §1 anchor 表 + design note / CHANGELOG 措辭。

---

## R8 整體統計

| 項目 | 數字 |
|---|---|
| 總 commit 數 | 13 個（含 prompt commits） |
| Implement commits | 9 個（R8a 2 + R8b 5 + Fix commits 2） |
| 修改檔數總和 | ~85 個檔次 |
| 新增檔（Sub-wave 1） | 14 個（12 templates + 2 維護契約） |
| 維護契約檔總計 | 2 個（TEMPLATE-COVERAGE.md / TEMPLATE-LANGUAGE-GLOSSARY.md） |
| GLOSSARY 最終 term 數 | 18 個 |
| TEMPLATE-COVERAGE 最終 rows | 14 rows × 7 columns |
| dflow:section anchor 個數 | 6 個 initial anchor |
| 通過的 review findings | R8a 13 + R8b 29 = 42 個 findings 全程處理 |

---

## 關鍵設計決定（影響 Post-R8 走向）

### 1. Path A 全英化路線（DD-A 拍板）

使用者明示：「目前全版英文，主要是要國際化，等可以國際化的版本確定後，未來會演化為 npm 安裝的版本，到時候會評估在可以的地方做中文（本地語系）。」

**對 Post-R8 影響**：
- Closeout C1 中文版同步可重新評估必要性（Tutorial 中文版優先級高於 skill 中文版）
- npm 散佈時可考慮 anchor-enabled localized heading 機制（已在 backlog § 1.1 紀錄）

### 2. 兩個 repo 模型（dev + dist）— 延後到 npm 前

使用者提案：本 repo 改名為 dev repo，新建 dist repo 作發布用。**結論：V1 階段先單 repo**（避免兩 repo 同步機制 overhead；真實使用者出現再切）。

### 3. Tutorial = 同事審視介面（不是給 AI 讀的）

Tutorial 必須能讓「未實際使用 Dflow 的同事」看完就理解流程 → 結構性決定：劇情驅動 + 完整對話展示 + 生成 spec 檔可瀏覽。

---

## R8 留給 Post-R8 的 4 個未完成事項

| 事項 | 來源 | 處理時點 |
|---|---|---|
| README.md `git-flow-integration.md` 殘留 | R8a cross-round-notes 議題 4 | Closeout 統一處理 |
| Tutorial 重建 | R8 期未在範圍內 | Post-R8 立即開工（Subagent 模式驗證後） |
| 繁中版 skill 同步 | R8 範圍外（per Closeout C1）| Tutorial 完成後評估必要性 |
| 改名遷移 + 新 repo | R8 範圍外 | Closeout 全部完成後 |

---

## Post-R8 待辦清單（順序建議）

1. **本 session 內**：
   - ✅ R8 全程總覽（本檔）
   - 寫 Tutorial 規劃 + Subagent 實驗計畫（`planning/tutorial-and-post-r8-plan.md`）
   - 跑 Tutorial Subagent 第 1 段（試水溫）
   - 寫 handoff notes（`planning/post-r8-handoff.md`）給 Debian 新 session

2. **Debian 新 session（連假期間）**：
   - 跑 Tutorial Subagent 剩餘段（依規劃文件）

3. **連假後**：
   - Closeout C1（中文版 skill 同步 — 視 Tutorial 中文版產出再決定範圍）
   - Closeout C2（Tutorial 收尾、調整、補充）
   - Global Sweep（仿 R6 後做法，可選）
   - 改名遷移（dev repo + dist repo 切割）
   - **Dflow V1 ships**

---

## 與相關文件的關聯

| 文件 | 關係 |
|---|---|
| `planning/public-distribution-backlog.md` | npm 前才處理的議題；R8 解決了路徑（§5）+ 結構性語言（§1 部分接管）+ Glossary（§4 部分接管） |
| `planning/project-review-brief.md` | 系統文件結構觀；R8a 已更新前瞻性 `_共用` 引用 |
| `reviews/cross-round-notes.md` | 議題 4（README.md `git-flow-integration.md` 殘留）由 R8a Implement Review Fix 加入 |
| `proposals/PROPOSAL-012-distribution-friendly-paths.md` | R8a 藍圖；狀態 approved |
| `proposals/PROPOSAL-013-system-document-template-coverage.md` | R8b 藍圖；狀態 approved；含 R8b Implement Review Fix F-06 修訂 |
| `CHANGELOG.md` | 對外可讀的版本變更紀錄；含 R8a + R8b 完整段 |
