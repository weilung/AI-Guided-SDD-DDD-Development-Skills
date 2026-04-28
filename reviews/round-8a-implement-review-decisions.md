# Round 8a Implement Review Decisions — PROPOSAL-012 實作品質審查 fix

**對應 review 報告**: `reviews/round-8a-implement-review-report.md`
**決議日期**: 2026-04-28
**決議者**: 使用者（Claude Opus 4.7 整理 + 當場修訂）
**Reviewer 模型**: Cursor Claude Opus 4.7（implement review 階段）
**Session 模式**: Approve + Fix 合併 session（仿 R7 Implement Review Fix pattern；3 finding 全部 inline 處理）

---

## 決議彙總

| Finding | 嚴重度 | 維度 | 決議 | 說明 |
|---|---|---|---|---|
| F-01 | Major | D1 | accept | 雙版 SKILL.md § Project Structure tree 將 `_overview.md` / `_conventions.md` 移到 `specs/shared/` 之下 |
| F-02 | Minor | D2 | accept (Option 2 內容式自指) | CHANGELOG R8a 段內自指改為「2026-04-24 R7 Implement Review Fix 段下的 F-03 條目」「2026-04-22 R7 Wave 3 段下的 P010 init-project `CLAUDE.md` 特別處理條目」 |
| F-03 | Minor | D2 | accept | CHANGELOG R8a 段「修改檔案」標題加註「P012 §影響範圍 11 列中 `README.md` 經 verify 無 `_共用` 引用、無需修改」 |
| 跨輪議題 | — | — | 轉記 cross-round-notes.md | README.md 引用舊檔名 `git-flow-integration.md`（P011 rename 殘留），延到 Closeout 處理 |

**統計**：accept 3 / accept-with-choice 0 / reject 0 / defer 0 / clarify 0

---

## Accept 項目的修訂紀錄

### F-01 (accept)

- **修訂檔案**：
  - `sdd-ddd-webforms-skill/SKILL.md`
  - `sdd-ddd-core-skill/SKILL.md`
- **修訂段落**：§ Project Structure Reference（WebForms line 252-253）/ § Project Structure（Core line 268-269）
- **改動摘要**：
  - WebForms：tree 內將原本平鋪於 `specs/` 直接子節點的 `_overview.md` / `_conventions.md` 移到新增的 `specs/shared/` 子節點下，註解標明「Project-level governance docs (seeded by /dflow:init-project)」
  - Core：同樣處理；tree 結構與 `templates/CLAUDE.md` 雙版 + `scaffolding/CLAUDE-md-snippet.md` WebForms 版 tree 對齊
- **Verify**：`grep _共用 sdd-ddd-{webforms,core}-skill/` 仍為 0；`templates/CLAUDE.md`、`scaffolding/CLAUDE-md-snippet.md`、SKILL.md tree 結構現完全對稱

### F-02 (accept, Option 2 內容式自指)

- **修訂檔案**：`CHANGELOG.md`
- **修訂段落**：R8a 段內「歷史條目保持原樣」段（原 line 26）
- **改動摘要**：將「line 17（R7 F-03 紀錄）、line 61（P010 init-project 紀錄）」改為「**2026-04-24 R7 Implement Review Fix 段下的 F-03 條目**、**2026-04-22 R7 Wave 3 段下的 P010 init-project `CLAUDE.md` 特別處理條目**」；末尾補一句說明 P012 §影響範圍 row 10 描述的行號是 R8a 修訂期位置
- **Option 2 選擇理由**：未來 CHANGELOG 再增段時不會再次失效（vs Option 1 「更新行號」每次新增段都要再校準）

### F-03 (accept)

- **修訂檔案**：`CHANGELOG.md`
- **修訂段落**：R8a 段「修改檔案（10 檔）」標題
- **改動摘要**：標題從「**修改檔案（10 檔）**」改為「**實際修改檔案（10 檔；P012 §影響範圍 11 列中 `README.md` 經 verify 無 `_共用` 引用、無需修改）**」，明示 10 vs 11 的差異來源

### 跨輪議題（轉記 cross-round-notes.md）

- **轉記位置**：`reviews/cross-round-notes.md` 新增「R8a Implement Review 發現、屬於 P011 後 cleanup 的備忘」段，其下「議題 4：`README.md` 引用舊檔名 `git-flow-integration.md`」
- **內容**：記錄 README.md:55 / README.md:79 兩處舊 `git-flow-integration.md` 引用；planning/project-review-brief.md:296 已自陳 `AGENTS.md` 同問題；建議 Closeout 期統一處理
- **理由**：跨輪議題 per R8a Implement Review prompt 第五步規則（「若發現 R8b 相關問題或其他議題的問題，記入 `reviews/cross-round-notes.md`，不佔本輪 Finding 數量」）；該議題與 P012 無關，屬 P011 落地後 cleanup 漏網

---

## Reject / Defer 項目備忘

無（本輪無 reject 或 defer）。

---

## Out-of-scope 項目

無。

---

## 修改檔案彙總

本輪 fix 共修改 4 檔：

| 檔案 | 對應 Finding | 修改性質 |
|---|---|---|
| `sdd-ddd-webforms-skill/SKILL.md` | F-01 | tree restructure |
| `sdd-ddd-core-skill/SKILL.md` | F-01 | tree restructure |
| `CHANGELOG.md` | F-02 + F-03 | R8a 段內兩處修訂 |
| `reviews/cross-round-notes.md` | 跨輪議題 | 新增段 + 議題 4 |

新增 1 檔：
- `reviews/round-8a-implement-review-decisions.md`（本文件）

---

## R8a 整體收尾狀態

| 項目 | 狀態 |
|---|---|
| PROPOSAL-012 狀態 | approved（R8a Approve session 已推進，2026-04-27） |
| R8a Implement | 完成（commit `2803f27`） |
| R8a Implement Review | 完成（Cursor Claude Opus 4.7，3 findings） |
| R8a Implement Review Fix | 完成（本 session） |
| 雙版 skill 目錄 `_共用` 殘留 | 0 |
| P012 核心意圖落地 | ✓ 完整 |

**R8a 全程結束。** P012 路徑英文化（`specs/_共用/` → `specs/shared/`）已 100% 落地，雙版 skill / scaffolding / templates / planning brief / CHANGELOG 全部對齊。

---

## 下一步

1. **R8a 收尾 commit**：將本輪 4 處 fix + 本決議文件 + cross-round-notes 更新打包 commit。
2. **R8b Implement 啟動條件達成**：P012 已完整落地，R8b（PROPOSAL-013）implement 路徑前提（`specs/shared/`）穩固，可放心啟動 `reviews/prompts/round-8b-implement.md` → Codex（依 4 個 sub-wave 切分執行）。
3. **R8b Implement 完成後**：仿 R8a pattern 啟動 R8b Implement Review（屆時再客製 prompt）。
4. **跨輪議題追蹤**：cross-round-notes.md 議題 4 留到 Closeout 期統一處理。

---

## 跨輪發現備忘

本輪 fix 過程中除已轉記的議題 4 外，無新跨輪發現。
