## R1 發現、屬於 R4 的備忘

- 位置：`sdd-ddd-webforms-skill/SKILL.md:6-7`, `sdd-ddd-webforms-skill/SKILL.md:48`, `sdd-ddd-webforms-skill/SKILL.md:91`, `sdd-ddd-core-skill/SKILL.md:6-7`, `sdd-ddd-core-skill/SKILL.md:65`, `sdd-ddd-core-skill/SKILL.md:106`
- 描述：兩版 `SKILL.md` 都已納入 `/dflow:verify` 與 drift verification 入口。這屬於 P008 / R4 範圍，不列入本輪 finding，但 R4 應確認其指令定義、Decision Tree 與參照檔是否完整對齊。
- 建議轉交：R4（Source of Truth 與漂移驗證）
- 狀態加註（Global Sweep 2026-04-21）：已由 R4 確認並處理。R4 F-01 把 `/dflow:verify` 從 Control commands 移至新設的 Standalone commands 小節，解決分類自相矛盾問題；Decision Tree、Slash Commands、Reference Files 表格的完整對齊皆在 R4 審查範圍內通過（見 `reviews/round-4-decisions.md`）。

## R1 發現、屬於 R5 的備忘

- 位置：`sdd-ddd-webforms-skill/references/modify-existing-flow.md:91-110`
- 描述：WebForms `modify-existing-flow` 已含「Systematic Baseline Capture」段落，屬於 P007a 的 brownfield reverse-documentation 內容，不列入本輪 finding。R5 應確認它是否忠於 P007a 邊界，且沒有和本輪的 Workflow Transparency 規則衝突。
- 建議轉交：R5（Ceremony 與逆向文件化）
- 狀態加註（Global Sweep 2026-04-21）：已由 R5 確認。R5 Codex 報告 0 findings，並在「單版本設計說明」段主動確認 P007a Systematic Baseline Capture 忠於邊界，與 Workflow Transparency 無衝突（見 `reviews/round-5-decisions.md` 總體結論第 2 點與第 4 點）。

## R7 Approve 發現、延後處理的議題

### 議題 1：`lightweight-spec.md` 缺 OpenSpec `tasks.md` 對應段

- 來源：R7 Approve F-01 討論（2026-04-22，使用者在對照 OpenSpec 四檔模型時提出）
- 描述：OpenSpec 的 `tasks.md` 對應 Dflow 的 phase-spec.md「實作任務」段，但現況 `lightweight-spec.md` 沒有對應的實作任務 checklist。P002 / P004 當初設計 lightweight-spec 時未納入，但 lightweight 修改（T2 tier）仍可能需要 AI 產出「要改哪些檔」清單給開發者追蹤。
- 建議下一步：另開 proposal 或於 Wave D 精簡化審查時處理；評估「lightweight-spec 末尾加一個精簡的『實作任務』段」是否值得
- 影響檔案：`sdd-ddd-*-skill/templates/lightweight-spec.md`（雙版）

### 議題 2：三層 Ceremony（T1/T2/T3）判準在長期實戰下的調整

- 來源：R7 Approve F-01 決議（PROPOSAL-009 § H 決議 13）
- 描述：R7 定案的 T1/T2/T3 判準是首次在 Dflow 明文化，T2 與 T3 的邊界（是否涉及 BR Delta）在實戰中是否會出現灰色地帶待觀察。例如：修改 UI 驗證提示文字（純文字）但底層驗證規則未變，算 T2 還是 T3？
- 建議下一步：R7 Implement 完成後蹲點觀察 1-2 個 feature 跨 T2/T3 的實例，若邊界模糊再回頭調整
- 影響檔案：`sdd-ddd-*-skill/references/modify-existing-flow.md`（雙版）、`SKILL.md`（Ceremony Scaling 段）
