# SDD/DDD Workflow vs OpenSpec：比較分析摘要

> 分析日期：2026-04-11
> 完整 OpenSpec 說明見：`openspec-reference.md`
> 具體改進項目見：PROPOSAL-002 至 PROPOSAL-007

## 結論

OpenSpec 和我們的 SDD/DDD Skill 互補性強。OpenSpec 擅長**變更管理**（delta spec、source of truth、tasks 追蹤），我們擅長**深度引導**（DDD 建模、架構規範、遷移策略）。

融合策略：把 OpenSpec 的變更管理機制融入我們的 DDD 引導框架，而非取代。

## 八個比較面向速覽

| 面向 | 我們 vs OpenSpec | 行動 |
|---|---|---|
| 1. 規格結構 | 我們單一模板 vs 他們四檔案分離 | 借鏡：delta 格式、tasks 段、source of truth → P002, P003, P004 |
| 2. AI 引導 | 我們深度引導 vs 他們文件產生 | 保持優勢，微調 CLAUDE.md → P007 |
| 3. 領域建模 | 我們完整 DDD vs 他們不涉及 | 保持優勢，加分級 → P006 |
| 4. 變更管理 | 我們手動歸檔 vs 他們自動 merge | 借鏡：完成 checklist + 驗證 → P005 |
| 5. Git 整合 | 我們完整 vs 他們不管 | 保持優勢 |
| 6. 品質把關 | 我們全鏈路 vs 他們格式驗證 | 小改善：PR Review 加 spec intent → P007 |
| 7. 導入門檻 | 我們較高 vs 他們極低 | 加漸進導入路徑 → P006 |
| 8. Brownfield | 兩邊互補 | 強化逆向文件化 → P007 |

## Proposal 清單

| # | 名稱 | 優先順序 | 狀態 |
|---|---|---|---|
| P002 | Delta Spec 格式 | 高 | draft |
| P003 | 系統現狀 Source of Truth | 高 | draft |
| P004 | Spec 模板重構 | 中 | draft |
| P005 | 完成流程強化 | 高（工作量小） | draft |
| P006 | Ceremony Scaling 擴展 | 中 | draft |
| P007 | Brownfield 與 Review 強化 | 低 | draft |

高優先項之間有依賴關係：P003（source of truth）需要 P002（delta 格式）才完整，P005（完成流程）需要 P003 和 P004 才知道完成時要更新什麼。建議實施順序：P002 → P003 → P004 → P005 → P006 → P007。
