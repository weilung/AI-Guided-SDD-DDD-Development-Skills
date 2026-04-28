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

### 議題 3：Dflow 長期散佈策略——npm install + 多 AI 工具相容

- 來源：R7 Approve F-07 附帶討論（2026-04-22，使用者在讀 README 安裝路徑矛盾時延伸提出）
- 描述：使用者提出長期散佈構想：
  - **Dflow 以 npm 套件形式散佈**，降低對 Claude Code skill 分發機制的依賴
  - 支援多 AI 工具：Claude Code / Codex / Gemini / GitHub Copilot 共用同一份 Dflow skill 內容
  - `/dflow:init-project` Q5 的選擇性 scaffolding（如 Git-principles-gitflow / Git-principles-trunk 二版本）可由 npm install 時的 CLI 參數 / 互動選項取代，更符合開源生態慣例
  - Git-principles 等 scaffolding 名稱若中文化（例如 `Git-原則.md`）對國際散佈不利，應在散佈層保持英文
  - 使用者原話：「最終我是希望可以 npm 安裝，也同時也可以在 Codex、Gemini、GitHub Copilot 中使用，另外 npm 也可以提供一些選擇...這實施方式我覺得可能最適合的是 npm install。dflow init 時候處理，未必比 npm install 好」
- **明確定位**：本議題**不列入 R7 範圍**（使用者自己強調「這項應該是最後處理，不在此案」）
- 建議下一步：R7 Implement 完成、R1–R6 全體 Closeout 後，於 Wave D 精簡化審查階段或之後的獨立 wave 開新 proposal 評估：
  - npm 套件結構 vs 現況 skill 目錄結構的 mapping
  - 多 AI 工具相容所需的「skill 格式抽象層」
  - `/dflow:init-project` 與 npm install 的職責切分（哪些動作留 `/dflow:init-project`，哪些改由 npm install）
  - scaffolding 範本命名國際化（移除中文檔名如 `Git-原則.md`）
- 影響範圍：repo 結構本身、README、可能新增 `package.json` / bin entrypoints、scaffolding 檔命名全面英文化

## R8a Implement Review 發現、屬於 P011 後 cleanup 的備忘

### 議題 4：`README.md` 引用舊檔名 `git-flow-integration.md`（P011 rename 後未同步）

- 來源：R8a Implement Review（2026-04-27，Cursor Claude Opus 4.7）跨輪議題段
- 位置：`README.md:55`、`README.md:79`；`planning/project-review-brief.md:296` 也已自陳觀察到「`AGENTS.md` 裡仍提到 `git-flow-integration.md`」
- 描述：P011（Git Flow Decoupling）已將 `references/git-flow-integration.md` rename 為 `references/git-integration.md`，但 README.md 內的 references 表（兩版各一列）仍寫舊檔名 `git-flow-integration.md`。屬 P011 落地後的 cleanup 漏網。
- 建議下一步：Closeout 期間（Global Sweep 或 README 重寫前）一併處理。修正範圍：兩處 `git-flow-integration.md` → `git-integration.md`；同步描述去除「Git Flow」字眼以反映 P011 中立化原則。
- 影響檔案：`README.md`、可能含 `AGENTS.md`（待 verify）
- **不擋 R8a 進度**：本議題與 P012 無關；R8a Implement Review F-01 已修，整體 R8a 落地不受此議題影響
