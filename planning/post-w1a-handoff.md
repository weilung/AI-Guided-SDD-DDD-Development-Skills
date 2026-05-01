# Post-W1a Handoff Notes — 給下個主線 session 接續用

> **目的**：讓下個主線 session 從 cold start 也能接續 P014 + P015 shared-cut implementation 的 W1b / W2 工作
> **建立日期**：2026-04-30（本 session token 安全考量結點）
> **接續者**：**Codex CLI 主線 session**（per user 2026-04-30 拍板，Claude token 餘量低，主線改 Codex；Claude 留作 cross-model review 召喚）
> **本 session 角色**：Claude Code director（將交接給 Codex director）；延續自前一個主線 session 交接的 Post-R8 階段

> **狀態更新（2026-05-01）**：本 handoff 是 W1a 後的歷史交接文件；W1b、W2a、W2b、W2c 已完成並提交。現行 baseline 是 `npx dflow init` + `dflow/specs/`，tutorial outputs 已搬到 `outputs/dflow/specs/`。下方 W2 待辦與 `outputs/specs/` 描述保留為當時交接脈絡，不再代表目前狀態。

---

## 第一句話（給新 session 看的開場）

> 「我接續 Post-R8 / P014+P015 implement 階段，作為 Codex director。先讀 `AGENTS.md § Director vs Worker session 模式` + `planning/post-w1a-handoff.md` + `planning/init-contract-spec.md` + 最新 commit log 建立 baseline，然後派 W1b 給 user 帶去另一個 Codex worker session 跑。」

---

## 你的角色（Codex director session）

- **Director**：統籌 + 寫提示詞給 worker / sub-session + 產出品質 review + 與 user 對齊大方向
- **Worker**：本檔內 W1b prompt 已寫好；user 會帶去另一個 Codex CLI session（worker 角色）跑
- **Cross-model review trigger**：以下情境建議 user 開 Claude Code worker session 做 review，得獨立視角：
  - W1b implementation 完成後（JS 程式碼跨模型 review；catch logic bugs / dependency 選擇問題）
  - W2 cascade 完成後 sub-wave（檔數多 + drift risk 高，獨立視角檢查 path 一致性）
  - 任何 PROPOSAL revision 後（仿本 session 已採用的「Codex draft → Codex 不同 model review」模式）
  - 一般 implement progress review 不需要跨模型；同 model review 即可
- **User 角色**：approve / reject 拍板（PROPOSAL 評估紀錄段）；commit；director ↔ worker 之間 copy-paste prompts
- **Token 風險**：context 接近安全閾值前寫下一份 handoff 交接，自然 milestone 點選結（commit 後、prompt 派工前），不要 mid-task overflow
- **PROPOSAL 評估紀錄是 user 決定**：不是任何 AI session 的工作，包括 director；director 提供 approve / reject 建議模板，最終是 user 填寫

### 本 setup 沒有「Codex.ai Project」/「Claude.ai Project」

兩個 docs（CLAUDE.md / AGENTS.md）舊文字曾提及外部 SaaS 評估環境，已知 stale。本 setup 全部評估在 repo 內進行：user 直接讀 PROPOSAL + review report 後 approve / reject。AGENTS.md 已於本 session 更新；CLAUDE.md 待下次有 Claude director session 時順手更新。

---

## 當前進度（截至 commit `03fd637`）

### 本 session（2026-04-30）已完成

```
b3d5963  棄用繁中版 skill：刪除 *-tw 目錄 + 同步 active doc 引用
14d6456  PROPOSAL-014 draft：CLI init 與 Dflow root namespace（合併拍板）
6daff21  PROPOSAL-015 draft：Templates prose language 規範補完
602ab2f  PROPOSAL-014 + PROPOSAL-015 設計 review 報告（GPT-5 產出）
dbb668f  PROPOSAL-014 + PROPOSAL-015 revision：依 review report 修正全部 findings
5e66363  approve PROPOSAL-014 + PROPOSAL-015
03fd637  W1a: Init contract specification (Codex CLI 產出)
```

### 兩 PROPOSAL 已 approved

- `proposals/PROPOSAL-014-cli-init-and-dflow-root-namespace.md`：狀態 `approved`，2026-04-30
- `proposals/PROPOSAL-015-templates-prose-language-convention.md`：狀態 `approved`，2026-04-30
- 兩 PROPOSAL implement 採 **shared-cut**：W1 共同主幹（init contract + npm skeleton）→ W2 repo-wide cascade

### W1a 已完成

- `planning/init-contract-spec.md`（657 行英文）= W1b 直接翻譯成 JS 的 authoritative spec
- 涵蓋：pre-flight / Q&A / validation / file preview / write actions / post-write / error handling / out-of-scope / W1b 銜接
- 所有 P014 + P015 設計決策已收斂；W1b 不重新決策

### 正在 working tree（接續處）

- 無 uncommitted change；working tree clean

---

## 本 session 已決定但尚未落地的事

- **棄用繁中版 skill**：commit `b3d5963` 已刪除 `sdd-ddd-*-skill-tw/`；C1 工作不再需要做。Tutorial 已是人讀介面
- **Tutorial outputs 路徑**：目前在 `tutorial/01-greenfield-core/outputs/specs/` + `tutorial/02-brownfield-webforms/outputs/specs/`；**W2 cascade 階段** 將搬到 `outputs/dflow/specs/`

---

## 你的下一步：派 W1b Codex prompt

### 任務：W1b — npm package 雛形實作

依 `planning/init-contract-spec.md` 撰寫實際 JS 程式碼，產出可運作的 `npx dflow init`。

**輸出檔**：

```
package.json
bin/
└── dflow.js
lib/
├── init.js         # 主 flow（也可拆 lib/init/preflight.js / prompts.js / writer.js）
templates/
├── core/           # 從 sdd-ddd-core-skill/templates + scaffolding 翻譯打包
└── webforms/       # 從 sdd-ddd-webforms-skill/templates + scaffolding 翻譯打包
```

### Codex Prompt — W1b: npm Package 雛形實作

複製整段給 Codex CLI（建議 GPT-5 或同等 npm 生態熟悉的 model）：

````
你的任務是為 P014 + P015 shared-cut implementation 的 **W1b：npm package 雛形實作** 撰寫實際 JS 程式碼。

## Authoritative spec（不可重新決策設計）

`planning/init-contract-spec.md` 是 W1a 已完成且 approved 的 init contract 規格。本任務是把該 spec 翻譯成可運作的 npm package。**不要重新決策 init 行為**；任何與 spec 不一致的實作都需先回報給 user，不要自行決定。

## 必讀 baseline

1. `AGENTS.md`（你的協作規範）
2. `planning/init-contract-spec.md`（**authoritative**；逐節對應實作）
3. `proposals/PROPOSAL-014-cli-init-and-dflow-root-namespace.md`（approved；§4 npm package 雛形）
4. `proposals/PROPOSAL-015-templates-prose-language-convention.md`（approved；§7.1 Prose Language 段格式）
5. `sdd-ddd-core-skill/templates/`（W1b templates/core/ 來源）
6. `sdd-ddd-core-skill/scaffolding/`（同上）
7. `sdd-ddd-webforms-skill/templates/`（W1b templates/webforms/ 來源）
8. `sdd-ddd-webforms-skill/scaffolding/`（同上）

## 任務範圍

### 1. 建立 npm package 雛形

| 檔 | 責任 | spec 章節對應 |
|---|---|---|
| `package.json` | metadata、`bin.dflow`、`engines.node: ">=22.0.0"`、`files`、`scripts.test` | §10.2 |
| `bin/dflow.js` | CLI entry；解析 `dflow init` / `--help` / `--version`；delegate 給 `lib/init.js` | §1.1, §10.2 |
| `lib/init.js` | 主 init flow；可拆子模組（preflight / prompts / writer / report）但不強制 | §2-§7, §10.1 |
| `templates/core/...` | Core edition 完整 template + scaffolding 集合 | §3.3, §6.4 |
| `templates/webforms/...` | WebForms edition 完整 template + scaffolding 集合 | §3.3, §6.4 |

### 2. 翻譯既有 templates 進 npm package

`templates/core/` 與 `templates/webforms/` 應從 `sdd-ddd-{core,webforms}-skill/{templates,scaffolding}/` **複製打包**：

- 保持原 markdown 結構不動（H2/H3 / anchor / canonical English heading 全保留）
- 保留所有 placeholder（`{XXX}`）；W1b 不修改 placeholder 內容
- `_conventions.md` 應為基底，**不**預先注入 Prose Language 段（W1b runtime 在寫檔後才注入，per spec §7.1）
- 只複製，不重新撰寫；若有檔在 `templates/` 也在 `scaffolding/` 重複，依 spec §3.3 packaged 來源映射

### 3. 實作 init flow per spec

- §2 Pre-flight 4 檢查（existing init / legacy specs / writable / Node version）
- §3 Q&A Q1-Q6 + Q5a 條件式 custom BCP-47
- §4 Validation；§4.1 prose-language 必填 explicit；reject any/skip/natural-language
- §5 File-list preview + default N graceful abort
- §6 Write actions：existing-file protection；W1 無 --force；placeholder substitution per §6.3
- §7 Post-write：注入 `## Prose Language` 段、results report、next-step hint
- §8 Error handling 8 類

### 4. dependencies 選擇

依 spec §10.3 指引；narrow 為原則：

- Prompts：建議 `prompts` / `inquirer` / `@inquirer/prompts` / `enquirer` 之一（你判斷）
- File ops：Node built-in `fs/promises`
- Path：Node built-in `path`
- BCP-47 validation：建議用 `bcp-47` / `language-tag` / 等小套件，否則用 spec §4.1 regex hint
- CLI args：`mri` / `minimist` / 簡單手寫皆可

避免需要 native compile 的 dep。

### 5. Smoke test

依 spec §10.4 acceptance criteria：

- 建立乾淨 temp dir
- 執行 `node /path/to/bin/dflow.js init`（或 `npm link` 後 `dflow init`）
- 依 §10.4 表格答 Q1-Q6
- 驗證 `dflow/specs/` 結構符合 §10.4 expected structure
- 驗證 `_conventions.md` 含 `## Prose Language` 段且 `{prose-language}` 已替換為 `zh-TW`
- 重跑同 dir 應 abort at pre-flight

可直接在 `scripts/smoke-test.sh` 或 `test/smoke.mjs` 寫成 reproducible script。Smoke test 通過才算 W1b 完成。

## 補充要求

- ESM 或 CJS 自選；package.json `type` 設定一致
- 整體程式碼風格以可讀性為主；不要過度抽象
- 註解可有可無；公開 function 簽名清晰即可
- 錯誤訊息英文（per spec §3.1 CLI 介面語言）
- **不要 commit**（user review 後 commit）
- **不要動 spec / PROPOSAL / 既有 skill files**
- **不要創建非範圍內檔**（例如 docs/ / website/ / GitHub Actions config）

完成後回報：
- 建立檔案清單
- 選用的 dependencies + 版本
- Smoke test 是否 pass + 輸出摘要
- spec 中任何模糊或執行困難處（不重新決策；只 surfacing）
- 給 W2 cascade 階段的提醒（如 npm package 內 templates/ 路徑與 sdd-ddd-*-skill/templates/ 的 sync 機制建議）
````

### W1b 跑完後（給你的 review checklist）

1. `git status` 看新增檔；確認沒動到非範圍檔
2. 抽 read：`package.json`（engines.node、bin、files 欄位）+ `bin/dflow.js` 全文 + `lib/init.js` 主流程
3. 確認 `templates/core/` 與 `templates/webforms/` 結構與 sdd-ddd-*-skill/ 一致
4. 跑 smoke test：`cd /tmp && mkdir test-dflow && cd test-dflow && node {repo}/bin/dflow.js init`
5. 驗證 `dflow/specs/shared/_conventions.md` 內含 `## Prose Language` + `zh-TW`
6. 重跑同 dir 應 abort
7. 若 OK：commit；若有問題：寫 fix prompt 給 Codex revise

W1b commit message 範本：

```
W1b: npm package 雛形實作 (Codex CLI 產出)

依 planning/init-contract-spec.md 翻譯為實際 JS：
- package.json + bin/dflow.js + lib/init.js + templates/{core,webforms}/
- engines.node >=22；deps：{選用的 prompts / cli-args / BCP-47 套件}
- Smoke test：clean temp dir → npx dflow init → 結構符合 spec §10.4

P014 + P015 shared-cut W1 已落地；W2 repo-wide cascade 待開工。

Co-Authored-By: Claude {model} <noreply@anthropic.com>
```

---

## 接下來：W2 outline（W1b 完成後）

W2 = repo-wide cascade。範圍大，預估接近 R8b：

### W2 影響範圍（per P014 影響範圍表 + P015 影響範圍表 整合）

**雙版 skill（4 大塊）**：
- `sdd-ddd-{webforms,core}-skill/SKILL.md`：移除 `/dflow:init-project` slash command；Project Structure tree 改 `dflow/specs/`；template language 規則加入 prose-language 規範
- `sdd-ddd-{webforms,core}-skill/references/init-project-flow.md`：定位改為「CLI internal flow spec / 無 Node.js fallback」；路徑改 `dflow/specs/`
- `sdd-ddd-{webforms,core}-skill/references/{new-feature,modify-existing,new-phase,finish-feature,bug-fix}-flow.md`：**Step 1 mandatory 加讀 `_conventions.md` Prose Language**（per P015 F-07）；路徑改 `dflow/specs/`
- `sdd-ddd-{webforms,core}-skill/references/{drift-verification,git-integration,pr-review-checklist,ddd-modeling-guide}.md`：路徑改 `dflow/specs/`
- `sdd-ddd-{webforms,core}-skill/templates/*.md`：路徑改 `dflow/specs/`
- `sdd-ddd-{webforms,core}-skill/scaffolding/{_conventions,_overview,CLAUDE-md-snippet,Git-principles-*}.md`：路徑改 `dflow/specs/`；`_conventions.md` 加入 `## Prose Language` 段

**Tutorial 搬移 + 改寫**：
- `tutorial/01-greenfield-core/outputs/specs/**` → `outputs/dflow/specs/**`（git mv + 內容路徑同步）
- `tutorial/02-brownfield-webforms/outputs/specs/**` → `outputs/dflow/specs/**`（同上）
- `tutorial/01-greenfield-core/{01-init-project,02-new-feature,03-new-phase,04-modify-existing,05-bug-fix,06-finish-feature}.md`：對話內 `specs/` 引用改 `dflow/specs/`；段 1 重寫為「Alice 在 shell 執行 `npx dflow init`」
- `tutorial/02-brownfield-webforms/{00-setup,02-modify-existing,03-baseline-capture,04-new-feature,05-bug-fix,06-finish-feature}.md`：路徑同步改
- **新增** `tutorial/02-brownfield-webforms/01-init-project.md`：原 brownfield 劇情缺段 1，補 CLI init 入口段
- `tutorial/01-greenfield-core/outputs/CLAUDE.md` + `tutorial/02-brownfield-webforms/outputs/CLAUDE.md`：路徑改

**Root docs**：
- `README.md`：adoption 說明改 npm CLI first；`npx dflow init` 為 V1 預設；baseline path 改 `dflow/specs/`
- `TEMPLATE-COVERAGE.md`：Generated/maintained path 欄位改 `dflow/specs/...`；Project AI guide 維持 root `CLAUDE.md` 例外
- `TEMPLATE-LANGUAGE-GLOSSARY.md`：補 "Prose Language" / "free prose" / "structural language" 等術語繁中對照
- `CHANGELOG.md`：檔尾新增 R9 / P014 + P015 段；不改 R7/R8 既有條目

**檔名含 `_tw` 但在 active runtime / tutorial 目錄內**（per P014 line 147, line 152；non-skip）：
- `sdd-ddd-core-skill/PRACTICE_PLAN_tw.md`：路徑改 `dflow/specs/`
- `tutorial/TUTORIAL_{CORE,WEBFORMS}_tw*.md`：路徑改 `dflow/specs/`

### W2 切割建議

可分 2-3 個 sub-wave：

| Sub-wave | 範圍 | 預估 |
|---|---|---|
| W2a | 雙版 skill 全 cascade（SKILL.md + references + templates + scaffolding）；雙版 _conventions.md 加 Prose Language 段；雙版 5 prose-generating flow 改 mandatory | 中-大（~30 檔） |
| W2b | Tutorial outputs 搬移 + 對話 path refs + 段 1 重寫 + 補 brownfield 段 1 | 大（~30+ 檔） |
| W2c | Root docs（README / TEMPLATE-COVERAGE / GLOSSARY / CHANGELOG R9 段） | 小（~5 檔） |

### W2 prompt 規模預估

- W2a prompt ~250 行（含每檔修改點清單）
- W2b prompt ~200 行（含 git mv 規則 + 段 1 新增規格）
- W2c prompt ~100 行

W2 prompt 撰寫前應先看 W1b 實際產出（templates/ 結構是否影響 W2a scaffolding 引用），微調再派工。

---

## P014 + P015 落地完成後的 closeout

W1 + W2 全部完成後：

1. 兩 PROPOSAL 狀態：`approved` → `implemented`（user 在「評估紀錄」段補一句「2026-XX-XX implemented at commits {W1b SHA}-{W2c SHA}」）
2. CHANGELOG R9 段已包含在 W2c
3. **不需要 V1 ships 動作**：本階段只是 implement P014 + P015；V1 ships 是另一個更大議題（包含 npm publish、license、README 公開版、改名遷移 dev/dist repo 切割等）
4. 後續可開：
   - `planning/post-r8-handoff.md` § 階段 3「Closeout C1」：已決定**跳過**（C1 是繁中版 skill 同步，已棄用）
   - 「Closeout C2 / Tutorial 收尾」：W2b 已順帶處理大部分；剩 `tutorial/README.md` 索引文件可獨立小工作
   - 議題 1（init-project 升級 CLI）：已 P014 拍板 + W1/W2 implement，**closed**
   - § 1.2 PROPOSAL-014 候選議題：實質 = PROPOSAL-015 已拍板 + implement，**closed**
   - 改名遷移（dev + dist repo 切割）：仍是 V1 ships 前的獨立議題；P014 implement 落地後可重新評估

---

## Token-budget-driven 決策（為什麼本 session 在此交接）

- 本 session 已執行：4 個 PROPOSAL workflow milestone（draft P014 / draft P015 / review / revision / approve）+ W1a draft + 多次 spot-check review + 多個 commit message
- 估計 context 約 80-90%（保守估）
- W1b 預期 Codex worker 產出 60-100k token（含 templates/ 副本）；本 session review + 寫 W2 prompt 風險高
- W1a 是天然交接結點（spec 已 approved，後續 implement 機械翻譯）
- 下個 director session（Codex）cold start 讀 `AGENTS.md` + 本檔 + `planning/init-contract-spec.md` + 最新 commit log = baseline 完整
- **主線從 Claude Code 切換到 Codex CLI**：user token 經濟學決定（Claude 餘量低）；Claude 後續只在 cross-model review checkpoint 被召喚

## Director session token 安全 protocol（給下個 director 用）

context 進入安全區後，director 應：

1. 在自然 milestone（commit 後、prompt 派工前）停下
2. 寫 `planning/post-{checkpoint}-handoff.md`，仿本檔結構
3. handoff 內含：
   - 第一句話 cold start 開場
   - 當前進度（最新 commit chain）
   - 下個任務的 Codex prompt（pre-written，user 直接 copy-paste）
   - 後續任務 outline（不寫 prompt 細節，留給下個 director）
   - Cross-model review trigger 提醒
   - 為何此時交接的 token 估算
4. Commit handoff doc
5. End session

下個 director（無論 Claude 或 Codex）cold start 讀 handoff doc + 該檔指出的必讀 baseline = 完整接續。

---

## 需新 session 注意的事

1. **不要重新決策 init 行為**：W1a spec 已 approved；W1b 是翻譯，不是設計
2. **Codex 跑 W1b 後 user 會回報**：你的 review checklist 在本檔上方第 W1b 段「W1b 跑完後」清單；逐項驗證
3. **smoke test 是硬性條件**：W1b 不接受「沒跑 smoke test 但程式碼看起來對」；必須有實際執行 + 結構驗證輸出
4. **W1b commit 後再開 W2**：兩 wave 序列，不要平行（W2 依賴 W1b 的 templates/ 結構穩定）
5. **W2 prompt 寫之前先做小 spike**：grep `specs/` 在 active 範圍（非 *-tw 已刪、planning/、proposals/、reviews/ 排除）看真實命中數，確認 P014 影響範圍表的 72 檔估算
6. **不要碰 *-tw 排除邏輯**：commit `b3d5963` 已刪除目錄；任何 W2 prompt 不需排除 *-tw（不存在）
7. **CHANGELOG R9 段在 W2c 一次寫**：不要在 W2a / W2b 各自 commit 加 CHANGELOG，會造成 cascade 雙改
