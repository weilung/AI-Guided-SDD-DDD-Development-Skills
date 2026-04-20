# Review Archive

此目錄存放因 review 範圍調整而失效的舊產物。

## 2026-04-20：R1 範圍調整為 English-only

**背景**：R1 原本包含繁中版（`sdd-ddd-*-tw/`）檔案同步檢查。執行時發現 Skill 還在持續修改的過程中要求 TW 即時同步，造成移動標靶問題——Skill 每變一次，TW 得跟著變、review 又要比對。決定將繁中版和 tutorial 延後至所有 R1-R6 英文 Skill review 完成後的 **Post-Review Closeout** 階段一次處理。

**被歸檔的檔案**：

- `round-1-report-v1-deprecated.md`：Codex 基於舊 scope 產出的 review 報告，F-01 即為 TW drift 誤報。
- `cross-round-notes-v1-deprecated.md`：同一次 R1 review 產生的跨輪備忘。

**後續動作**：`reviews/prompts/round-1-*.md` 依新 scope 重寫，Codex 重新 run R1 review，會產生新的 `reviews/round-1-report.md`。
