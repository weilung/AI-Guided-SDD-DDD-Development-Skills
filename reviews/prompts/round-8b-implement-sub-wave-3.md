# Round 8b Sub-wave 3 — references cascade + SKILL.md（給 Cursor + Codex 5.3 Extra High 或其他 frontier 模型使用）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Cursor。
> **本檔的角色**：Sub-wave 3 的**獨立 wrapper prompt**，引用既有 round-8b-implement.md § Sub-wave 3 為權威 spec，但加上「baseline 驗證 + 邊界守住 + Sub-wave 1+2 lessons」三層保護，避免 fresh session 受到 Sub-wave 1/2/4 干擾。
> **絕對禁止事項**：
>   - 不要碰 templates/ 與 scaffolding/（Sub-wave 1+2 已完成）
>   - 不要碰 lightweight-spec.md Implementation Tasks 段（屬 Sub-wave 4）
>   - 不要動 CHANGELOG（屬 Sub-wave 4）
>   - 不要新增任何檔（Sub-wave 1 已新增完）
> **目標執行者**：Cursor + Codex 5.3 Extra High（或其他 frontier 模型）；本 prompt 模型無關。
> **預估工作量**：15 references + 2 SKILL.md = 約 60-90 分鐘
> **斷點 handoff 設計**：本輪有內部 checkpoint「`SUB-WAVE 3.REFERENCES COMPLETE`」+「`SUB-WAVE 3.SKILL COMPLETE`」+「`SUB-WAVE 3 COMPLETE`」三個 marker；若中途斷可在 references 邊界 stop。

---BEGIN PROMPT---

你執行 Round 8b Sub-wave 3：references cascade + SKILL.md cascade。Sub-wave 1（新增 14 檔）與 Sub-wave 2（templates / scaffolding canonical English + anchors）已完成，本輪只處理 references 與 SKILL.md 對前兩波 outputs 的 cascade 同步。

## 第一步：建立接手 baseline

執行以下指令確認你接手的起點狀態：

```bash
git log --oneline -10
# 應看到 (由新到舊):
# 2aaba8b Round 8b Sub-wave 2 COMPLETE: scaffolding canonical English + GLOSSARY +3
# 74b53f3 Round 8b Sub-wave 2 resume prompt
# 0cd99fb Round 8b Sub-wave 2 PARTIAL: templates 全部完成，scaffolding 僅 _conventions.md
# 62640c7 Round 8b Sub-wave 1: 新增 14 檔 templates / scaffolding / 維護契約
# fb763a2 Round 8a Implement Review Fix
# ...

git status
# 預期: clean (僅 .claude/settings.local.json 為 pre-existing dirty，可忽略)
```

如果 git log 沒看到 `2aaba8b` 或 `62640c7`，**STOP**，回報「baseline 不對，無法接手」。

## 第二步：讀入必要檔案

### A. 權威 spec（必讀）

1. **`reviews/prompts/round-8b-implement.md` line 279-335**（Sub-wave 3 完整 spec —— 本輪你要做的事都在這裡，包含 8 個 references group + 1 個 SKILL.md group 的具體 cascade 規則）
2. `proposals/PROPOSAL-013-system-document-template-coverage.md`（approved；§5 init mandatory baseline + §6 references 修改 + §7 lightweight-spec 補 task 段 + §1 anchor 機制）
3. `reviews/round-8b-decisions.md`（17 個 finding 的 path 定案；特別 F-03 / F-04 / F-05 / F-07 / F-08 / F-13 / F-14 / F-17 是 Sub-wave 3 的具體執行細節）

### B. Sub-wave 1 + 2 已落地的 outputs（必讀，作為 cascade 來源）

4. `TEMPLATE-COVERAGE.md`（14 列 matrix；含 Section anchors 欄；雙版 templates 對照）
5. `TEMPLATE-LANGUAGE-GLOSSARY.md`（**13 個 canonical English term**：10 初始 + Sub-wave 2 新增的 `Feature Goal` / `Related BR-IDs` / `Phase Count`）
6. **Sub-wave 2 已英文化的 templates**（你的 cascade 來源；不要改它們）：
   - `sdd-ddd-{webforms,core}-skill/templates/_index.md`（H2/H3 / 6 sections / dflow:section anchors）
   - `sdd-ddd-{webforms,core}-skill/templates/phase-spec.md`
   - `sdd-ddd-{webforms,core}-skill/templates/lightweight-spec.md`（**注意：Sub-wave 4 才會加 Implementation Tasks 段，本輪不動**）
   - `sdd-ddd-{webforms,core}-skill/templates/context-definition.md`
   - `sdd-ddd-{webforms,core}-skill/templates/behavior.md`（已加 dflow:section behavior-scenarios anchor）
   - `sdd-ddd-{webforms,core}-skill/templates/rules.md`（已加 dflow:section business-rules anchor）
   - `sdd-ddd-{webforms,core}-skill/templates/CLAUDE.md`（H2/H3 全英文化）
   - `sdd-ddd-core-skill/templates/aggregate-design.md`
7. **Sub-wave 1 新增 templates**（references 應指向這些做為 fallback template）：
   - `sdd-ddd-{webforms,core}-skill/templates/glossary.md`
   - `sdd-ddd-{webforms,core}-skill/templates/models.md`
   - `sdd-ddd-{webforms,core}-skill/templates/rules.md`（注意：rules.md 既是新增也是已加 anchor 的）
   - `sdd-ddd-{webforms,core}-skill/templates/context-map.md`
   - `sdd-ddd-{webforms,core}-skill/templates/tech-debt.md`
   - `sdd-ddd-core-skill/templates/events.md`（Core only）
   - `sdd-ddd-core-skill/scaffolding/architecture-decisions-README.md`（Core only，per F-07）

**閱讀目的**：吸收 canonical English 詞彙與結構，讓 references 內所有引用的 template section name 與其完全一致。

### C. 本輪要處理的目標檔（讀現況才能改）

按 round-8b-implement.md § Sub-wave 3 spec 順序：

**Sub-wave 3.references（15 檔）**：
8. `sdd-ddd-{webforms,core}-skill/references/init-project-flow.md`（per P013 §5 + F-07）
9. `sdd-ddd-{webforms,core}-skill/references/new-feature-flow.md`（per P013 §6）
10. `sdd-ddd-{webforms,core}-skill/references/modify-existing-flow.md`（per P013 §6 + §7 部分；§7 細節留 Sub-wave 4）
11. `sdd-ddd-{webforms,core}-skill/references/finish-feature-flow.md`（per F-04，Step 5 Integration Summary labels 大量改寫）
12. `sdd-ddd-{webforms,core}-skill/references/drift-verification.md`（per F-08，新增「Anchor coexistence with `dflow:section`」段）
13. `sdd-ddd-{webforms,core}-skill/references/pr-review-checklist.md`（per F-03，section name cascade）
14. `sdd-ddd-{webforms,core}-skill/references/git-integration.md`（per F-17，檢查級別；預期無修改、加 traceability comment）
15. `sdd-ddd-core-skill/references/ddd-modeling-guide.md`（Core only，per F-13）

**Sub-wave 3.SKILL（2 檔）**：
16. `sdd-ddd-{webforms,core}-skill/SKILL.md`（per F-05，Templates 表新增 living document templates + maintenance contract 段 + section name cascade）

### D. 不要讀 / 不要動的東西

- 任何繁中版檔（`sdd-ddd-*-skill-tw/`）— 繁中同步延到 Closeout C1
- `tutorial/` 目錄
- R1-R7 的歷史 review 報告
- `templates/`、`scaffolding/` 任何檔（Sub-wave 1+2 已完成）
- `templates/lightweight-spec.md` 的 Implementation Tasks 段（屬 Sub-wave 4）
- `references/new-phase-flow.md`（如已存在，是 P009 R7 產物，不在 P013 §6 範圍內 — 除非發現它引用到舊 Chinese section name；先 grep 確認再決定是否動）
- `CHANGELOG.md`（屬 Sub-wave 4）
- `proposals/`、`reviews/`、`planning/`

## 第三步：本輪要做的事

**直接依 `reviews/prompts/round-8b-implement.md` line 279-335 的具體 Sub-wave 3 指引執行**。本 prompt 不重述 15 個檔的逐一 cascade 規則（避免 spec drift）；以那份 spec 為唯一權威。

關鍵提醒（基於 Sub-wave 1+2 lessons）：

### 3.1 Cascade 命名以 GLOSSARY + 已英文化 templates 為準

GLOSSARY 現有 13 term。對 references 內每個引用的 template section name，到對應 template 用 grep 確認確切英文 heading（不要憑記憶或推測）：

```bash
# 範例：要 cascade _index.md 的 section name 時
grep -E '^## |^### ' sdd-ddd-webforms-skill/templates/_index.md
grep -E '^## |^### ' sdd-ddd-core-skill/templates/_index.md
```

若雙版 templates 用了不同的對應英文，回報使用者；不要自行統一。

### 3.2 Sub-wave 3.references 與 3.SKILL 之間的順序

按 round-8b-implement.md spec：先做 3.references，輸出 `### CHECKPOINT: SUB-WAVE 3.REFERENCES COMPLETE`，再做 3.SKILL。

理由：3.SKILL 內 Templates 表新增列要對齊 references 已 cascade 完成的 canonical naming；3.references 先穩定才好 SKILL.md cascade。

### 3.3 F-17 git-integration.md 的特別注意

per round-8b-implement.md spec line 309-312 + R8b decisions F-17：

```bash
# 預期 0 命中
grep -E "Change Scope|變動範圍|Lightweight Change|輕量修改|Feature 目標|關聯 BR-ID|Phase 數" sdd-ddd-webforms-skill/references/git-integration.md
grep -E "Change Scope|變動範圍|Lightweight Change|輕量修改|Feature 目標|關聯 BR-ID|Phase 數" sdd-ddd-core-skill/references/git-integration.md
```

若如預期 0 命中：在檔尾加一行 traceability comment：
```markdown
<!-- R8b verified: no Chinese structural terms in scope; per F-17 Path A. -->
```

若有命中：列為意外發現，回報使用者再決定如何處理（不擅自修改）。

### 3.4 SKILL.md cascade 的 Templates 表新增列

per round-8b-implement.md spec line 321-326，雙版 SKILL.md Templates 表要新增以下 living document templates（這些是 Sub-wave 1 新增的）：

WebForms (5 列新增): glossary.md / models.md / rules.md / context-map.md / tech-debt.md
Core (7 列新增): glossary.md / models.md / rules.md / context-map.md / tech-debt.md / events.md / aggregate-design.md（Core 既有）

aggregate-design.md 在 Core 既有但 SKILL.md Templates 表是否已列，請 verify；如已列則不重複，如未列則補上。

### 3.5 Templates 段加段：Template Coverage Matrix + Template Language Glossary

per round-8b-implement.md spec line 328：在雙版 SKILL.md Templates / Scaffolding 段加段，說明：
- `<repo root>/TEMPLATE-COVERAGE.md` 位置 + 用途（review reference / maintenance contract）+ 使用情境（不在 runtime 讀，僅在 review / maintenance / new template 新增時參考）
- `<repo root>/TEMPLATE-LANGUAGE-GLOSSARY.md` 位置 + 用途（canonical English term ↔ 繁中對照，給人讀文件用、不是第二套 template）

### 3.6 不要做的事（重要）

- **不要動 templates/** 任何檔（Sub-wave 1+2 已完成）
- **不要動 scaffolding/** 任何檔（Sub-wave 1+2 已完成）
- **不要動 templates/lightweight-spec.md 的 Implementation Tasks 段**（屬 Sub-wave 4；雖然 Sub-wave 2 已注入 anchor，但 Implementation Tasks 內容是 Sub-wave 4 才補）
- **不要動 CHANGELOG.md**（屬 Sub-wave 4）
- **不要新增任何檔**（Sub-wave 1 已新增完所有 12 templates / 1 scaffolding / 2 維護契約）
- **不要動繁中版**（`*-tw/`）

## 第四步：完成判定與 checkpoint

### Checkpoint marker（內部 + 完成）

依 round-8b-implement.md spec line 317 + 335：

1. 完成 3.references 後輸出：
```markdown
### CHECKPOINT: SUB-WAVE 3.REFERENCES COMPLETE

本輪 references 修改（{N} 檔）：
- `sdd-ddd-{webforms,core}-skill/references/init-project-flow.md`：{要點}
- `sdd-ddd-{webforms,core}-skill/references/new-feature-flow.md`：{要點}
- `sdd-ddd-{webforms,core}-skill/references/modify-existing-flow.md`：{要點}
- `sdd-ddd-{webforms,core}-skill/references/finish-feature-flow.md`：{要點}
- `sdd-ddd-{webforms,core}-skill/references/drift-verification.md`：{要點}
- `sdd-ddd-{webforms,core}-skill/references/pr-review-checklist.md`：{要點}
- `sdd-ddd-{webforms,core}-skill/references/git-integration.md`：{verify pass + traceability comment / 意外命中 X 處}
- `sdd-ddd-core-skill/references/ddd-modeling-guide.md`：{要點}
```

2. 完成 3.SKILL 後輸出：
```markdown
### CHECKPOINT: SUB-WAVE 3.SKILL COMPLETE

雙版 SKILL.md 修改：
- Templates 表新增 living document 列：{列出新增列}
- Templates / Scaffolding 段加段：Template Coverage Matrix + Template Language Glossary 引用
- 內部引用 cascade：{section name 改寫摘要}
```

3. 全部完成後輸出：
```markdown
### CHECKPOINT: SUB-WAVE 3 COMPLETE

verify：
- 雙版 references 對應 Sub-wave 1+2 templates / GLOSSARY canonical naming：✓
- 雙版 SKILL.md Templates 表完整列出新 living document templates：✓
- templates/ scaffolding/ lightweight-spec Implementation Tasks / CHANGELOG 未動：✓
- 繁中版 / tutorial / proposals / reviews 未動：✓

下一步：commit + 啟動 Sub-wave 4（lightweight Implementation Tasks 段內容 + modify-existing-flow §7 task guard 細節 + CHANGELOG 新段）
```

### 若中途必須斷（你跑到 limit）

- stop 在「檔案邊界」（不要在某檔中間斷）
- 優先 stop 在 3.references / 3.SKILL 邊界（即輸出 SUB-WAVE 3.REFERENCES COMPLETE 後就停）
- 輸出 partial checkpoint：

```markdown
### CHECKPOINT: SUB-WAVE 3 STILL PARTIAL — completed {X 檔}, pending {Y 檔}

完成：
- `{已完成檔列表}`

待完成：
- `{未完成檔列表}`

下一輪接手者：依本檔範圍繼續，不重做已完成項目
```

## 第五步：重要規則

- **不要修改 proposals/、reviews/、tutorial/、planning/**
- **不要 commit**（commit 由 main session 統一執行）
- **不要新建任何檔**（Sub-wave 1 已完成）
- **不要動繁中版檔（`*-tw/`）**
- **遇到不確定的設計決策**：優先參考 `reviews/prompts/round-8b-implement.md` § Sub-wave 3 + P013 §5/§6/§7 + R8b decisions；若仍不確定 stop 並回報使用者
- **Cascade 命名以「實際 templates 內既有 heading」為唯一權威**：用 grep verify，不憑記憶

## 第六步：開工檢查清單

開工前先 verify：
1. `git log --oneline -10` 看到 `2aaba8b`、`62640c7`、`fb763a2` ✓
2. `git status` clean（除 `.claude/settings.local.json`）✓
3. 已讀 §A、§B 必讀檔（特別 round-8b-implement.md line 279-335）✓
4. 已 grep templates 內現有的 canonical English heading 建立對齊基準 ✓
5. 已知本輪邊界：不動 templates / scaffolding / lightweight-spec Implementation Tasks 段 / CHANGELOG ✓

開始處理。完成後輸出 3 個 CHECKPOINT marker。

---END PROMPT---
