# Public Distribution Backlog

> 本文件記錄 Dflow 在公開散佈 / npm 發布前需要處理的議題。
> 這些事項刻意不塞進目前的 proposal，避免把當前設計修正擴大成發布工程。

---

## 定位

Dflow 原本偏向公司內部使用，後續可能轉向社群散佈與 npm 安裝。

因此需要區分：

- **Machine-facing / tooling-facing**：目錄、檔名、套件名稱、CLI 參數，預設應採英文，降低跨工具與非中文使用者摩擦。
- **Human-facing document content**：產出的文件標題與說明，可依團隊語言選擇；目前繁體中文內容對內部團隊仍有價值，不急著在本階段英文化。
- **AI-facing skill content**：英文版 skill 仍以英文為主，確保 AI runtime 讀取穩定。

---

## 發布前待處理事項

### 1. 文件內容語言策略

- 決定公開版預設產出的文件標題是英文、繁體中文，或依 `/dflow:init-project` 的語言選項決定。
- 評估是否需要雙語 template set：
  - `templates/en/`
  - `templates/zh-TW/`
  - `scaffolding/en/`
  - `scaffolding/zh-TW/`
- 目前 skill template 內仍有許多繁體中文標題，例如 `目標與範圍`、`行為規格`、`實作任務`。這在內部使用可接受；公開發布前需重新評估預設值。

### 2. npm / CLI 散佈策略

- 決定 npm package name。
- 決定 npm CLI 是否取代或補充 `/dflow:init-project` 的 scaffolding 選項。
- 評估多 AI 工具相容：
  - Codex
  - Claude Code
  - Gemini
  - GitHub Copilot
- 降低對單一 AI 工具命名與約定的依賴：
  - 評估 `CLAUDE.md` / `CLAUDE-md-snippet.md` 是否需要抽象為 tool-neutral 的協作指引檔，或在 npm CLI 中依目標工具產生不同檔名。
  - 確認 Dflow 公開版文件不讓使用者誤以為只能搭配 Claude Code 使用。
- 決定 skill 安裝流程是否仍依賴各 AI 工具自己的 skill/plugin 機制，或由 npm CLI 統一處理。

### 3. 公開版文件

- README 的公開安裝說明。
- License。
- Contribution guidelines。
- Versioning / release policy。
- CHANGELOG 對公開使用者的整理方式。

### 4. Template Localization

- 決定 template localization 是否在 npm 發布前完成，或先發布英文路徑 + 繁中內容的 internal-friendly 版本。
- 若支援多語 template，需定義 fallback：
  - 使用者選英文但某 template 尚未翻譯時怎麼辦？
  - 使用者選繁中但公開版預設為英文時怎麼辦？

### 5. Legacy / Internal Compatibility

- 若公開版預設路徑改為英文，例如 `specs/shared/`，需決定是否支援既有內部專案的 `specs/_共用/`。
- 若支援 legacy path，需定義：
  - 是否只讀不寫？
  - 是否提供 migration guide？
  - 是否讓 `/dflow:init-project` 偵測舊路徑並提示？

---

## 暫不處理

以下事項不納入目前即將起草的 proposal：

- 將所有文件內容標題全面英文化。
- 建立 npm package。
- 設計完整 CLI。
- 製作公開官網 / 文件站。
- 重建繁中版 skill。

目前優先處理的是：先穩定 machine-facing 的目錄與檔名策略，再補齊 Dflow-managed `.md` 文件的樣板覆蓋。
