## PROPOSAL-014: CLI init 與 Dflow root namespace

**狀態**: `implemented`

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
| One-shot npx | `npx dflow init` 或 `npx dflow@x.y.z init` | 可每次明確選最新版或 pin 到指定版本；專案內的 Dflow 文件不會因 CLI 更新自動變動 | 不需全域安裝，摩擦低；需要 Node.js/npm 可用 | 比 `npm create` 更自然，因為 Dflow 不只 scaffolding，未來同一 CLI 可承接 verify / doctor 類命令 | **V1 唯一正式入口** |
| npm initializer | `npm create dflow` | initializer 慣例通常抓最新版，也可透過版本 pin 控制；但需要額外定義 create wrapper package 或特殊 npm package 拓撲 | 對前端生態使用者直覺，first-run 文字最短 | Dflow 不是純 scaffolding。若 V1 同時支援會增加 package topology、publish 與文件維護複雜度 | **V1 不實作；V2 依真實使用者反饋再評估** |

本 proposal 依 review `F-01` 收斂為 V1 **npx-only**：`npx dflow init` 是 V1 唯一正式入口。理由是 Dflow 目前沒有公開使用者，V1 不需要同時承擔 `npm create` wrapper package、雙 package publish 或 alias routing 的拓撲複雜度；單一入口可降低 implement 風險，也讓文件與 Tutorial 敘事更一致。

`npm install -g dflow` 在 V1 不作為推薦入口，但可以允許並放在 README 進階用法中，服務需要固定全域 CLI 的進階使用者。`npm create dflow` 在 V1 不實作；若未來有真實使用者回饋顯示 initializer 心智模型有必要，再於 V2 另案評估 package topology 與 publish strategy。

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

本 proposal 只規範 V1 新專案 clean baseline 與文件引用更新，不提供 legacy `specs/` migration tool、dual-read 或自動偵測搬移。既有 root-level `specs/` 不被視為 V1 必須相容的 Dflow 執行期路徑；未來若出現真實公開使用者，再另案評估 versioning policy。

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
| `package.json` | 定義 package metadata、`bin` entry、基本 script；package name 是否使用 `dflow` 需在 publish 前確認 npm 可用性；V1 不新增 `create-dflow` wrapper package |
| `bin/dflow.js` | CLI entry。解析 `dflow init`、`--help`、`--version`，並把 init 交給 `lib/init.js` |
| `lib/init.js` | 實作 P010 init flow 的機械步驟：repo inventory、intake prompts、file-list preview、existing-file protection、template copy / placeholder substitution、results report |

V1 npm package skeleton 只需支援 `npx dflow init` 對應的 `bin.dflow` entry。`npm create dflow` 相關的 create-wrapper package、同 repo 雙 package 管理、alias routing 與 publish 流程都不列入 V1。

#### 5. V1 clean cut：不提供 legacy `specs/` 相容層

本 proposal 依 review `F-02` 與 user 拍板採 **V1 clean cut**：Dflow 目前 0 公開使用者，因此 V1 不背負 backward compatibility 包袱。新 baseline 直接採 `npx dflow init` + `dflow/specs/`；舊的 skill slash command 與 root-level `specs/` 不提供 runtime 相容層。

| 範圍 | 決定 |
|---|---|
| 新專案 default | CLI init 寫入 `dflow/specs/` |
| `dflow migrate` | V1 不提供 migration tool |
| dual-read 模式 | V1 不同時讀 `specs/` 與 `dflow/specs/` |
| legacy `specs/` 自動偵測搬移 | V1 不自動判斷、搬移或改寫根目錄 `specs/` |
| 舊 Tutorial outputs | 本 repo 的 tutorial outputs 直接改路徑，因它們是教學產物，不是使用者專案 migration |
| 使用者既有專案 | 不提供向下相容或自動 migration；V1 前提是 clean baseline |

未來若 Dflow 出現真實公開使用者，再另案評估 versioning policy、compatibility promise、manual migration guide 或 migration CLI。此決策不需要另開 P016；本 proposal 直接宣告 V1 的破壞性升級契約。

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
| `sdd-ddd-webforms-skill/SKILL.md` | 修改 | 移除 `/dflow:init-project` trigger / decision tree / slash command entry；Project Structure 改為 `dflow/specs/`；依 review `F-04`，驗收：`grep '/dflow:init-project' sdd-ddd-webforms-skill/SKILL.md` 應為空（保留 CHANGELOG / 歷史註記不算） |
| `sdd-ddd-core-skill/SKILL.md` | 修改 | 同 WebForms；Core tree 內 `architecture/` 與 ADR path 改到 `dflow/specs/`；依 review `F-04`，驗收：`grep '/dflow:init-project' sdd-ddd-core-skill/SKILL.md` 應為空（保留 CHANGELOG / 歷史註記不算） |
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
| `README.md` | 修改 | Adoption 說明改為 npm CLI first：V1 預設 `npx dflow init`；`npm install -g dflow` 只放進階用法；`npm create dflow` 標註為 V2 評估；baseline path 改為 `dflow/specs/` |
| `TEMPLATE-COVERAGE.md` | 修改 | Generated / maintained path 欄位改為 `dflow/specs/...`；Project AI guide 若維持 repo root `CLAUDE.md` 需明示其例外 |
| `TEMPLATE-LANGUAGE-GLOSSARY.md` | 確認 / 視需要修改 | 目前 grep 無 `specs/` 命中；若 implement 階段新增 root namespace 術語，再補 glossary |
| `CHANGELOG.md` | 修改 | 只在檔尾新增 R9 / P014 段；不改寫 R7/R8 歷史條目中的 `specs/` |

不納入本 proposal implement 範圍：

| 檔案 / 範圍 | 理由 |
|---|---|
| `planning/` | 歷史規劃紀錄，不追溯改寫 |
| 既有 `proposals/` 與 `reviews/` | 已 implemented 或評估紀錄，不追溯改寫 |
| `CHANGELOG.md` R7/R8 既有段落 | 保留時序紀錄；只新增新段 |
| legacy `specs/` migration tool / dual-read / 自動偵測搬移 | V1 clean cut，不提供 migration tool、不提供向下相容、不提供 legacy `specs/` 自動搬移 |
| templates prose language 規範 | backlog § 1.2，預計 PROPOSAL-015 獨立拍板 |
| 合併成單一 PROPOSAL | 依 review `F-12` 決議不合併；P014 與 P015 問題層次不同，但 implement 採 shared-cut |

### 預估工作量

`大`

依 review `F-05`，工作量不應只用單一檔數估算。較務實的模型分三段：

| 類型 | 預估 | 說明 |
|---|---:|---|
| 直接修改檔 | 約 40-50 檔 | 雙版 `SKILL.md`、references、templates、scaffolding、root docs、README / TEMPLATE docs / CHANGELOG 新段，以及 npm skeleton |
| 路徑搬移檔 | 約 25-35 檔 | `tutorial/*/outputs/specs/**` 移到 `tutorial/*/outputs/dflow/specs/**`；實際數量依 implement 當下 outputs 內容為準 |
| 驗證檔 | full repo | grep 一致性、link 檢查、Tutorial 對話內 path refs、`/dflow:init-project` 殘留掃描、`specs/` vs `dflow/specs/` 語意檢查 |

整體仍歸類為「大」。主要成本不是 npm skeleton，而是 repo-wide cascade 與驗證。

依 review `F-11`，P014 與 P015 implement 不應分成兩批反覆修改同一批 init / conventions / Tutorial 檔案。建議採 **shared-cut implementation**，把共同主幹先定義清楚，再一次 cascade：

| Wave | 範圍 | 說明 |
|---|---|---|
| W1 | Init contract spec + npm skeleton | 定義共同 init contract：V1 npx-only、`dflow/specs/` 寫入位置、file-list preview、existing-file protection、P015 `prose-language` 必填 Q、寫入 `dflow/specs/shared/_conventions.md` 的規格；再新增 `package.json`、`bin/dflow.js`、`lib/init.js` 實作 skeleton |
| W2 | Repo-wide cascade | skill 雙版、references、templates、scaffolding、Tutorial 對話與 outputs、root docs 一次 cascade；同批處理 P014 root namespace 與 P015 prose-language，避免同一批 `_conventions.md` / init flow / Tutorial prose 連續雙改 |

此重構回應 review `F-11`，也明示 P014 與 P015 維持兩份 proposal，但 implement 階段採共同 wave。

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| `proposals/PROPOSAL-009-feature-directory-and-phases.md` | 依賴 | 依 review `F-03`，P014 改的是 root prefix（`specs/` → `dflow/specs/`），P009 的 feature path 結構契約（`features/active/SPEC-...`）不變；只 cascade prefix |
| `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md` | 取代部分流程 | P010 是本 proposal 的直接前身；P014 保留 init flow 的內容，但移除 skill slash command，改由 CLI 觸發 |
| `proposals/PROPOSAL-012-distribution-friendly-paths.md` | 互補 | P012 解決 path 內 machine-facing ASCII / English 問題；P014 解決 root namespace 與公開版撞名問題 |
| `proposals/PROPOSAL-013-system-document-template-coverage.md` | 互補 | P013 的 template coverage 與 anchor 機制讓本 proposal implement 時的 heading / generated path drift 可控 |
| `proposals/PROPOSAL-015-templates-prose-language-convention.md` | 互補（同期 implement） | 依 review `F-10`，P015(B) 強依賴 P014 init CLI 落地；P015(C) fallback 可獨立。Implement 階段採 review `F-11` shared-cut，兩 PROPOSAL 共同 wave 一次 cascade |

### 評估紀錄（整合評估時填寫）

**評估日期**: 2026-04-30

**結論**: approved

**理由**: 兩議題（init CLI 化 + dflow/specs/ namespace）合併拍板避免 cascade 雙改；revision 已處理全部 7 finding。F-01 V1 npx-only 收斂入口拓撲；F-02 V1 clean cut 處理破壞性升級（無現存使用者前提）；F-11 sub-wave 重整為 W1 init contract + W2 repo-wide cascade，與 P015 共享切面。Implement 階段採 shared-cut 與 P015 同期。

**如果 rejected**: N/A

### 實施紀錄

**實施日期**: 2026-05-01

**結論**: implemented

**實施摘要**: P014 已依 shared-cut 完成。W1 建立 `npx dflow init` 的 init contract 與 npm package skeleton；W2a 將 skill source、references、templates、scaffolding cascade 到 `dflow/specs/` 並移除 runtime `/dflow:init-project` 入口；W2b 將 Tutorial 對話與 outputs cascade 到 `dflow/specs/`；W2c 完成 root docs closeout，README adoption 改為 npm CLI first，`TEMPLATE-COVERAGE.md` generated / maintained paths 改為 `dflow/specs/...`，CHANGELOG 新增 R9 段。

**保留決策**: V1 clean cut 維持不提供 legacy root `specs/` migration tool、dual-read 或自動搬移；既有 R7/R8 CHANGELOG 歷史條目不追溯改寫。
