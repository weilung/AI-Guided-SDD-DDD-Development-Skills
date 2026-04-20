# Round 1 Decisions — 流程入口與透明度

**對應 review 報告**: `reviews/round-1-report.md`
**決議日期**: 2026-04-20
**決議者**: 使用者（Claude 整理）
**審查範圍**: P001（Workflow Transparency）、P007b（PR Review Spec Intent）— 僅英文 Skill

## 決議彙總

| Finding | 嚴重度 | 決議 | 說明 |
|---|---|---|---|
| F-01 | Major | **accept** | Core SKILL.md 的 `/dflow:status` 範例 Step 3 名稱沿用 WebForms 的 `Domain Concept Discovery`,與 Core flow 檔正式名稱 `Domain Modeling` 不一致,照建議改。 |

**統計**:accept 1 / reject 0 / defer 0 / clarify 0 / out-of-scope 0。

---

## Accept 項目的實作指引(交給 Implement session)

### F-01 — Core SKILL.md `/dflow:status` 範例 Step 3 名稱對齊

**修改檔案**: `sdd-ddd-core-skill/SKILL.md`

**背景**(讓 Implement session 不必回翻 review 報告):
- Core 版 `references/new-feature-flow.md:47,49,85` 的 Step 3 正式名稱為 **`Domain Modeling`**(聚焦 Aggregate / VO / Events 識別)。
- Core `SKILL.md:173-194` 的 `/dflow:status` Response Format 範例,Step 3 仍寫成 `Domain Concept Discovery`(這是 WebForms 版的用語,屬於範例複製後未對齊)。
- WebForms 版兩處一致(`Domain Concept Discovery`),不需改動。兩版 Step 3 名稱**刻意不同**:WebForms Step 3 做概念盤點、Core Step 3 做完整戰術建模,這是設計意圖,不是筆誤。

**具體改動**:

1. **Line 179**:
   - 原:`Current step: Step 3 — Domain Concept Discovery`
   - 改:`Current step: Step 3 — Domain Modeling`

2. **Line 186**:
   - 原:`- [ ] Step 3: Domain Concept Discovery — identifying Aggregate / VO / Events`
   - 改:`- [ ] Step 3: Domain Modeling — identifying Aggregate / VO / Events`

**注意事項**:
- 只改 Core 版這兩行字串;**不要動 WebForms 版**(WebForms 的 `Domain Concept Discovery` 是正確的)。
- 改完後請順手複檢 Core `SKILL.md:173-194` 範例其餘 step 名稱(Step 1、2、4、5、6、7、8)是否都對應 Core `references/new-feature-flow.md` 的正式標題。本 Finding 只明確指出 Step 3,但由於範例整體是從 WebForms 複製過去的風險存在,一次檢查較穩妥。若發現其他 step 名稱也有不一致,列為本次一併修正(不另開 Finding),並在 CHANGELOG 條目中註明。
- 不要修改繁中版(`sdd-ddd-core-skill-tw/`),繁中版延後至 Post-Review Closeout 階段單向同步。

---

## Reject / Defer 項目備忘

本輪無 reject 或 defer 項目。

---

## Out-of-scope 項目

本輪無越界 Finding。Codex 正確遵守了「僅審英文 Skill」的範圍邊界;繁中版與 tutorial 的問題未被列入本輪 Finding。

---

## 跨輪發現備忘

本輪 approve 階段未新增任何跨輪發現。`reviews/cross-round-notes.md` 已存在兩項 R1 期間由 Codex 記入的備忘(屬於 R4、R5 範圍),本輪未變動該檔。
