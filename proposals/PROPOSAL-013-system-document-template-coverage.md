## PROPOSAL-013: 系統文件樣板覆蓋與 Template Coverage Matrix

**狀態**: `implemented`

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
- `templates/CLAUDE.md` 的 H2 navigation 契約沿用 P007c 設計（系統脈絡 / 開發流程 等分段切割），但 label 採 canonical English；H2/H3 結構性標題與 SKILL.md / flow 對齊處同步英文化，散文段落仍為使用者填寫範例，不在覆蓋範圍。

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
| `dflow:section business-rules` | `Business Rules` | `rules.md` | BR-ID / anchor 對應與 drift verification |
| `dflow:section open-questions` | `Open Questions` | specs / living docs as applicable | 未決問題收斂 |

> **Same-section single-anchor rule**（per F-06 Path A）：`_index.md` 的 Business Rules 概念由 `current-br-snapshot` anchor 涵蓋；同一段不重複注入兩個 stable id，避免 namespacing 衝突與 drift-verification 索引困惑。`business-rules` anchor 的權威使用位置為 `rules.md`。

**Anchor coexistence rule**（per F-08）：`<!-- dflow:section X -->` HTML comment anchor 與 P008 drift-verification 既有的 markdown auto-id heading anchor 是**不同維度**：

- **Markdown auto-id heading anchor**（如 `behavior.md#br-001-rule-name`）：用於 BR-ID 之間的 link 解析，由 markdown renderer 自動生成；P008 機械驗證 #2「Anchor 有效性」依此運作
- **`dflow:section` HTML comment anchor**：用於 AI / 工具的段落定位（grep），不參與 BR-ID link 解析

兩者並存，`dflow:section` 不取代 BR-ID markdown anchor link；drift-verification 仍以 BR-ID + markdown auto-id 為主索引，`dflow:section` 僅作為段落定位輔助。`templates/rules.md` 的 `Behavior anchor` 欄位範例值仍為 `[BR-001](./behavior.md#br-001-rule-name)` 樣式（讓 P008 既有演算法可直接套用）。

**Anchor naming rules**（per F-15 Path B）：

- **Namespacing**：anchor id 採全 repo 共用語意——同 id 在不同 template / instance 表示同類段落（例如 `implementation-tasks` 在 `phase-spec.md` 與 `lightweight-spec.md` 都是 task checklist 段，AI 讀到任一處都應理解為同類）。anchor id 為 document-type-agnostic 的 stable identifier。
- **Versioning（延後設計）**：anchor id 重命名 / deprecation 規則屬 backlog § 7「Dflow Version Upgrade / Migration Strategy」範疇；npm 散佈後與其他 versioning 議題（路徑遷移、heading 遷移）一起設計，本 proposal 不在現階段定義具體過渡語法或時長。

**Design intent**：anchor 機制不只是「heading 漂移的 fail-safe」，更是**啟用 localized heading 的基礎契約**。當 anchor 是 stable identifier 後，heading 文字可在未來 npm 散佈階段依專案語言本地化（中文 / 英文 / 其他），而不影響 AI 段落定位。本 proposal 階段先把 anchor 機制建立 + canonical English heading 統一；本地化 heading 屬 `planning/public-distribution-backlog.md` § 1.1 候選方向，留待 npm 散佈後依實證評估。

#### 1.1 新增 Template Language Glossary

新增一份 repo 維護文件，置於 repo 根（per F-11 Path A），兩版 skill 共用：

```text
<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md
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
| Lightweight Changes | 輕量修改紀錄 | `_index.md` | _index.md 中登記 T2 外連 + T3 inline 的 section heading（複數，section 用法；對應 P009 § H 既有「輕量修改紀錄」段名）|
| Resume Pointer | 接續入口 | `_index.md` | _index.md 末段「目前進展 + 下一動作」的 section heading（per F-03 cascade）|
| Behavior Delta | 行為變更 | `lightweight-spec.md` | lightweight-spec 中 BR delta 段的 section heading |

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

新增一份 repo 維護文件，置於 repo 根（per F-11 Path A），兩版 skill 共用：

```text
<repo root>/TEMPLATE-COVERAGE.md
```

用途：列出 WebForms / Core 兩版的 logical template 對照，供 review 檢查「物理上分開、邏輯上同步」。

本矩陣只處理 `templates/` 與 `scaffolding/` 來源檔的覆蓋與兩版對照；`references/*.md` 的流程同步矩陣若未來需要，應另案處理，避免把本 proposal 從「文件樣板覆蓋」擴大成「全 workflow 一致性重構」。

建議欄位：

| Logical document | Generated / maintained path | WebForms template | Core template | Parity requirement | Allowed differences | Section anchors |
|---|---|---|---|---|---|---|
| Feature dashboard | `specs/features/{active|completed}/{SPEC-ID}-{slug}/_index.md` | `templates/_index.md` | `templates/_index.md` | Required sections same | Core may mention Aggregate / Domain Events | `current-br-snapshot`, `lightweight-changes` |
| Phase spec | `specs/features/active/{SPEC-ID}-{slug}/phase-spec-YYYY-MM-DD-{slug}.md` | `templates/phase-spec.md` | `templates/phase-spec.md` | Lifecycle sections same | Core has layer-by-layer plan + Domain Events | `implementation-tasks`, `behavior-scenarios`, `open-questions` |
| Lightweight spec | `specs/features/active/{SPEC-ID}-{slug}/lightweight-YYYY-MM-DD-{slug}.md` or `BUG-{NUMBER}-{slug}.md` | `templates/lightweight-spec.md` | `templates/lightweight-spec.md` | T2 structure and task checklist intent same | Layer tags differ | `implementation-tasks` |
| Glossary | `specs/domain/glossary.md` | `templates/glossary.md` | `templates/glossary.md` | Same columns | none | — |
| Bounded Context definition | `specs/domain/{context}/context-definition.md` | `templates/context-definition.md` | `templates/context-definition.md` | Same purpose / structural sections | Core may reference Aggregate / Domain Service / Repository Interface | — |
| Rules index | `specs/domain/{context}/rules.md` | `templates/rules.md` | `templates/rules.md` | BR-ID / anchor / status format same | Core may include Aggregate column | `business-rules` |
| Models catalog | `specs/domain/{context}/models.md` | `templates/models.md` | `templates/models.md` | Same purpose | Core has Aggregate / Specification depth | — |
| Aggregate worksheet | `specs/domain/{context}/aggregates/{name}.md` (per Aggregate, on demand) | n/a | `templates/aggregate-design.md` | Core only | WebForms does not use the Aggregate worksheet | — |
| Behavior snapshot | `specs/domain/{context}/behavior.md` | `templates/behavior.md` | `templates/behavior.md` | BR anchor and drift-verification structure same | Core may reference Domain Events | `behavior-scenarios` |
| Events catalog | `specs/domain/{context}/events.md` | n/a | `templates/events.md` | Core only | WebForms does not require event catalog | — |
| Context map | `specs/domain/context-map.md` | `templates/context-map.md` optional | `templates/context-map.md` mandatory | Similar concept | WebForms optional / emergent | — |
| Tech debt | WebForms: `specs/migration/tech-debt.md`; Core: `specs/architecture/tech-debt.md` | `templates/tech-debt.md` | `templates/tech-debt.md` | Same backlog intent | WebForms migration focus; Core architecture focus | — |
| ADR guide | `specs/architecture/decisions/README.md` | n/a | `scaffolding/architecture-decisions-README.md` | Core only | WebForms not applicable | — |
| Project AI guide | `<project root>/CLAUDE.md` | `templates/CLAUDE.md` | `templates/CLAUDE.md` | H2 navigation 與 H3 結構標題對齊（canonical English，per F-01 Path A） | Core 包含 Aggregate / Architecture Decisions 等 Core-specific H3 | — |

這份文件是維護契約，不是 runtime 大腦。`SKILL.md` 不需要完整複製此矩陣，只需指向它作為 repo maintenance / review reference。

**Section anchors 欄使用方式**（per F-06 Path A 決議）：anchor 清單**統一**在本矩陣的 `Section anchors` 欄維護，不另開 `SECTION-ANCHORS.md`。新增 anchor 時：(1) 在 §1「Important Dflow-updated sections」表新增 anchor 定義；(2) 在本矩陣對應的 logical document 行 `Section anchors` 欄補上 anchor id。anchor 命名 / namespacing / versioning 規範見 §1 「Anchor naming rules」段。

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
specs/architecture/decisions/README.md      ← scaffolding/architecture-decisions-README.md   (NEW; 擴張自 P010)
```

Optional project-level scaffolding 仍由 `scaffolding/_overview.md`、`scaffolding/_conventions.md`、`Git-principles-*.md`、`CLAUDE-md-snippet.md` 提供。

##### Baseline 比對（per F-07 / F-16 Path A）

P013 對 P010 § 1 Step 4 既有 mandatory baseline 的關係：

| Baseline 項 | P010 Step 4 | P013 §5 | 差異 |
|---|---|---|---|
| `specs/features/{active,completed,backlog}/` | ✓ 必產 | ✓（無變動）| 不變 |
| `specs/domain/glossary.md` | ✓ 空範本 | ✓ ← `templates/glossary.md` | 補 template source |
| `specs/domain/context-map.md`（Core only）| ✓ 空範本 | ✓ ← `templates/context-map.md` | 補 template source |
| `specs/architecture/tech-debt.md`（Core only）| ✓ 空範本 | ✓ ← `templates/tech-debt.md` | 補 template source |
| `specs/architecture/decisions/`（Core only）| ✓ **空目錄** | （目錄維持空目錄） | 不變 |
| `specs/architecture/decisions/README.md`（Core only）| **未列**（P010 漏列）| ✓ ← `scaffolding/architecture-decisions-README.md` | **NEW**（P013 修補 P010 baseline 的 gap：空目錄沒有 README 對新人不友好） |
| `specs/migration/tech-debt.md`（WebForms only）| ✓ 空範本 | ✓ ← `templates/tech-debt.md` | 補 template source |

**結論**：P013 §5 對 P010 既有 baseline 4-5 項僅做「補 template source」（不改 P010 既有 baseline 項目）；唯一新增的 baseline 推薦輸出是 Core `decisions/README.md`，本 proposal 視為**修補 P010 漏列**而非全新引入。P010 文件無需改動內容，但 implemented 後可在 P010 Step 4 對應段加註「`decisions/README.md` 由 PROPOSAL-013 補上 baseline 推薦輸出」cross-reference（見 §關聯 Proposal）。

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

> DOC-1 中的 `Lightweight Changes` 與 `Current BR Snapshot` 為 §1.1 glossary 列出的 canonical heading；實施時必須與 `_index.md` 英文化後的 heading 完全一致（per F-12）。

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
| `TEMPLATE-COVERAGE.md`（repo 根，per F-11 Path A）| 新增 | WebForms / Core logical template 對照矩陣，作為 review 檢查依據；兩版 skill 共用一份；置於 repo 根（與 `CHANGELOG.md` / `README.md` 同層）|
| `TEMPLATE-LANGUAGE-GLOSSARY.md`（repo 根，per F-11 Path A）| 新增 | Template canonical English terms 的繁體中文對照表，幫助人理解但不建立第二套 template；兩版 skill 共用一份；置於 repo 根 |
| `TEMPLATE-COVERAGE.md` 內 `Section anchors` 欄 | 包含於上行新增 | 記錄高價值 `dflow:section` anchors 與使用位置；併入 coverage matrix（per F-06 Path A）；不另開 `SECTION-ANCHORS.md`，避免雙檔同步 overhead |
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
| `sdd-ddd-webforms-skill/templates/CLAUDE.md` | 修改 / 檢查 | H2 / H3 結構性標題（`系統脈絡` / `開發流程` / `核心原則` / `目錄結構` / `Ceremony 三層` / `新增功能` / `修改既有功能` / `Bug 修復` / `Feature 收尾` / `Git 整合` / `Domain 層規範` / `術語表` / `AI 協作注意事項` 等）改為 canonical English；H2 navigation 契約以英文形式延續 P007c；散文 / 範例內容可保留繁中。檔內 `specs/` 目錄結構同步交由 PROPOSAL-012 處理 |
| `sdd-ddd-core-skill/templates/CLAUDE.md` | 修改 / 檢查 | 同上（含 Aggregate / Architecture Decisions 等 Core 特有 H3）|
| `sdd-ddd-webforms-skill/scaffolding/_overview.md` | 檢查 | 確認 project-level scaffolding 結構文字維持 canonical English |
| `sdd-ddd-core-skill/scaffolding/_overview.md` | 檢查 | 同上 |
| `sdd-ddd-webforms-skill/scaffolding/_conventions.md` | 檢查 | 確認 project-level scaffolding 結構文字維持 canonical English |
| `sdd-ddd-core-skill/scaffolding/_conventions.md` | 檢查 | 同上 |
| `sdd-ddd-webforms-skill/scaffolding/Git-principles-gitflow.md` | 修改 / 檢查 | Integration Commit Message Conventions 段內的固定 labels（`Feature 目標` / `變動範圍` / `關聯 BR-ID` / `Phase 數` / `輕量修改` 等）改為 canonical English；其他段落已英文，僅檢查；新詞補入 `TEMPLATE-LANGUAGE-GLOSSARY.md` |
| `sdd-ddd-core-skill/scaffolding/Git-principles-gitflow.md` | 修改 / 檢查 | 同上 |
| `sdd-ddd-webforms-skill/scaffolding/Git-principles-trunk.md` | 修改 / 檢查 | 同上（trunk 版的 squash / rebase 相關段；其他段落已英文，僅檢查；新詞補入 `TEMPLATE-LANGUAGE-GLOSSARY.md`） |
| `sdd-ddd-core-skill/scaffolding/Git-principles-trunk.md` | 修改 / 檢查 | 同 webforms trunk 版（squash / rebase 相關段；其他段落已英文，僅檢查） |
| `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md` | 修改 / 檢查 | 結構性中文詞彙改為 canonical English；工具命名本身另由 public distribution backlog 處理 |
| `sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md` | 修改 / 檢查 | 同上 |
| `sdd-ddd-webforms-skill/SKILL.md` | 修改 | (1) Templates 表新增 living document templates；(2) 說明 Template Coverage Matrix；(3) 同步更新 Templates & Scaffolding 段內所有引用的 template section name 為 canonical English（`_index.md` 的 `目標與範圍` / `輕量修改紀錄` / `接續入口` 等；CLAUDE-md-snippet 描述視 F-01 Path A 同步全英化） |
| `sdd-ddd-core-skill/SKILL.md` | 修改 | 同上（含 Core 特有段落引用） |
| `sdd-ddd-webforms-skill/references/init-project-flow.md` | 修改 | mandatory baseline 建立來源改為對應 templates |
| `sdd-ddd-core-skill/references/init-project-flow.md` | 修改 | 同上，Core ADR README 來源指向 scaffolding |
| `sdd-ddd-webforms-skill/references/new-feature-flow.md` | 修改 | 缺 domain docs 時使用 template 建立 |
| `sdd-ddd-core-skill/references/new-feature-flow.md` | 修改 | 同上，含 events/context-map |
| `sdd-ddd-webforms-skill/references/modify-existing-flow.md` | 修改 | baseline capture / T2 task checklist / completion checklist 更新 |
| `sdd-ddd-core-skill/references/modify-existing-flow.md` | 修改 | 同上 |
| `sdd-ddd-webforms-skill/references/finish-feature-flow.md` | 修改 | (1) BC sync 前缺檔時用 template 建立；(2) Step 5 Integration Summary 段的固定 labels（`Feature 目標` / `變動範圍` / `關聯 BR-ID` / `Phase 清單` / `輕量修改` / `下一步` 等）改為 canonical English；引用 `_index.md` 的 section name 同步更新；新詞補入 `TEMPLATE-LANGUAGE-GLOSSARY.md` |
| `sdd-ddd-core-skill/references/finish-feature-flow.md` | 修改 | 同上（含 events/context-map；Integration Summary Chinese labels 同步英文化） |
| `sdd-ddd-webforms-skill/references/drift-verification.md` | 修改 | (1) rules.md 缺失時指向 `templates/rules.md`；(2) 補一段說明「`dflow:section` anchor 不取代 BR-ID markdown anchor link，drift-verification 演算法不變，仍以 BR-ID + markdown auto-id 為主索引」(per F-08) |
| `sdd-ddd-core-skill/references/drift-verification.md` | 修改 | 同上，保留 events.md bonus check |
| `sdd-ddd-webforms-skill/references/pr-review-checklist.md` | 修改 | 同步引用的 template section name 改為 canonical English（與 §1.1 glossary 對齊）：`輕量修改紀錄` → `Lightweight Changes`、`接續入口` → `Resume Pointer`、`行為變更（Delta）` → `Behavior Delta` 等 |
| `sdd-ddd-core-skill/references/pr-review-checklist.md` | 修改 | 同上 |
| `sdd-ddd-core-skill/references/ddd-modeling-guide.md` | 修改 | Bounded Context Relationships 段補上「`specs/domain/context-map.md` 不存在時使用 `templates/context-map.md` 建立」的引導，與 §6 其他 reference flow 的缺檔處理一致 |
| `sdd-ddd-webforms-skill/references/git-integration.md` | 檢查 | 確認與 §1.1 glossary 列出的 canonical 名（`Change Scope` / `Lightweight Change`）一致；R8b approve verify 顯示該檔已全英無 P013 §1 範圍內的 Chinese 結構性詞，implement 階段執行者跑一次 grep verify 並明列「已 verify 無需改動」(per F-17 Path A) |
| `sdd-ddd-core-skill/references/git-integration.md` | 檢查 | 同上 |
| `CHANGELOG.md` | 修改 | 實作時新增一段，記錄樣板覆蓋與 lightweight task checklist |

### 不納入影響範圍

| 範圍 | 理由 |
|---|---|
| `sdd-ddd-*-skill-tw/` | 繁中版同步仍屬後續 Closeout / localization 階段 |
| `templates/en/` / `templates/zh-TW/` | 不建立雙語 template set；本 proposal 改採單一 canonical English template + `TEMPLATE-LANGUAGE-GLOSSARY.md` |
| `templates/adr.md` | 目前沒有 Dflow ADR workflow；只補 Core ADR directory README source |
| 重新設計 T1/T2/T3 Ceremony | 本 proposal 僅補 T2 task checklist，不改 tier 判準 |
| P012 的 `specs/shared/` 路徑變更 | 由 PROPOSAL-012 處理；本 proposal 以 P012 定案後路徑為前提 |
| `templates/CLAUDE.md` 內 specs/ 目錄結構同步 | 由 PROPOSAL-012 處理（P012 §影響範圍已列：directory tree `_overview.md` / `_conventions.md` 從 `specs/` 平鋪改為 `specs/shared/` 之下）；本 proposal 只負責同檔 H2/H3 canonical English 結構標題（per F-01 Path A） |
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
| PROPOSAL-010 | 修補缺口（baseline 擴張）| P010 引入 `/dflow:init-project`，但 mandatory baseline 的部分 `.md` 沒有 template source；本 proposal 補齊。同時把 Core `specs/architecture/decisions/README.md` 從 P010 漏列補上為 baseline 推薦輸出（見 §5 baseline 比對；per F-07 / F-16 Path A）；P013 implemented 後 P010 Step 4 對應段加註 cross-reference |
| PROPOSAL-012 | 前置 | P012 先穩定 generated path policy；本 proposal 以其路徑決策為前提 |
| PROPOSAL-003 / PROPOSAL-008 | 互補 | `rules.md` / `behavior.md` 的 source-of-truth 與 drift verification 需要穩定 template 格式 |
| PROPOSAL-009 | 互補 | `_index.md` Current BR Snapshot 最終同步到 BC layer docs；BC layer docs 需要 template source |
| `reviews/cross-round-notes.md` 議題 1 | 納入 | `lightweight-spec.md` 缺 `實作任務` 段，本 proposal 直接處理 |
| `planning/public-distribution-backlog.md` | 邊界（部分接管，per F-10）| (1) backlog § 1「文件內容語言策略」的**結構性語言策略**部分（單一 canonical English template set + 術語對照表，不建立雙語 template）由 P013 §1 接管；**content 語言策略**（使用者填入的需求 / 規則 / 決策內容可依專案語言撰寫）仍延後到 npm 散佈前處理。(2) backlog § 4「Template Language Glossary」由 P013 §1.1 的 `TEMPLATE-LANGUAGE-GLOSSARY.md` 接管。P013 implemented 後，backlog § 1 / § 4 對應段加註「partially fulfilled by P013」 |

---

### 評估紀錄（整合評估時填寫）

**評估日期**: 2026-04-27

**結論**: approved

**理由**: 經 R8b review（2026-04-27，Cursor Claude Opus 4.7）+ R8b approve（2026-04-27 本 session）處理 17 個 finding（accept: 11，accept-with-choice: 6，reject: 0，defer: 0），所有需修訂部分已在 R8b approve session 內完成。4 項新概念設計決策全部拍板（canonical English structural language / `<!-- dflow:section X -->` anchor / TEMPLATE-COVERAGE.md / TEMPLATE-LANGUAGE-GLOSSARY.md）；anchor 機制補上「Design intent」說明其啟用 localized heading 的長期動機。

**實施紀錄**: R8b Implement 已完成並記錄於 `CHANGELOG.md` 的「2026-04-28 — R8b 實施：PROPOSAL-013 系統文件樣板覆蓋與 Template Coverage Matrix」。R8b Implement Review Fix 已修訂 P013 anchor 表與相關 cascade，見 `reviews/round-8b-implement-review-decisions.md`。

**如果 rejected**: n/a
