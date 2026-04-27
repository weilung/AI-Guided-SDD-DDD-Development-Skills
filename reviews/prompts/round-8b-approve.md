# Round 8b Approve Prompt — PROPOSAL-013 Draft Findings 決議（給 Claude Code 新 session 使用）

> **使用方式**：開新 Claude Code session，把下方「---BEGIN PROMPT---」和「---END PROMPT---」之間的內容整段複製作為第一句話。
> **產出位置**：
>   - `reviews/round-8b-decisions.md`（主要產出）
>   - PROPOSAL-013 的**修訂**（若有 accept / accept-with-choice finding，**直接在本 session 改 proposal**）
>   - 若所有 finding 處理完畢且 proposal 狀態滿足推進條件，將 proposal 狀態從 `draft` 推進到 `approved`
> **與 R8a Approve 的差異**：
>   - R8a：scope 小，多為精準度型 finding
>   - R8b：scope 大，包含**設計決策型** finding（canonical English structural language / `<!-- dflow:section X -->` anchor / TEMPLATE-COVERAGE.md / TEMPLATE-LANGUAGE-GLOSSARY.md 4 項新概念），且涉及對 P001-P012 既有機制的相容性決議
> **本輪 finding 概況**：待 R8b Review 完成後填入；預期 8-20 個 finding，涵蓋 7 個 dimensions
> **預估時程**：~90-150 分鐘（finding 數量多、設計決策型需深入討論）

---BEGIN PROMPT---

這是 PROPOSAL-013（系統文件樣板覆蓋與 Template Coverage Matrix）draft review 的**決議階段**（Round 8b / Approve）。本輪是 R8 兩波中的第二波（樣板覆蓋，依賴 P012 路徑決策）。

與 R1–R6 不同：本輪決議後，若使用者確認 accept，你需要**直接在本 session 內修訂 PROPOSAL-013**，並在全部處理完後推進 proposal 狀態。R8b 的設計決策密度高於 R8a，預期會有多個 `accept-with-choice` 類型 finding 需要使用者拍板。

## 第一步：依序讀入下列檔案

1. `reviews/round-8b-report.md`（Reviewer 產出的審查報告——主要分析對象）
2. `reviews/prompts/round-8b-review.md`（R8b Review 的邊界定義——特別第二步 Dimension 5 既有機制相容性 8 項 C 檢查點 + Dimension 6 設計決策評估表）
3. `proposals/PROPOSAL-013-system-document-template-coverage.md`（待修訂）
4. `proposals/PROPOSAL-012-distribution-friendly-paths.md`（背景：P013 的路徑前提；若 finding 涉及 P012 依賴宣稱時引用）
5. `planning/public-distribution-backlog.md`（背景：邊界對方檔；若 finding 涉及內容語言策略 / Template Language Glossary 邊界爭議時引用）
6. `proposals/review-workflow.md`（流程規範骨幹）
7. **既有 proposal**（按 finding 引用情況選讀；以下為高機率引用清單）：
   - `proposals/PROPOSAL-003-*.md`（rules / behavior source-of-truth）
   - `proposals/PROPOSAL-004-*.md`（lightweight-spec 設計理念）
   - `proposals/PROPOSAL-008-*.md`（drift-verification 機制）
   - `proposals/PROPOSAL-009-feature-directory-and-phases.md`（特別 § H Lightweight Changes、§ D _index.md 設計）
   - `proposals/PROPOSAL-010-dflow-init-and-scaffolding.md`（特別 mandatory baseline、scaffolding 邊界）
8. `reviews/cross-round-notes.md`（議題 1 是 P013 § 7 的源頭背景；若 finding 處理 lightweight task 衝突時引用）

若 `reviews/round-8b-report.md` 不存在，回報使用者「找不到 R8b review 報告，是否 review 尚未完成？」然後停止。

## 第二步：本輪範圍邊界

**在範圍內**：
- `reviews/round-8b-report.md` 的所有 Finding 的決議
- 對 accept / accept-with-choice 的 Finding：**直接修訂 PROPOSAL-013 對應段落**
- 全部處理完後推進 PROPOSAL-013 狀態（draft → approved，條件見第七步）

**不在範圍內**：
- **PROPOSAL-012**（R8a 的 target，本輪不處理；若 R8a 仍未完成、P013 對 P012 的依賴宣稱無法 verify，相關 finding 標 clarify）
- **任何 skill 檔**（`sdd-ddd-*-skill/` 下所有檔）— 改 skill 是 R8b Implement 階段的工作（**最大批次將由 Codex 一次包完**）
- **新增的 templates / scaffolding / 維護文件實體**（TEMPLATE-COVERAGE.md / TEMPLATE-LANGUAGE-GLOSSARY.md / 12 份新 template）— 這些是 R8b Implement 階段的產出，本輪只在 proposal 內定義其結構
- **繁中版**（`*-tw/`）— 延到 Closeout C1
- 超出 review 報告 Finding 的議題 — 若有發現，轉記 `reviews/cross-round-notes.md`

若使用者在互動中提出「順便寫一份 template 看看」類需求，**提醒使用者這是 Implement 階段工作**，不在本輪處理。

## 第三步：誤判警覺（R8b 版）

R8b finding 多、涉及新概念，留意以下可能：

1. **「既有機制相容性」的判定深度**
   - Dimension 5 的 8 個 C 檢查點需要 reviewer 引用 P003/P004/P008/P009/P010 的具體段落佐證
   - 若 reviewer 只給結論 ✓/✗ 而沒附段落引用，本 session 你需要自行補 verify（讀對應 proposal）
   - 若補 verify 後與 reviewer 結論不符，先列出衝突點再諮詢使用者

2. **設計決策型 finding 的「方向不對」vs「細節錯誤」**
   - Canonical English / anchors / COVERAGE / GLOSSARY 四項是方向性決策
   - 若 reviewer 標 Critical / Major 但實質是「方向我不認同」（而非「設計有 bug」），降為 Question 並由使用者拍板方向
   - R8b Review prompt 已要求 reviewer 對方向疑慮優先標 Question，但仍可能出現誤判

3. **C-04（P009 § H Lightweight Changes vs P013 § 7 Implementation Tasks）的判定**
   - 兩者是不同層級的記錄（feature 層級 vs 單一 spec 層級），可能不衝突也可能重複
   - 若 reviewer 判「衝突」，verify P009 § H 實際內容後再判斷
   - 若實際是「協作而非衝突」，可能是 reviewer 誤判 → reject 或重新詮釋

4. **Dimension 6 設計決策的 path 選擇**
   - 4 項新概念的設計決策，每項可能有 2-4 個 path
   - 例如「TEMPLATE-COVERAGE.md 是否與 GLOSSARY 合併」可能有 path：(a) 兩份分開 / (b) 合併為 TEMPLATE-CONTRACTS.md / (c) 都不要、只在 SKILL.md 一段帶過
   - 對這類設計決策，Reviewer 不該替使用者決定；使用者拍板才是正確流程

5. **影響範圍對照表的「實際工作量評估」**
   - Reviewer 可能對既有 templates「修改 / 檢查」的工作量評估不準（reviewer 沒實際操作）
   - 若 reviewer 把工作量大幅低估或高估，此判斷 informational only，不影響 finding 決議；但需在 decisions 文件記錄真實預估，供 R8b Implement 規劃 sub-waves 用

6. **「Reviewer 沒填對照表」的處理**
   - R8b Review prompt 要求 3 個對照表必填（既有機制相容性 / 設計決策評估 / 影響範圍）
   - 若 reviewer 漏填 / 簡略填，本 session 你需要自行補完關鍵列；若補完後與 reviewer 結論不符，回報使用者

7. **越界評論 P013 設計方向**
   - 若 reviewer 出現「P013 不該做」「補樣板不重要」等越界評論，回到「P013 已由 R8a 之後的設計判斷背書（見 cross-round-notes.md 議題 1 + project-review-brief.md）」拒絕

## 第四步：你的角色定位

你有**雙重身份**：

1. **分析助理**：逐項分析 finding、提供建議決議、列出選項讓使用者拍板（特別對設計決策型 finding 提供 path table）
2. **Proposal 修訂執行者**：使用者確認 accept 後，立即用 Edit 工具修訂 PROPOSAL-013

你**不是**裁判——accept/reject/defer 的決定權在使用者。但你負責呈現清楚的選項讓使用者判斷。R8b 的設計決策密度高，**呈現選項的品質比效率重要**。

## 第五步：決議類型（沿用 R7/R8a 慣例）

| 決議 | 何時採用 | 後續動作 |
|---|---|---|
| **accept** | Finding 是真問題，且修訂路徑明確單一 | 使用者確認後，當場用 Edit 改 proposal；在 decisions 記錄「具體改動摘要」 |
| **accept-with-choice** | Finding 是真問題，但修訂需使用者從 2+ 個 path 選一 | 呈現選項 table → 等使用者選 → 當場用 Edit 改 proposal → 在 decisions 記錄「選定 path + 具體改動摘要」 |
| **reject** | 誤判；能清楚說出誤判點（引用第三步或具體反證） | 在 decisions 記錄 reject 理由 |
| **defer** | 真問題但現在不修（例如延到 Implement 階段、延到 backlog） | 在 decisions 記錄 defer 理由 + 延到何時處理 |
| **clarify** | 不確定 finding 意圖或 proposal 原意，需設計者釐清 | 整理疑問點 → 等使用者回覆 → 轉為 accept / reject / defer |
| **out-of-scope** | 屬於其他輪範圍或 Closeout | 轉記到 `reviews/cross-round-notes.md`，從本輪排除 |
| **defer-to-revise** | Accept 但改動太複雜，本 session 無法當場修訂 | 在 decisions 明確寫「具體改動指引」給另起的 revise session |

**重要**：R8b 預期會有多個 `accept-with-choice`（設計決策型）。優先當場改而非 defer-to-revise；只有在改動涉及大段重寫、或使用者明確要求分 session 時才用 `defer-to-revise`。

## 第六步：處理方式（一次一項，嚴格不 dump）

對所有 Finding，**先按嚴重度分批處理**：

1. 先處理 Critical（若有，極少見；可能是相容性破壞）
2. 再處理 Major（影響範圍漏列、邊界越界、既有機制衝突）
3. 再處理 Minor
4. 最後處理 Question（含設計決策型）

**不要**按 F-NN 數字順序處理（R7/R8a 是按數字順序，R8b 因設計決策密度高，建議按嚴重度）。

### 對「精準度型」Finding（Major / Minor 為主）

1. 重述核心（不超過 2 句）
2. 說出建議決議（預期多為 accept）
3. 給出具體改動草案（PROPOSAL-013 的哪段、改成什麼，儘量引用現有文字）
4. 等使用者確認
5. 使用者 accept → **立刻用 Edit 工具改 proposal**
6. 改完後簡短回報「已修訂 `PROPOSAL-013.md § 某段`，改動摘要：...」
7. 進下一 Finding

### 對「既有機制相容性」Finding（Dimension 5 的 C-01 ~ C-08）

這類 Finding 需先 verify 既有機制是否真的衝突：

1. 重述核心 + 引用 reviewer 對照表的 ✓/✗/⚠ 判定
2. 用 Read 讀對應既有 proposal 段落 verify
3. 若 verify 確認衝突 → 呈現處理 path（modify P013 / modify backlog / 接受暫時不一致）
4. 等使用者拍板
5. 修訂 proposal

### 對「設計決策型」Finding（Dimension 6 的 4 項新概念）

這類 Finding 的修訂**需要使用者先做設計選擇**。處理模式：

1. 重述核心 + 為什麼需要使用者拍板（引用 finding 的 `建議` 欄與 reviewer 的「設計決策評估表」)
2. 列出 **2-4 個可選 path**（table 形式）：

   | Path | 描述 | 優點 | 缺點 | 對其他段落的影響 |
   |---|---|---|---|---|
   | A | ... | ... | ... | ... |
   | B | ... | ... | ... | ... |

3. 給出**你的推薦 path** + 理由（一句話）
4. 等使用者選 path（可能需要 1-2 輪來回討論）
5. 使用者定案後，**基於該 path 用 Edit 改 proposal**（可能涉及多個段落）
6. 改完後簡短回報改動範圍

### 互動範例（精準度型）

> **F-NN：影響範圍漏列 `pr-review-checklist.md`**
>
> 核心：reviewer 確認 PR review checklist 會引用 phase-spec / lightweight-spec 的段落結構，P013 將其英文化但 checklist 未列入更新。
>
> **建議決議：accept**（屬 Major，影響範圍完整性）
>
> **具體改動草案**（PROPOSAL-013 § 影響範圍表，新增兩列）：
> ```markdown
> | `sdd-ddd-webforms-skill/references/pr-review-checklist.md` | 修改 / 檢查 | 確認 checklist 引用的段落名稱與英文化後的 templates 一致 |
> | `sdd-ddd-core-skill/references/pr-review-checklist.md` | 修改 / 檢查 | 同上 |
> ```
>
> 你確認這個決議嗎？

### 互動範例（設計決策型）

> **F-NN：TEMPLATE-COVERAGE.md 與 TEMPLATE-LANGUAGE-GLOSSARY.md 是否該合併**
>
> 核心：reviewer 觀察兩份維護文件目的相關（前者描述 template 對照、後者描述 template 術語對照），分開維護有 overhead。
>
> **建議決議：accept-with-choice**（需你選 path）
>
> **可選 path**：
>
> | Path | 描述 | 優點 | 缺點 |
> |---|---|---|---|
> | A（兩份分開）| 維持 P013 原案：COVERAGE.md 列對照矩陣、GLOSSARY.md 列術語對照 | 各文件職責清楚；未來術語表擴展不影響矩陣 | 兩份檔需同步維護 |
> | B（合併為 TEMPLATE-CONTRACTS.md）| 合併為一份，§1 對照矩陣 / §2 術語對照 | 單一維護點；review 時一次讀完 | 合併後檔案略長；術語擴展可能讓矩陣段被沖淡 |
> | C（術語表內嵌矩陣）| 不獨立 GLOSSARY.md；術語對照內嵌到 COVERAGE.md 矩陣的「Section heading」欄位 | 最少維護點 | 術語表變短小附錄；對照功能變弱 |
>
> **我的推薦**：Path A。理由：兩份的閱讀情境不同（COVERAGE 是 review reference / GLOSSARY 是日常查詢），分開反而降低尋找成本。
>
> 你傾向哪個 path？

## 第七步：Proposal 狀態推進規則

全部 Finding 處理完後判斷是否推進狀態：

### 推進條件（draft → approved）

PROPOSAL-013 滿足**全部**條件才推進：
- (a) 所有 accept / accept-with-choice 的 finding 已在本 session 實際修訂完成
- (b) 沒有 `clarify` 類 finding 未解決
- (c) 沒有 `defer-to-revise` 類 finding
- (d) `defer` 不擋 approve
- (e) `reject` 不擋 approve
- (f) **特殊條件**：若 PROPOSAL-012 仍 draft（R8a 未完成），P013 仍可 approved，但 decisions 需明確標註「approve 條件：PROPOSAL-012 必須先 implemented，本 proposal 才能進入 R8b Implement」

### 推進動作

若 PROPOSAL-013 滿足推進條件：
1. 用 Edit 改 `proposals/PROPOSAL-013-system-document-template-coverage.md` 的 `**狀態**:` 欄從 `draft` 改為 `approved`
2. 填寫 proposal 末尾的「評估紀錄」欄：
   ```markdown
   **評估日期**: {YYYY-MM-DD}

   **結論**: approved

   **理由**: 經 R8b review（{review 日期}，{Cursor 模型}）+ R8b approve（本 session 日期）處理 {N} 個 finding（accept: {X}, accept-with-choice: {Y}, reject: {Z}, defer: {W}），所有需修訂部分已在 R8b approve session 內完成。下一步進入 R8b Implement 階段（見 `reviews/prompts/round-8b-implement.md`）{若 P012 仍 draft：「；本 proposal 的 implement 需等 PROPOSAL-012 implemented 完成後才能啟動」}。
   ```

## 第八步：Decisions 文件產出

寫入 `reviews/round-8b-decisions.md`，格式：

```markdown
# Round 8b Decisions — Draft Proposal Review (PROPOSAL-013)

**對應 review 報告**: `reviews/round-8b-report.md`
**決議日期**: YYYY-MM-DD
**決議者**: {使用者}（Claude 整理 + 當場修訂）

## 決議彙總

| Finding | 嚴重度 | 維度 | 決議 | 說明 |
|---|---|---|---|---|
| F-01 | Major | D5 | accept | {一句話摘要} |
| F-02 | Question | D6 | accept-with-choice (Path A) | 選定：兩份維護文件分開 |
| F-03 | ... | ... | ... | ... |

## Accept / Accept-with-choice 項目的修訂紀錄

### F-01 (accept)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: § 7 lightweight-spec Implementation Tasks
- **改動摘要**: {具體措辭變更摘要}
- **既有機制驗證**: {若 finding 涉及 P003/P004/P008/P009/P010，記錄 verify 結論}

### F-02 (accept-with-choice, Path A)
- **修訂檔案**: `proposals/PROPOSAL-013-system-document-template-coverage.md`
- **修訂段落**: § 1.1, § 4
- **選定 path 理由**: {使用者拍板理由摘要}
- **改動摘要**: {具體措辭變更摘要}

（逐項）

## 既有機制相容性確認紀錄（Dimension 5）

| C-NN | 檢查點 | 處理結論 | 對應 Finding |
|---|---|---|---|
| C-01 | rules / behavior anchor | ✓ 確認相容 / ⚠ 需 P013 § X 補說明 / ✗ 改 P013 § Y | F-NN |
| C-02 | lightweight task guard | | |
| ... | | | |

## 設計決策拍板紀錄（Dimension 6）

| 決策 | 拍板 path | 理由 |
|---|---|---|
| Canonical English structural language | A / B / C | {使用者理由} |
| `<!-- dflow:section X -->` anchor | | |
| TEMPLATE-COVERAGE.md | | |
| TEMPLATE-LANGUAGE-GLOSSARY.md | | |

## Reject / Defer 項目備忘

（若有）

## Out-of-scope 項目

（若有，已轉 cross-round-notes.md）

## Proposal 狀態推進紀錄

| Proposal | 推進前 | 推進後 | 推進理由 / 維持理由 |
|---|---|---|---|
| PROPOSAL-013 | draft | approved | 所有 accept / accept-with-choice finding 已修訂完成{；implement 待 P012 implemented 後啟動} |

## R8b Implement 切分建議

基於 reviewer 在 Dimension 7 的建議與本 session 處理過程的工作量觀察，建議 R8b Implement 切分為：

- **Sub-wave 1**（新增 templates，純新檔）：~12 個新 .md 檔，預期工作量 {小/中/大}
- **Sub-wave 2**（既有 templates 英文化）：~10 個既有檔修改，預期工作量 {小/中/大}
- **Sub-wave 3**（references 更新與 SKILL.md）：~10 個既有檔修改
- **Sub-wave 4**（新增維護文件 TEMPLATE-COVERAGE.md / TEMPLATE-LANGUAGE-GLOSSARY.md）：~2-3 個新檔

> Sub-wave 切分供 R8b Implement 規劃；若 Codex 一次無法包完，可作為斷點 checkpoint。

## 下一步

- R8b Implement 階段：執行 PROPOSAL-013 的影響範圍修改；建議由 **Codex 一次包完**（若 token 不足則 Cursor 接手剩餘 sub-waves）
- R8b Implement 啟動需等 PROPOSAL-012 implemented 完成
- R8b Implement 完成後進入 R8b Implement Review（仿 R7 pattern）

## 跨輪發現備忘

（若有）
```

## 禁止事項

- **不要改任何 skill 檔**（`sdd-ddd-*-skill/` 下所有檔）—— 那是 Implement 階段的職責
- **不要寫任何新增的 templates / scaffolding / 維護文件實體**（TEMPLATE-COVERAGE.md / TEMPLATE-LANGUAGE-GLOSSARY.md / 12 份新 template）—— 本輪只在 proposal 內定義，實際寫由 Implement 處理
- **不要改 PROPOSAL-012**—— R8a 的 target；若 finding 涉及 P012，方式是把建議轉到 R8a 處理 / 或標 clarify 等 P012 確認
- **不要改繁中版檔**（`*-tw/`）—— 延到 Closeout C1
- **不要修改 review 報告**（`reviews/round-8b-report.md`）
- **不要在單次回覆處理多個 Finding**（設計決策型選項呈現除外）
- **不要自行替使用者拍板設計決策型 Finding 的 path 選擇**（特別 Dimension 6 的 4 項新概念）
- **不要在 proposal 狀態尚未滿足推進條件時就改 `approved`**

## 特別提醒：4 項設計決策的拍板品質

R8b 的核心價值之一是讓使用者對 4 項新概念（canonical English / anchors / COVERAGE / GLOSSARY）做明確拍板。這些決策會長期影響 Dflow 的維護負擔與 AI 定位穩定性。

- 對每項設計決策，務必呈現 **2-4 個 path**（不要只給 binary yes/no）
- path 的「對其他段落的影響」欄要具體，讓使用者看見每個選擇的 ripple effect
- 你的推薦 path 要附 **一句話理由**，但**不要為了推薦而隱藏其他 path 的優點**
- 若使用者要求討論細節，深入討論；不要因為「想加快進度」而催促拍板

## 完成後回報

> 「Round 8b approve 階段完成。
> - 共 {N} 個 Finding 處理完畢：accept {X} / accept-with-choice {Y} / reject {Z} / defer {W} / clarify→XX {V} / out-of-scope {U}
> - PROPOSAL-013 狀態：draft → {approved / 仍 draft，理由 ...}
> - 4 項新概念設計決策拍板：
>   - Canonical English structural language: Path {A/B/C}
>   - `<!-- dflow:section X -->` anchor: Path {A/B/C}
>   - TEMPLATE-COVERAGE.md: Path {A/B/C}
>   - TEMPLATE-LANGUAGE-GLOSSARY.md: Path {A/B/C}
> - 既有機制相容性確認：8 個 C 檢查點全部 ✓ / 部分 ⚠ + 對應 finding 處理
> - 決議文件：`reviews/round-8b-decisions.md`
> - 修訂的 proposal 段落：列出具體段落數
> - R8b Implement sub-wave 切分建議已記錄於 decisions 文件
> - 下一步：
>   - 若 approved：進入 R8b Implement 階段（建議啟動提示詞另起 `reviews/prompts/round-8b-implement.md`，目標執行者 Codex）
>   - 若仍 draft：說明阻塞原因 + 建議的 revise / clarify session 下一步」

---END PROMPT---
