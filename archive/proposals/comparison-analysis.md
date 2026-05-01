# SDD/DDD Workflow vs OpenSpec：比較分析摘要

> 分析日期：2026-04-11（Proposal 清單與實施順序更新於 2026-04-16）
> 完整 OpenSpec 說明見：`openspec-reference.md`
> 具體改進項目見：PROPOSAL-001 至 PROPOSAL-008（含拆分子項）
> 整合評估詳見：`evaluation-p002-p008.md`

## 結論

OpenSpec 和我們的 SDD/DDD Skill 互補性強。OpenSpec 擅長**變更管理**（delta spec、source of truth、tasks 追蹤），我們擅長**深度引導**（DDD 建模、架構規範、遷移策略）。

融合策略：把 OpenSpec 的變更管理機制融入我們的 DDD 引導框架，而非取代。

## 八個比較面向速覽

| 面向 | 我們 vs OpenSpec | 行動 |
|---|---|---|
| 1. 規格結構 | 我們單一模板 vs 他們四檔案分離 | 借鏡：delta 格式、tasks 段、source of truth → P002, P003, P004 |
| 2. AI 引導 | 我們深度引導 vs 他們文件產生 | 保持優勢，加指令化入口 + 透明化 → P001；微調 CLAUDE.md → P007c |
| 3. 領域建模 | 我們完整 DDD vs 他們不涉及 | 保持優勢，加分級 → P006a |
| 4. 變更管理 | 我們手動歸檔 vs 他們自動 merge | 借鏡：完成 checklist + 驗證 → P005a/P005b；drift 驗證 → P008 |
| 5. Git 整合 | 我們完整 vs 他們不管 | 保持優勢 |
| 6. 品質把關 | 我們全鏈路 vs 他們格式驗證 | PR Review 加 spec intent → P007b |
| 7. 導入門檻 | 我們較高 vs 他們極低 | Ceremony Scaling 分級 → P006a；漸進導入 defer（方案 γ）|
| 8. Brownfield | 兩邊互補 | 強化逆向文件化 → P007a |

## Proposal 清單

| # | 名稱 | 排程 | 狀態 |
|---|---|---|---|
| P001 | Skill 指令化入口 + 流程透明化（Hybrid） | Wave A | approved（已定稿） |
| P002 | Delta Spec 格式（含 RENAMED + UNCHANGED） | Wave A | approved |
| P003 | 系統現狀 Source of Truth（A+C 結構 + pilot） | Wave B | approved |
| P004 | Spec 模板精簡（tasks + phase 標記） | Wave A | approved |
| P005a | 完成流程：驗證 + 文件更新 + 歸檔 checklist | Wave A | approved |
| P005b | 完成流程：behavior.md + Tasks 補完 | Wave B | approved |
| P006a | Ceremony Scaling 加 DDD 深度欄 | Wave C | approved |
| P007a | Brownfield 系統性逆向文件化 | Wave C | approved |
| P007b | PR Review Spec Intent（Step 0） | Wave A | approved |
| P007c | CLAUDE.md 標題分段強化 | Wave C | approved |
| P008 | Drift Verification（`/dflow:verify`） | Wave C | approved |
| ~~P006~~ | ~~Ceremony Scaling 擴展~~ | — | superseded → P006a + P006-2 defer |
| ~~P007~~ | ~~Brownfield 與 Review 強化~~ | — | superseded → P007a/b/c |

## 實施順序（Wave A→D）

```
Wave A（基礎結構 + 低成本高價值，可並行）
  P001 → P002 → P004 精簡 → P005a → P007b

  ── Checkpoint A ──
  驗證 checklist 和 Spec Intent Review 是否被實際採用。

Wave B（源於 Wave A）
  P003 pilot 單一 BC → P005b

  ── Checkpoint B ──
  確認 behavior.md 維護成本、A+C 結構實效。

Wave C（獨立微調，可並行）
  P006a、P007a、P007c、P008

  ── Checkpoint C ──
  累積複雜度審視。

Wave D（實證後決定）
  Skill 精簡化審查、P003 全面 rollout、P006-2 處理、P008 語意層
```

詳細依賴圖、Checkpoint 判斷原則、Wave D 觸發條件見 `evaluation-p002-p008.md` 第四、六節。
