## PROPOSAL-011: Git Flow Decoupling

**狀態**: `draft`

**提出日期**: 2026-04-22

**優先級**: `高`

**來源**: `reviews/field-reference-candidates.md` 候選 L（2026-04-22 新增，源自社群散佈定位下「Git Flow 非普世」的觀察），Obts 實戰背書通過。

**實施排程**: R7 Wave 1（3-proposal 序列中最先；為 PROPOSAL-009 在穩定基礎上加 `git mv` 規範段的前置條件）

---

### 問題描述

Dflow 當前把 Git Flow（develop / release / hotfix 分支策略）當作預設前提，這在 Obts 團隊內部可運作，但對社群散佈造成以下摩擦：

1. **檔名即綁定特定分支策略**。`references/git-flow-integration.md` 檔名明確標示 Git Flow 耦合，採用 trunk-based、GitHub Flow、或單一 main 分支策略的專案無法直接沿用。

2. **內容混入 Git Flow 專屬流程**。該檔目前含：
   - develop / release / hotfix 分支結構圖（`git-flow-integration.md:8-23`）
   - release branch 合併前檢查（「all specs in `active/` have status `completed`」— `git-flow-integration.md:104-110`）
   - hotfix 24 小時補 spec 流程（`git-flow-integration.md:92-102`）
   
   這些是 Git Flow 專屬概念，不是 SDD 工作流程的必要條件。

3. **`/dflow:bug-fix` 語意混淆**。命令設計原意是「輕量 ceremony 的 SDD 修改」，但在 `git-flow-integration.md` 脈絡下容易被讀成「Git Flow 的 hotfix 流程」，造成「沒有 Git Flow 的專案不能用 `/dflow:bug-fix`」的誤解。

4. **實戰驗證**：Obts 團隊實際使用下，Git Flow 的「hotfix 24 小時內補 spec」規則**未被遵守**（見候選 E 背書）。這反映 AI 無法追蹤時間，且 hotfix 時限本質上是給人的承諾、不是 skill 的職責。

**關鍵觀察**（使用者在候選審查過程的原話）：

> 「Dflow 與 SDD-Starter-Kit V2 我在設計之初都是高耦合 Git Flow，但是如果要社群散佈，git 可以高耦合但是 git flow 不可以，不過要開 feature branch 這件事，可以是在 Dflow 必須的我覺得這沒問題。也就是說有沒有用 git flow，Dflow 並不關切，只差一 feature branch 是哪一個 branch 開出去的，是 develop 還是 master/main。」

Dflow 真正需要的 Git 耦合只有 3 點：
- Feature branch per feature（SDD 需要 branch ↔ feature 目錄 ↔ slug 對應）
- 用 `git mv` 維護目錄 / 檔案重命名的歷史追蹤
- SDD 產物與 git commit 的時機指引

Git Flow 的 develop / release / hotfix 分支策略、release checklist、hotfix expedited process 則超出 Dflow 應承擔的範圍。

---

### 提議的解法

#### 雙層區分

| 層次 | Dflow 的立場 | 理由 |
|---|---|---|
| **Git**（版控本身）| ✓ 高耦合 | `git mv`、commit、branch、history 是普世版控概念 |
| **Git Flow**（develop / release / hotfix 分支工作流）| ✗ 解耦 | Git Flow 只是一種分支策略；專案可能採 trunk-based / GitHub Flow / 單一 main |
| **Feature branch per feature** | ✓ 維持為 Dflow 強制 | SDD 需要 branch ↔ feature 目錄 ↔ slug 對應 |
| **Feature branch 從哪開、怎麼合** | ✗ Dflow 不決定 | 是專案決策（develop vs main/master、merge vs squash vs rebase）|

#### 1. 檔名重命名 + 重構

```
references/git-flow-integration.md  →  references/git-integration.md
```

使用 `git mv`（**本 proposal 即首次實踐「目錄/檔案移動必須用 `git mv`」的原則**，同時為 PROPOSAL-009 決議 11 建立先例）。

#### 2. `git-integration.md` 保留內容

- **Branch 命名規範**：`feature/{SPEC-ID}-{slug}`；slug 語言跟隨使用者 / AI 討論語言（註：slug 語言段落的細節由 PROPOSAL-009 決議 8 正式納入——本 proposal 先移除 `{CONTEXT-ID}-{short-description}` 這個過時命名，留 placeholder 給 009 填實）
- **Feature branch per feature** 強制規則（SDD ↔ Git 的核心耦合）
- **`git mv` 規範**：目錄 / 檔案重命名、feature slug 調整、feature 完成後搬目錄都必須用 `git mv`（完整段由 PROPOSAL-009 決議 11 展開；本 proposal 先建框架 + 一段總則）
- **SDD 產物與 commit 時機指引**（對應原檔 Daily Development Flow 的「Before PR/merge → Run merge checklist」等）
- **Gate Checks by Branch Type**（feature/ 前/後、bugfix/ 前/後）——保留 SDD 相關項，移除 Git Flow 專屬項
- **Commit Message Convention**（`[SPEC-ID] Short description`）
- **CI/CD Future Enhancement** 段（可保留，屬通用未來展望）

#### 3. `git-integration.md` 移除內容

- **develop / release / hotfix 分支結構圖**（`git-flow-integration.md:8-23`）
  → 以簡化版替代：只展示 `main ← feature/{SPEC-ID}-{slug}`，註明「若你的專案採 Git Flow / GitHub Flow，feature branch 從哪個 base branch 開由專案決定，Dflow 不規定」
- **release/ branch — Release Checklist 整節**（`git-flow-integration.md:104-110`）→ 移除
- **hotfix/ branch — Expedited Process 整節**（`git-flow-integration.md:92-102`）→ 移除；其中「tech-debt.md if reveals systemic issue」的概念併入 bugfix/ 段
- **hotfix 24 小時補 spec 時限** → 不納入（Obts 實戰未遵守，AI 無法追蹤時間）
- **Branch 結構中的 hotfix/ 分類** → 移除（`/dflow:bug-fix` 對應的分支統一為 `bugfix/` 或由專案決定）

#### 4. `/dflow:bug-fix` 語意澄清

- 命令描述從「modify-existing-flow with lightweight ceremony」強化為：
  > **Lightweight-ceremony modification of existing functionality. Not tied to any branch strategy (not Git Flow's hotfix).**
- 雙版 SKILL.md 的 Primary triggers 與決策樹同步調整
- `modify-existing-flow.md` 內若有提及「hotfix」一律改為「bug fix / 輕量修改」；保留「輕量 ceremony」的概念

#### 5. 交叉影響的檔案

- `references/pr-review-checklist.md`：移除 release branch 專屬檢查項；保留 feature/bugfix 前/後檢查
- `templates/CLAUDE.md`：若有段落預設 Git Flow（例如「專案採 Git Flow」語句），改為中立措辭，或標示為「若採 Git Flow 可選採用」

#### 6. 對既有使用者的影響

- **Obts 等既有 Git Flow 使用者**：可維持既有分支策略，本變更不強迫他們改變 Git 工作流——Dflow 只是不再把 Git Flow 寫死在 skill 文件裡。Git Flow 規範可移入專案級的 `Git-原則.md`（由 PROPOSAL-010 提供 scaffolding 範本）。
- **社群新採用者**：可在任何分支策略下使用 Dflow，不必先學 Git Flow。

---

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `sdd-ddd-webforms-skill/references/git-flow-integration.md` | 重命名 + 修改 | → `git-integration.md`（用 `git mv`）；移除 Git Flow 專屬段；保留 SDD ↔ Git 直接連結部分 |
| `sdd-ddd-core-skill/references/git-flow-integration.md` | 重命名 + 修改 | 同上 |
| `sdd-ddd-webforms-skill/SKILL.md` | 修改 | 更新 references 表內檔名 + `/dflow:bug-fix` 描述；決策樹中對應節點更新 |
| `sdd-ddd-core-skill/SKILL.md` | 修改 | 同上 |
| `sdd-ddd-webforms-skill/references/modify-existing-flow.md` | 修改 | 「hotfix」措辭改「bug fix / 輕量修改」；確認 `/dflow:bug-fix` 的 ceremony 描述不綁分支策略 |
| `sdd-ddd-core-skill/references/modify-existing-flow.md` | 修改 | 同上 |
| `sdd-ddd-webforms-skill/references/pr-review-checklist.md` | 修改 | 移除 release branch 專屬檢查項 |
| `sdd-ddd-core-skill/references/pr-review-checklist.md` | 修改 | 同上 |
| `sdd-ddd-webforms-skill/templates/CLAUDE.md` | 修改 | 檢查並中立化 Git Flow 預設措辭 |
| `sdd-ddd-core-skill/templates/CLAUDE.md` | 修改 | 同上 |
| `sdd-ddd-webforms-skill-tw/git-flow-integration_tw.md` | 重命名 + 修改 | → `git-integration_tw.md`（同步英文版變更）|
| `sdd-ddd-core-skill-tw/git-flow-integration_tw.md` | 重命名 + 修改 | 同上 |
| 繁中版其他對應檔 | 修改 | SKILL_tw.md / modify-existing-flow_tw.md / pr-review-checklist_tw.md 同步措辭 |

---

### 預估工作量

小

主要是內容剝離與檔名重命名，不新增新流程 / 新命令。雙版 + 繁中同步為機械工作。

---

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-009 | 前置於 009 | 009 要在穩定的 `git-integration.md` 上加「Directory Moves Must Use git mv」規範段（決議 11）；011 先完成檔名重構與基底內容，009 再擴增。**R7 Review F-04 定案**：009 的 `/dflow:finish-feature` 產出 Git-strategy-neutral 的 Integration Summary，不綁特定 merge strategy，與本 proposal 的解耦目標一致 |
| PROPOSAL-010 | 前置於 010 | 010 的 `Git-原則.md` scaffolding 範本可選地保留 Git Flow 版本，但需先在 Dflow 本體解耦後才能以「可選範本」形式提供 |
| PROPOSAL-008 | 互補 | `/dflow:verify` 不受 Git Flow 解耦影響；但若 pr-review-checklist 段落變動，需確認 verify 相關觸發指引不失效 |

---

### 關鍵假設

- **假設 1**：社群使用者未必採用 Git Flow，但幾乎都會採用「feature branch per feature」（SDD 所需最小 Git 耦合）。
- **假設 2**：解耦後 Dflow 的所有 SDD workflow（new-feature-flow / modify-existing-flow / pr-review-checklist / drift-verification）不依賴 develop / release / hotfix 任何一個分支的存在。
- **假設 3**：Obts 團隊本身的 Git Flow 分支工作流不會因此失去指引——因為 Git Flow 規範可移入專案級 `Git-原則.md`（PROPOSAL-010 scaffolding 範本）。
- **驗證點**：實施後檢查 SDD workflow 是否都能在「只有 `main` + `feature/*`」的最簡分支配置下執行；若 pr-review-checklist / completion checklist 任一處仍假設 `develop` / `release` 存在，視為 regression。

---

### 預期後果（風險）

- **風險 1**：既有 Obts 讀者找不到 Git Flow 指引。
  - 緩解：變更後在 `git-integration.md` 頂部留一行註解：「若你的專案採用 Git Flow，可參考 [scaffolding/Git-原則.md 範本（PROPOSAL-010）]」；實施期可在 CHANGELOG 突顯此變化。
- **風險 2**：檔名重命名造成既有外部連結（文件 / issue / PR 留言）失效。
  - 緩解：用 `git mv` 保留 git 歷史；README 或相關 changelog 註記舊檔名 → 新檔名對應。
- **風險 3**：`/dflow:bug-fix` 語意澄清後，使用者以為「以前的 hotfix 用法被廢」。
  - 緩解：澄清訊息強調「功能不變，只是從語意上與 Git Flow 的 hotfix 概念脫鉤」；命令行為（輕量 ceremony）保持一致。

---

### 評估紀錄（整合評估時填寫）

**評估日期**:

**結論**:

**理由**:
