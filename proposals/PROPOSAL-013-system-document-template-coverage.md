## PROPOSAL-013: 系統文件樣板覆蓋與 Template Coverage Matrix

**狀態**: `draft`

**提出日期**: 2026-04-25

**優先級**: `高`

**來源**:
- 2026-04-25 重新複習 Dflow skill 時發現：多份 Dflow 會建立、初始化或長期維護的 `.md` 系統文件沒有對應樣板，導致 AI 只能依散落在 `SKILL.md` / flow reference 裡的描述臨場產生格式。
- `reviews/cross-round-notes.md` 已記錄「`lightweight-spec.md` 缺 OpenSpec `tasks.md` 對應段」議題，與本 proposal 的 template completeness 同屬一類問題。
- PROPOSAL-012 先處理 machine-facing path policy（`specs/_共用/` → `specs/shared/`）；本 proposal 接續處理「所有 Dflow-managed `.md` 都應有可追溯樣板」。

---

### 問題描述

Dflow 的 SDD/DDD 流程會產生兩類文件：

1. **Workflow artifacts**：feature / phase / bug fix 過程中產生的文件，例如 `_index.md`、`phase-spec.md`、`lightweight-spec.md`。
2. **System documents / living docs**：專案長期維護的系統文件，例如 `glossary.md`、`models.md`、`rules.md`、`behavior.md`、`tech-debt.md`。

目前第一類大多已有 `templates/` 樣板，但第二類覆蓋不完整：

| 系統文件 | WebForms | Core | 現況問題 |
|---|---:|---:|---|
| `specs/domain/glossary.md` | init 會建立；workflow 會維護 | init 會建立；workflow 會維護 | 無樣板 |
| `specs/domain/{context}/models.md` | workflow 會建立 / 維護 | workflow 會建立 / 維護 | 無樣板 |
| `specs/domain/{context}/rules.md` | workflow 會建立 / 維護 | workflow 會建立 / 維護 | 無樣板 |
| `specs/domain/{context}/events.md` | 非主軸 | Core workflow 會維護 | Core 無樣板 |
| `specs/domain/context-map.md` | optional / emergent | init mandatory；workflow 會維護 | Core 無樣板；WebForms 若自然建立也無樣板 |
| `specs/migration/tech-debt.md` | init mandatory；workflow 會維護 | 不適用 | WebForms 無樣板 |
| `specs/architecture/tech-debt.md` | 不適用 | init mandatory；workflow 會維護 | Core 無樣板 |
| `specs/architecture/decisions/README.md` | 不適用 | init mandatory | Core 無 scaffolding source |

這會造成幾個問題：

1. **格式漂移**：不同 session / 不同模型建立 `rules.md` 或 `models.md` 時，欄位與章節可能不一致。

2. **流程描述與實作來源斷裂**：`init-project-flow.md` 明確說會建立 `glossary.md` / `tech-debt.md` / Core `context-map.md` / ADR README，但沒有說「從哪份 template 寫出」。

3. **rules.md ↔ behavior.md 的 A+C 結構缺一半樣板**：`behavior.md` 有 template，但 `rules.md` 沒有；這會影響 `/dflow:verify` 依賴的 BR-ID / anchor 格式穩定性。

4. **兩版 skill 的共通樣板缺少對照契約**：WebForms / Core 物理上各自維護 template 是合理的，但目前沒有一份 matrix 說明哪些樣板應保持同章節、哪些差異是刻意的。

5. **T2 lightweight-spec 缺 task checklist**：T1 `phase-spec.md` 有 `實作任務`；T2 `lightweight-spec.md` 雖然較輕，但仍可能需要修改程式碼、測試與文件。完全沒有 task checklist 讓追蹤性不一致。

6. **template 結構語言混用會降低 AI 定位穩定性**：英文 skill instructions 可能引用 `Implementation Tasks`、`Business Rules`、`Behavior Scenarios` 等概念，但 template 內若使用中文標題，AI 必須靠語意推測對應段落。短期通常可運作，但長期跨模型、跨 session、公開散佈時容易造成誤判。

根本問題不是「少幾個檔案」，而是缺少一條明確規則：

> Any persistent `.md` file that Dflow creates, initializes, or requires AI to maintain should have a corresponding source template.

---

### 提議的解法

#### 1. 定義 Dflow-managed `.md` 樣板覆蓋原則

新增原則：

> Dflow-managed `.md` files must be backed by templates. If Dflow creates, initializes, or instructs AI to maintain a persistent Markdown file, the expected structure must live in `templates/` or `scaffolding/`.

新增 template 語言原則：

> Dflow templates and scaffolding use English as the canonical structural language. Generated documents keep the English section headings, field labels, anchors, and placeholder names from the template; developer-authored content inside those sections may follow the user's project language.

理由：

- AI-facing skill instructions 以英文撰寫，template 結構也用英文，可降低段落定位錯誤。
- 公開散佈時不需要維護 `templates/en/` 與 `templates/zh-TW/` 兩套平行樣板。
- 團隊仍可用繁體中文撰寫實際需求、規則、決策、任務內容；只有結構標題與欄位名稱維持英文。
- 使用者需要理解的英文 template 術語數量有限，可用一份對照表補足。
- `scaffolding/` 也納入此原則；若 `_overview.md` / `_conventions.md` 已是英文，主要只需檢查少量殘留中文。`Git-principles-*.md`、`CLAUDE-md-snippet.md` 若含結構性中文詞彙，應改為英文並把重要詞彙加入 `TEMPLATE-LANGUAGE-GLOSSARY.md`。
- 判斷標準以「會被 AI 或開發者當作固定結構 / 欄位 / 狀態 / 類型理解的詞」為主；偶發的自然語言文字若只是填寫範例且不影響流程定位，可由實作者判斷是否需要納入 glossary。

分類如下：

| 類型 | 放置位置 | 定義 |
|---|---|---|
| Workflow / living document templates | `templates/` | workflow 期間會建立或長期維護的文件，例如 `glossary.md`、`rules.md`、`models.md`、`behavior.md`、`tech-debt.md` |
| Project-level scaffolding | `scaffolding/` | `/dflow:init-project` 可選建立、寫入後主要由專案持有的治理文件，例如 `_overview.md`、`_conventions.md`、`Git-principles-*.md` |
| Directory guide / README scaffolding | `scaffolding/` | init 階段建立、用於說明目錄用途但不屬於日常 flow 的文件，例如 Core `architecture/decisions/README.md` |

這也釐清一點：`glossary.md` 雖然 init 時會 seed，但它不是一般 scaffolding；它是 Dflow 會持續讀寫的 living document，因此應放在 `templates/`。

新增少量高價值 section anchors：

> Important Dflow-updated sections should carry stable HTML comment anchors in the form `<!-- dflow:section {section-id} -->`. Headings are for humans; anchors are for AI/tool positioning. Do not add anchors to every heading — only to sections that workflows repeatedly read or update.

建議初始 anchors：

| Section anchor | Typical heading | 使用位置 | 用途 |
|---|---|---|---|
| `dflow:section implementation-tasks` | `Implementation Tasks` | `phase-spec.md`, `lightweight-spec.md` | task checklist 生成、追蹤、completion check |
| `dflow:section current-br-snapshot` | `Current BR Snapshot` | `_index.md` | feature-level BR snapshot 更新與 finish-feature sync |
| `dflow:section lightweight-changes` | `Lightweight Changes` | `_index.md` | T2/T3 修改紀錄 |
| `dflow:section behavior-scenarios` | `Behavior Scenarios` | `phase-spec.md`, `behavior.md` | Given/When/Then 行為規格定位 |
| `dflow:section business-rules` | `Business Rules` | `rules.md`, `_index.md` | BR-ID / anchor 對應與 drift verification |
| `dflow:section open-questions` | `Open Questions` | specs / living docs as applicable | 未決問題收斂 |

#### 1.1 新增 Template Language Glossary

新增一份 repo 維護文件：

```text
TEMPLATE-LANGUAGE-GLOSSARY.md
```

用途：列出 template 中使用的 canonical English headings / field labels / task tags，提供繁體中文對照，幫助人讀文件時理解英文結構，但不讓 runtime 維護雙語 template set。

建議欄位：

| English term | 繁體中文對照 | 使用位置 | 說明 |
|---|---|---|---|
| Implementation Tasks | 實作任務 | `phase-spec.md`, `lightweight-spec.md` | AI 產生與追蹤 task checklist 的段落 |
| Behavior Scenarios | 行為情境 | `phase-spec.md`, `behavior.md` | Given/When/Then 行為規格 |
| Business Rules | 業務規則 | `rules.md`, `_index.md` | BR-ID declarative rules |
| Current BR Snapshot | 目前業務規則快照 | `_index.md` | feature-level rules snapshot |
| Domain Models | 領域模型 | `models.md` | Entities / Value Objects / Services 等模型索引 |
| Change Scope | 變動範圍 | `Git-principles-*.md`, spec templates | 描述本次變更涵蓋的功能 / 文件 / 程式碼範圍 |
| Lightweight Change | 輕量修改 | `_index.md`, `lightweight-spec.md`, Git principles | T2 / small change 類型的固定術語 |

這份對照表是人讀輔助與 review reference，不是第二套 template。

#### 2. 補齊 WebForms 樣板

新增：

```text
sdd-ddd-webforms-skill/templates/glossary.md
sdd-ddd-webforms-skill/templates/models.md
sdd-ddd-webforms-skill/templates/rules.md
sdd-ddd-webforms-skill/templates/context-map.md      # optional / emergent use
sdd-ddd-webforms-skill/templates/tech-debt.md
```

建議內容定位：

| Template | 目的 | 主要章節 / 欄位 |
|---|---|---|
| `glossary.md` | Ubiquitous Language 術語表 | `Term` / `Definition` / `Bounded Context` / `Code Mapping` / optional notes |
| `models.md` | Context 內 domain model catalog | Entities、Value Objects、Domain Services、Repository Interfaces、Code Mapping |
| `rules.md` | BR-ID declarative index | BR-ID、Rule summary、Behavior anchor、Status、Last updated |
| `context-map.md` | optional BC relationship map | Context list、Relationships、Integration notes、Open questions |
| `tech-debt.md` | WebForms migration debt backlog | Item、Location、Description、Severity、Migration impact、Status |

`context-map.md` 在 WebForms 版不列為 init mandatory；但若 brownfield baseline capture 或團隊選擇建立 context map，應有 template 可用。

#### 3. 補齊 Core 樣板

新增：

```text
sdd-ddd-core-skill/templates/glossary.md
sdd-ddd-core-skill/templates/models.md
sdd-ddd-core-skill/templates/rules.md
sdd-ddd-core-skill/templates/events.md
sdd-ddd-core-skill/templates/context-map.md
sdd-ddd-core-skill/templates/tech-debt.md
sdd-ddd-core-skill/scaffolding/architecture-decisions-README.md
```

建議內容定位：

| Template | 目的 | 主要章節 / 欄位 |
|---|---|---|
| `glossary.md` | Ubiquitous Language 術語表 | 與 WebForms 同欄位 |
| `models.md` | Context 內 DDD model catalog | Aggregates、Entities、Value Objects、Domain Services、Specifications、Repository Interfaces |
| `rules.md` | BR-ID declarative index | BR-ID、Rule summary、Behavior anchor、Aggregate、Status、Last updated |
| `events.md` | Domain Events catalog | Event name、Producer、Trigger、Payload、Consumers、Delivery expectation |
| `context-map.md` | BC relationship map | Contexts、Relationships、Upstream/Downstream、Published Language、ACL、Events |
| `tech-debt.md` | Architecture debt backlog | Item、Layer、Decision / debt、Impact、Follow-up、Status |
| `architecture-decisions-README.md` | ADR directory guide | ADR naming convention、when to write ADR、minimal ADR fields |

暫不新增 `templates/adr.md`，因為目前 Dflow 沒有明確的 `/dflow:new-adr` 或「AI 會建立 ADR 單篇」流程；若未來引入 ADR workflow，再另案補 ADR template。

#### 4. 新增 Template Coverage Matrix

新增一份 repo 維護文件：

```text
TEMPLATE-COVERAGE.md
```

用途：列出 WebForms / Core 兩版的 logical template 對照，供 review 檢查「物理上分開、邏輯上同步」。

本矩陣只處理 `templates/` 與 `scaffolding/` 來源檔的覆蓋與兩版對照；`references/*.md` 的流程同步矩陣若未來需要，應另案處理，避免把本 proposal 從「文件樣板覆蓋」擴大成「全 workflow 一致性重構」。

建議欄位：

| Logical document | Generated / maintained path | WebForms template | Core template | Parity requirement | Allowed differences |
|---|---|---|---|---|---|
| Feature dashboard | `specs/features/{active|completed}/{SPEC-ID}-{slug}/_index.md` | `templates/_index.md` | `templates/_index.md` | Required sections same | Core may mention Aggregate / Domain Events |
| Phase spec | `specs/features/active/{SPEC-ID}-{slug}/phase-spec-YYYY-MM-DD-{slug}.md` | `templates/phase-spec.md` | `templates/phase-spec.md` | Lifecycle sections same | Core has layer-by-layer plan + Domain Events |
| Lightweight spec | `specs/features/active/{SPEC-ID}-{slug}/lightweight-YYYY-MM-DD-{slug}.md` or `BUG-{NUMBER}-{slug}.md` | `templates/lightweight-spec.md` | `templates/lightweight-spec.md` | T2 structure and task checklist intent same | Layer tags differ |
| Glossary | `specs/domain/glossary.md` | `templates/glossary.md` | `templates/glossary.md` | Same columns | none |
| Rules index | `specs/domain/{context}/rules.md` | `templates/rules.md` | `templates/rules.md` | BR-ID / anchor / status format same | Core may include Aggregate column |
| Models catalog | `specs/domain/{context}/models.md` | `templates/models.md` | `templates/models.md` | Same purpose | Core has Aggregate / Specification depth |
| Behavior snapshot | `specs/domain/{context}/behavior.md` | `templates/behavior.md` | `templates/behavior.md` | BR anchor and drift-verification structure same | Core may reference Domain Events |
| Events catalog | `specs/domain/{context}/events.md` | n/a | `templates/events.md` | Core only | WebForms does not require event catalog |
| Context map | `specs/domain/context-map.md` | `templates/context-map.md` optional | `templates/context-map.md` mandatory | Similar concept | WebForms optional / emergent |
| Tech debt | WebForms: `specs/migration/tech-debt.md`; Core: `specs/architecture/tech-debt.md` | `templates/tech-debt.md` | `templates/tech-debt.md` | Same backlog intent | WebForms migration focus; Core architecture focus |
| ADR guide | `specs/architecture/decisions/README.md` | n/a | `scaffolding/architecture-decisions-README.md` | Core only | WebForms not applicable |

這份文件是維護契約，不是 runtime 大腦。`SKILL.md` 不需要完整複製此矩陣，只需指向它作為 repo maintenance / review reference。

#### 5. 更新 `/dflow:init-project` 的 template source

兩版 `references/init-project-flow.md` 需把 mandatory baseline 的建立來源說清楚：

WebForms：

```text
specs/domain/glossary.md          ← templates/glossary.md
specs/migration/tech-debt.md      ← templates/tech-debt.md
```

Core：

```text
specs/domain/glossary.md                    ← templates/glossary.md
specs/domain/context-map.md                 ← templates/context-map.md
specs/architecture/tech-debt.md             ← templates/tech-debt.md
specs/architecture/decisions/README.md      ← scaffolding/architecture-decisions-README.md
```

Optional project-level scaffolding 仍由 `scaffolding/_overview.md`、`scaffolding/_conventions.md`、`Git-principles-*.md`、`CLAUDE-md-snippet.md` 提供。

#### 6. 更新 workflow references：缺檔時使用對應 template 建立

在相關 flow 中明確補上：

- `new-feature-flow.md`
  - 若 `glossary.md` / `models.md` / `rules.md` / `behavior.md` 不存在，使用對應 template 建立
  - Core 若涉及 Domain Events，`events.md` 不存在時使用 `templates/events.md`
  - Core 若涉及 cross-context，`context-map.md` 不存在時使用 `templates/context-map.md`
- `modify-existing-flow.md`
  - baseline capture 時若 domain docs 不存在，使用 template 建立
  - tech debt 檔不存在時使用 `templates/tech-debt.md`
- `finish-feature-flow.md`
  - sync BC layer 前，若目標 `rules.md` / `behavior.md` / `events.md` 不存在，使用 template 建立後再 merge
- `drift-verification.md`
  - 已提示 behavior.md 可用 `templates/behavior.md` 建立；可補充 rules.md 若缺失時使用 `templates/rules.md`

#### 7. 補 `lightweight-spec.md` 精簡 `Implementation Tasks`

兩版 `templates/lightweight-spec.md` 新增一段：

```markdown
## Implementation Tasks

> Keep T2 Light tasks concise. If the fix scope starts to expand, AI should pause and ask the developer whether to keep this as T2 or upgrade it to T1. Do not auto-upgrade based on task count alone.

- [ ] {LAYER}-1: {minimal required change}
- [ ] TEST-1: {minimal verification / regression test}
- [ ] DOC-1: Update `_index.md` Lightweight Changes and Current BR Snapshot
```

WebForms 建議 layer tags：

```text
DOMAIN / PAGE / DATA / TEST / DOC
```

Core 建議 layer tags：

```text
DOMAIN / APP / INFRA / API / TEST / DOC
```

同步更新 `modify-existing-flow.md`：

- 不再說 lightweight spec 一律跳過 task generation
- 改為 T2 產生精簡 task checklist
- 若 task list 看起來超出 short fix checklist，AI 應詢問開發者是否升級為 T1
- 不以固定項目數作為自動升級標準

同步更新 completion checklist：

- `Implementation Tasks` 完成檢查適用於 `phase-spec.md` 與 `lightweight-spec.md`
- 「是否 collapse / remove 實作任務」問題可保留為 team convention，但不應只限 full spec；若團隊不想在 T2 保留 task section，可由開發者確認

---

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `TEMPLATE-COVERAGE.md` | 新增 | WebForms / Core logical template 對照矩陣，作為 review 檢查依據 |
| `TEMPLATE-LANGUAGE-GLOSSARY.md` | 新增 | Template canonical English terms 的繁體中文對照表，幫助人理解但不建立第二套 template |
| `SECTION-ANCHORS.md` 或 `TEMPLATE-COVERAGE.md` 內 anchor 欄位 | 新增 / 擇一 | 記錄高價值 `dflow:section` anchors 與使用位置；可併入 coverage matrix，避免再多一份維護檔 |
| `sdd-ddd-webforms-skill/templates/glossary.md` | 新增 | WebForms glossary 樣板 |
| `sdd-ddd-webforms-skill/templates/models.md` | 新增 | WebForms domain models 樣板 |
| `sdd-ddd-webforms-skill/templates/rules.md` | 新增 | WebForms business rules index 樣板 |
| `sdd-ddd-webforms-skill/templates/context-map.md` | 新增 | WebForms optional context map 樣板 |
| `sdd-ddd-webforms-skill/templates/tech-debt.md` | 新增 | WebForms migration tech debt 樣板 |
| `sdd-ddd-core-skill/templates/glossary.md` | 新增 | Core glossary 樣板 |
| `sdd-ddd-core-skill/templates/models.md` | 新增 | Core DDD models 樣板 |
| `sdd-ddd-core-skill/templates/rules.md` | 新增 | Core business rules index 樣板 |
| `sdd-ddd-core-skill/templates/events.md` | 新增 | Core Domain Events catalog 樣板 |
| `sdd-ddd-core-skill/templates/context-map.md` | 新增 | Core context map 樣板 |
| `sdd-ddd-core-skill/templates/tech-debt.md` | 新增 | Core architecture tech debt 樣板 |
| `sdd-ddd-core-skill/scaffolding/architecture-decisions-README.md` | 新增 | Core ADR directory README source |
| `sdd-ddd-webforms-skill/templates/lightweight-spec.md` | 修改 | 新增精簡 `Implementation Tasks` 段 |
| `sdd-ddd-core-skill/templates/lightweight-spec.md` | 修改 | 同上，使用 Core layer tags |
| `sdd-ddd-webforms-skill/templates/_index.md` | 修改 / 檢查 | 將 template 結構標題、欄位、placeholder 統一為 canonical English；內容範例可保留使用者語言 placeholder |
| `sdd-ddd-core-skill/templates/_index.md` | 修改 / 檢查 | 同上 |
| `sdd-ddd-webforms-skill/templates/phase-spec.md` | 修改 / 檢查 | 將 template 結構標題、欄位、placeholder 統一為 canonical English |
| `sdd-ddd-core-skill/templates/phase-spec.md` | 修改 / 檢查 | 同上 |
| `sdd-ddd-webforms-skill/templates/context-definition.md` | 修改 / 檢查 | 將 template 結構標題、欄位、placeholder 統一為 canonical English |
| `sdd-ddd-core-skill/templates/context-definition.md` | 修改 / 檢查 | 同上 |
| `sdd-ddd-webforms-skill/templates/behavior.md` | 修改 / 檢查 | 將 template 結構標題、欄位、placeholder 統一為 canonical English |
| `sdd-ddd-core-skill/templates/behavior.md` | 修改 / 檢查 | 同上 |
| `sdd-ddd-core-skill/templates/aggregate-design.md` | 修改 / 檢查 | Core aggregate worksheet 結構文字統一為 canonical English |
| `sdd-ddd-webforms-skill/scaffolding/_overview.md` | 檢查 | 確認 project-level scaffolding 結構文字維持 canonical English |
| `sdd-ddd-core-skill/scaffolding/_overview.md` | 檢查 | 同上 |
| `sdd-ddd-webforms-skill/scaffolding/_conventions.md` | 檢查 | 確認 project-level scaffolding 結構文字維持 canonical English |
| `sdd-ddd-core-skill/scaffolding/_conventions.md` | 檢查 | 同上 |
| `sdd-ddd-webforms-skill/scaffolding/Git-principles-gitflow.md` | 修改 / 檢查 | 結構性中文詞彙改為 canonical English，重要詞彙補入 `TEMPLATE-LANGUAGE-GLOSSARY.md` |
| `sdd-ddd-core-skill/scaffolding/Git-principles-gitflow.md` | 修改 / 檢查 | 同上 |
| `sdd-ddd-webforms-skill/scaffolding/Git-principles-trunk.md` | 修改 / 檢查 | 結構性中文詞彙改為 canonical English，重要詞彙補入 `TEMPLATE-LANGUAGE-GLOSSARY.md` |
| `sdd-ddd-core-skill/scaffolding/Git-principles-trunk.md` | 修改 / 檢查 | 同上 |
| `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md` | 修改 / 檢查 | 結構性中文詞彙改為 canonical English；工具命名本身另由 public distribution backlog 處理 |
| `sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md` | 修改 / 檢查 | 同上 |
| `sdd-ddd-webforms-skill/SKILL.md` | 修改 | Templates 表新增 living document templates；說明 Template Coverage Matrix |
| `sdd-ddd-core-skill/SKILL.md` | 修改 | 同上 |
| `sdd-ddd-webforms-skill/references/init-project-flow.md` | 修改 | mandatory baseline 建立來源改為對應 templates |
| `sdd-ddd-core-skill/references/init-project-flow.md` | 修改 | 同上，Core ADR README 來源指向 scaffolding |
| `sdd-ddd-webforms-skill/references/new-feature-flow.md` | 修改 | 缺 domain docs 時使用 template 建立 |
| `sdd-ddd-core-skill/references/new-feature-flow.md` | 修改 | 同上，含 events/context-map |
| `sdd-ddd-webforms-skill/references/modify-existing-flow.md` | 修改 | baseline capture / T2 task checklist / completion checklist 更新 |
| `sdd-ddd-core-skill/references/modify-existing-flow.md` | 修改 | 同上 |
| `sdd-ddd-webforms-skill/references/finish-feature-flow.md` | 修改 | BC sync 前缺檔時用 template 建立 |
| `sdd-ddd-core-skill/references/finish-feature-flow.md` | 修改 | 同上，含 events/context-map |
| `sdd-ddd-webforms-skill/references/drift-verification.md` | 修改 | rules.md 缺失時指向 `templates/rules.md` |
| `sdd-ddd-core-skill/references/drift-verification.md` | 修改 | 同上，保留 events.md bonus check |
| `CHANGELOG.md` | 修改 | 實作時新增一段，記錄樣板覆蓋與 lightweight task checklist |

### 不納入影響範圍

| 範圍 | 理由 |
|---|---|
| `sdd-ddd-*-skill-tw/` | 繁中版同步仍屬後續 Closeout / localization 階段 |
| `templates/en/` / `templates/zh-TW/` | 不建立雙語 template set；本 proposal 改採單一 canonical English template + `TEMPLATE-LANGUAGE-GLOSSARY.md` |
| `templates/adr.md` | 目前沒有 Dflow ADR workflow；只補 Core ADR directory README source |
| 重新設計 T1/T2/T3 Ceremony | 本 proposal 僅補 T2 task checklist，不改 tier 判準 |
| P012 的 `specs/shared/` 路徑變更 | 由 PROPOSAL-012 處理；本 proposal 以 P012 定案後路徑為前提 |
| `references/*.md` WebForms / Core 流程同步矩陣 | 本 proposal 的 `TEMPLATE-COVERAGE.md` 只處理樣板與 scaffolding；流程同步矩陣可作為後續候選議題 |

---

### 預估工作量

`中高`

新增樣板數量較多，且會整理既有 templates / scaffolding 的 canonical English 結構文字；主要風險在於：

- WebForms / Core 共同章節要保持同步
- 差異要明確寫入 `TEMPLATE-COVERAGE.md`
- flow references 要確實指向新增 template，避免樣板閒置
- 英文化時要區分「結構性術語」與「使用者可填寫內容 / 範例文字」，避免過度機械翻譯

---

### 關聯的 Proposal / 文件

| Proposal / 文件 | 關係 | 說明 |
|---|---|---|
| PROPOSAL-010 | 修補缺口 | P010 引入 `/dflow:init-project`，但 mandatory baseline 的部分 `.md` 沒有 template source；本 proposal 補齊 |
| PROPOSAL-012 | 前置 | P012 先穩定 generated path policy；本 proposal 以其路徑決策為前提 |
| PROPOSAL-003 / PROPOSAL-008 | 互補 | `rules.md` / `behavior.md` 的 source-of-truth 與 drift verification 需要穩定 template 格式 |
| PROPOSAL-009 | 互補 | `_index.md` Current BR Snapshot 最終同步到 BC layer docs；BC layer docs 需要 template source |
| `reviews/cross-round-notes.md` 議題 1 | 納入 | `lightweight-spec.md` 缺 `實作任務` 段，本 proposal 直接處理 |
| `planning/public-distribution-backlog.md` | 邊界 | 雙語 template set / localized headings 不納入；本 proposal 採單一 canonical English template + 術語對照表 |

---

### 評估紀錄（整合評估時填寫）

**評估日期**: {YYYY-MM-DD}

**結論**: {approved / rejected / 需要修改}

**理由**: {為什麼做這個決定}

**如果 rejected**: {不做的原因，留作日後參考}
