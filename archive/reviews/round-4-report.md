# Round 4 Review Report — Source of Truth 與漂移驗證

**審查範圍**: P003, P008
**審查日期**: 2026-04-20
**審查者**: Codex

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| 2 | 0 | 2 | 0 | 0 |

## Findings

### F-01 `/dflow:verify` 在 `SKILL.md` 被歸為「active workflow control command」，和其 standalone 定位互相衝突
**嚴重度**: Major
**位置**: `sdd-ddd-webforms-skill/SKILL.md:88-92`, `sdd-ddd-core-skill/SKILL.md:103-107`
**對照 Proposal**: P008 的「問題描述 / 提議的解法」段落：`/dflow:verify [<bc>]` 是供 PR 前、release 前、refactor 後獨立執行的低成本驗證機制
**現況**: 兩版 `SKILL.md` 都把 `/dflow:verify [<bc>]` 放在 `**Control commands** — manage an active workflow` 小節下，但同一行又補註 `standalone, no active workflow needed`。
**問題**: 這不是單純用語鬆散，而是同一個入口的使用前提互相打架。P008 的定位是「可單獨執行的 drift verification」，現在卻先被 `manage an active workflow` 這個分類框住，開發者很容易誤讀成只有 workflow 進行中才能用，削弱 proposal 想建立的 pre-PR / pre-release safety net。
**建議**: 把 `/dflow:verify` 從 `Control commands` 重新分類成獨立小節（例如 `Standalone verification command`），或把小節標題改成不暗示「必須有 active workflow」的分類名稱，避免和 bullet 本身的 standalone 描述互相衝突。

### F-02 drift verification 的 BR-ID 擷取規則過寬，會讓「有無對應情境 section」檢查失真
**嚴重度**: Major
**位置**: `sdd-ddd-webforms-skill/references/drift-verification.md:54-66,75-79`, `sdd-ddd-core-skill/references/drift-verification.md:54-66,75-79`
**對照 Proposal**: P008 的「機械層驗證內容」與「輸出範例」段落：要求檢查 `rules.md` 的 BR-ID 在 `behavior.md` 中是否有**對應情境**，範例也明寫 `behavior.md has no section for BR-003`
**現況**: Step 3 要求從 `behavior.md` 擷取「section headings or body text」中的所有 `BR-*`；Step 4 的 forward / reverse check 也只以「appears in behavior.md」為通過條件。
**問題**: 這會把「正文順手提到某個 BR-ID」和「真的有一個對應 scenario section」混為一談。結果是：`behavior.md` 即使沒有 BR-003 的正式情境，只要在別段內文提到 `BR-003`，forward check 就可能誤判通過；reverse check 也會因為任意正文提及而產生噪音。這直接削弱 P008 最核心的機械層檢查準確度。
**建議**: 把 `behavior.md` 的主要擷取基準收斂為「BR-ID 對應的 scenario section / heading」，正文中的 BR-ID 僅作為補充訊號，不可單獨滿足 forward check。報告格式也應明確區分：
1. `section exists for BR-xxx`
2. `body text additionally references BR-xxx`
這樣才能符合 proposal 對「對應情境」與「反向檢查」的原始意圖。

## 兩版一致性對照（WebForms 版 vs Core 版）

| 檢查項 | WebForms 版 | Core 版 | 一致？ |
|---|---|---|---|
| `templates/behavior.md` 是否說明 consolidated current truth、A+C 關係、merge final state | 有，見 `templates/behavior.md:1-14,48-57` | 有，見 `templates/behavior.md:1-14,50-61` | ✓ |
| `templates/behavior.md` 是否按 feature area 組織，且每個 BR-ID 有 Given/When/Then section | 有，見 `templates/behavior.md:18-44` | 有，見 `templates/behavior.md:18-47` | ✓ |
| Core 版是否明確補 Aggregate state transition / Domain Event | 不適用 | 有，見 `templates/behavior.md:22-25,43-46,59-60` | ✓ |
| `templates/context-definition.md` 的「關鍵業務規則」是否提及 `behavior.md` | 有，見 `templates/context-definition.md:43-48` | 有，見 `templates/context-definition.md:43-48` | ✓ |
| `SKILL.md` 的 `Behavior Source of Truth` 段是否說明 `rules.md` vs `behavior.md` 分工，並對照 OpenSpec | 有，見 `SKILL.md:241-250` | 有，見 `SKILL.md:329-338` | ✓ |
| Project Structure / Templates 是否納入 `behavior.md` | 有，見 `SKILL.md:214-223,321-329` | 有，見 `SKILL.md:234-242,390-399` | ✓ |
| `/dflow:verify` 是否加入 frontmatter、Decision Tree、Slash Commands、Reference Files | 有，見 `SKILL.md:3-7,43-49,80-92,309-319` | 有，見 `SKILL.md:3-7,60-66,95-107,379-388` | ✓ |
| `/dflow:verify` 的分類是否自洽 | 放在 `manage an active workflow` 下，但 bullet 又寫 standalone | 同樣放在 `manage an active workflow` 下，但 bullet 又寫 standalone | 是（皆有 F-01） |
| `references/drift-verification.md` 是否具備 5 步驟流程 | 有，見 `references/drift-verification.md:35-93` | 有，見 `references/drift-verification.md:35-93` | ✓ |
| 三項機械檢查是否都有列出 | 有，見 `references/drift-verification.md:11-18,58-66` | 有，見 `references/drift-verification.md:11-18,58-66` | ✓ |
| BR-ID 對應檢查是否精準鎖定「對應 scenario section」 | 否，Step 3/4 把 body text 也算進主要比對集合 | 否，Step 3/4 同樣把 body text 也算進主要比對集合 | 是（皆有 F-02） |
| 是否明確排除語意層驗證 | 有，見 `references/drift-verification.md:19-24` | 有，見 `references/drift-verification.md:19-24` | ✓ |
| Core 版是否明確標示 `events.md` 交叉引用警告為額外 warning | 不適用 | 有，見 `references/drift-verification.md:95-103` | ✓ |
