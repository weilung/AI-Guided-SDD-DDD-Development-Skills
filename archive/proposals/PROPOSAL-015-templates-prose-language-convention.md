## PROPOSAL-015: Templates prose language 規範補完

**狀態**: `implemented`

**提出日期**: 2026-04-30

**優先級**: `高`

**來源**: 2026-04-29 Tutorial 試跑 + `planning/public-distribution-backlog.md` § 1.2 + commit `82c5b53` fix evidence

---

### 問題描述

PROPOSAL-013 已經補齊 R8b 的 template coverage、canonical English structural language、section anchor 與 glossary。其語言策略刻意聚焦在「結構性語言」：H2/H3 headings、table headers、固定 labels、placeholder、HTML comment anchors。這讓 AI-facing skill instructions 與 generated documents 有穩定的段落定位基準，也避免維護 `templates/en/` / `templates/zh-TW/` 兩套 template set。

但 R8b 沒有處理「自由 prose」的語言規範。自由 prose 包含：

- `Implementation Tasks` 的 checklist 描述
- `_index.md` 的 `Integration Summary`
- lightweight / bug-fix spec 中的 `Root Cause`、`Fix Approach`、`Reason for Change`
- `Tech Debt` 條目內的說明、risk、proposed approach
- `Open Questions`、`Resume Pointer` 等段落內的人寫內容

2026-04-29 Tutorial 劇情 1 (Core greenfield) 試跑時出現實證問題：Codex 看到 template 結構是英文後，預設把自己生成的自由 prose 也寫成英文；只有 Alice 對話中已用中文描述過的內容才自然保留中文。結果同一批 output 變成三層混排：

| 層級 | 語言 | 來源 |
|---|---|---|
| H2/H3、table headers、anchors、frontmatter labels | 英文 | R8b canonical structural language |
| Codex / subagent 自寫 prose | 英文 | AI 依英文 template 結構推斷 |
| Alice 對話帶入的需求內容 | 繁中 | 使用者原始輸入 |

commit `82c5b53` 的 commit message 已把這個問題記錄為真實 evidence，並以 single fix commit 將 12 個 Tutorial 劇情 1 outputs 中的 Codex / subagent 自寫英文 prose 翻譯為繁中。該 commit 明確列出可翻譯範圍：Implementation Tasks 列項描述、Behavior Delta prose、Root Cause / Fix Approach / Problem / Reason for Change prose、Tech Debt prose、Open Questions prose、Resume Pointer prose；同時保留 headings、frontmatter、table headers、anchors、ID、DDD / tech stack terms 與 inline code。

這代表目前缺少一條可被 AI 穩定遵循的規則：

> Dflow generated documents keep canonical English structural language, but free prose should follow the project prose language convention.

這個缺口沒有在 R8b 一併處理是合理的。`planning/public-distribution-backlog.md` § 1.2 已說明：R8b 已同時落地 canonical English、anchor 機制、`TEMPLATE-COVERAGE.md`、`TEMPLATE-LANGUAGE-GLOSSARY.md` 等設計；再加入 prose-language 規範會讓 scope 過大。而且 Tutorial 混語言現象是在 R8b 後試跑才觀察到，需要先整理 evidence 與候選方案，再獨立評估。

本 proposal 不處理 `planning/public-distribution-backlog.md` § 1.1 的 anchor-enabled localized heading。P015 只處理 prose language；headings 仍沿用 PROPOSAL-013 的 canonical English structural language。

### 提議的解法

建議採用候選 **(B) init CLI prose-language 問題** 作為 default design，並以候選 **(C) `_conventions.md` 手動段落** 作為 fallback。

核心方向：

1. `npx dflow init` 在 P014 的 CLI init flow 中新增 `prose-language` 問題，且必須填入 explicit language tag，例如 `zh-TW`、`en`、`ja-JP`。
2. CLI 將答案寫入 `dflow/specs/shared/_conventions.md` 的 `## Prose Language` 段。
3. AI 在產生或維護 specs 時，先讀取 project-level convention，依該設定撰寫自由 prose。
4. 依 review `F-07`，`new-feature` / `modify-existing`（含 bug-fix path） / `new-phase` / `finish-feature` 四條 prose-generating flow **必須**在 Step 1 讀取 `dflow/specs/shared/_conventions.md` 的 `Prose Language` 段。任何 flow 跳過此步即違背 P015 契約。
5. 若 P014 延後或 init CLI 尚未落地，先由 `_conventions.md` scaffolding 提供相同段落，讓團隊手動填寫 explicit language。

P015 依 review `F-09` 採強制 explicit 策略：不允許 `any`，不允許 init 時 skip / later。若 workflow 讀不到 `Prose Language`，應先要求使用者補上 explicit language 並更新 `_conventions.md`，再繼續產生自由 prose。

#### 候選做法比較

| 候選 | 機制說明 | 實作成本 | 維護成本 | AI 理解可靠度 | 與 P014 的耦合度 | 與 P013 anchor 機制的兼容 | 評估 |
|---|---|---:|---:|---|---|---|---|
| (A) `<!-- dflow:prose-language: zh-TW -->` directive | 在 templates / scaffolding 的檔頭放置 machine-readable directive；有 frontmatter 的 template 可放在 frontmatter 後、H1 前；無 frontmatter 的 scaffolding 可放在第一個 H1 前。directive 必須是 explicit language tag，例如 `zh-TW`、`en`、`ja-JP` 或其他 BCP-47 tag。生成後保留 directive，AI 看到該檔即可知道 prose 語言。 | 小到中 | 中 | 高，因為 directive 與目標檔同檔共址 | 低，可獨立於 CLI | 高。directive 與 `dflow:section` anchor 是不同用途，不互相衝突 | 適合需要 per-file override 的進階場景；但會在每份生成文件留下額外 metadata，且同一專案多檔 directive 可能 drift |
| (B) init CLI 新增 `prose-language` 問題 | `npx dflow init` 問使用者自由 prose 要用哪種 explicit language，並寫入 `dflow/specs/shared/_conventions.md`。後續 AI workflow 讀 project convention。 | 中 | 低 | 高。初始化時明示、集中在 project convention，且 prose-generating flow 必須在 Step 1 讀 `_conventions.md` | 高。依賴 P014 shared-cut W1 的 CLI init Q&A flow | 高。不改 heading / anchor，只規範 anchor 所定位段落內的 prose | **建議 default**。最符合公開散佈與團隊級 convention 心智模型，避免每份 template 重複標記 |
| (C) `_conventions.md` 新增 `Prose Language` 段 | 不新增 CLI 問題，只在雙版 `_conventions.md` scaffolding 加一段可手動填寫的 explicit prose language policy；AI 依現有 convention 文件對齊。 | 小 | 低 | 中。只要 AI 有讀 `_conventions.md` 就穩定；若 workflow 沒提醒，可能被漏讀 | 低。可獨立於 P014 的 CLI；採 review `F-11` shared-cut implementation 後，path 過渡問題自我消解 | 高。同 (B)，只管 prose，不碰 anchor / heading | **建議 fallback**。若 P014 尚未 approved / implemented，可先用此方案補洞 |
| (D) 不做規範，依賴 prompt engineering | 維持現狀，由團隊在每次 prompt AI 時明示「自由 prose 請用繁中 / 英文」。可在 README adoption section 提醒使用者。 | 極小 | 高 | 低。每次對話都要記得提示，跨 session / subagent 容易遺漏 | 無 | 高，因為不改任何機制 | 不建議。commit `82c5b53` 已證明現狀會產生可見混語言成本 |

#### 建議方案

預設建議：**(B) init CLI `prose-language` 問題 + `_conventions.md` 持久化設定**。

理由：

- prose language 是 project-level convention，不是單一 template 的結構屬性。集中寫在 `_conventions.md` 比每份文件各放 directive 更符合「團隊規範」心智模型。
- P014 已把 init 入口從 skill slash command 上移到 npm CLI；prose-language 正好屬於 init intake question，應在專案 bootstrap 時明示。
- 與 P013 的 anchor 機制完全互補：P013 確保 AI 能定位段落，P015 確保 AI 在段落內用哪種語言撰寫自由 prose。
- 對 public distribution 較友善。國際團隊可選 `en` 或其他 BCP-47 language tag；中文團隊可選 `zh-TW`；不需要建立平行 template set。
- 強制 explicit language 可避免多 session / 多代理協作時 prose 語言漂移，否則 P015 想解的混語言問題會被「未指定偏好」重新引入。
- 相較 (A)，(B) 降低 per-file metadata drift。若未來真的需要單檔 override，再另案加入 directive，不必在 V1 先引入。

備援建議：**(C) `_conventions.md` 手動段落**。

若 P014 尚未 approved、P014 implement 延後，或 CLI Q&A flow 尚未能穩定寫入 conventions，P015 可先落地 (C)：雙版 `_conventions.md` scaffolding 新增相同 `## Prose Language` 段，並在雙版 `SKILL.md` / workflow 指引中要求 AI 讀取 explicit language。等 P014 CLI init 落地後，再把同一段由 CLI 問答自動填入。這個 fallback 不會與 (B) 衝突，因為 (B) 的持久化位置本來就是 `_conventions.md`。依 review `F-08`，採 review `F-11` shared-cut implementation 後，P014 + P015 同期 cascade，pre-P014 / post-P014 path 過渡問題自我消解。

#### Init CLI 對接點

P014 shared-cut W1 建立 npm package skeleton 與 `npx dflow init` Q&A flow 後，P015 implement 應在 init 問題中新增：

```text
Project prose language for generated spec content? (required)
Examples:
- zh-TW  = write free prose in Traditional Chinese
- en     = write free prose in English
- ja-JP  = write free prose in Japanese
- custom = enter another explicit BCP-47 language tag
```

建議儲存值：

```text
en | zh-TW | ja-JP | {explicit BCP-47 language tag}
```

建議 default：

- public package default：無。init 必填一個 explicit language，不提供 skip / later。
- Tutorial / 本 repo 的 greenfield core 範例：`zh-TW`
- 若使用者明確選擇語言，CLI 不應再依 terminal locale 覆寫

CLI 寫入位置：

```text
dflow/specs/shared/_conventions.md
```

P015 目標設計以 P014 後的 `dflow/specs/shared/_conventions.md` 為準；採 review `F-11` shared-cut implementation 後，不再維護 pre-P014 `specs/shared/_conventions.md` 過渡規則。

強制 explicit 的理由是：P015 的問題本質是 AI 在沒有明確 prose-language convention 時，會依英文 template 結構推斷自由 prose 也應使用英文。若 public default 允許 `any`，多 session、subagent 或 long-running workflow 仍會回到「依上下文猜測」的狀態，混語言問題不會被制度性消除。因此 V1 直接要求 init 時寫入 explicit language。

#### `_conventions.md` 段落格式

雙版 scaffolding 建議新增下列段落，位置放在 `## Filling the Templates` 前後皆可；較建議放在 `## Filling the Templates` 之前，因為 prose language 是填寫規則的前置 convention。

```markdown
## Prose Language

Project prose language: `{prose-language}`

Dflow templates keep canonical English structural language: headings,
table headers, fixed labels, placeholders, IDs, anchors, and code-facing
terms remain English.

Free prose written inside those sections should follow the project prose
language:

- `en`: write free prose in English.
- `zh-TW`: write free prose in Traditional Chinese.
- `{xx-XX}`: write free prose in that explicit BCP-47 language.

Do not translate code identifiers, DDD pattern names, BR IDs, SPEC IDs,
file paths, branch names, anchors, or inline code only to satisfy the
prose-language setting.
```

AI 解讀規則：

| 設定 | AI 行為 |
|---|---|
| `en` | 自由 prose 使用英文；保留 code / DDD / ID 原樣。 |
| `xx-XX` | 自由 prose 使用該 BCP-47 語言，例如 `zh-TW`；保留 structural English 與 code-facing terms。 |
| 未設定 | 視為 invalid；AI 應先要求使用者選擇 explicit language 並更新 `_conventions.md`，再繼續 prose-generating workflow。 |

#### 為何不建議 (A) 作為 default

(A) 的可靠度最高，因為 directive 與生成文件同檔共址；但它把 project-level convention 複製到每份 template / generated document，後續會有兩個風險：

- 同一專案內不同文件的 directive 可能不一致，AI 必須再決定哪個來源優先。
- 如果使用者改變團隊 prose language，需要掃過大量文件，或接受舊檔留著不同設定。

因此 (A) 建議保留為未來 per-file override 的可能方向，不在 P015 V1 採用。若未來採用，directive 應採 explicit value，例如 `<!-- dflow:prose-language: zh-TW -->`；優先順序應是：file-level directive > `_conventions.md` project setting > missing value 時停止並要求補設定。

#### 為何不建議 (D)

(D) 只能把現狀記錄成 README adoption guidance，例如「prompt AI 時請明示 prose 語言」。這對單次對話有效，但對 subagent、長流程、跨 session continuation、Tutorial outputs 與 public distribution 都不穩定。commit `82c5b53` 的修正成本已說明這不是純文件說明可可靠解決的問題。

### 影響範圍

以下影響範圍以採用 default recommendation (B) 為準，並保留 fallback (C) 可獨立落地的最小切面。

> 依 review `F-06` 與 `F-11`：Tutorial prose 改寫由 P014 + P015 shared-cut implementation 處理，see `F-11`；P015 不獨立追溯，因此本節不獨立列 `tutorial/` 檔案。

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `lib/init.js`（P014 implement 後新增） | 修改 | 在 `npx dflow init` Q&A flow 加入必填 `prose-language` 問題；不允許 `any`、不允許 skip / later；將 explicit language 寫入 `dflow/specs/shared/_conventions.md` |
| `sdd-ddd-webforms-skill/references/init-project-flow.md` | 修改 | 將必填 prose-language intake 納入 init flow spec；若 P014 已落地，定位為 CLI internal flow spec |
| `sdd-ddd-core-skill/references/init-project-flow.md` | 修改 | 同 WebForms；Core init flow 寫入相同 `_conventions.md` 段 |
| `sdd-ddd-webforms-skill/scaffolding/_conventions.md` | 修改 | 新增 `## Prose Language` 段與 `{prose-language}` placeholder |
| `sdd-ddd-core-skill/scaffolding/_conventions.md` | 修改 | 同 WebForms |
| `sdd-ddd-webforms-skill/SKILL.md` | 修改 | 在 template language / project convention 說明中加入「structural English 固定，free prose follow project prose language」規則；要求 workflow 讀 `_conventions.md` |
| `sdd-ddd-core-skill/SKILL.md` | 修改 | 同 WebForms |
| `sdd-ddd-webforms-skill/references/{new-feature,modify-existing,new-phase,finish-feature}-flow.md` | 修改（mandatory） | 依 review `F-07`，Step 1 必須加讀 `dflow/specs/shared/_conventions.md` 的 prose-language 規則；bug-fix path 由 `modify-existing-flow.md` 覆蓋；任何 flow 跳過此步即違背 P015 契約 |
| `sdd-ddd-core-skill/references/{new-feature,modify-existing,new-phase,finish-feature}-flow.md` | 修改（mandatory） | 同 WebForms |
| `sdd-ddd-webforms-skill/templates/*.md` | 確認 | 不批量新增 directive；只確認 template note 沒有暗示 prose 必須英文 |
| `sdd-ddd-core-skill/templates/*.md` | 確認 | 同 WebForms |
| `TEMPLATE-LANGUAGE-GLOSSARY.md` | 修改 | 若新增 "Prose Language" / "free prose" / "structural language" 等術語，補上繁中對照 |
| `TEMPLATE-COVERAGE.md` | 確認 / 視需要修改 | 若 coverage matrix 有 template language notes，補充 prose language convention 的來源位置；不需重做 coverage |
| `README.md` | 修改 | Adoption section 說明 Dflow 結構語言與 prose 語言的分工；不採用 (D) 時仍可提醒使用者 init 後檢查 `_conventions.md` |
| `CHANGELOG.md` | 修改 | 新增 P015 / R9 段；不改寫 R7/R8 既有歷史段落 |

不納入 P015 implement 範圍：

| 檔案 / 範圍 | 理由 |
|---|---|
| `planning/` | 歷史規劃紀錄，不追溯改寫 |
| 既有 `proposals/` 與 `reviews/` | 時序紀錄，不追溯改寫；本檔除外 |
| `CHANGELOG.md` R7/R8 既有段落 | 保留歷史；只新增 P015 新段 |
| `sdd-ddd-*-skill-tw/` | 已於 commit `b3d5963` 棄用刪除，不存在 |
| `planning/public-distribution-backlog.md` § 1.1 heading localization | P015 不處理 heading 本地化；只處理 prose |
| 每份 template 的 file-level prose directive | 屬候選 (A)，本 proposal 不作為 default implement |
| 合併成單一 PROPOSAL | 依 review `F-12` 決議不合併；P014 與 P015 問題層次不同，但 implement 採 shared-cut |

### 預估工作量

`中`

採用 (B) 時工作量為中，因為 proposal 自身主要修改 init contract、雙版 skill / init flow / scaffolding / prose-generating flow / README。但 implement 階段依 review `F-11` 採 P014 + P015 **shared-cut implementation**，不應把 P015 排成獨立後置 wave，否則會重複修改 `lib/init.js`、`_conventions.md`、雙版 `references/init-project-flow.md` 與 Tutorial prose。

P015 在 shared-cut 中的切面如下：

| Wave | P015 切面 | 說明 |
|---|---|---|
| W1 | Init contract + `_conventions.md` 寫入規格 | 與 P014 W1 一起定義必填 `prose-language` Q、explicit language validation、`dflow/specs/shared/_conventions.md` 的 `## Prose Language` 寫入格式，以及缺值時的停止規則 |
| W2 | Repo-wide cascade | 雙版 `SKILL.md`、五條 prose-generating flow、雙版 `_conventions.md` scaffolding、README / glossary / coverage 視需要同步；Tutorial prose 改寫由 P014 + P015 shared-cut 一次處理，不在 P015 獨立追溯 |

若 P014 延後而先採用 fallback (C)，工作量為小：主要修改雙版 `_conventions.md`、雙版 `SKILL.md`、必要的 workflow 指引與 README，約 5-8 檔。但目前拍板路徑是 shared-cut，因此 fallback 是 contingency，不是預設排程。

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| `proposals/PROPOSAL-013-system-document-template-coverage.md` | 互補 | P013 定義 structural language：canonical English headings、labels、anchors 與 template coverage；P015 補上 structural section 內自由 prose 的語言規範 |
| `proposals/PROPOSAL-014-cli-init-and-dflow-root-namespace.md` | 依賴 / 互補 | 依 review `F-10`，候選 (B) 強依賴 P014 的 `npx dflow init` Q&A flow 與 `dflow/specs/shared/_conventions.md` 目標路徑；候選 (C) 可獨立於 P014。Implement 階段採 review `F-11` shared-cut，兩 PROPOSAL 共同 wave 一次 cascade |

### 評估紀錄（整合評估時填寫）

**評估日期**: 2026-04-30

**結論**: approved

**理由**: P015 補完 R8b 結構英化後的 prose 語言缺口；revision 處理全部 5 finding。採 default (B) init CLI 必填 + (C) _conventions.md fallback；F-09 強制 explicit 避免 prose 漂移；F-07 五 prose-generating flow 改 mandatory 確保契約可靠；F-11 與 P014 同期 shared-cut implement。

**如果 rejected**: N/A

### 實施紀錄

**實施日期**: 2026-05-01

**結論**: implemented

**實施摘要**: P015 已依 shared-cut 完成。`npx dflow init` 納入必填 explicit `prose-language` 問題並寫入 `dflow/specs/shared/_conventions.md`；雙版 `_conventions.md` scaffolding 新增 `## Prose Language` 段；五條 prose-generating flow 在 Step 1 讀取 project prose-language convention；README 補 structural English 與 free prose 的分工；`TEMPLATE-LANGUAGE-GLOSSARY.md` 補 Prose Language / free prose / structural language 等術語對照。

**保留決策**: P015 V1 採 project-level `_conventions.md` convention，不新增 per-file prose directive；缺少 explicit prose language 時 workflow 應停止並要求補設定。
