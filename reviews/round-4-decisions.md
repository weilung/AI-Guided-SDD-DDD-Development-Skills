# Round 4 Decisions — Source of Truth 與漂移驗證（P003 / P008）

**對應 review 報告**: `reviews/round-4-report.md`
**決議日期**: 2026-04-21
**決議者**: weilung（Claude 整理）

---

## 決議彙總

| Finding | 嚴重度 | 決議 | 說明 |
|---|---|---|---|
| F-01 | Major | **accept** | `/dflow:verify` 分類標題（manage an active workflow）與條目描述（standalone, no active workflow needed）自相矛盾——另開 Standalone commands 小節解決 |
| F-02 | Major | **accept** | BR-ID 擷取規則過寬，section heading 與 body text 被等值處理，違背 P008「對應情境段落」原意——Step 3/4/5 改為 section-based 主檢查 + body text 輔助訊號 |

---

## 總體結論

Round 4 review 報告 2 個 Finding，**全數 accept**，皆為實作與 proposal 意圖的真實落差：

1. **F-01** 指出的是 SKILL.md 內同一條目**自相衝突**（分類 header vs bullet 括號註記），不是格式性抱怨；修正後可移除 `/dflow:verify` 讀者（開發者 / AI）在「是否需要先進入 flow」這一判斷點上的搖擺。
2. **F-02** 指出的是 `drift-verification.md` 的機械檢查精準度問題；P008「輸出範例」明寫失敗訊息指向「找不到**段落**」，與現行實作「出現在 behavior.md 任何位置即通過」的寬鬆條件背離，會產生誤通過與誤告警兩類雜訊。

兩項都落在 R4 指定範圍內（SKILL.md `/dflow:verify` 相關段 + `references/drift-verification.md` 整份）。其他範圍（behavior.md template、context-definition.md 提及段、Behavior Source of Truth 段、兩版一致性、Core 版 events.md 額外 warning）審查皆通過，不產生 Finding。

---

## Accept 項目的實作指引

### F-01：`/dflow:verify` 重新分類為 Standalone commands

**修改檔案**：
- `sdd-ddd-webforms-skill/SKILL.md`（Slash Commands 段，約 L80-92）
- `sdd-ddd-core-skill/SKILL.md`（Slash Commands 段，約 L95-107）

**具體改動**：

在 Slash Commands 子節下，新增第三類 `**Standalone commands** — run independently of any workflow:`，把 `/dflow:verify [<bc>]` 從原本的 `**Control commands** — manage an active workflow:` 小節移出，放到新類別下。原 bullet 括號註記 `(standalone, no active workflow needed)` 可以移除（因為新 section header 已涵蓋此語意）或保留為強調用，由 Implement session 依可讀性判斷。

修改後結構（兩版相同）：

```markdown
### Slash Commands

**Flow entry commands** — start a workflow:
- `/dflow:new-feature` — enter new-feature-flow
- `/dflow:modify-existing` — enter modify-existing-flow
- `/dflow:bug-fix` — enter modify-existing-flow with lightweight ceremony
- `/dflow:pr-review` — enter PR review checklist

**Control commands** — manage an active workflow:
- `/dflow:status` — report current workflow, step, and progress
- `/dflow:next` — confirm proceeding to the next phase (equivalent to "OK" / "continue")
- `/dflow:cancel` — abort current workflow, return to free conversation (artifacts created so far are kept as-is)

**Standalone commands** — run independently of any workflow:
- `/dflow:verify [<bc>]` — run drift verification on rules.md ↔ behavior.md
```

**注意事項**：
- 兩版必須同步改（共通段）
- 若 frontmatter / Decision Tree / Reference Files 表格中也有對 `/dflow:verify` 的敘述提及 Control commands 歸屬，一併檢查修正（預期無，因為原實作只在 Slash Commands 段指定分類）
- 本輪不碰繁中版；繁中同步延後至 Post-Review Closeout（見 review-workflow.md 第七節）

---

### F-02：drift-verification.md Step 3/4/5 改為 section-based 檢查

**修改檔案**：
- `sdd-ddd-webforms-skill/references/drift-verification.md`
- `sdd-ddd-core-skill/references/drift-verification.md`

**具體改動**：

#### (1) Step 3 擷取規則分兩類

將現行 L54-56「Scan `behavior.md` for all `BR-*` identifiers referenced in section headings or body text.」改為明確分層：

```markdown
### Step 3: Extract BR-IDs from behavior.md

Build two sets:

- **Primary set (scenario-bound)**: BR-IDs that appear in section headings
  (e.g. `## Amount Validation (BR-001)`) or in the formal `(BR-NNN)` marker
  inside a Given/When/Then scenario block. These represent BR-IDs that have
  a dedicated scenario section.
- **Supplementary set (body-text mentions)**: BR-IDs that appear only in
  prose / discussion text, outside any Given/When/Then block. These are
  informational references, not equivalent to a scenario section.
```

#### (2) Step 4 Forward / Reverse check pass 條件改為 primary set

將現行 L58-66 的表格改為：

```markdown
### Step 4: Cross-reference

Run the three checks using the primary set from Step 3 as the main comparison basis:

| Check | Pass condition | Fail message |
|---|---|---|
| Forward | Every BR-ID in rules.md has a corresponding scenario section in behavior.md (primary set) | `✗ BR-NNN declared in rules.md but has no scenario section in behavior.md` |
| Anchor | Every `behavior.md#anchor` in rules.md resolves to an existing heading | `✗ BR-NNN links to behavior.md#section but anchor not found` |
| Reverse | Every BR-ID formally referenced in behavior.md (primary set) is declared in rules.md | `✗ BR-NNN referenced in behavior.md scenario but not declared in rules.md` |

Body-text mentions (supplementary set) do **not** satisfy forward / reverse
pass conditions on their own. They are reported separately as informational
signals (see Step 5).
```

#### (3) Step 5 報告格式增加區分層

將現行 L70-93 的輸出範例改為包含三種狀態（✓ / ✗ / ℹ）：

```markdown
### Step 5: Report

Output format:

```
Verifying {Context} — rules.md ↔ behavior.md consistency

✓ BR-001 → scenario section exists and references BR-001
✓ BR-002 → scenario section exists and references BR-002
✗ BR-003 → behavior.md has no scenario section for BR-003
          (note: BR-003 appears in body text of another section,
           but that does not satisfy the forward check)
ℹ BR-005 → body text reference only — no dedicated scenario section;
           confirm this is intentional (e.g. cross-reference to another BR)

✗ BR-010 → formally referenced in a behavior.md scenario but not
          declared in rules.md

Summary: 3 passed, 2 issues, 1 informational

Issues:
1. rules.md declares BR-003, but behavior.md has no corresponding
   scenario section
   → Possible cause: rule was implemented but behavior.md wasn't
     updated in the completion flow; or a body-text mention was
     mistaken for a scenario
   → Action: check the implementation, then either add the scenario
     to behavior.md (preferred) or remove BR-003 from rules.md if
     deprecated

2. behavior.md's {scenario section name} formally references BR-010,
   but rules.md doesn't declare it
   → Possible cause: scenario was added directly to behavior.md
     without updating rules.md
   → Action: add BR-010 to rules.md with a one-line summary, or
     remove the stale scenario reference from behavior.md
```
```

**注意事項**：
- 兩版**同步修改**；Core 版 `Core-Specific Notes`（events.md bonus check，L95-103）**不受影響**、保持原樣
- `Purpose` / `Scope — This command does (mechanical layer)` 的三項概述（L11-17）措辭要檢查與 Step 4 一致性：`1. BR-ID forward check: Every BR-* declared in rules.md has a corresponding section in behavior.md` 原本已寫「corresponding section」，正好跟 Step 4 新條件對齊，無需改動；但 `3. BR-ID reverse check: Every BR-* referenced in behavior.md is declared in rules.md` 的措辭可視情況補一個「formally / in a scenario」以和 Step 4 reverse 條件呼應（若 Implement session 認為加上會顯得過度 verbose，可保留原樣——概述層允許略寬）
- 維持 P008「明確排除語意層」邊界不變；本次修正仍然是**字串比對**，只是把「出現在哪裡」的標準收斂
- 本輪不碰繁中版 `drift-verification_tw.md`；延後至 Post-Review Closeout

---

## Reject/Defer 項目備忘

無。

---

## Out-of-scope 項目

無。Codex 未在本輪報告中提出任何指定範圍以外的 Finding，`cross-round-notes.md` 無需新增條目。

---

## 跨輪發現備忘

無新增跨輪發現。本輪 2 項 Findings 皆在 R4 指定範圍內完整處理，未外溢到其他輪次。

---

## 輪次狀態

- **R4 Review**：✓ 完成（2026-04-20）
- **R4 Approve**：✓ 完成（2026-04-21）
- **R4 Implement**：待開新 Claude Code session 執行
