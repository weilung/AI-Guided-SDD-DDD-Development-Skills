# Round 7 Decisions — Draft Proposal Review (P009 / P010 / P011)

**對應 review 報告**: `reviews/round-7-report.md`
**決議日期**: 2026-04-22（R7 Approve session）
**決議者**: 使用者（Claude 整理選項 + 當場修訂 proposal）
**檔案狀態**: **進行中**（每處理完一項 finding 當場追加；全部處理完後補 Proposal 狀態推進紀錄 + 下一步段）

---

## 決議彙總（陸續更新）

| Finding | Proposal | 嚴重度 | 維度 | 決議 | 說明 |
|---|---|---|---|---|---|
| F-01 | 009 | Major | D1 | **accept-with-choice（Path A-3）** | 選定：phase-spec 由 feature-spec `git mv` 重命名 + 新增 Delta 段；lightweight-spec 保留獨立檔並置於 feature 目錄內；`_index.md` 六段範本（含 Current BR Snapshot）；三層 Ceremony T1/T2/T3 |
| F-02 | 009 | Major | D1 | _（待決）_ | |
| F-03 | 009 | Major | D1 | _（待決）_ | |
| F-04 | 009/010/011 | Major | D3 | _（待決）_ | |
| F-05 | 010 | Major | D4 | _（待決）_ | |
| F-06 | 011 | Major | D1 | _（待決）_ | |
| F-07 | 010 | Minor | D2 | _（待決）_ | |

---

## Accept / Accept-with-choice 項目的修訂紀錄

### F-01（accept-with-choice, Path A-3：phase-spec 重命名 + Delta 段 + _index.md 含 BR Snapshot + 三層 Ceremony）

**背景脈絡**：
- Codex 指出 P009 § 影響範圍的 Templates 區塊對 phase-spec / feature-spec / lightweight-spec 的終局策略仍寫三種可能（新增/重命名、刪除/重命名、修改或保留），把「是否保留 lightweight 獨立模板」留到「實施時評估」。這不是可延後的彈性，會直接決定下游 flow / SKILL.md 的修訂無法一致落稿。
- R7 Approve session 討論中，使用者延伸發現兩個關聯 gap：(a) phase-spec 範本沒有 Delta 機制，phase 2+ 的 BR-ID 會撞 / 看不出演化 / 讓 drift-verification 誤報；(b) 現況 ceremony 只有兩層（new-feature heavy / bug-fix lightweight），沒有涵蓋「按鈕顏色、文案修正」這類無 BR 變動的表層修改。
- 討論中也確認 Dflow 本來就是「workflow 按 feature 切、system state 按 BC 切」的雙軸設計，BC 層系統狀態由 P003 `rules.md` / `behavior.md` 承擔（Step 8.3 / Step 5.3 的既有 sync 機制），F-01 只需把 phase 層 Delta + feature 層 Snapshot 補起來，不需新設系統層機制。

**選定 Path A-3 的終局決定**：

1. **phase-spec.md**：由 `templates/feature-spec.md` 用 `git mv` 重命名；沿用現有全部章節；於「業務規則」之後新增「Delta from prior phases」選用段（首 phase 免填，phase 2+ 必填；格式沿用 P002/P004 的 ADDED / MODIFIED / REMOVED / RENAMED / UNCHANGED）；Delta 僅記本 phase 變化，不累積歷史
2. **`_index.md`**（新增範本）：六段結構——Metadata / 目標與範圍 / Phase Specs / **Current BR Snapshot**（feature 層累積狀態，AI 在 new-phase 進入 / 完成 phase-spec / T2 lightweight 定稿時 regenerate）/ 輕量修改紀錄（T2 外連 + T3 inline 雙用）/ 接續入口
3. **lightweight-spec.md**：保留獨立範本（內容不變）；實例化檔置於 feature 目錄內，命名 `lightweight-{YYYY-MM-DD}-{slug}.md` 或 `BUG-{NUMBER}-{slug}.md`；對應 T2 ceremony
4. **三層 Ceremony（T1 Heavy / T2 Light / T3 Trivial）**：T3 判準四條同時成立才算（無 BR 變動、無 Domain 概念動、無資料結構動、只改 UI 表層/註解/格式化）；低於 T3 的純 typo / 格式化 commit 直接 `git commit` 不走 Dflow
5. **`/dflow:finish-feature`**：收尾時依 `_index.md` BR Snapshot 同步 BR 變化到對應 BC 的 `rules.md` / `behavior.md`，延續 Step 8.3 / Step 5.3 既有機制，不新設流程

**修訂檔案**: `proposals/PROPOSAL-009-feature-directory-and-phases.md`

**具體修訂段落**（7 處）：
- § A 結構類 / 決議 4（重寫）：明列 feature 目錄含三類檔案（_index / phase-spec / lightweight-spec）+ 指向 § H
- § C 命令類 / 決議 10（finish-feature 產出補一項）：新增「依 Current BR Snapshot 同步到 BC 層」動作
- § D 流程類（新增三項 bullet）：`/dflow:new-phase` 進入時 refresh BR Snapshot；`/dflow:finish-feature` 同步 BC 層（延續既有機制）；`modify-existing-flow` 加 Ceremony 判準表
- **§ H（全新段）範本與 Ceremony 終局**：決議 12（phase-spec 重命名 + Delta 段）/ 決議 13（三層 Ceremony + 判準 + Dflow 不走的底線）/ 決議 14（`_index.md` 六段結構）/ 決議 15（lightweight-spec 實例化位置與命名）
- § 影響範圍 Flow 段（四行描述更新）：new-feature-flow、modify-existing-flow、new-phase-flow（新增）、finish-feature-flow（新增）
- § 影響範圍 Templates 段（六行描述定稿）：_index.md / phase-spec.md / feature-spec.md / lightweight-spec.md 終局全部寫死
- § 關鍵假設 5：從「兩者關係由實施時決定」改為「T2/T3 定案 + 長期測試點」

**交叉影響**: 無跨 proposal 修訂（僅動 P009）。但 F-01 的 A-3 終局同時**預先解決了 F-03 的大部分問題**（drift-verification 未來對照的對象從「跨多份 phase spec」縮回「_index.md 的 BR Snapshot 一張表」，scope 不再混入 feature spec 聚合器），F-03 處理時再確認。

**Cross-round-notes 延後項目**（2 項）：
- `lightweight-spec.md` 缺 OpenSpec `tasks.md` 對應的「實作任務」段（P002/P004 當初未納入；本輪不擴大範圍處理）
- 三層 Ceremony T2/T3 邊界在長期實戰的調整（R7 Implement 後蹲點觀察）

---

## Reject / Defer 項目備忘

_（暫無，待後續 finding 產出時填入）_

---

## Out-of-scope 項目

_（暫無；若後續討論產生，轉記 `reviews/cross-round-notes.md`）_

---

## Proposal 狀態推進紀錄

_（待全部 finding 處理完後填入）_

---

## 下一步

_（待全部 finding 處理完後填入）_

---

## 跨輪發現備忘

R7 Approve 過程中延後處理的議題已寫入 `reviews/cross-round-notes.md`（見 § R7 Approve 發現、延後處理的議題）。
