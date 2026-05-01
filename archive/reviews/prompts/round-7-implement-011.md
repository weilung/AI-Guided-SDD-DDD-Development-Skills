# Round 7 Implement Prompt — PROPOSAL-011（給 Cursor Opus 4.7 High 使用）

> **使用方式**：開新 Cursor session（建議模型 Opus 4.7 High），把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。也可用 Claude Code 執行，但依 Phase 3 AI 分派，本 proposal 屬小規模、明確 scope，指派 Cursor 以節省 Claude Code token。
> **產出位置**：
>   - 修改 / 重命名 skill 檔（`sdd-ddd-*-skill/`）
>   - 更新 `CHANGELOG.md`
>   - 不改任何繁中版檔
>   - 不改 proposal 本身（proposal 已 approved，實施內容由 skill 檔承載）
> **禁止事項**：不補繁中版（延到 Closeout C1）、不動其他 proposal 範圍、不自動 commit、**所有檔案重命名必須用 `git mv`**（此原則本 proposal 即首次實踐）。
> **前置依賴**：無（P011 是 R7 三份序列中最先實施的）。
> **預估工作量**：小（~1-2 小時）。主要為內容剝離 + 檔名重命名。
> **下一步**：本 session 完成後，接 `round-7-implement-009.md`。

---BEGIN PROMPT---

你是 PROPOSAL-011（Git Flow Decoupling）的實施者。本 proposal 已於 R7 Review + Approve 階段完成設計評估與修訂，狀態為 `approved`。你的工作是把 approved proposal 的內容落實到 skill 檔。

## Step 1：讀入必要檔案

### A. 權威設計來源（必讀，逐段對照）

1. `proposals/PROPOSAL-011-git-flow-decoupling.md`（**THE 實施藍圖**，影響範圍表是你的 checklist）

### B. R7 context（供理解 finding 定案）

2. `reviews/round-7-decisions.md`（特別是 F-04 / F-06 兩個 finding 的決議說明）
3. `reviews/round-7-report.md`（Codex 原始 finding 文字）

### C. 流程規範

4. `proposals/review-workflow.md`（第三節「Implementation 紀錄」格式要求）

### D. 現況檔（要實際修改的檔）

5. `sdd-ddd-webforms-skill/references/git-flow-integration.md`
6. `sdd-ddd-core-skill/references/git-flow-integration.md`
7. `sdd-ddd-webforms-skill/SKILL.md`
8. `sdd-ddd-core-skill/SKILL.md`
9. `sdd-ddd-webforms-skill/references/drift-verification.md`
10. `sdd-ddd-core-skill/references/drift-verification.md`
11. `sdd-ddd-webforms-skill/templates/CLAUDE.md`
12. `sdd-ddd-core-skill/templates/CLAUDE.md`

若 PROPOSAL-011 狀態不是 `approved`，停止並回報使用者。

## Step 2：本輪範圍邊界（嚴格遵守）

**在範圍內**（PROPOSAL-011 § 影響範圍表所列）：
- 檔名重命名：`git-flow-integration.md` → `git-integration.md`（雙版，**必須用 `git mv`**）
- 內容重構 `git-integration.md`（雙版）：移除 Git Flow 專屬段、保留 SDD ↔ Git 耦合段
- SKILL.md（雙版）：references 表檔名更新、`/dflow:bug-fix` 描述強化
- drift-verification.md（雙版）：移除「Before a release branch cut」觸發點（現況 `webforms:122` / `core:132`）
- templates/CLAUDE.md（雙版）：檢查並中立化 Git Flow 預設措辭

**不在範圍內**：
- **繁中版**（`*-tw/`）—— 本輪不動，延到 Post-Review Closeout C1
- `modify-existing-flow.md` / `pr-review-checklist.md`（雙版）—— R7 Review F-06 verify 現況無 Git Flow 字樣，PROPOSAL-011 已從 scope 移除
- PROPOSAL-009 / 010 範圍的改動（例如 `_index.md`、`scaffolding/`、`Directory Moves Must Use git mv` 段擴展等）
- R1-R6 既有機制的調整（P003 / P005 / P007 / P008 等）

若實施過程中發現 proposal 沒列但邏輯上牽連的檔，**停下來回報使用者**，不要自行擴大 scope。

## Step 3：實施動作（依照順序執行）

### 3.1 檔名重命名（**必須用 `git mv`**）

```bash
git mv sdd-ddd-webforms-skill/references/git-flow-integration.md \
       sdd-ddd-webforms-skill/references/git-integration.md

git mv sdd-ddd-core-skill/references/git-flow-integration.md \
       sdd-ddd-core-skill/references/git-integration.md
```

**不要用普通 `mv`**——這會破壞 git 歷史追蹤。PROPOSAL-011 本身即首次實踐「Directory Moves Must Use git mv」原則（見 § 1），同時為 PROPOSAL-009 決議 11 建立先例。

### 3.2 重構 `git-integration.md` 內容（雙版同步）

依 PROPOSAL-011 § 2（保留內容）與 § 3（移除內容）執行：

**保留內容**：
- Branch 命名規範：`feature/{SPEC-ID}-{slug}`（slug 語言跟隨使用者/AI 討論語言——**本段可留 placeholder**，完整 slug 語言段由 PROPOSAL-009 決議 8 在其實施時補齊；本 proposal 先移除過時的 `{CONTEXT-ID}-{short-description}` 格式）
- Feature branch per feature 強制規則
- `git mv` 規範總則（一段說明 + 幾個範例；完整段由 PROPOSAL-009 決議 11 擴展「Directory Moves Must Use git mv」時再補齊）
- SDD 產物與 commit 時機指引（沿用原檔 Daily Development Flow 的 merge checklist 時機）
- Gate Checks by Branch Type（feature/ 前/後、bugfix/ 前/後）—— 保留 SDD 相關項，移除 Git Flow 專屬項
- Commit Message Convention（`[SPEC-ID] Short description`）
- CI/CD Future Enhancement 段（屬通用展望，可保留）

**移除內容**：
- develop / release / hotfix 分支結構圖（原檔 `:8-23`）→ 替換為簡化版：只展示 `main ← feature/{SPEC-ID}-{slug}`，並加一行註解：「若你的專案採 Git Flow / GitHub Flow / trunk-based，feature branch 從哪個 base branch 開由專案決定，Dflow 不規定」
- 整節「release/ branch — Release Checklist」（原檔 `:104-110`）—— 刪除
- 整節「hotfix/ branch — Expedited Process」（原檔 `:92-102`）—— 刪除；其中「tech-debt.md if reveals systemic issue」的概念併入 bugfix/ 段
- hotfix 24 小時補 spec 時限 —— 不納入（AI 無法追蹤時間；Obts 實戰未遵守）
- Branch 結構中的 hotfix/ 分類 —— 移除

**建議在 `git-integration.md` 頂部加一行註解**（對應 proposal § 預期後果 / 風險 1 的緩解）：

> 「若你的專案採用 Git Flow，可參考 `scaffolding/Git-principles-gitflow.md` 範本（由 PROPOSAL-010 提供）。」

### 3.3 更新 SKILL.md（雙版）

- **References 表**：`git-flow-integration.md` → `git-integration.md`（檔名欄位）；描述欄位若有提及 Git Flow，中立化（例：「Branch management and Git ↔ SDD coupling」）
- **決策樹**：若決策樹節點有指向 `references/git-flow-integration.md` 的描述（例如「I'm creating a branch」節點 → 檢查 `git-integration.md` 檔名引用是否更新）
- **`/dflow:bug-fix` 描述強化**（依 PROPOSAL-011 § 4）：
  - 舊：「/dflow:bug-fix → MODIFY EXISTING WORKFLOW (lightweight ceremony)」或類似
  - 新：「**Lightweight-ceremony modification of existing functionality. Not tied to any branch strategy (not Git Flow's hotfix).**」
  - 雙版的 Primary triggers frontmatter 描述同步更新
  - Slash Commands 表內該命令的描述欄同步

### 3.4 更新 drift-verification.md（雙版，輕量）

依 R7 Review F-06 決議：

- 找到「Before a release branch cut」觸發點（現況 `webforms:122` / `core:132`）
- **移除此觸發點**（刪除該 bullet 或 list item）
- 其他觸發點（pre-PR、after refactor、onboarding 等）**不動**
- drift-verification 的 `rules.md` ↔ `behavior.md` 機械對照邏輯完全**不變**

### 3.5 更新 templates/CLAUDE.md（雙版）

- Grep 兩份檔，尋找 `Git Flow` / `git flow` / `develop` / `release branch` / `hotfix` 等語句
- 若有「專案採 Git Flow」類預設措辭，改為中立措辭（例：「專案採用的分支策略」），或加註「若採 Git Flow 可選採用」
- 若現況兩份檔已無相關語句（類似 modify-existing-flow 的情況），記錄「verify 過，無需改動」作為實施紀錄

## Step 4：實施後自我驗收

跑過以下檢查（可用 Grep 工具）：

1. **檔名重命名 verify**：
   - `ls sdd-ddd-webforms-skill/references/git-flow-integration.md` 應不存在
   - `ls sdd-ddd-webforms-skill/references/git-integration.md` 應存在
   - Core 版同理
   - `git log --follow sdd-ddd-webforms-skill/references/git-integration.md` 應能看到舊檔歷史（`git mv` 的保留證據）

2. **git-integration.md 內容 verify**：
   - Grep `hotfix` / `release.*branch` / `24.*hour` / `develop` 在 `git-integration.md` 應無結果（或僅出現在「若專案採 Git Flow，可參考 ...」註解段）
   - 「feature branch per feature」、「git mv」、「Commit Message Convention」段應存在

3. **drift-verification.md verify**：
   - Grep `release branch cut` / `release.*branch` 在 drift-verification.md 應無結果
   - 其他觸發點（pre-PR 等）應仍存在

4. **SKILL.md 引用 verify**：
   - Grep `git-flow-integration` 在 SKILL.md 雙版應無結果
   - Grep `git-integration` 應有對應引用
   - `/dflow:bug-fix` 的新描述應存在

5. **範圍外檔案 untouched verify**：
   - `modify-existing-flow.md` 雙版、`pr-review-checklist.md` 雙版**未被修改**（R7 Review F-06 verify 過現況無 Git Flow 字樣，本 proposal 不動）
   - 繁中版檔案（`*-tw/`）**未被修改**

若任一驗收失敗，修正後重跑。

## Step 5：CHANGELOG 更新

在 `CHANGELOG.md` 最上方新增一段（格式參考既有條目）：

```markdown
## {YYYY-MM-DD} — R7 Wave 1 實施：PROPOSAL-011 Git Flow Decoupling

**前置**：R7 Review（Codex，2026-04-22 產出 `reviews/round-7-report.md`）+ R7 Approve（2026-04-22）處理 2 個 finding（accept-with-choice: F-04 Path B；accept: F-06）
**Proposal**：`proposals/PROPOSAL-011-git-flow-decoupling.md`（approved）
**影響範圍**：
- 重命名：`references/git-flow-integration.md` → `references/git-integration.md`（雙版，`git mv`）
- 內容重構：`git-integration.md`（雙版）—— 移除 Git Flow 專屬段（develop/release/hotfix 分支圖、release checklist、hotfix expedited process、24h 補 spec）、保留 SDD ↔ Git 核心耦合
- 修改：`SKILL.md`（雙版）—— references 表檔名更新、`/dflow:bug-fix` 語意澄清（不綁分支策略）
- 修改：`drift-verification.md`（雙版）—— 移除「Before a release branch cut」觸發點
- 修改：`templates/CLAUDE.md`（雙版）—— 中立化 Git Flow 預設措辭（若有）

**繁中版同步**：延至 Post-Review Closeout C1（依 review-workflow.md §七）

**下一步**：R7 Wave 2（PROPOSAL-009 實施）—— 本 proposal 完成後，`git-integration.md` 基底穩定，009 可在其上擴增「Directory Moves Must Use git mv」完整段與 slug 語言段
```

## Step 6：完成後回報

```
R7 Wave 1 實施完成（PROPOSAL-011 Git Flow Decoupling）。

**已修改檔案**：
- 重命名（git mv）：
  - sdd-ddd-webforms-skill/references/git-flow-integration.md → git-integration.md
  - sdd-ddd-core-skill/references/git-flow-integration.md → git-integration.md
- 內容修改：
  - {列出每個修改的檔 + 一句話描述主要改動}
- CHANGELOG.md：R7 Wave 1 實施段已新增

**驗收結果**：
- 檔名重命名 verify：✓ / ✗（{若失敗，列出問題}）
- git-integration.md 內容 verify：✓ / ✗
- drift-verification.md verify：✓ / ✗
- SKILL.md 引用 verify：✓ / ✗
- 範圍外檔案 untouched verify：✓ / ✗

**待使用者 commit 後進入 R7 Wave 2（PROPOSAL-009 實施）**。建議 commit message：
  `Implement PROPOSAL-011: Git Flow Decoupling (R7 Wave 1)`
  含 R7 Review F-04 (Path B) 與 F-06 的 finding 修正。

**備註 / 意外發現**（如有，記入 cross-round-notes.md；若無，寫「無」）：
```

## 禁止事項

- **不要用普通 `mv` 重命名檔案**——PROPOSAL-011 本身即首次實踐 `git mv` 原則
- **不要動繁中版檔**（`*-tw/`）—— 延到 Closeout C1
- **不要動 proposal 本身**（`proposals/PROPOSAL-011-*.md` 已 approved，不需改）
- **不要動範圍外的 skill 檔**——特別警覺：
  - `modify-existing-flow.md`（雙版）：R7 Review F-06 verify 過現況無 Git Flow 字樣
  - `pr-review-checklist.md`（雙版）：同上
  - 任何 P009 / P010 scope 的檔（`_index.md` / `scaffolding/` / `init-project-flow.md` 等）—— 這些是後續 wave 的工作
- **不要自動 commit**—— 使用者會自己 commit
- **不要 skip hooks**（`--no-verify` 禁用）
- **不要在實施過程中修改 `git-integration.md` 的核心結構**（例如：不要把 Gate Checks 整段刪除，即使覺得「太細」）—— proposal § 2 明列要保留的段落，照做即可
- **PROPOSAL-009 的「Directory Moves Must Use git mv」完整段不在本輪 scope**—— 本輪在 `git-integration.md` 只建框架 + 一段總則；完整段由 Wave 2 實施 P009 時擴展

## 若遇到歧義

- 第一優先：回到 `proposals/PROPOSAL-011-*.md` § 影響範圍表對應描述
- 第二優先：回到 `reviews/round-7-decisions.md` 對應 finding 段（F-04 / F-06）
- 第三優先：若仍無法決定，**停下來回報使用者**，不要自行裁定
- **不要**試圖跑去讀 PROPOSAL-009 / 010 來「幫忙連貫」—— 那是後續 wave 的職責

---END PROMPT---
