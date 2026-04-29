# Public Distribution Backlog

> 本文件記錄 Dflow 在公開散佈 / npm 發布前需要處理的議題。
> 這些事項刻意不塞進目前的 proposal，避免把當前設計修正擴大成發布工程。

---

## 定位

Dflow 原本偏向公司內部使用，後續可能轉向社群散佈與 npm 安裝。

因此需要區分：

- **Machine-facing / tooling-facing**：目錄、檔名、套件名稱、CLI 參數，預設應採英文，降低跨工具與非中文使用者摩擦。
- **Human-authored document content**：使用者在文件中填寫的需求、規則、決策、任務內容，可依團隊語言撰寫；template 的結構標題與欄位名稱則應以英文作為 canonical form，避免 AI 定位不穩。
- **AI-facing skill content**：英文版 skill 仍以英文為主，確保 AI runtime 讀取穩定。

---

## 發布前待處理事項

### 1. 文件內容語言策略

- 採單一 canonical English template set：template 標題、欄位、placeholder、section anchor 預設使用英文。
- 使用者填入的內容可依專案語言撰寫，例如繁體中文需求、業務規則、決策原因、任務描述。
- 不建立 `templates/en/` / `templates/zh-TW/` 平行目錄；改以 `TEMPLATE-LANGUAGE-GLOSSARY.md` 提供英文 template 術語的繁中對照。
- 仍需在公開文件中說明：Dflow 文件結構是英文，但內容可以使用團隊語言。
- **註**：第 1-2 條的「結構性語言策略」與「Template Language Glossary」由 PROPOSAL-013 接管處理，於 P013 implemented 後本節 partially fulfilled；剩餘 content 語言策略（使用者填入內容可依語言撰寫的原則溝通）仍延後到本 backlog 處理。

### 1.1 候選：anchor-enabled localized heading（P013 後）

PROPOSAL-013 引入 `<!-- dflow:section X -->` anchor 機制後，AI 段落定位不再依賴 heading 文字。這啟用一個新的演進方向：

- anchor id 維持 canonical English（programmatic identifier）
- heading 文字可依專案語言本地化（人讀友善），例如同一個 anchor `lightweight-changes` 可對應 `## Lightweight Changes` 或 `## 變更紀錄`
- 不需維護雙語 template set；單一 template 內 heading 在 `/dflow:init-project` 或 npm install 時依語言參數產生

**Why（設計動機）**：anchor 機制的長期 ROI 不只是 heading 漂移的 fail-safe，更是「結構穩定 vs 人讀體驗」二者兼得的關鍵基礎契約。當 anchor stable 後，heading 漂移成本歸零 → 本地化 heading 不再需要折衷 AI 定位穩定性。

**實證條件（npm 散佈後評估）**：

- anchor 機制在實際使用中穩定（無大量 anchor versioning 問題）
- 使用者反饋顯示 heading 中文化或其他語言有實質需求（不是設計者單方猜測）
- 維護成本（一份 template + heading 變數）低於「統一英文 heading 多一條 glossary」方案

**為何不在 P013 立刻 implement**：P013 階段先建立 anchor 機制 + canonical English heading 統一基礎；本地化是 advanced use case，需先有 anchor 機制實證 + 真實使用者偏好，再進入設計。屬本 backlog 範疇。

### 1.2 候選：templates prose language 規範補完（P013 後）

R8b PROPOSAL-013 涵蓋「canonical English structural language」（H2/H3 headings、table headers、固定 labels、anchors），但**未涵蓋 prose 內容語言規範**（如 Implementation Tasks 列項描述、Integration Summary、Root Cause / Fix Approach、Tech Debt 段內 prose）。

**證據（2026-04-29 Tutorial 劇情 1 觀察）**：派 Codex CLI 跑劇情 1 (Core greenfield) 6 段時，產生「混語言 prose」現象：
- Codex 看到 templates 結構是英文 → default 把自由 prose 也寫英文
- 對話中 Alice 已用中文說過的內容才會中文化
- 結果：同檔內 H2/H3 英文（per R8b 設計）+ subagent 自寫 prose 英文 + Alice 對話帶來的內容中文，三層混排
- 最終以單一 fix commit (`82c5b53`) 把 Codex 自寫 prose 統一翻為繁中收斂

**設計議題**：是否在 R8b canonical English structural language 之上加一條 prose-language 規範？

候選做法：
- (A) `<!-- dflow:prose-language: any | en | xx-XX -->` directive：在 templates 開頭明示，AI 看到後依此語言寫 prose
- (B) `/dflow:init-project` 增加 `prose-language` 問題：寫進 `_overview.md` 或 `_conventions.md`，後續 AI 讀此設定
- (C) `_conventions.md` 新增「Prose Language」段：團隊自訂，AI 在建議 spec 時對齊
- (D) 不做規範，依賴 prompt engineering（保持現狀；team 在實際 prompt AI 時明示）

**Why（設計動機）**：
- 國際團隊（多語言）想要「同一份 templates 結構英文不變，prose 用團隊母語」是真實需求
- 沒明示時 AI 行為不可預測（混語言 = reviewer 認知負擔）
- 與 § 1.1 anchor-enabled localized heading 互補：1.1 解決 heading 本地化，1.2 解決 prose 本地化

**實證條件（npm 散佈後評估）**：
- 多團隊使用後反饋 prose 語言混亂問題的實際比例
- 用戶是否主動要求 prose 語言指示
- (A)/(B)/(C) 三種候選在實作 / 維護成本對比

**為何不在 R8b 立刻 implement**：R8b 已落地 4 項設計決策（canonical English / anchor 機制 / TEMPLATE-COVERAGE / GLOSSARY），再加 prose-language 規範會讓 R8b 過大；且劇情 1 觀察是發現於 Tutorial 試跑，需更多實證才能評估候選做法。屬本 backlog 範疇，可能起 PROPOSAL-014。

**與 § 1.1 的順序**：1.1 (heading localization) 與 1.2 (prose language) 可同 PROPOSAL 處理，也可獨立。建議 V1 ships 前一併評估。

### 2. npm / CLI 散佈策略

- 決定 npm package name。
- npm 安裝目前定位為部署 / 安裝 Dflow 到專案或 AI 工具環境；不預設取代 runtime 內的 `/dflow:` workflow 指令。
- 若未來提供 npm CLI，優先視為 installer / scaffolding helper，而不是日常開發流程的主要操作介面。
- 評估多 AI 工具相容：
  - Codex
  - Claude Code
  - Gemini
  - GitHub Copilot
- 降低對單一 AI 工具命名與約定的依賴：
  - 評估 `CLAUDE.md` / `CLAUDE-md-snippet.md` 是否需要抽象為 tool-neutral 的協作指引檔，或在安裝 / 初始化時依目標工具產生不同檔名。
  - 確認 Dflow 公開版文件不讓使用者誤以為只能搭配 Claude Code 使用。
  - 評估同一專案同時使用多種 AI CLI 時的協作方式：Dflow-managed docs 應維持單一 source of truth；各工具專用檔案只保留最小啟動規則與指向 Dflow 文件的索引，避免多份規範互相漂移。
- 決定 skill 安裝流程是否仍依賴各 AI 工具自己的 skill/plugin 機制，或由 npm CLI 統一處理。

#### 2.1 候選議題：`/dflow:init-project` 從 skill 上移到 CLI（V1 ships 前需拍板，可能起草 PROPOSAL-014）

**洞察來源**：2026-04-28 使用者於 Post-R8 規劃時提出 — 既然走 npm 散佈路線，init 是 one-time bootstrap、不需 AI 判斷邏輯，比較適合作為 CLI 動作而非 skill 內 slash command。

**動機**：
- init 是純機械流程（讀 inventory → 問 5 題 → 寫檔），AI 引導價值低於日常 workflow（new-feature / modify-existing 等）
- npm CLI 更符合工程師直覺：`dflow init` 比「找 AI tool → 觸發 skill → /dflow:init-project」流暢
- 多 AI 工具相容：CLI 不綁特定 AI 工具，純 npm/node.js 邏輯
- 與 dist repo 結構強相關：dist repo 若是 npm package，init 自然是 CLI first-run

**三種可能架構**：

| 模式 | CLI 命令 | 工程師體驗 |
|---|---|---|
| Global install | `npm install -g dflow` + `dflow init` | 先裝後用，類似 git / docker CLI |
| One-shot npx | `npx dflow init` | 不用全域安裝，run-time 抓 package |
| npm initializer | `npm create dflow` | 跟隨 npm initializer 慣例（如 `npm create vite`）|

**對既有設計的影響**：
- P010 `/dflow:init-project` 內容不會浪費 — CLI 內部仍可呼叫同一 init flow 邏輯（從 Q1-Q5 到 Step 4-5），只是入口點變動
- skill 內 `/dflow:init-project` 可保留（fallback / 教學用），或完全移除（避免雙入口混淆）
- Tutorial 劇情 1 段 1 可能需要重寫（從「Alice 觸發 /dflow:init-project」改為「Alice 在 shell 跑 dflow init」）—— 但其他段（new-feature / modify-existing 等）不受影響

**決策時點**：V1 ships 前，與 dist repo 結構決策一起拍板。建議至少評估「Global install vs npx」二擇一（`npm create dflow` 偏 scaffolding 一次性，可能不適合 Dflow 的「持續維護 specs/」性質）。

**影響範圍如果改**：
- skill `references/init-project-flow.md` 雙版：是否保留 / 改寫為「CLI internal flow」
- skill `SKILL.md` 雙版：移除 `/dflow:init-project` slash command
- 新增 npm package（package.json + bin/dflow.js + lib/init.js 等）
- README 安裝說明全部改寫
- 可能影響 `planning/project-review-brief.md` 描述

### 3. 公開版文件

- README 的公開安裝說明。
- License。
- Contribution guidelines。
- Versioning / release policy。
- CHANGELOG 對公開使用者的整理方式。

### 4. Template Language Glossary

- 維護一份 template 英文術語對照表，讓非英文母語使用者理解 template headings / field labels。
- 對照表只作為人讀輔助，不作為 runtime template source。
- 若未來真的需要 localized headings，再另案評估，但預設不維護雙語 template set。

### 5. Legacy / Internal Compatibility

- 若公開版預設路徑改為英文，例如 `specs/shared/`，需決定是否支援既有內部專案的 `specs/_共用/`。
- 若支援 legacy path，需定義：
  - 是否只讀不寫？
  - 是否提供 migration guide？
  - 是否讓 `/dflow:init-project` 偵測舊路徑並提示？

### 6. Dflow 文件根目錄命名策略

- 評估公開版是否仍以 `specs/` 作為預設文件根目錄，或改為較不易和既有專案衝突的 `dflow/`、`.dflow/`、`docs/dflow/` 等命名。
- 保留 `specs/` 的優點：
  - 和 SDD / OpenSpec 語境接近，語意直覺。
  - 目前 skill、proposal、review、template 已大量使用，短期改動成本低。
- 改用 `dflow/` 的優點：
  - 對既有專案較不容易撞名。
  - 公開散佈時品牌與工具邊界更清楚。
  - 未來 npm CLI 可更容易判斷哪些文件由 Dflow 管理。
- npm 前需決定：
  - 公開版預設根目錄是否改名。
  - 是否支援 `--root specs` / `--root dflow` 類似選項。
  - 若改名，既有內部 `specs/` 專案是否提供 migration guide 或相容讀取。
  - `/dflow:init-project` 是否應偵測既有 `specs/`，若看起來不是 Dflow 產物，提示改用 `dflow/`。

### 7. Dflow Version Upgrade / Migration Strategy

- Dflow 實際被專案使用後，skill / templates / scaffolding 會持續改版；未來需要定義既有專案如何升級。
- 目前不要求自動 migration；若既有專案採用 Dflow 前身或舊版 Dflow，可由該專案與 AI 個別討論處理。
- npm / public distribution 前需評估：
  - 是否在 generated docs 中記錄 Dflow version。
  - 是否提供 migration guide，例如 `specs/_共用/` → `specs/shared/`、中文 template heading → canonical English heading。
  - 是否提供 read-only legacy support 或偵測提示。
  - 是否需要 `dflow doctor` / `dflow migrate` 類工具，或仍交由 AI session guided migration。

---

## 暫不處理

以下事項不納入目前即將起草的 proposal：

- 建立雙語 template 目錄或讓同一份 template 依語言切換標題。
- 建立 npm package。
- 設計完整 CLI。
- 將 Dflow 文件根目錄從 `specs/` 全面改為 `dflow/` 或其他名稱。
- 製作公開官網 / 文件站。
- 重建繁中版 skill。

目前優先處理的是：先穩定 machine-facing 的目錄與檔名策略，再補齊 Dflow-managed `.md` 文件的樣板覆蓋。
