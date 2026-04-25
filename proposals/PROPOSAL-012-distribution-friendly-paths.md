## PROPOSAL-012: 目錄與檔名英文化的社群散佈預備調整

**狀態**: `draft`

**提出日期**: 2026-04-25

**優先級**: `高`

**來源**:
- 2026-04-25 重新複習 Dflow skill 時發現：P010 `/dflow:init-project` 產生的專案級 scaffolding 目錄使用 `specs/_共用/`，這對公司內部使用可接受，但與後續社群散佈 / npm 發布方向存在摩擦。
- `reviews/cross-round-notes.md` 已記錄「Dflow 長期散佈策略——npm install + 多 AI 工具相容」議題，其中包含 scaffolding 範本命名國際化與避免中文檔名的方向。
- `planning/public-distribution-backlog.md` 已另行記錄 npm 發布前才處理的內容語言、template localization、package/CLI 等議題。

---

### 問題描述

Dflow 原本偏向公司內部使用，文件與產物路徑可以接受繁體中文語境；但 P010 之後，Dflow 已開始朝「可被新專案採用、可社群散佈」的方向演進：

- README 已新增「How to adopt Dflow in your project」
- `/dflow:init-project` 會在目標專案中建立 baseline `specs/` 結構
- `scaffolding/` 已被定位為專案級起始文件範本
- `reviews/cross-round-notes.md` 已記錄未來可能走 npm / 多 AI 工具散佈

目前仍存在一個 machine-facing 的路徑問題：P010 的 init flow 會將專案級 scaffolding 寫入：

```text
specs/_共用/
```

例如：

```text
specs/_共用/_overview.md
specs/_共用/_conventions.md
specs/_共用/Git-principles-gitflow.md
specs/_共用/Git-principles-trunk.md
specs/_共用/CLAUDE-md-snippet.md
```

這在內部團隊可讀性上沒有問題，但對社群散佈存在幾個風險：

1. **非中文使用者理解成本高**：`_共用` 對不懂中文的使用者不透明，降低採用時的第一印象與可預期性。

2. **CLI / npm / 多工具散佈應降低路徑特殊性**：雖然現代 Git / OS 多數支援 Unicode 路徑，但公開工具的預設產物最好避免語言特定目錄名，降低 shell、文件、issue 回報、CI 腳本中的摩擦。

3. **machine-facing 與 human-facing 混在一起**：目錄 / 檔名是工具契約；文件內容標題是人讀語言。兩者應分開決策。即使產出的文件內容仍可使用繁體中文，路徑與檔名仍應採社群友善的英文預設。

4. **後續 template coverage 會受路徑政策影響**：若接下來要補齊所有 Dflow-managed `.md` 文件的樣板，必須先穩定「這些樣板產生到哪裡」的路徑契約，避免新樣板沿用即將淘汰的 `specs/_共用/`。

重要的是：本問題不等於「現在要把所有文件內容英文化」。目前 skill templates 中仍有許多繁體中文標題（例如 `目標與範圍`、`行為規格`、`實作任務`），這對內部團隊理解有價值。文件內容語言策略應延後到 npm / public distribution 前再整體決策，不應和本 proposal 混在一起。

---

### 提議的解法

#### 1. 定義 Dflow 路徑 / 檔名政策

新增一條明確原則：

> Dflow 的 machine-facing paths and filenames default to English. Human-facing document content may remain localized and can later be selected by language.

具體分層如下：

| 層級 | 預設策略 | 說明 |
|---|---|---|
| Skill repo 內部目錄 / 檔名 | 英文 | 例如 `templates/`、`scaffolding/`、`references/`、`Git-principles-trunk.md` |
| `/dflow:init-project` 產生的系統目錄 / 檔名 | 英文 | 例如 `specs/shared/`、`specs/domain/glossary.md` |
| 文件內容標題與段落 | 暫不強制英文化 | 可保留繁中，未來 npm 前再決定是否提供 en / zh-TW template variants |
| 使用者提供的 feature slug | 跟隨討論語言 | 沿用 P009 決議 8：中文討論可產生中文 slug；這是使用者語境，不是 Dflow 固定系統路徑 |

#### 2. 將 generated shared docs destination 改為 `specs/shared/`

把 P010 產生的專案級 scaffolding 目的地從：

```text
specs/_共用/
```

改為：

```text
specs/shared/
```

對應檔案：

```text
specs/shared/_overview.md
specs/shared/_conventions.md
specs/shared/Git-principles-gitflow.md
specs/shared/Git-principles-trunk.md
specs/shared/CLAUDE-md-snippet.md
```

選擇 `shared/` 而非 `_shared/` 的理由：

- `shared/` 對非中文使用者直覺
- 不需要用底線隱藏或排序；它是正常的專案級 shared docs 目錄
- 與 `features/`、`domain/`、`architecture/`、`migration/` 這些既有英文目錄風格一致

#### 3. 更新 `/dflow:init-project` 的檔案預覽與寫入規則

兩版 `references/init-project-flow.md` 需同步修改：

- Step 2 Q5 的可選 starter files 路徑改為 `specs/shared/...`
- Step 3 file-list preview 中 optional files destination 改為 `specs/shared/...`
- Step 4 `CLAUDE.md` special handling：
  - repo root `CLAUDE.md` 不存在：仍可建立 root `CLAUDE.md`
  - repo root `CLAUDE.md` 已存在：將 snippet 寫到 `specs/shared/CLAUDE-md-snippet.md`
- Step 5 results report 中所有 `_共用` 路徑改為 `shared`

#### 4. 更新 skill 與 scaffolding 內的引用

兩版需同步更新：

- `SKILL.md` 的 `Templates & Scaffolding` 段：
  - `scaffolding/` 目的地由 `specs/_共用/` 改為 `specs/shared/`
- `scaffolding/CLAUDE-md-snippet.md`：
  - 所有指向 `_overview.md`、`_conventions.md`、`Git-principles-*.md` 的路徑改為 `specs/shared/...`
- `templates/CLAUDE.md`：
  - 若其專案結構範例列出 `_overview.md` / `_conventions.md`，需改為 `shared/` 下的路徑
- README 若提到 scaffolding 產物位置，需同步改為 `specs/shared/`

#### 5. 明確保留文件內容語言為 out of scope

本 proposal 不處理以下事項：

- 不將 `phase-spec.md` / `_index.md` / `lightweight-spec.md` 中的繁體中文標題改成英文
- 不建立 `templates/en/` 或 `templates/zh-TW/`
- 不重新翻譯 `scaffolding/` 內容
- 不實作 npm package 或 CLI
- 不將 Dflow 文件根目錄從 `specs/` 改為 `dflow/` / `.dflow/` / `docs/dflow/` 等其他名稱；根目錄命名策略已記錄於 `planning/public-distribution-backlog.md`，應留到 npm / CLI 發布前與相容策略一起決策
- 不處理繁中版 skill 目錄同步

這些事項已集中記錄於：

```text
planning/public-distribution-backlog.md
```

等接近 npm / public distribution 時再整體處理。

#### 6. 與 P009 slug 語言規則保持相容

本 proposal 只處理 Dflow 固定產生的 system paths / filenames，不推翻 P009 的 slug 規則。

仍保留：

```text
specs/features/active/SPEC-20260421-001-報表調整/
feature/SPEC-20260421-001-報表調整
```

這類由使用者討論語言決定的 slug。理由是：

- `SPEC-ID` 與結構詞仍是英文 / ASCII
- slug 是 feature 名稱，不是 Dflow 固定系統目錄
- P009 已有 Obts 實戰背書，中文 slug 在團隊現況可運作

若未來公開散佈需要提供「強制 ASCII slug」選項，可另案處理，不納入本 proposal。

---

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `sdd-ddd-webforms-skill/SKILL.md` | 修改 | `Templates & Scaffolding` 段目的地由 `specs/_共用/` 改為 `specs/shared/`；如 Project Structure 範例列出 `_overview.md` / `_conventions.md`，同步調整 |
| `sdd-ddd-core-skill/SKILL.md` | 修改 | 同 WebForms |
| `sdd-ddd-webforms-skill/references/init-project-flow.md` | 修改 | Q5、file-list preview、write rules、results report 中的 `specs/_共用/` 改為 `specs/shared/` |
| `sdd-ddd-core-skill/references/init-project-flow.md` | 修改 | 同 WebForms |
| `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md` | 修改 | 專案內引用路徑改為 `specs/shared/...` |
| `sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md` | 修改 | 同 WebForms |
| `sdd-ddd-webforms-skill/templates/CLAUDE.md` | 修改 | 專案結構範例如列出 `_overview.md` / `_conventions.md`，改放 `specs/shared/` |
| `sdd-ddd-core-skill/templates/CLAUDE.md` | 修改 | 同 WebForms |
| `README.md` | 修改 | 若 adoption / scaffolding 說明提及 `specs/_共用/`，改為 `specs/shared/`；目前 README 可能只需確認無顯式路徑 |
| `CHANGELOG.md` | 修改 | 實作時新增一段，說明 `specs/_共用/` → `specs/shared/` 的路徑政策變更 |
| `planning/public-distribution-backlog.md` | 已新增 / 可能補充 | 保留 npm 前處理的內容語言與 localization 議題；本 proposal 只引用，不實作其中項目 |

### 不納入影響範圍

| 檔案 / 範圍 | 理由 |
|---|---|
| `sdd-ddd-*-skill-tw/` | 目前先不處理繁中翻譯版；待 Closeout / localization 階段 |
| `tutorial/` | Tutorial 重建屬 Closeout C2 或後續文件工作 |
| `templates/_index.md`、`templates/phase-spec.md`、`templates/lightweight-spec.md` 的中文 heading | 文件內容語言延後到 npm 前整體決策 |
| npm / package files | 公開散佈工程另案處理 |
| Dflow 文件根目錄命名（`specs/` vs `dflow/` / `.dflow/` / `docs/dflow/`） | 牽涉所有 flow、template、verify、finish-feature 與既有專案相容；留到 npm / CLI 前整體決策 |

---

### 預估工作量

`小`

主要是路徑文字替換與雙版一致性檢查，實作風險低；但需仔細 grep，避免殘留 `specs/_共用/` 或錯誤地改到歷史紀錄語境。

---

### 關聯的 Proposal

| Proposal / 文件 | 關係 | 說明 |
|---|---|---|
| PROPOSAL-009 | 相容 | 保留 P009 的 feature slug 跟隨討論語言規則；本 proposal 只處理固定 system paths |
| PROPOSAL-010 | 修正 / 延伸 | P010 引入 `/dflow:init-project` 與 scaffolding，本 proposal 修正其 generated shared docs destination |
| PROPOSAL-011 | 延伸同一方向 | P011 已將 Git Flow 從 skill 本體解耦，朝社群散佈友善前進；本 proposal 對路徑 / 檔名採同一方向 |
| `reviews/cross-round-notes.md` 議題 3 | 呼應 | 長期 npm / 多 AI 工具散佈策略已記錄，本 proposal 只處理其中的路徑與檔名預備調整 |
| `planning/public-distribution-backlog.md` | 前置 / 邊界 | npm 前才處理的內容語言、template localization、package/CLI 等議題集中放在此 backlog，不納入本 proposal |
| 後續「Dflow 文件根目錄命名策略」 | 延後 | 目前仍保留 `specs/` 作為文件根目錄；若 npm 前決定改為 `dflow/` 或其他根目錄，需另案處理 migration / compatibility |
| 後續「System Document Template Coverage」proposal | 前置 | 先穩定 generated path，再補齊所有 Dflow-managed `.md` 的樣板覆蓋 |

---

### 評估紀錄（整合評估時填寫）

**評估日期**: {YYYY-MM-DD}

**結論**: {approved / rejected / 需要修改}

**理由**: {為什麼做這個決定}

**如果 rejected**: {不做的原因，留作日後參考}
