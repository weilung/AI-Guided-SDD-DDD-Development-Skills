# Dflow Project Review Brief

> 整理日期：2026-04-25  
> 用途：協助重新複習 Dflow 專案目前狀況、兩套 skill 的結構、會產生的系統文件樣板，以及接下來可思考的問題。  
> 性質：暫時性工作筆記，不是正式 proposal，也不是 skill runtime 文件。

---

## 1. 目前複習範圍

本次先聚焦英文版 skill：

- `sdd-ddd-webforms-skill/`
- `sdd-ddd-core-skill/`

暫不檢查：

- `sdd-ddd-webforms-skill-tw/`
- `sdd-ddd-core-skill-tw/`
- tutorial 或未來 npm 發布實作細節

原因是英文版是 AI runtime 讀取與執行的主體；繁中版目前定位為人讀翻譯，且版本尚未同步。

---

## 2. 專案定位速記

Dflow 是一套 AI 引導的 SDD / DDD 工作流程 skill。AI 在開發過程中扮演 Workflow Guardian，協助開發者先釐清規格，再進入實作。

兩套 skill 的差異：

| Skill | 適用情境 | 架構深度 | 核心目標 |
|---|---|---|---|
| WebForms | 既有 ASP.NET WebForms 專案，漸進式抽離業務邏輯 | Domain 層 + thin Code-Behind | 在維護中累積可遷移的規格與 Domain 資產 |
| Core | ASP.NET Core 新專案或模擬練習 | Clean Architecture + DDD 戰術模式 | 從一開始建立清楚的分層、Aggregate、Domain Event 與行為規格 |

共同原則：

- 先規格後程式碼。
- 流程嚴謹度依變更影響調整。
- WebForms 抽離出的 Domain 概念應能遷移到 Core。
- 英文 skill 供 AI 使用，需精確且工具友善。
- 繁中內容可保留給團隊閱讀，但公開散佈前要重新規劃語言與工具相容策略。

---

## 3. Dflow 產生的系統專案目錄結構

這一節描述的是「某個應用系統專案導入 Dflow 後，Dflow 會在該專案內建立或維護的文件結構」，不是本 repo 自己的結構。

目前正式 flow 使用 `specs/shared/`（由 P012 確立，2026-04-27 implemented）。下方目錄結構即實際路徑。

### 3.1 WebForms 專案導入 Dflow 後

```text
target-webforms-project/
├── CLAUDE.md                                      # 可選；若根目錄沒有 CLAUDE.md，init 可建立
├── specs/
│   ├── shared/                                   # 專案級治理文件（init 階段選擇性寫入）
│   │   ├── _overview.md                          # optional scaffolding：系統概覽、遷移策略
│   │   ├── _conventions.md                       # optional scaffolding：文件與協作慣例
│   │   ├── Git-principles-gitflow.md             # optional scaffolding：Git Flow 專案才選
│   │   ├── Git-principles-trunk.md               # optional scaffolding：trunk / GitHub Flow 專案
│   │   └── CLAUDE-md-snippet.md                  # 若根目錄已存在 CLAUDE.md，放這裡供手動合併
│   ├── features/
│   │   ├── active/
│   │   │   └── {SPEC-ID}-{slug}/
│   │   │       ├── _index.md                     # feature 索引、BR snapshot、phase 列表
│   │   │       ├── phase-spec-YYYY-MM-DD-{slug}.md
│   │   │       └── lightweight-YYYY-MM-DD-{slug}.md
│   │   ├── completed/
│   │   │   └── {SPEC-ID}-{slug}/                 # /dflow:finish-feature 後由 git mv 移入
│   │   └── backlog/
│   ├── domain/
│   │   ├── glossary.md                           # mandatory baseline；P013 建議補 template
│   │   ├── context-map.md                        # WebForms 非 init mandatory；多 context 後自然產生
│   │   └── {context}/
│   │       ├── context-definition.md
│   │       ├── models.md                         # P013 候選 template
│   │       ├── rules.md                          # P013 候選 template
│   │       └── behavior.md                       # 由 feature 完成或 baseline capture 時建立
│   └── migration/
│       └── tech-debt.md                          # mandatory baseline；WebForms 用 migration/
└── src/
    └── Domain/                                   # Dflow 不一定建立程式碼目錄，但會要求業務邏輯往 Domain 抽離
```

WebForms init 的 mandatory baseline 目前是：

| Path | 來源 / 性質 |
|---|---|
| `specs/features/active/.gitkeep` | 空目錄佔位 |
| `specs/features/completed/.gitkeep` | 空目錄佔位 |
| `specs/features/backlog/.gitkeep` | 空目錄佔位 |
| `specs/domain/glossary.md` | mandatory baseline |
| `specs/migration/tech-debt.md` | mandatory baseline |

WebForms init 不會先建立：

| Path | 理由 |
|---|---|
| `specs/domain/{context}/behavior.md` | 由 `/dflow:new-feature` 完成時或 brownfield baseline capture 建立，避免空 placeholder 漂移 |
| `specs/domain/context-map.md` | WebForms 版讓 Bounded Context 從既有系統抽離過程自然浮現 |

### 3.2 Core 專案導入 Dflow 後

```text
target-core-project/
├── CLAUDE.md                                      # 可選；若根目錄沒有 CLAUDE.md，init 可建立
├── specs/
│   ├── shared/                                   # 專案級治理文件（init 階段選擇性寫入）
│   │   ├── _overview.md
│   │   ├── _conventions.md
│   │   ├── Git-principles-gitflow.md
│   │   ├── Git-principles-trunk.md
│   │   └── CLAUDE-md-snippet.md
│   ├── features/
│   │   ├── active/
│   │   │   └── {SPEC-ID}-{slug}/
│   │   │       ├── _index.md
│   │   │       ├── phase-spec-YYYY-MM-DD-{slug}.md
│   │   │       ├── lightweight-YYYY-MM-DD-{slug}.md
│   │   │       └── aggregate-design.md           # Core 特有；需要 Aggregate 設計時產生
│   │   ├── completed/
│   │   │   └── {SPEC-ID}-{slug}/
│   │   └── backlog/
│   ├── domain/
│   │   ├── glossary.md                           # mandatory baseline；P013 建議補 template
│   │   ├── context-map.md                        # Core mandatory baseline
│   │   └── {context}/
│   │       ├── context-definition.md
│   │       ├── models.md                         # P013 候選 template
│   │       ├── rules.md                          # P013 候選 template
│   │       ├── events.md                         # Core 特有；P013 候選 template
│   │       └── behavior.md                       # 由 feature 完成或 baseline capture 時建立
│   └── architecture/
│       ├── tech-debt.md                          # mandatory baseline；Core 用 architecture/
│       └── decisions/
│           ├── README.md                         # ADR 目錄說明；P013 建議補 scaffolding/template
│           └── ADR-NNNN-{slug}.md                # 有架構決策時才建立
└── src/
    ├── Domain/
    ├── Application/
    ├── Infrastructure/
    └── Presentation 或 Web/API                   # Dflow 不一定建立程式碼目錄，但會以此分層檢查
```

Core init 的 mandatory baseline 目前是：

| Path | 來源 / 性質 |
|---|---|
| `specs/features/active/.gitkeep` | 空目錄佔位 |
| `specs/features/completed/.gitkeep` | 空目錄佔位 |
| `specs/features/backlog/.gitkeep` | 空目錄佔位 |
| `specs/domain/glossary.md` | mandatory baseline |
| `specs/domain/context-map.md` | mandatory baseline |
| `specs/architecture/tech-debt.md` | mandatory baseline |
| `specs/architecture/decisions/README.md` | mandatory baseline，作為 ADR 目錄說明 |

Core init 不會先建立：

| Path | 理由 |
|---|---|
| `specs/domain/{context}/behavior.md` | 由 `/dflow:new-feature` 完成時或 brownfield baseline capture 建立 |
| `specs/architecture/decisions/ADR-*.md` | 架構決策出現時才建立，不在 Day 0 預先產生 |

### 3.3 本 repo 的結構位置

本 repo 的結構仍可作為維護背景，但不是 Dflow 在目標系統專案中產生的結構：

```text
AI-Guided-SDD-DDD-Development-Skills/
├── planning/
├── proposals/
├── reviews/
├── sdd-ddd-webforms-skill/
│   ├── SKILL.md
│   ├── references/
│   ├── templates/
│   └── scaffolding/
└── sdd-ddd-core-skill/
    ├── SKILL.md
    ├── references/
    ├── templates/
    └── scaffolding/
```

---

## 4. Skill 內部目錄角色

| 目錄 / 檔案 | 角色 | 讀取時機 |
|---|---|---|
| `SKILL.md` | 主控文件，定義觸發入口、決策樹、Ceremony Scaling、核心規則、引用檔案索引 | AI 啟用 skill 時最主要讀取 |
| `references/` | 流程細節，例如 init、new-feature、modify-existing、finish-feature、review、verify | 進入對應 `/dflow:` 指令或流程時讀取 |
| `templates/` | workflow 會實際產生或更新的 `.md` 文件樣板 | 產生 feature spec、phase spec、behavior 等文件時使用 |
| `scaffolding/` | `/dflow:init-project` 一次性種到專案中的輔助文件 | 專案初始化時使用，寫入後由目標專案維護 |

目前我們對 `scaffolding/` 的理解：

- 它不是一般流程反覆維護的文件樣板。
- 它是 project bootstrap 的 starter material。
- `CLAUDE-md-snippet.md` 屬於這類：用於既有專案已有 `CLAUDE.md` 時，提供手動合併片段，避免覆蓋原檔。

---

## 5. 流程入口與階段速記

目前 Dflow 採 Hybrid design：

- 主要入口：明確的 `/dflow:` slash commands。
- 次要入口：自然語言 auto-trigger safety net。
- safety net 的意思不是自動進流程，而是 AI 判斷意圖後提示建議指令，等待開發者確認。

| 入口 | 用途 | 主要產物 |
|---|---|---|
| `/dflow:init-project` | 初始化專案，建立 `specs/` baseline 與 optional scaffolding | shared docs、domain / features / architecture 基礎目錄、可選 onboarding / Git / CLAUDE snippet |
| `/dflow:new-feature` | 新功能，完整 T1 SDD cycle | feature directory、`_index.md`、第一份 `phase-spec-*.md`、可能的 context / behavior docs |
| `/dflow:new-phase` | active feature 加新 phase | 新增 `phase-spec-*.md`，更新 `_index.md` |
| `/dflow:modify-existing` | 修改既有行為，可依影響落在 T1 / T2 / T3 | phase spec、lightweight spec，或只更新 `_index.md` |
| `/dflow:bug-fix` | 輕量 bug 修復，不等同 Git Flow hotfix | 通常是 `lightweight-*.md` 或 bug spec |
| `/dflow:finish-feature` | feature closeout | 驗證 phase 完成、同步 BC 文件、移到 completed、輸出 Integration Summary |
| `/dflow:pr-review` | PR 審查 | 不產生主要 spec，但用 spec intent 先理解變更再 review code |
| `/dflow:verify` | drift verification | 檢查 `rules.md` 與 `behavior.md` 等 source of truth 是否漂移 |
| `/dflow:status` | 回報目前流程狀態 | 狀態摘要 |
| `/dflow:next` | 確認進下一步 | 無主要文件，作為 phase gate 確認訊號 |
| `/dflow:cancel` | 中止目前流程 | 保留已建立 artifacts |

---

## 6. 目前實際 templates 與 scaffolding

### 6.1 WebForms templates

| 檔案 | 作用 | 備註 |
|---|---|---|
| `sdd-ddd-webforms-skill/templates/_index.md` | feature directory 的索引、目前 BR snapshot、phase 列表、輕量修改紀錄 | P009 後的 feature directory 核心文件 |
| `sdd-ddd-webforms-skill/templates/phase-spec.md` | T1 新功能 / 新 phase 的主要規格文件 | 取代早期單一 `feature-spec.md` 概念 |
| `sdd-ddd-webforms-skill/templates/lightweight-spec.md` | T2 bug fix 或輕量修改規格 | P013 建議補上精簡 `實作任務` |
| `sdd-ddd-webforms-skill/templates/context-definition.md` | Bounded Context 定義 | init 或 feature 發現新 context 時使用 |
| `sdd-ddd-webforms-skill/templates/behavior.md` | BC 行為規格 snapshot | 與 `rules.md` / phase specs 形成 source of truth 關係 |
| `sdd-ddd-webforms-skill/templates/CLAUDE.md` | 專案根目錄 AI 協作規範完整樣板 | 公開散佈前需處理 Claude 命名依賴 |

### 6.2 Core templates

| 檔案 | 作用 | 備註 |
|---|---|---|
| `sdd-ddd-core-skill/templates/_index.md` | feature directory 的索引、目前 BR snapshot、phase 列表、輕量修改紀錄 | 與 WebForms 同類 |
| `sdd-ddd-core-skill/templates/phase-spec.md` | T1 新功能 / 新 phase 的主要規格文件 | Core 版會多考慮四層與 DDD tactical patterns |
| `sdd-ddd-core-skill/templates/lightweight-spec.md` | T2 bug fix 或輕量修改規格 | P013 建議補上精簡 `實作任務` |
| `sdd-ddd-core-skill/templates/context-definition.md` | Bounded Context 定義 | 與 WebForms 同類 |
| `sdd-ddd-core-skill/templates/behavior.md` | BC 行為規格 snapshot | 與 drift verification 有關 |
| `sdd-ddd-core-skill/templates/aggregate-design.md` | Aggregate 設計工作表 | Core 特有 |
| `sdd-ddd-core-skill/templates/CLAUDE.md` | 專案根目錄 AI 協作規範完整樣板 | 公開散佈前需處理 Claude 命名依賴 |

### 6.3 WebForms scaffolding

| 檔案 | 作用 | 備註 |
|---|---|---|
| `sdd-ddd-webforms-skill/scaffolding/_overview.md` | 專案概覽 starter | init 時可選 |
| `sdd-ddd-webforms-skill/scaffolding/_conventions.md` | 專案協作與文件慣例 starter | init 時可選 |
| `sdd-ddd-webforms-skill/scaffolding/Git-principles-gitflow.md` | Git Flow 版 Git 原則 | init 時依團隊分支策略選用 |
| `sdd-ddd-webforms-skill/scaffolding/Git-principles-trunk.md` | Trunk-based 版 Git 原則 | init 時依團隊分支策略選用 |
| `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md` | 既有 `CLAUDE.md` 的合併片段 | 不覆蓋既有 root `CLAUDE.md` |

### 6.4 Core scaffolding

| 檔案 | 作用 | 備註 |
|---|---|---|
| `sdd-ddd-core-skill/scaffolding/_overview.md` | 專案概覽 starter | init 時可選 |
| `sdd-ddd-core-skill/scaffolding/_conventions.md` | 專案協作與文件慣例 starter | init 時可選 |
| `sdd-ddd-core-skill/scaffolding/Git-principles-gitflow.md` | Git Flow 版 Git 原則 | init 時依團隊分支策略選用 |
| `sdd-ddd-core-skill/scaffolding/Git-principles-trunk.md` | Trunk-based 版 Git 原則 | init 時依團隊分支策略選用 |
| `sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md` | 既有 `CLAUDE.md` 的合併片段 | 不覆蓋既有 root `CLAUDE.md` |

---

## 7. 目前 references 文件

| 檔案 | WebForms | Core | 作用 |
|---|---:|---:|---|
| `init-project-flow.md` | 有 | 有 | `/dflow:init-project` 的問答、檔案預覽、寫入規則 |
| `new-feature-flow.md` | 有 | 有 | 新功能完整流程 |
| `new-phase-flow.md` | 有 | 有 | active feature 新增 phase |
| `modify-existing-flow.md` | 有 | 有 | 修改既有行為、T1/T2/T3 判斷、Delta |
| `finish-feature-flow.md` | 有 | 有 | feature 收尾、BC 文件同步、歸檔 |
| `pr-review-checklist.md` | 有 | 有 | PR review 前先讀 spec intent |
| `drift-verification.md` | 有 | 有 | `/dflow:verify` 漂移檢查 |
| `git-integration.md` | 有 | 有 | Git 分支策略與 Dflow 的關係 |
| `ddd-modeling-guide.md` | 無 | 有 | Core 專用 DDD 建模指引 |

觀察：

- `references/git-flow-integration.md` 已依 P011 改名為 `references/git-integration.md`，skill 本體應維持 Git-strategy-neutral。
- Git Flow 並沒有被完全刪除，而是移到 `/dflow:init-project` 可選 scaffolding：`scaffolding/Git-principles-gitflow.md`。Trunk / GitHub Flow 則對應 `scaffolding/Git-principles-trunk.md`。
- `AGENTS.md` 裡仍提到 `git-flow-integration.md`，比較像 repo 協作說明沒有同步到 P011 後狀態；這不代表 skill runtime 有缺漏，但可列為文件 cleanup。

---

## 8. 會產生的系統文件與樣板覆蓋狀態

這裡先用「目前討論中的理解」整理，不代表已經全部實作完成。

| 產出文件 | 目前樣板 | 覆蓋狀態 | 說明 |
|---|---|---|---|
| feature `_index.md` | `templates/_index.md` | 已有 | feature directory 的聚合入口 |
| phase spec | `templates/phase-spec.md` | 已有 | T1 主要規格 |
| lightweight spec | `templates/lightweight-spec.md` | 已有但可補強 | P013 建議補精簡 `實作任務` |
| context definition | `templates/context-definition.md` | 已有 | 新 BC 定義 |
| behavior snapshot | `templates/behavior.md` | 已有 | BR anchor / drift verification 依據 |
| aggregate design | `templates/aggregate-design.md` | Core 已有 | Core 特有，WebForms 不一定需要 |
| project CLAUDE.md | `templates/CLAUDE.md` | 已有 | 公開散佈前要重新思考 tool-neutral 命名 |
| CLAUDE snippet | `scaffolding/CLAUDE-md-snippet.md` | 已有 | 是合併片段，不是缺樣板 |
| `_overview.md` | `scaffolding/_overview.md` | 已有 | init optional scaffolding |
| `_conventions.md` | `scaffolding/_conventions.md` | 已有 | init optional scaffolding |
| Git principles | `scaffolding/Git-principles-*.md` | 已有 | 依 Git Flow / Trunk-based 選用 |
| `glossary.md` | 尚未有明確 template | P013 候選 | living Dflow-managed doc，應評估補樣板 |
| `models.md` | 尚未有明確 template | P013 候選 | Domain model snapshot |
| `rules.md` | 尚未有明確 template | P013 候選 | Business rules snapshot |
| `events.md` | Core 尚未有明確 template | P013 候選 | Core Domain Events snapshot |
| `context-map.md` | 尚未有明確 template | P013 候選 | 系統 context 關係圖 |
| `tech-debt.md` | 尚未有明確 template | P013 候選 | 技術債累積與遷移線索 |
| `architecture/decisions/README.md` | Core init 可能建立但缺 scaffolding template | P013 候選 | 若 init 產生，應有來源樣板 |
| Integration Summary | 無 | 可能合理 | `/dflow:finish-feature` 輸出的暫時摘要，不一定是持久系統文件 |

目前推論：

- 持久、Dflow-managed、會反覆維護的 `.md` 文件，原則上應有 template。
- 一次性 init starter 可放 `scaffolding/`。
- 暫時性輸出或對話摘要不一定需要 template。

---

## 9. Proposal 與 review 工作模式回憶

目前看到的模式大致是：

| 類型 | 代表文件 | 角色 |
|---|---|---|
| 獨立 proposal | `proposals/PROPOSAL-001-workflow-transparency.md` | 單一問題的完整設計與實作依據 |
| 批次評估 | `proposals/evaluation-p002-p008.md` | 從比較分析拆出多個 proposals |
| 實地候選整理 | `reviews/field-reference-candidates.md` | 從 V2 / field reference 對照後產生 P009-P011 |
| 跨 session prompt | `reviews/prompts/*.md` | 主控 session 提供給其他模型 / session 的任務說明 |
| review report | `reviews/round-*-report.md` | reviewer 找問題 |
| decision record | `reviews/round-*-decisions.md` | 主控或熟悉設計意圖的 session 判斷採納 / 延後 / 拒絕 |
| global sweep | `reviews/global-sweep-report.md` | 一批 proposal 實作後做橫向一致性檢查 |

這套模式的價值：

- 將 proposal、review、decision、implementation 拆開，避免單一模型一路自我確認。
- 透過跨模型與跨 session 降低盲點。
- 當上下文太大時，主控 session 可產生 prompts，把局部任務交給另一個 session。

需要注意：

- sweep 比較適合一批改動完成後再做，不適合每個小問題都做。
- `reviews/prompts/` 很適合保存「當時怎麼請另一個 session 看」的脈絡，但不應變成正式規格的唯一來源。

---

## 10. 目前已經浮現的問題候選

### P012：distribution-friendly paths

核心問題：

- `specs/_共用/` 是 machine-facing path，不利於公開散佈與跨工具使用。（已由 P012 處理）

目前提案方向：

- 改為 `specs/shared/`。
- 不處理文件內容標題語言。
- 不處理 npm 實作。
- 不動 `*-tw`。

### P013：system document template coverage

核心問題：

- 若 Dflow 會產生並維護某份持久 `.md` 系統文件，理論上應有 template，否則 AI 不知道結構應該長什麼樣。

目前提案方向：

- 補齊 `glossary.md`、`models.md`、`rules.md`、`events.md`、`context-map.md`、`tech-debt.md` 等缺口。
- 建立一份 WebForms / Core logical template coverage matrix。
- 保持物理 template 分開，不強制共用同一個檔案。

### Public distribution backlog

已另記於 `planning/public-distribution-backlog.md`：

- npm / CLI 散佈策略。
- 多 AI 工具相容。
- `CLAUDE.md` / `CLAUDE-md-snippet.md` 命名依賴。
- template localization。
- legacy internal path compatibility。

---

## 11. 接下來可思考的問題

以下問題可作為後續討論清單，不一定都要立刻寫 proposal。

| 問題 | 建議處理位置 |
|---|---|
| `git-flow-integration.md` 與實際 `git-integration.md` 命名不一致 | 小修正或納入 P012 / 後續 cleanup |
| `templates/CLAUDE.md` 與 `scaffolding/CLAUDE-md-snippet.md` 的界線是否需要寫更清楚 | public backlog 或後續 tool-neutral proposal |
| `scaffolding/` 與 `templates/` 的定義是否需在 `SKILL.md` 更明確 | P013 可順手補 |
| WebForms / Core 兩套共用邏輯如何維持同步 | P013 的 `TEMPLATE-COVERAGE.md` 可先處理樣板層；references 共通流程可能需另有矩陣 |
| T2 lightweight spec 是否應有統一 task 格式 | P013 已納入，但 task 數量是否觸發 T1 需開發者確認 |
| 中文內容何時英文化 | 暫放 public backlog，npm 前處理 |
| `-tw` 何時同步 | 英文版穩定後的 closeout / localization 階段 |
| sweep 是否值得做 | 一批 proposal 實作後值得；單點探索時成本偏高 |

---

## 12. 建議閱讀順序

如果目標是快速恢復全貌，可照這個順序：

1. 本文件。
2. `README.md`，只看定位與兩套 skill 差異。
3. 兩份 `SKILL.md` 的 trigger / workflow transparency / reference table。
4. `references/init-project-flow.md`，理解專案初始化會寫什麼。
5. `references/new-feature-flow.md`、`modify-existing-flow.md`、`finish-feature-flow.md`，理解主開發循環。
6. `templates/` 與 `scaffolding/` 清單，對照是否每個產物都有來源。
7. `proposals/PROPOSAL-012-distribution-friendly-paths.md`。
8. `proposals/PROPOSAL-013-system-document-template-coverage.md`。
9. `planning/public-distribution-backlog.md`。

---

## 13. 目前暫定結論

- `CLAUDE-md-snippet.md` 不是缺樣板，而是既有專案的安全合併片段。
- `scaffolding/` 本身合理，但定義可以更清楚。
- 「所有會產生的系統 `.md` 都應有樣板」這個原則大致成立，但要排除暫時性輸出與一次性手動合併片段。
- 下一個最值得收斂的正式提案仍是 P013，因為它能補齊 AI 寫文件時的結構依據。
- npm / public distribution 相關問題很重要，但目前應放在 backlog，避免污染當前 skill 邏輯修正。
