## PROPOSAL-014: CLI init 與 Dflow root namespace

**狀態**: `draft`

**提出日期**: 2026-04-30

**優先級**: `高`

**來源**: 2026-04-28 Post-R8 規劃 + Tutorial 試跑 + backlog § 2.1/§ 6 累積

---

### 問題描述

Dflow 在 P010 階段把 `/dflow:init-project` 設計為 skill 內 slash command，目標是讓新專案能建立最小 `specs/` baseline、選擇性 scaffolding，以及 `CLAUDE.md` 片段。這個設計已經讓 init flow 成形，但 Dflow 接下來要走向 npm 公開散佈時，init 的入口點需要重新定位。

第一個問題是：init 本質上適合 CLI，而不是 skill 內的 AI workflow。init 是 one-time bootstrap，主要工作是盤點目錄、問固定 intake 問題、計算檔案清單、寫入範本、避免覆寫。這些都是純機械流程，不需要 AI 做 domain 判斷，也不應依賴某一個 AI 工具的 slash command 機制。若 init 留在 skill 內，使用者必須先理解「安裝 skill → 開 AI tool → 觸發 `/dflow:init-project`」這條路徑；若改為 npm CLI，工程師可以直接在專案根目錄執行 `dflow init` 類命令，且可跨 Codex、Claude Code、Gemini、GitHub Copilot 等工具共用同一套 bootstrap 行為。

第二個問題是：目前 Dflow 產物預設落在專案根的 `specs/`。`specs/` 對 SDD 語境直覺，但公開散佈時有撞名風險。既有專案很可能已有測試 specs、API specs、OpenAPI specs、BDD specs 或其他工具管理的 `specs/` 目錄。npm CLI 若看到專案根有 `specs/`，也很難判斷它是否屬於 Dflow 管轄。Dflow 需要一個工具 namespace，讓 CLI 能明確辨識「哪些檔案是 Dflow 管的」，也讓使用者一眼看出這不是任意 specs 集合。

第三個問題是兩個議題高度耦合。init 從 skill 移到 CLI 會動到 `SKILL.md` decision tree、`references/init-project-flow.md`、scaffolding 寫入位置、README adoption 說明和 Tutorial init 段；文件根目錄從 `specs/` 改為 `dflow/specs/` 也會動到同一批檔案。如果拆成兩個 proposal，implement 階段會先改一次 init flow，再為 root namespace 重改一次同樣的 tree、Q&A、Tutorial outputs 和 scaffolding，造成 cascade 雙改與 review 負擔。

本 proposal 不處理 templates prose language 規範。backlog § 1.2 的 prose language 問題和本 proposal 有散佈脈絡上的關聯，但它改的是文件內容語言與 AI 寫作行為；本 proposal 只處理 init 入口與 machine-facing path namespace。

### 提議的解法

#### 1. 確認 init 從 skill slash command 上移到 npm CLI

本 proposal 直接採用 CLI 作為解法，不再把 `/dflow:init-project` 視為主要入口。P010 既有 init flow 不廢棄，而是轉為 CLI 的內部流程規格與無 Node.js 環境時的 AI 手動引導參考。

三種 CLI 架構比較如下：

| 模式 | 命令 | 升級可控性 | first-run 摩擦 | 持續維護 `dflow/specs/` 的適配性 | 評估 |
|---|---|---|---|---|---|
| Global install | `npm install -g dflow` + `dflow init` | 使用者可控制何時升級 global CLI，但容易出現機器上 CLI 版本與專案文件版本不一致 | 需要先安裝，對第一次試用者多一步 | 適合未來有 `dflow doctor`、`dflow migrate` 等常用命令的 power user；但不應要求所有專案跟著 global CLI 自動升級 | 不作為預設入口，可作為進階選項 |
| One-shot npx | `npx dflow init` 或 `npx dflow@x.y.z init` | 可每次明確選最新版或 pin 到指定版本；專案內的 Dflow 文件不會因 CLI 更新自動變動 | 不需全域安裝，摩擦低；需要 Node.js/npm 可用 | 比 `npm create` 更自然，因為 Dflow 不只 scaffolding，未來同一 CLI 可承接 verify / doctor / migrate 類命令 | **建議預設入口** |
| npm initializer | `npm create dflow` | initializer 慣例通常抓最新版，也可透過版本 pin 控制；但使用者容易理解為「只跑一次的專案建立器」 | 對前端生態使用者直覺，first-run 文字最短 | Dflow 不是純 scaffolding。`npm create` 可以作為 init alias，但不宜成為唯一心智模型，否則會弱化 Dflow 對 `dflow/specs/` 的長期維護角色 | 建議作為替代入口，呼叫同一套 init implementation |

建議 default 為 `npx dflow init`。理由是它同時滿足低 first-run 摩擦、版本可控、且不把 Dflow 誤定位成一次性 scaffolder。`npm create dflow` 可保留為 alternative，服務偏好 initializer 慣例的使用者，但它應該只是同一個 init flow 的別名，不應維護第二份邏輯。`npm install -g dflow` 可保留於 README 的進階用法，不作為 V1 的主推薦。

#### 2. skill 內 `/dflow:init-project` 的處置

skill 內的 `/dflow:init-project` slash command 移除，避免雙入口混淆與 logic drift。

實作方向：

| 項目 | 決定 | 說明 |
|---|---|---|
| `SKILL.md` frontmatter primary triggers | 移除 `/dflow:init-project` | skill runtime 不再宣告 init slash command |
| `SKILL.md` decision tree | 移除 `/dflow:init-project → INIT PROJECT WORKFLOW` entry | init 由 CLI 觸發，不是 AI runtime workflow |
| `SKILL.md` Slash Commands 表 | 移除 Project-level command entry | 保留 new-feature / modify-existing / bug-fix / new-phase / finish-feature 等日常 workflow |
| `references/init-project-flow.md` | 保留並改寫定位 | 作為 CLI internal flow spec，以及無 Node.js/npm 時 AI 可手動引導的參考，不再宣稱是 slash command |
| Tutorial init 段 | 改為 shell 執行 CLI | 劇情 1/2 段 1 從「Alice 觸發 `/dflow:init-project`」改為「Alice 在 shell 執行 `npx dflow init`」 |

#### 3. Dflow 文件根目錄改為 `dflow/specs/`

新專案 default 採兩層結構：

```text
dflow/
└── specs/
    ├── shared/
    ├── domain/
    ├── features/
    ├── architecture/   # Core edition
    └── migration/      # WebForms edition
```

`dflow/` 是工具 namespace，`specs/` 是 SDD 語意 namespace。兩層結構把工具邊界與文件語意分開：CLI 可以把 `dflow/` 視為自己的管轄範圍，使用者仍能從 `dflow/specs/` 看出這些文件是規格與行為知識。

三種 root 選項比較如下：

| 選項 | 撞名風險 | 工具邊界 | SDD 語意連續性 | npm CLI 管轄判斷 | 評估 |
|---|---|---|---|---|---|
| `specs/` | 高。既有專案常有測試 specs、API specs 或其他工具 specs | 弱。CLI 很難判斷既有 `specs/` 是否歸 Dflow 管 | 強。最直覺連到 SDD / OpenSpec 語境 | 弱。需要額外 marker 或複雜偵測 | 不適合作為公開版新專案 default |
| `dflow/` | 低。工具名稱較不易撞名 | 強。整個目錄都可視為 Dflow 管轄 | 中。若所有內容直接放 `dflow/` 下，`features/`、`domain/` 的規格語意少一層說明 | 強。CLI 好判斷 | 比 `specs/` 好，但語意過度工具化 |
| `dflow/specs/` | 低。外層 namespace 避免撞名 | 強。CLI 可把 `dflow/` 視為邊界 | 強。內層 `specs/` 保留 SDD 語意 | 強。CLI 可安全掃描與寫入 `dflow/specs/` | **採用** |

本 proposal 只規範新專案 default 與文件引用更新，不提供 legacy `specs/` migration tool。既有專案若已使用 `specs/`，未來由 backlog § 5/§ 7 的 compatibility / migration proposal 決定是否提供 `dflow migrate`、read-only legacy support 或手動 migration guide。

#### 4. npm package 雛形

本 proposal 不設計完整 CLI，只要求 V1 implement 階段建立能支撐 init 的最小 npm package skeleton：

```text
package.json
bin/
└── dflow.js
lib/
└── init.js
```

高層責任：

| 檔案 | 責任 |
|---|---|
| `package.json` | 定義 package metadata、`bin` entry、基本 script；package name 是否使用 `dflow` 需在 publish 前確認 npm 可用性 |
| `bin/dflow.js` | CLI entry。解析 `dflow init`、`--help`、`--version`，並把 init 交給 `lib/init.js` |
| `lib/init.js` | 實作 P010 init flow 的機械步驟：repo inventory、intake prompts、file-list preview、existing-file protection、template copy / placeholder substitution、results report |

若同時支援 `npm create dflow`，initializer 應呼叫同一個 `lib/init.js`，避免 `npx dflow init` 與 `npm create dflow` 邏輯雙寫。

#### 5. Legacy `specs/` migration 延後

本 proposal 明確不處理 migration tool。Implement 階段只做：

| 範圍 | 決定 |
|---|---|
| 新專案 default | CLI init 寫入 `dflow/specs/` |
| 既有 repo 偵測 | 若看到根目錄 `specs/`，只提示可能是 legacy 或非 Dflow 目錄，不自動搬移 |
| 舊 Tutorial outputs | 本 repo 的 tutorial outputs 直接改路徑，因它們是教學產物，不是使用者專案 migration |
| 使用者既有專案 | 不提供自動 migration；另案處理 |

### 影響範圍

本節依下列命令建立 baseline：

```bash
grep -rn "specs/" --include="*.md" .
```

排除：`planning/`、既有 `proposals/` 歷史檔、`reviews/`、`CHANGELOG.md` 既有 R7/R8 歷史段。注意：檔名含 `_tw` 的檔案（如 `PRACTICE_PLAN_tw.md`、`tutorial/TUTORIAL_*_tw.md`）若仍是目前文件的一部分，需列入「修改 / 確認」範圍。依本 proposal 的實作口徑，grep-derived 既有目標為 72 檔：skill / tutorial / root docs 中被 `specs/` 命中的檔案。`CHANGELOG.md` 新增段與 npm package 新檔不在 grep count 內。

> **註（2026-04-30）**：原 Codex draft 列 `sdd-ddd-*-skill-tw/` 為排除目錄；該目錄已於 commit `b3d5963` 棄用刪除，故本節不再列為排除項。

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `package.json` | 新增 | npm package skeleton；定義 `bin.dflow` 與基本 metadata |
| `bin/dflow.js` | 新增 | CLI entry；支援 `dflow init` / help / version，呼叫 `lib/init.js` |
| `lib/init.js` | 新增 | init flow 的機械實作；預設寫入 `dflow/specs/` |
| `sdd-ddd-webforms-skill/SKILL.md` | 修改 | 移除 `/dflow:init-project` trigger / decision tree / slash command entry；Project Structure 改為 `dflow/specs/` |
| `sdd-ddd-core-skill/SKILL.md` | 修改 | 同 WebForms；Core tree 內 `architecture/` 與 ADR path 改到 `dflow/specs/` |
| `sdd-ddd-webforms-skill/references/init-project-flow.md` | 修改 | 從 slash command flow 改為 CLI internal flow spec；所有 baseline / optional paths 改為 `dflow/specs/` |
| `sdd-ddd-core-skill/references/init-project-flow.md` | 修改 | 同 WebForms；Core mandatory paths 改為 `dflow/specs/domain/context-map.md`、`dflow/specs/architecture/...` |
| `sdd-ddd-webforms-skill/references/{drift-verification,finish-feature-flow,git-integration,modify-existing-flow,new-feature-flow,new-phase-flow,pr-review-checklist}.md` | 修改 | 將日常 workflow、git move、verify、review checklist 中的 `specs/...` 引用改為 `dflow/specs/...` |
| `sdd-ddd-core-skill/references/{ddd-modeling-guide,drift-verification,finish-feature-flow,git-integration,modify-existing-flow,new-feature-flow,new-phase-flow,pr-review-checklist}.md` | 修改 | 同 WebForms；另含 Core DDD modeling guide 的 context-map path |
| `sdd-ddd-webforms-skill/templates/{CLAUDE,_index,behavior,lightweight-spec,phase-spec}.md` | 修改 | Template 內生成位置、Domain doc 引用、feature path examples 改為 `dflow/specs/...` |
| `sdd-ddd-core-skill/templates/{CLAUDE,_index,behavior,lightweight-spec,phase-spec}.md` | 修改 | 同 WebForms；Core-specific path examples 同步 |
| `sdd-ddd-webforms-skill/scaffolding/{CLAUDE-md-snippet,Git-principles-gitflow,Git-principles-trunk,_conventions,_overview}.md` | 修改 | 專案級 scaffolding 內所有規格路徑與 directory tree 改為 `dflow/specs/...` |
| `sdd-ddd-core-skill/scaffolding/{CLAUDE-md-snippet,Git-principles-gitflow,Git-principles-trunk,_conventions,_overview}.md` | 修改 | 同 WebForms；Core architecture debt / ADR 路徑同步 |
| `sdd-ddd-core-skill/PRACTICE_PLAN_tw.md` | 修改 / 確認 | 檔名含 `_tw`，位於 runtime skill 目錄內；若仍保留為目前 Core practice plan，需同步 `dflow/specs/` path refs |
| `tutorial/01-greenfield-core/01-init-project.md` | 修改 | 重寫 init 段：Alice 改在 shell 執行 `npx dflow init`；outputs path 改為 `dflow/specs/` |
| `tutorial/02-brownfield-webforms/01-init-project.md` | 新增 | 目前 Brownfield 劇情缺段 1；新增 CLI init 入口段，承接 00 setup 與後續 02 modify-existing |
| `tutorial/01-greenfield-core/{02-new-feature,03-new-phase,04-modify-existing,05-bug-fix,06-finish-feature}.md` | 修改 | 對話內 `specs/` 引用同步改為 `dflow/specs/` |
| `tutorial/02-brownfield-webforms/{00-setup,02-modify-existing,03-baseline-capture,04-new-feature,05-bug-fix,06-finish-feature}.md` | 修改 | 對話內 `specs/` 引用同步改為 `dflow/specs/` |
| `tutorial/TUTORIAL_{CORE,WEBFORMS}_tw*.md` | 修改 / 確認 | 檔名含 `_tw`，位於 tutorial 目錄內；若這些 top-level tutorial 摘要仍是目前對外閱讀文件，需同步 `dflow/specs/` path refs |
| `tutorial/01-greenfield-core/outputs/CLAUDE.md` | 修改 | Project AI guide 內的規格路徑改為 `dflow/specs/` |
| `tutorial/02-brownfield-webforms/outputs/CLAUDE.md` | 修改 | 同 Core tutorial output |
| `tutorial/01-greenfield-core/outputs/specs/**` | 路徑搬移 / 修改 | 全部移到 `tutorial/01-greenfield-core/outputs/dflow/specs/**`；檔內相對或絕對 `specs/` 引用同步 |
| `tutorial/02-brownfield-webforms/outputs/specs/**` | 路徑搬移 / 修改 | 全部移到 `tutorial/02-brownfield-webforms/outputs/dflow/specs/**`；檔內引用同步 |
| `README.md` | 修改 | Adoption 說明改為 npm CLI first：預設 `npx dflow init`，替代 `npm create dflow`；baseline path 改為 `dflow/specs/` |
| `TEMPLATE-COVERAGE.md` | 修改 | Generated / maintained path 欄位改為 `dflow/specs/...`；Project AI guide 若維持 repo root `CLAUDE.md` 需明示其例外 |
| `TEMPLATE-LANGUAGE-GLOSSARY.md` | 確認 / 視需要修改 | 目前 grep 無 `specs/` 命中；若 implement 階段新增 root namespace 術語，再補 glossary |
| `CHANGELOG.md` | 修改 | 只在檔尾新增 R9 / P014 段；不改寫 R7/R8 歷史條目中的 `specs/` |

不納入本 proposal implement 範圍：

| 檔案 / 範圍 | 理由 |
|---|---|
| `planning/` | 歷史規劃紀錄，不追溯改寫 |
| 既有 `proposals/` 與 `reviews/` | 已 implemented 或評估紀錄，不追溯改寫 |
| `CHANGELOG.md` R7/R8 既有段落 | 保留時序紀錄；只新增新段 |
| legacy `specs/` migration tool | backlog § 5/§ 7 另案處理 |
| templates prose language 規範 | backlog § 1.2，預計 PROPOSAL-015 獨立拍板 |

### 預估工作量

`大`

Implement 規模預估約 30-50 檔次，接近 R8b 規模。雖然 grep-derived 目標數高於 50，tutorial output 的 `outputs/specs/**` 可用 directory move 與批次 path replacement 收斂，不必逐檔人工重寫。

建議切成 4 個 sub-wave：

| Sub-wave | 範圍 | 說明 |
|---|---|---|
| Sub-wave 1 | npm package 雛形 | 新增 `package.json`、`bin/dflow.js`、`lib/init.js`；先讓 `npx dflow init` 的 skeleton 成形 |
| Sub-wave 2 | skill 雙版更新 | 雙版 `SKILL.md` 移除 `/dflow:init-project` slash entry；`references/init-project-flow.md` 改為 CLI internal flow；skill / references / templates / scaffolding 路徑改 `dflow/specs/` |
| Sub-wave 3 | Tutorial 更新 | 搬移 tutorial outputs 到 `outputs/dflow/specs/**`；補劇情 1/2 段 1 的 CLI 入口敘事；同步所有對話 path refs |
| Sub-wave 4 | Root docs cascade | 更新 `README.md`、`TEMPLATE-COVERAGE.md`、必要時 `TEMPLATE-LANGUAGE-GLOSSARY.md`，並在 `CHANGELOG.md` 新增 P014 / R9 段 |

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md` | 取代部分流程 | P010 是本 proposal 的直接前身；P014 保留 init flow 的內容，但移除 skill slash command，改由 CLI 觸發 |
| `proposals/PROPOSAL-012-distribution-friendly-paths.md` | 互補 | P012 解決 path 內 machine-facing ASCII / English 問題；P014 解決 root namespace 與公開版撞名問題 |
| `proposals/PROPOSAL-013-system-document-template-coverage.md` | 互補 | P013 的 template coverage 與 anchor 機制讓本 proposal implement 時的 heading / generated path drift 可控 |

### 評估紀錄（整合評估時填寫）

**評估日期**:

**結論**:

**理由**:

**如果 rejected**:
