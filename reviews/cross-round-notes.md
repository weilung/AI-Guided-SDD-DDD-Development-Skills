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
