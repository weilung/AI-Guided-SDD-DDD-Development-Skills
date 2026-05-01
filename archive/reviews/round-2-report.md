# Round 2 Review Report — 變更描述格式

**審查範圍**: P002, P004
**審查日期**: 2026-04-20
**審查者**: Codex

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| 1 | 0 | 1 | 0 | 0 |

## Findings

### F-01 Step 8 的第一個驗證項未依 P004 決議優先檢查實作任務段
**嚴重度**: Major
**位置**: `sdd-ddd-webforms-skill/references/new-feature-flow.md:216-226`, `sdd-ddd-core-skill/references/new-feature-flow.md:228-240`
**對照 Proposal**: P004 的「實施紀錄」第 2 點明確寫明：Step 8（Completion）加入**第一項**驗證「實作任務」段全部勾選或未勾選項標註 follow-up。
**現況**: 兩版 `new-feature-flow.md` 的 Step 8.1 都先驗證 `Given/When/Then`、`BR-*`、`EC-*` 等項目；`實作任務` 驗證分別落在 WebForms 第 5 項、Core 第 8 項，而不是第一項。
**問題**: 這不是單純排序偏好。P004 把「實作任務」段列為核心目的之一，且實施紀錄已把 Step 8 第一項的檢查內容定義為已採納行為。現在的實作雖然有檢查 tasks，但沒有照 approved 決議把它提升為 Completion 的第一個檢查點，和 proposal 的明確要求不符。
**建議**: 將兩版 Step 8.1 的 `實作任務` 驗證移到清單第一項，並保留現有的 follow-up 標註要求；其他驗證項可依現況順延。

## 兩版一致性對照（WebForms 版 vs Core 版）

| 檢查項 | WebForms 版 | Core 版 | 一致？ |
|---|---|---|---|
| P002：`modify-existing-flow` Step 2 有 5 個 delta markers（ADDED / MODIFIED / REMOVED / RENAMED / UNCHANGED） | 有，見 `references/modify-existing-flow.md:52-89` | 有，見 `references/modify-existing-flow.md:39-78` | 是 |
| P002：`MODIFIED` 保留「原本 / 改為」對照 | 有，見 `:65-69` | 有，見 `:53-57` | 是 |
| P002：Delta 範例採 Given/When/Then，而非 RFC 2119 | 有，見 `:59-69` | 有，見 `:46-57` | 是 |
| P002：`UNCHANGED` 明示為可選 | 有，見 `:79-88` | 有，見 `:67-76` | 是 |
| P002：`RENAMED` 有使用情境說明 | 有，見 `:75-88` | 有，見 `:63-78` | 是 |
| P002：`lightweight-spec` 採精簡 delta，而非照搬完整 5-marker 格式 | 有，見 `templates/lightweight-spec.md:16-26` | 有，見 `templates/lightweight-spec.md:16-26` | 是 |
| P004：`feature-spec` 以 HTML comment 標記 phase，未重構為五段式 | 有，見 `templates/feature-spec.md:13-24,26-115` | 有，見 `templates/feature-spec.md:13-24,26-117` | 是 |
| P004：文件頂部有 template note，說明 phase 標記用途與對應 flow | 有，見 `templates/feature-spec.md:13-24` | 有，見 `templates/feature-spec.md:13-24` | 是 |
| P004：`實作任務` 段分類符合刻意差異 | `DOMAIN / PAGE / DATA / TEST`，見 `templates/feature-spec.md:115-130` | `DOMAIN / APP / INFRA / API / TEST`，見 `templates/feature-spec.md:117-135` | 是（差異符合 proposal） |
| P004：Core 版有標註建議產出順序 `DOMAIN → APP → INFRA → API` | 不適用 | 有，見 `templates/feature-spec.md:127-128` | 是 |
| P004：`new-feature-flow` Step 5 末尾有 Generate Implementation Tasks List | 有，見 `references/new-feature-flow.md:155-176` | 有，見 `references/new-feature-flow.md:148-173` | 是 |
| P004：`new-feature-flow` Step 8 第一項驗證 tasks | 無，tasks 驗證在第 5 項，見 `references/new-feature-flow.md:220-226` | 無，tasks 驗證在第 8 項，見 `references/new-feature-flow.md:232-240` | 是（皆不符 proposal） |
| P004：`modify-existing-flow` planning phase 末尾加入 tasks 產生步驟，且限定 full spec | 有，見 `references/modify-existing-flow.md:165-168` | 有，見 `references/modify-existing-flow.md:115-118` | 是 |
| P004：`modify-existing-flow` 收尾 step 加入 tasks 驗證項 | 有，見 `references/modify-existing-flow.md:226-230` | 有，見 `references/modify-existing-flow.md:152-159` | 是 |
