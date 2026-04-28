# Round 8b Sub-wave 2 RESUME — Scaffolding canonical English（給 Cursor + Codex 5.3 Extra High 或其他 frontier 模型使用）

> **使用方式**：把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製，貼到 Cursor。
> **本檔的角色**：Sub-wave 2 的**接續 prompt**，不是重新開始。前一個 Codex session（gpt-5.4 xhigh）在 Sub-wave 2 跑到 weekly limit 中斷，stop 點落於 templates → scaffolding 邊界（已 commit `0cd99fb`，commit message 含完整 todo 清單）。
> **絕對禁止事項**：不重做已完成項目（templates 層全部已英文化 + 加 anchors）。本輪只處理 commit message todo 列出的剩餘 8 + 1 項。
> **目標執行者**：Cursor + Codex 5.3 Extra High（或其他 frontier 模型）；本 prompt 模型無關。
> **預估工作量**：8 個 scaffolding 檔英文化檢查 + GLOSSARY 同步 = 約 30-60 分鐘
> **斷點 handoff 設計**：本 resume 完成後仍需 commit（commit message 應為「Round 8b Sub-wave 2 COMPLETE」），然後再啟動 Sub-wave 3。

---BEGIN PROMPT---

你接續 Round 8b Sub-wave 2 的剩餘工作。前一個 Codex session（gpt-5.4 xhigh）跑到 weekly limit 中斷，已 commit 部分產物於 `0cd99fb` (`Round 8b Sub-wave 2 PARTIAL`)。本輪**只**處理剩餘範圍，**不重做**已完成項目。

## 第一步：建立接手 baseline

執行以下指令確認你接手的起點狀態：

```bash
git log --oneline -7
# 應看到 (由新到舊):
# 0cd99fb Round 8b Sub-wave 2 PARTIAL: templates 全部完成，scaffolding 僅 _conventions.md
# 62640c7 Round 8b Sub-wave 1: 新增 14 檔 templates / scaffolding / 維護契約
# fb763a2 Round 8a Implement Review Fix
# f5df981 Round 8a/8b Implement prompts
# 2803f27 Round 8a Implement
# c9480f8 Round 8a與b Approve完成
# ...

git status
# 預期: clean (僅 .claude/settings.local.json 為 pre-existing dirty，可忽略)
```

如果 git log 沒看到 `0cd99fb` 或 `62640c7`，**STOP**，回報使用者「commit baseline 不對，無法接手」。

## 第二步：讀入必要檔案

### A. 設計權威來源（必讀）

1. `proposals/PROPOSAL-013-system-document-template-coverage.md`（approved；藍圖。特別 §1 canonical English structural language 原則 + Anchor naming rules + Design intent）
2. `reviews/round-8b-decisions.md`（17 個 finding 的 path 定案；4 項設計決策 Path A 的具體應用）

### B. Sub-wave 1 / Sub-wave 2 PARTIAL 已落地的證據（必讀）

3. `TEMPLATE-COVERAGE.md`（14 列 matrix；含 Section anchors 欄）
4. `TEMPLATE-LANGUAGE-GLOSSARY.md`（10 個初始 canonical English term）
5. **前一個 Codex session 在 Sub-wave 2 已英文化的 templates**（用於對齊 canonical naming convention，不要改它們）：
   - `sdd-ddd-{webforms,core}-skill/templates/_index.md`
   - `sdd-ddd-{webforms,core}-skill/templates/phase-spec.md`
   - `sdd-ddd-{webforms,core}-skill/templates/lightweight-spec.md`
   - `sdd-ddd-{webforms,core}-skill/templates/context-definition.md`
   - `sdd-ddd-{webforms,core}-skill/templates/behavior.md`（已加 dflow:section behavior-scenarios anchor）
   - `sdd-ddd-{webforms,core}-skill/templates/rules.md`（已加 dflow:section business-rules anchor）
   - `sdd-ddd-{webforms,core}-skill/templates/CLAUDE.md`（H2/H3 全英文化）
   - `sdd-ddd-core-skill/templates/aggregate-design.md`（Core only）
   - `sdd-ddd-{webforms,core}-skill/scaffolding/_conventions.md`（部分英文化，可能需要 verify）

**閱讀目的**：吸收前一個 session 已決定的 canonical English 詞彙與風格，讓你接手時的命名一致。**不重新英文化**這些檔。

### C. 本輪要處理的目標檔（讀現況才能改）

6. `sdd-ddd-webforms-skill/scaffolding/_overview.md`
7. `sdd-ddd-core-skill/scaffolding/_overview.md`
8. `sdd-ddd-webforms-skill/scaffolding/Git-principles-gitflow.md`
9. `sdd-ddd-core-skill/scaffolding/Git-principles-gitflow.md`
10. `sdd-ddd-webforms-skill/scaffolding/Git-principles-trunk.md`
11. `sdd-ddd-core-skill/scaffolding/Git-principles-trunk.md`
12. `sdd-ddd-webforms-skill/scaffolding/CLAUDE-md-snippet.md`
13. `sdd-ddd-core-skill/scaffolding/CLAUDE-md-snippet.md`

加上：
14. **`TEMPLATE-LANGUAGE-GLOSSARY.md` 同步擴充**（如下述 §3 步驟所述）

### D. 不要讀的東西

- 任何繁中版檔（`sdd-ddd-*-skill-tw/`）— 繁中同步延到 Closeout C1
- `tutorial/` 目錄
- R1-R7 的歷史 review 報告
- `references/*.md`（Sub-wave 3 範圍，本輪不動）
- `SKILL.md`（Sub-wave 3 範圍，本輪不動）

## 第三步：本輪要做的事

### 3.1 對 8 個 scaffolding 檔執行 canonical English 處理

對每個目標檔，執行以下處理：

**(a) Heading 與固定 labels 英文化**：
- H2/H3 中文 heading → canonical English（與 P013 §1 原則 + Sub-wave 2 templates 已採用的命名對齊）
- 固定欄位 / 列表標籤（`目標` / `變動範圍` / `關聯 BR-ID` / `輕量修改` 等屬於「結構性術語」）→ 英文
- 使用者可填寫的內容範例 / 自然語言段落描述 → 保留中文（屬「使用者可填寫內容」，不是結構性術語）

**(b) 不加 dflow:section anchor**（重要）：
- P013 §1 + R8b decisions 的 anchor 機制適用於 **templates 層**（`templates/` 下的檔），用於 AI 跨檔定位 highest-frequency sections
- scaffolding 層是 init-project 一次性寫入給專案的治理文件，不是 Dflow 持續維護的 living document，**不需要 anchor**
- 已落地的 Sub-wave 1 新 scaffolding（`architecture-decisions-README.md`）也沒有 anchor，與此一致

**(c) 處理已部分英文化的 `_conventions.md`**：
- 雙版 `_conventions.md` 前一個 Codex session 各動了 2 行（很少）
- Verify 是否完成：對照 P013 §1 canonical English 原則，掃一遍 H2/H3 與固定 labels
- 若發現殘留中文結構性詞，補完
- 若已完整英文化，記為「verify pass，無新動作」

**(d) `Git-principles-{gitflow,trunk}.md`** 的特別注意：
- 重點是 `Integration Commit Message Conventions` 段（per R8b decisions F-14 accept），固定 labels（`Feature 目標` / `變動範圍` / `關聯 BR-ID` / `Phase 數` / `輕量修改` 等）改 canonical English
- 其他段落若已是英文（很可能大半都英文了），**僅檢查不改動**
- 與 Sub-wave 2 templates 中已採用的對應詞對齊（特別 `Feature Goal` / `Change Scope` / `Related BR-IDs` / `Phase Count` / `Lightweight Changes` 等，請先 grep templates 確認確切詞）

**(e) `CLAUDE-md-snippet.md`** 的特別注意：
- 重點是 H2 結構（`系統脈絡` / `開發流程`）+ H3 標題 + 固定 labels；對照 templates/CLAUDE.md 雙版（已英文化）的對應 heading
- 雙版各自處理（WebForms 較長、Core 較短）
- P012 已將 specs/ 路徑改為 specs/shared/，本輪只動結構性英文化，不再動路徑

### 3.2 同步擴充 `TEMPLATE-LANGUAGE-GLOSSARY.md`

對 §3.1 處理過程中遇到的「新 canonical English term」（即 templates 已採用、但目前 GLOSSARY 10 個 term 未涵蓋者），補入 GLOSSARY。

具體做法：
1. 處理完每個 scaffolding 檔後，記錄出現的新 canonical English heading / label
2. 全部處理完後，對照 GLOSSARY 現有 10 個 term，補入未涵蓋者
3. 特別關注 P013 §1.1 glossary 範例已列的 7 個 term（Implementation Tasks / Behavior Scenarios / Business Rules / Current BR Snapshot / Domain Models / Change Scope / Lightweight Change）+ R8b decisions F-12 accept 補的 3 個（Lightweight Changes / Resume Pointer / Behavior Delta）= 共 10 個。如果 Sub-wave 2 templates 用了這 10 個之外的固定詞（例如 `Feature Goal`、`Phase Count`），必須補入 GLOSSARY

### 3.3 不要做的事（重要）

- **不要修改 templates 層任何檔**（前一個 session 已完成，且本輪 scope 不含）
- **不要動 references/*.md**（屬 Sub-wave 3）
- **不要動 SKILL.md**（屬 Sub-wave 3）
- **不要加 dflow:section anchor 到 scaffolding**（per §3.1(b)）
- **不要加 lightweight-spec.md 的 Implementation Tasks**（屬 Sub-wave 4）
- **不要新增任何 templates / scaffolding 檔**（Sub-wave 1 已新增 12 + 2，本輪不擴）
- **不要動繁中版**（`*-tw/`）

## 第四步：完成判定與 checkpoint

### 完成條件（全部達成才算 Sub-wave 2 COMPLETE）

- 8 個 scaffolding 檔（_overview ×2、Git-principles-gitflow ×2、Git-principles-trunk ×2、CLAUDE-md-snippet ×2）的 canonical English 處理完成
- 雙版 `_conventions.md` verify 完成（若已完整英文化，明示 verify pass）
- TEMPLATE-LANGUAGE-GLOSSARY.md 同步擴充完成（若 Sub-wave 2 templates 引入新 canonical term）
- 全部修改與既有 Sub-wave 2 templates 的命名一致（互相對齊，無內部矛盾）

### Checkpoint 輸出格式

完成後輸出以下 markdown：

```markdown
### CHECKPOINT: SUB-WAVE 2 COMPLETE

本 session 修改檔（{N} 檔）：
- `sdd-ddd-{webforms,core}-skill/scaffolding/_overview.md`：{要點}
- `sdd-ddd-{webforms,core}-skill/scaffolding/Git-principles-gitflow.md`：{要點}
- `sdd-ddd-{webforms,core}-skill/scaffolding/Git-principles-trunk.md`：{要點}
- `sdd-ddd-{webforms,core}-skill/scaffolding/CLAUDE-md-snippet.md`：{要點}
- `sdd-ddd-{webforms,core}-skill/scaffolding/_conventions.md`：{verify pass / 補充 X 處}
- `TEMPLATE-LANGUAGE-GLOSSARY.md`：{新增 N 個 term：{列表}} 或 {無新 term，未動}

verify：
- 雙版 templates / scaffolding 命名對齊：✓
- scaffolding 未加 dflow:section anchor：✓
- references/*.md / SKILL.md 未動：✓
- 繁中版 / tutorial / proposals / reviews 未動：✓

下一步：commit + 啟動 Sub-wave 3（references cascade + SKILL.md）
```

### 若中途必須斷（你跑到自己的 limit）

- stop 在「檔案邊界」（不要在某檔中間斷）
- 輸出 partial checkpoint：

```markdown
### CHECKPOINT: SUB-WAVE 2 STILL PARTIAL — completed {X 檔}, pending {Y 檔}

完成：
- `{已完成檔列表}`

待完成：
- `{未完成檔列表}`

下一輪接手者：依本檔範圍繼續，不重做已完成項目
```

## 第五步：重要規則

- **不要修改 proposals/、reviews/、tutorial/**
- **不要 commit**（commit 由 main session 統一執行，本 session 只改檔 + 輸出 checkpoint）
- **不要新建檔**（除非為了 GLOSSARY 補欄而修改 GLOSSARY 本身，那是 modify 不是新建）
- **不要動繁中版檔（`*-tw/`）**
- **遇到不確定的設計決策**：優先參考 P013 §1 + R8b decisions §設計決策拍板紀錄；若仍不確定，stop 並回報使用者，不要憑主觀英文化某個結構性詞

## 第六步：開工檢查清單

開工前先 verify：
1. `git log --oneline -7` 看到 `0cd99fb` 與 `62640c7` ✓
2. `git status` clean（除 `.claude/settings.local.json`）✓
3. 已讀 §A、§B 必讀檔（特別吸收 Sub-wave 2 templates 的 canonical English 命名）✓
4. 已 grep templates 內現有的 canonical English heading（例如：`grep -rn "^##\|^###" sdd-ddd-{webforms,core}-skill/templates/`）以建立對齊基準 ✓

開始處理。完成後輸出 checkpoint marker。

---END PROMPT---
