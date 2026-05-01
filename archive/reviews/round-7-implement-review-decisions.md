# Round 7 Implement Review Decisions — Wave 1-3 實作品質審查後修正

**對應 review 報告**: `reviews/round-7-implement-review-report.md`
**決議日期**: 2026-04-24
**決議者**: 使用者（Claude Code 整理選項 + 當場修訂 skill 檔）
**Session 模式**: **Approve + Fix 合併 session**（R7 findings 少、多為機械修正，未分離 approve/fix 兩階段）

---

## 決議彙總

| Finding | Wave | 嚴重度 | 維度 | 決議 | 說明 |
|---|---|---|---|---|---|
| F-01 | W2 | Critical | D3 | **accept + 擴 scope** | `{slug}` → `{phase-slug}` 修正；scope 從 `new-phase-flow.md` 雙版擴至 `templates/_index.md` 雙版（同類 within-row 不一致 typo） |
| F-02 | W1 | Major | D1 | **accept-with-choice（Path B）** | 保留 commit 歷史，修 CHANGELOG 自述 + 雙版 `git-integration.md` 頂部加 file history note；附後記記錄「`git mv` + 大重寫 → 未來應拆兩個 commit」教訓 |
| F-03 | W3 | Major | D1 | **accept** | WebForms `CLAUDE-md-snippet.md` 5 處路徑引用加 `_共用/` 前綴（3 個 unique pattern），與 `init-project-flow.md` 實際產出路徑對齊 |
| F-04 | cross-wave | Minor | D2 | **accept** | 雙版 `templates/CLAUDE.md` 的 `Git-原則-gitflow.md` 改為 `Git-principles-gitflow.md`（與 Wave 3 scaffolding 實際檔名對齊） |

**統計**：accept: 3 / accept-with-choice: 1 / reject: 0 / defer: 0 / clarify: 0 / out-of-scope: 0

---

## Accept / Accept-with-choice 項目的修訂紀錄

### F-01（accept + 擴 scope）

**背景脈絡**：
- Codex 指出 `new-phase-flow.md` Step 5 的 `_index.md` Phase Specs row 範例誤用 `{slug}` 而非 `{phase-slug}`，會產生失配連結、破壞 P009 Path A-3 phase-spec 命名一致性
- Codex 建議「同步掃描同檔其餘 row / path 範例」
- Approve session 中擴大掃描發現 `templates/_index.md` 雙版有**同類** within-row 不一致（第 3 欄用 `{phase-slug}`、第 5 欄檔案連結用 `{slug}`）
- 亦發現 `new-feature-flow.md` 雙版 Row 1 範例使用 `{slug}` 在 col 3 + col 5 屬**self-consistent 設計選擇**（PROPOSAL-009 § D 只要求 `/dflow:new-phase` 確認「階段 slug」；首 phase 預設 phase-slug = feature-slug），**不是 bug**，不擴 scope

**決定**：
1. `new-phase-flow.md` 雙版：Step 5 row 範例第 5 欄 `{slug}` → `{phase-slug}`（與 Step 3/4 的檔名定義對齊）
2. `templates/_index.md` 雙版：Phase Specs table row 1 範例第 5 欄 `{slug}` → `{phase-slug}`（與同 row 第 3 欄對齊）
3. `new-feature-flow.md` 雙版：**不動**（self-consistent 設計）

**修訂檔案**（4 處修正）：
- `sdd-ddd-webforms-skill/references/new-phase-flow.md:160`
- `sdd-ddd-core-skill/references/new-phase-flow.md:171`
- `sdd-ddd-webforms-skill/templates/_index.md:58`
- `sdd-ddd-core-skill/templates/_index.md:66`

**交叉影響**：無。`finish-feature-flow` / `pr-review-checklist` 依 `_index.md` 驗證 phase-spec existence 的流程在修正後恢復正確對照。

### F-02（accept-with-choice Path B）

**背景脈絡**：
- Codex 指出 Wave 1 的 `git-flow-integration.md` → `git-integration.md` rename 在 `git diff-tree --summary` 預設（`-M50`）顯示為 delete+add 而非 rename；`git log --follow` 穿不回舊檔；CHANGELOG 自述「`git mv`」與 git 事實不一致
- Approve session verify 結果顯示**更深一層事實**：
  - `git mv` **實際上有執行**（用 `-M30`：WebForms 顯示 rename 43% 相似；用 `-M10`：Core 顯示 rename 18% 相似）
  - 問題是 bf5bb85 commit 內**同時**執行 rename + 大幅內容重寫（Core 版重寫更徹底），新舊檔相似度低於 git 預設門檻（50%）
  - 另外，commit `bf5bb85` **尚未 push**（`main` ahead of origin/main by 7 commits）—— Path A 改歷史可行
- Approve session 評估三個 path：
  - **Path A（重寫 commit history）**：7 個後續 commit 要 cherry-pick 或重做；Codex review report 以 `bf5bb85` 為對照 hash 會失去可追溯性
  - **Path B（保留歷史 + 誠實記錄 + file history note）**：工作量最小；保留 review 對照關係；記錄事實
  - **Path C（拆 commit：先純 rename + 再內容重寫）**：技術最乾淨，但工作量比 A 大；對 Dflow（方法論 repo，非活躍 blame 對象）實用價值有限

**選定 Path B 的決定**：
1. **保留現有 commit 歷史**（不 rewrite）
2. **CHANGELOG Wave 1 段修訂**：`重命名：... (雙版，git mv)` → 改為「實際執行 `git mv`；但因同 commit 內大幅內容重寫，git 預設 rename detection（`-M50`）無法識別為 rename，詳見下方 F-02 後記」
3. **CHANGELOG 新增「R7 Implement Review F-02 後記」段**：
   - 客觀事實（`git mv` 有執行 + 新舊檔相似度細節）
   - 查看 rename 軌跡的指令（`-M30` / `-M10`）
   - 教訓：`git mv` 不等於 git 會自動識別為 rename；大重寫需拆兩個 commit
   - 採 Path B 的三個理由（保留可追溯性、Dflow 不是活躍 blame 對象、誠實記錄的教學價值）
4. **雙版 `git-integration.md` 頂部加 file history note**：
   - 指向舊檔名 `references/git-flow-integration.md`
   - 說明 rename + 大重寫的 similarity 狀況
   - 指引用 `-M30`（WebForms）或 `-M10`（Core）的 `git log --follow` 查看 rename 軌跡

**修訂檔案**（3 檔，共 3 處修訂）：
- `CHANGELOG.md`（Wave 1 段）：`重命名` 那行修訂 + 新增 F-02 後記段
- `sdd-ddd-webforms-skill/references/git-integration.md`（頂部）：新增 file history note blockquote
- `sdd-ddd-core-skill/references/git-integration.md`（頂部）：同上（Core 版 note 註明 18% 相似度 + `-M10` 指令）

**交叉影響**：
- 本 finding 的教訓（`git mv` + 大重寫需拆 commit）**應納入 `git-integration.md` 的「Directory Moves Must Use git mv」段長期原則**。但 CHANGELOG 後記已點出此建議，實際納入 skill 原則段屬後續延伸（**不列本輪 scope**，可於 Wave D 精簡化審查或下次 `git-integration.md` 修訂時補）
- PROPOSAL-009 決議 11 實施時未違背 `git mv` 原則（Wave 2 的 `feature-spec.md` → `phase-spec.md` 用 `git mv` + 內容不變，rename detection 正常），僅 Wave 1 為一次性現象

### F-03（accept）

**背景脈絡**：
- Codex 指出 WebForms `CLAUDE-md-snippet.md` 3 處路徑引用寫 `specs/_overview.md` / `specs/_conventions.md` / `specs/Git-principles-*.md`，但 `init-project-flow.md` 實際放到 `specs/_共用/` 下
- Core 版已正確引用 `specs/_共用/...`，屬單版錯誤 + 雙版不一致
- 純精準度修訂，無設計決策

**決定**：
- WebForms `CLAUDE-md-snippet.md` 所有 `specs/_overview.md` / `specs/_conventions.md` / `specs/Git-principles-{gitflow|trunk}.md` 路徑引用加 `_共用/` 前綴
- 實際 grep 顯示 5 處（`:44`、`:115`、`:116`、`:117`、`:138`），對應 3 個 unique pattern（用 replace_all）
- Core 版保持不動（已正確）

**修訂檔案**：
- `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md`（5 處路徑修正，3 個 unique pattern）

**交叉影響**：無。修正後 WebForms 版與 Core 版路徑引用對齊、與 `init-project-flow.md` 實際產出位置對齊。

### F-04（accept）

**背景脈絡**：
- Codex 指出雙版 `templates/CLAUDE.md` 引用 `scaffolding/Git-原則-gitflow.md`（中文化檔名），但 Wave 3 實際新增檔名為 `Git-principles-gitflow.md`（英文）
- 屬 cross-wave 文檔漂移（Wave 1/2 的 Git-neutral 指引未隨 Wave 3 的實際檔名同步）
- Minor 精準度修訂，改動極輕

**決定**：雙版 `templates/CLAUDE.md` 中 `Git-原則-gitflow.md` 改為 `Git-principles-gitflow.md`。

**修訂檔案**（2 處）：
- `sdd-ddd-webforms-skill/templates/CLAUDE.md:116`
- `sdd-ddd-core-skill/templates/CLAUDE.md:136`

**交叉影響**：無。修正後 `templates/CLAUDE.md` 引用與 `sdd-ddd-*-skill/scaffolding/` 實際檔名對齊。

**Final sweep verify**（本 session 結束前 grep）：
- `Git-原則`在 skill 檔（`sdd-ddd-*-skill/**/*.md`）**無命中** ✓
- `{slug}.md` 在 skill 檔仍有命中，但全部為：
  - 一般 placeholder（如 `phase-spec-YYYY-MM-DD-{slug}.md` 用作範例說明）
  - Lightweight-spec / ADR / BUG 檔名（本身即用 `{slug}`）
  - `new-feature-flow.md` Row 1 範例（self-consistent 設計）
  
  **無 within-row 不一致殘留** ✓

---

## Reject / Defer 項目備忘

本輪**無** reject / defer 決議。4 個 finding 全部 accept（3 項）或 accept-with-choice（1 項，F-02 Path B）。

---

## Out-of-scope 項目

本輪**無**轉 out-of-scope 的 finding。

**延後項目**（cross-round-notes 候選）：
- **「`git mv` + 大重寫需拆兩個 commit」原則納入 `git-integration.md` 的「Directory Moves Must Use git mv」段**：F-02 後記已指出，但正式納入 skill 原則段屬後續延伸；可於 Wave D 精簡化審查或下次 `git-integration.md` 修訂時補（不緊急）

---

## Implementation 完成紀錄

| 檔案 | 修正 Finding | 改動摘要 |
|---|---|---|
| `sdd-ddd-webforms-skill/references/new-phase-flow.md` | F-01 | Step 5 row 範例第 5 欄 `{slug}` → `{phase-slug}` |
| `sdd-ddd-core-skill/references/new-phase-flow.md` | F-01 | 同上 |
| `sdd-ddd-webforms-skill/templates/_index.md` | F-01 擴 scope | Phase Specs row 1 範例第 5 欄 `{slug}` → `{phase-slug}` |
| `sdd-ddd-core-skill/templates/_index.md` | F-01 擴 scope | 同上 |
| `CHANGELOG.md` | F-02 | Wave 1 段「重命名」行修訂 + 新增 R7 Implement Review F-02 後記段 |
| `sdd-ddd-webforms-skill/references/git-integration.md` | F-02 | 頂部新增 file history note blockquote（指向舊檔名 + `-M30` 指令） |
| `sdd-ddd-core-skill/references/git-integration.md` | F-02 | 頂部新增 file history note blockquote（指向舊檔名 + `-M10` 指令；Core 版 18% 相似度） |
| `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md` | F-03 | 5 處路徑（3 個 unique pattern）加 `_共用/` 前綴 |
| `sdd-ddd-webforms-skill/templates/CLAUDE.md` | F-04 | `Git-原則-gitflow.md` → `Git-principles-gitflow.md` |
| `sdd-ddd-core-skill/templates/CLAUDE.md` | F-04 | 同上 |

**影響檔案總數**：10 檔（英文版）；繁中版同步延至 Closeout C1。

---

## 下一步

R7 全流程階段狀態：

- ✅ R7 Draft Review（Codex，`reviews/round-7-report.md`）
- ✅ R7 Approve（draft → approved，`reviews/round-7-decisions.md`）
- ✅ R7 Implement Wave 1（PROPOSAL-011，Cursor）
- ✅ R7 Implement Wave 2（PROPOSAL-009，Claude Code 主力）
- ✅ R7 Implement Wave 3（PROPOSAL-010，Cursor）
- ✅ R7 Implement Review（Codex，`reviews/round-7-implement-review-report.md`）
- ✅ **R7 Implement Review Fix（本 session）—— 4 finding 全部 resolved**

**R7 全部完成**。進入 Post-R7 階段：

1. **Global Sweep**（仿 R6 後做法）：整體檔案一致性檢查（若 Codex 還有 token，可以執行；Cursor token 已耗盡）
2. **Closeout C1**：繁中版同步（`sdd-ddd-*-skill-tw/`）——英文版 Skill 已定稿，可單向重新翻譯 / 對齊（不 diff 修補）
3. **Closeout C2**：Tutorial 重建（以定稿後 Skill 為基礎）
4. **改名 + 遷移**：`dflow-greenfield-skill` / `dflow-brownfield-skill` → 新 GitHub repo → Dflow V1 ships

---

## 跨輪發現備忘

本 session 在 Approve + Fix 過程中未新增跨輪議題至 `reviews/cross-round-notes.md`（既有 cross-round-notes 議題 1-3 保留）。

唯一延後項目（見 Out-of-scope 段）：「`git mv` + 大重寫需拆兩個 commit」原則正式納入 `git-integration.md`「Directory Moves Must Use git mv」段 —— 可於 Wave D 或下次 `git-integration.md` 修訂時補，**不需獨立 wave 處理**。
