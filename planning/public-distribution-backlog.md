# Public Distribution Backlog

> 本文件記錄 Dflow 在公開散佈 / npm 發布前後需要處理的議題。
> 這些事項刻意不塞進目前的 proposal，避免把當前設計修正擴大成發布工程。

---

## 定位

Dflow 原本偏向公司內部使用，後續可能轉向社群散佈與 npm 安裝。

因此需要區分：

- **Machine-facing / tooling-facing**：目錄、檔名、套件名稱、CLI 參數，預設應採英文，降低跨工具與非中文使用者摩擦。
- **Human-authored document content**：使用者在文件中填寫的需求、規則、決策、任務內容，可依團隊語言撰寫；template 的結構標題與欄位名稱則應以英文作為 canonical form，避免 AI 定位不穩。
- **AI-facing skill content**：英文版 skill 仍以英文為主，確保 AI runtime 讀取穩定。

---

## 發布與後續待處理事項

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
- 不需維護雙語 template set；單一 template 內 heading 在 future CLI / npm install 時依語言參數產生

**Why（設計動機）**：anchor 機制的長期 ROI 不只是 heading 漂移的 fail-safe，更是「結構穩定 vs 人讀體驗」二者兼得的關鍵基礎契約。當 anchor stable 後，heading 漂移成本歸零 → 本地化 heading 不再需要折衷 AI 定位穩定性。

**實證條件（npm 散佈後評估）**：

- anchor 機制在實際使用中穩定（無大量 anchor versioning 問題）
- 使用者反饋顯示 heading 中文化或其他語言有實質需求（不是設計者單方猜測）
- 維護成本（一份 template + heading 變數）低於「統一英文 heading 多一條 glossary」方案

**為何不在 P013 立刻 implement**：P013 階段先建立 anchor 機制 + canonical English heading 統一基礎；本地化是 advanced use case，需先有 anchor 機制實證 + 真實使用者偏好，再進入設計。屬本 backlog 範疇。

### 1.2 已處理：templates prose language 規範補完（P015）

R8b PROPOSAL-013 涵蓋 canonical English structural language，但未涵蓋自由 prose 語言。此缺口已由 PROPOSAL-015 在 R9 shared-cut 中處理：

- `npx dflow-sdd-ddd init` 必填 explicit project prose language。
- CLI 將設定寫入 `dflow/specs/shared/_conventions.md` 的 `## Prose Language`。
- 五條 prose-generating flow 必須在 Step 1 讀取該 convention。
- Templates 保持 structural English；free prose 依 project prose language。

後續仍可評估 §1.1 的 localized heading，但 prose-language convention 不再是 open backlog item。

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

#### 2.1 已處理：init 從 skill slash command 上移到 CLI（P014）

此候選已由 PROPOSAL-014 在 R9 shared-cut 中拍板並實作：

- V1 預設入口：`npx dflow-sdd-ddd init`。
- `npm install -g dflow-sdd-ddd` + `dflow init` 只作為進階用法。
- `npm create dflow` 不在 V1 實作範圍。
- skill 內 `/dflow:init-project` runtime slash command 已移除，避免雙入口混淆。
- `references/init-project-flow.md` 保留為 CLI internal flow spec / manual fallback reference。
- Tutorial init 段已改為 shell 執行 `npx dflow-sdd-ddd init`。

Package name availability 已於 Post-R9 publish prep 確認：

- `dflow` 已被 npm 上既有 package 使用，不可作為本專案 unscoped package name。
- V1 package name 採 `dflow-sdd-ddd`。
- `package.json` 同時提供 `dflow` 與 `dflow-sdd-ddd` 兩個 bin alias；`npx dflow-sdd-ddd init` 是 public default，global install 後可執行 `dflow init`。

Initial public publish 已於 2026-05-01 完成：`dflow-sdd-ddd@0.1.0` 可由 npm registry 透過 `npx dflow-sdd-ddd` 安裝執行。

仍待處理的是 versioning / release policy、release automation，以及多 AI 工具安裝說明。

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

- R9 / P014 已採 V1 clean cut：新 baseline 是 `dflow/specs/`，CLI 只 warning legacy root `specs/`，不提供 dual-read、不自動搬移、不改寫。
- 若未來真有公開使用者或內部舊專案需要升級，再另案定義：
  - 是否只讀不寫？
  - 是否提供 migration guide？
  - 是否讓 `dflow init` 偵測舊路徑並提示？

### 6. Dflow 文件根目錄命名策略（V1 已決）

- PROPOSAL-014 已決定 V1 新專案 default：`dflow/specs/`。
- `dflow/` 是工具 namespace；`specs/` 是 SDD document namespace。
- V1 不提供 `--root specs` / `--root dflow` 類似選項。
- Root-level `specs/` 只做 legacy / other-tool warning，不視為 Dflow runtime path。
- 後續仍可評估 `.dflow/`、`docs/dflow/` 或 configurable root，但那會是 V2 / migration policy 議題。

### 7. Dflow Version Upgrade / Migration Strategy

- Dflow 實際被專案使用後，skill / templates / scaffolding 會持續改版；未來需要定義既有專案如何升級。
- 目前不要求自動 migration；若既有專案採用 Dflow 前身或舊版 Dflow，可由該專案與 AI 個別討論處理。
- npm / public distribution 前需評估：
  - 是否在 generated docs 中記錄 Dflow version。
  - 是否提供 migration guide，例如 root `specs/` → `dflow/specs/`、`specs/_共用/` → `dflow/specs/shared/`、中文 template heading → canonical English heading。
  - 是否提供 read-only legacy support 或偵測提示。
  - 是否需要 `dflow doctor` / `dflow migrate` 類工具，或仍交由 AI session guided migration。

---

## 暫不處理

以下事項不納入 R9 / P014+P015 已完成的 V1 init cut：

- 建立雙語 template 目錄或讓同一份 template 依語言切換標題。
- release automation / versioning policy。
- 設計完整 CLI surface（V1 只有 `init`、`--help`、`--version`）。
- legacy root `specs/` migration tool / dual-read / 自動搬移。
- 製作公開官網 / 文件站。
- 重建繁中版 skill。

目前已穩定 V1 machine-facing baseline：`npx dflow-sdd-ddd init` + `dflow/specs/` + canonical English structure + explicit project prose language。
