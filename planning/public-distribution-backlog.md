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
