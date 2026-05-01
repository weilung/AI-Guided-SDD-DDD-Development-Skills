# Round 1 Review Report — 流程入口與透明度（P001 + P007b）

**審查範圍**: PROPOSAL-001（Workflow Transparency / Hybrid 指令化入口）、PROPOSAL-007b（PR Review Spec Intent Step 0）
**審查日期**: 2026-04-20
**審查者**: Claude（Cursor session；擔任 Codex 角色）

---

## 審查結論總覽

| 項目數 | Critical | Major | Minor | Question |
|---|---|---|---|---|
| 5 | 0 | 1 | 3 | 1 |

**總體評語**：P001 和 P007b 的核心設計意圖全部落地——7 個 `/dflow:` 指令到齊、三級透明度三層定義一致、Phase Gate 位置與 proposal 紀錄完全吻合、Step 0 在 pr-review-checklist 最前且不可略過的語意清楚、兩版的結構差異（Core 5 步 vs WebForms 6 步 modify-existing）都有明確註記。主要問題集中在繁中版（尤其 WebForms tw）的同步漏失，以及一個文案上的邊界情況（fallback 話術只提 Step 8 而未覆蓋 modify-existing 的 Step 6 / Step 5）。無任何 Critical 發現。

---

## Findings

### F-01 WebForms 繁中版 SKILL_tw.md 的「工作流程透明度」結構與英文版及 Core tw 不對齊

**嚴重度**: Major
**位置**: `sdd-ddd-webforms-skill-tw/SKILL_tw.md:23-58, 123-144`
**對照 Proposal**: P001「提議的解法 §1 指令化入口」、「§2 Auto-trigger 作為安全網」、以及實施紀錄列出的 Workflow Transparency 子段（Slash Commands、Auto-Trigger Safety Net、Three-Tier Transparency、Confirmation Signals、/dflow:status Response Format）

**現況**：

- 英文版 `sdd-ddd-webforms-skill/SKILL.md:80-108` 以三個明確子標題組織：`### Slash Commands`（分 Flow entry / Control 兩組列出）、`### Auto-Trigger Safety Net`（三步驟 + 三個失敗模式摘要）、`### Three-Tier Transparency`。
- Core 繁中 `sdd-ddd-core-skill-tw/SKILL_tw.md:82-108` 完整對齊這三個子標題。
- WebForms 繁中 `sdd-ddd-webforms-skill-tw/SKILL_tw.md` 則把 Slash Commands 資訊壓進決策樹（lines 31-38），把 Auto-Trigger 三步驟簡化為 lines 53-58 的「三選項格式」一段，**沒有** `### Slash 指令`、`### 自動觸發安全網` 這兩個明確子標題；也未出現英文版與 Core tw 都保留的「三個失敗模式」（missed triggers / wrong workflow selection / invisible state）說明。
- 另外 WebForms tw 缺少英文版/Core tw 都有的「Flow entry 指令 vs 控制指令」分組（只在決策樹列出一大串，開發者難以快速區分）。

**問題**：這屬於繁中同步漏失——在 Repo 設計原則「英文版與繁中版結構對應、資訊量不能落差」的前提下，WebForms tw 少了英文版用來讓人讀懂 Hybrid 設計核心機制的兩個重要子段。Core tw 做到了對齊，WebForms tw 沒有，造成兩個 tw 檔案自身不一致。

**建議**：在 WebForms `SKILL_tw.md` 的「工作流程透明度」節新增兩個子段（參考 Core tw 同段結構與 WebForms 英文版內容）：
- `### Slash 指令`：分「Flow entry 指令」與「控制指令」兩組條列，即使決策樹已列過也要重複一次以供快速查找；`/dflow:verify` 亦應納入控制指令組。
- `### 自動觸發安全網`：以 1-2-3 步驟條列（宣告判斷 → 提供三選項 → 等待確認），並補上「錯過觸發 / 選錯流程 / 狀態不可見」三個失敗模式說明。

---

### F-02 「Completion Checklist Execution」段落的 fallback 話術只提 Step 8，未涵蓋 modify-existing 的 Step 6（WebForms）/ Step 5（Core）

**嚴重度**: Minor
**位置**:
- `sdd-ddd-webforms-skill/SKILL.md:144`
- `sdd-ddd-core-skill/SKILL.md:159`
- `sdd-ddd-webforms-skill-tw/SKILL_tw.md:159`
- `sdd-ddd-core-skill-tw/SKILL_tw.md:146`

**對照 Proposal**: P001「三級流程透明化」與實施紀錄中 Phase Gate 位置（涵蓋 new-feature Step 7→8 與 modify-existing Step 5→6（WebForms）/ Step 4→5（Core））

**現況**：此段開頭已明確寫出兩個流程的完成觸發點（Step 7→8 與 Step 5→6 / Step 4→5），但段尾的自動觸發 fallback 話術統一寫為：

> "It looks like you're wrapping up — should I run the Step 8 checklist?" （英文兩版）
> 「看起來你要收尾了，要先跑 Step 8 checklist 嗎？」（繁中兩版）

「Step 8 checklist」是 new-feature-flow 的用詞；若開發者是在 modify-existing-flow 中跳過 Phase Gate 直接 commit，實際要跑的是 WebForms 的 Step 6 checklist 或 Core 的 Step 5 checklist。用詞單一化會讓 AI 在 modify-existing 情境下產生一個名稱不符的提示。

**問題**：文案與上下文描述的「覆蓋兩個流程」承諾不完全一致。實務上 AI 可能仍能正確執行（因為前面已列明兩個觸發點），但讀作 Skill 規範時會留下措辭上的邏輯小洞。

**建議**：將 fallback 話術改為條件式，或寫成「完成 checklist（視流程而定：new-feature 的 Step 8、modify-existing 的 Step 6/Step 5）」。四個檔案的同段皆要同步調整。

---

### F-03 WebForms 繁中版 `/dflow:cancel` 缺少「artifacts 保留」的澄清

**嚴重度**: Minor
**位置**: `sdd-ddd-webforms-skill-tw/SKILL_tw.md:38`
**對照 Proposal**: P001「提議的解法 §1 通用控制指令」/ 實施紀錄中 /dflow:cancel 的行為定義

**現況**：

- WebForms 英文 `sdd-ddd-webforms-skill/SKILL.md:92`：`/dflow:cancel — abort current workflow, return to free conversation (artifacts created so far are kept as-is)`
- Core 英文 `sdd-ddd-core-skill/SKILL.md:107`：同樣有 `(artifacts created so far are kept as-is)`
- Core 繁中 `sdd-ddd-core-skill-tw/SKILL_tw.md:94`：`/dflow:cancel — 取消當前流程,回到自由對話(已建立的檔案保留)`
- WebForms 繁中 `sdd-ddd-webforms-skill-tw/SKILL_tw.md:38`：`/dflow:cancel → 放棄當前流程，回到自由對話`（**缺** 保留物件的澄清）

**問題**：繁中同步漏失。雖然「artifacts 保留」這件事嚴格來說超出原 Proposal 字面（原 Proposal §1 只說「中止目前流程，回到自由對話」），但既然三個兄弟檔案都加上了澄清，WebForms tw 也應該補齊，避免讀者在取消時誤以為已產出的 spec / domain doc 會被清除。

**建議**：將 WebForms tw 的 `/dflow:cancel` 行改為 `/dflow:cancel → 放棄當前流程，回到自由對話（已建立的檔案保留）`，與 Core tw 一致。

---

### F-04 「Completion Checklist Execution」子段放在 § Workflow Transparency 內，但 P001 實施紀錄並未列為 P001 的子段內容

**嚴重度**: Question
**位置**:
- `sdd-ddd-webforms-skill/SKILL.md:130-144`
- `sdd-ddd-core-skill/SKILL.md:145-159`
- 以及兩個 tw 對應段落

**對照 Proposal**: P001 實施紀錄（§ 實施紀錄表格 row 1）列出 Workflow Transparency 包含：Slash Commands、Auto-Trigger Safety Net、Three-Tier Transparency、Confirmation Signals、/dflow:status Response Format——**沒有**「Completion Checklist Execution」

**現況**：目前四個 SKILL 檔的 § Workflow Transparency 都多了一個 `### Completion Checklist Execution`（或繁中「完成流程 Checklist 的執行方式」）子段，內容混合了 P001 的 Phase Gate 觸發邏輯（觸發點定義、fallback 安全網）與 P005a/P005b 的驗證分層細節（Section 8.1 / 6.1 / 5.1 分層、Pre-merge / Post-merge 時機、P005b 追加的兩個問題、B3 / B4 標記）。

**問題**：不確定這段該歸 P001 還是 P005。從設計角度：
- 觸發點描述（「Step 7→8 / Step 5→6 / Step 4→5 是 checklist 的唯一觸發點」）明顯承接 P001 的 Phase Gate 定義。
- 但內部四步執行順序、post-merge 標記、P005b 補題、B3/B4 的敘述都是 P005a/P005b 內容。
- 將這段整個納入 § Workflow Transparency 子段有結構上的邏輯（完成 checklist 的觸發屬於透明度機制），但 P001 實施紀錄沒宣告這個子段歸屬。

**需釐清**：

1. 此子段的定位是否為 P001 Phase Gate 概念的延伸（因此 Round 1 要審完整內容），還是 P005 的產物只借用 P001 的觸發點描述（因此 Round 3 再審內部細節）？
2. 若屬後者，目前的措辭（例如「P005b adds two questions: ...」）已明顯引用 P005 編號，是否應抽出為獨立 § Completion Checklist 章，與 § Workflow Transparency 並列？

若設計者決定屬 P001 延伸 → 本輪應補查「觸發邏輯描述是否精確」；若屬 P005 → 本輪無需深查，留給 Round 3。本 Finding 標 Question 交由 Approve session 判定。

---

### F-05 WebForms 繁中 SKILL_tw.md 的決策樹把 `/dflow:verify` 列為入口指令，但英文 SKILL.md 把 `/dflow:verify` 的說明放在決策樹 + § Workflow Transparency 控制指令組（兩處一致）

**嚴重度**: Minor
**位置**:
- `sdd-ddd-webforms-skill-tw/SKILL_tw.md:35`（決策樹）
- `sdd-ddd-webforms-skill/SKILL.md:48`（決策樹）、`sdd-ddd-webforms-skill/SKILL.md:91`（控制指令）

**對照 Proposal**: 嚴格來說 `/dflow:verify` 屬 P008 範圍，不在本輪。但因其與 P001 的「控制指令 vs Flow entry 指令」分組設計直接相關，在此一併指出。

**現況**：
- 英文 WebForms SKILL.md 把 `/dflow:verify` 明確標為「控制指令」（獨立指令，不需進行中的流程）——line 91。
- Core 英文和 Core tw 也都對齊這個分類。
- WebForms tw 的決策樹只把 `/dflow:verify` 和其他 /dflow 指令並列，但因為沒有 `### Slash 指令` 的 Flow entry / 控制指令分組子段（見 F-01），開發者讀 WebForms tw 時看不出 `/dflow:verify` 的「非 workflow 入口」性質。

**問題**：此問題與 F-01 根源相同（WebForms tw 缺少 Slash Commands 分組子段），但特別標出供 Approve session 參考——修 F-01 時應一併確保 `/dflow:verify` 的分類正確。若 F-01 被決議修正，F-05 會自動解消。

**建議**：修 F-01 時，在 WebForms tw 新增的「控制指令」組內將 `/dflow:verify` 明確納入（同時註明「獨立指令，不需要進行中的流程」）。若 F-01 被 reject，F-05 單獨處理難度不高，但意義有限。

---

## 兩版一致性對照（WebForms vs Core）

| 檢查項 | WebForms 版 | Core 版 | 一致？ |
|---|---|---|---|
| 7 個 `/dflow:` 指令（new-feature / modify-existing / bug-fix / pr-review / status / next / cancel）到齊 | ✓ | ✓ | ✓ |
| description frontmatter 同時列 PRIMARY / SECONDARY trigger 與「NL 觸發後等確認」 | ✓ (`SKILL.md:3-15`) | ✓ (`SKILL.md:3-15`) | ✓ |
| 決策樹有 `/dflow:` 分支與「NL → 安全網」分支 | ✓ (`SKILL.md:40-74`) | ✓ (`SKILL.md:57-89`) | ✓ |
| 三級透明度表格（Flow entry / Phase gate / Step-internal） | ✓ (`SKILL.md:112-116`) | ✓ (`SKILL.md:127-131`) | ✓ |
| Auto-Trigger Safety Net 三步驟規則 + 三失敗模式摘要 | ✓ (`SKILL.md:96-106`) | ✓ (`SKILL.md:111-121`) | ✓ |
| new-feature-flow Phase Gate 位置（Step 4→5、6→7、7→8） | ✓ (`new-feature-flow.md:5-8`) | ✓ (`new-feature-flow.md:7-10`) | ✓ |
| new-feature-flow 其餘 step-internal 標示（1→2、2→3、3→4、5→6） | ✓ | ✓ | ✓ |
| modify-existing-flow Phase Gate 位置 | 2→3、4→5、5→6（6 步）✓ | 2→3、3→4、4→5（5 步）✓ | ✓（刻意差異；Core `SKILL.md:140` 與 `modify-existing-flow.md:14` 已明確註記）|
| modify-existing-flow step-internal 標示 | 1→2、3→4 ✓ | 1→2 ✓ | ✓ |
| `/dflow:bug-fix` 的 ceremony 調整說明（頂部 Note） | ✓ (`modify-existing-flow.md:12`) | ✓ (`modify-existing-flow.md:16`) | ✓ |
| `/dflow:status` 回應格式定義 | ✓ (`SKILL.md:158-181`) | ✓ (`SKILL.md:173-196`) | ✓ |
| `/dflow:status` 無進行中流程時的 fallback 回覆（提到 4 個 flow entry 指令） | ✓ (`SKILL.md:181`) | ✓ (`SKILL.md:196`) | ✓ |
| Confirmation Signals（指令 / 英文 / 中文 / 隱含） | ✓ (`SKILL.md:146-156`) | ✓ (`SKILL.md:161-171`) | ✓ |
| pr-review-checklist Step 0 位於檔案最前（先於任何 Code Review 項） | ✓ (`pr-review-checklist.md:8`) | ✓ (`pr-review-checklist.md:5`) | ✓ |
| `/dflow:pr-review` 進入銜接語（Step 0 不可略過）明示於 checklist 頂部 | ✓ (`pr-review-checklist.md:6`) | ✓ (`pr-review-checklist.md:3`) | ✓ |
| Step 0 四要素（讀 spec / 讀 Delta / 陳述意圖 / 再看 code） | ✓ (`pr-review-checklist.md:12-15`) | ✓ (`pr-review-checklist.md:9-13`，多一項 behavior.md 交叉確認) | ✓（Core 延伸符合 P007b impl record）|
| 無 spec 時的引導話術 | ✓ (`pr-review-checklist.md:18-23`) | ✓ (`pr-review-checklist.md:16-21`) | ✓ |
| Completion Checklist fallback 話術「Step 8 checklist」用詞未涵蓋 modify-existing | ⚠（line 144） | ⚠（line 159） | ⚠ → 見 F-02 |

---

## 繁中同步對照

| 檢查項 | 英文版 | 繁中版 | 一致？ |
|---|---|---|---|
| **WebForms SKILL / SKILL_tw** | | | |
| description frontmatter 觸發規則 | 有 | N/A（tw 無 frontmatter，改用敘述） | ✓（繁中版本來就是純敘述文件；非 bug） |
| 決策樹列出 7+1 個 /dflow 指令 | 有 (`:44-51`) | 有 (`:31-38`) | ✓ |
| 決策樹含「NL → 安全網」分支 | 有 (`:53-57`) | 有 (`:40-50`) | ✓ |
| `### Slash Commands` 子段（Flow entry / 控制指令分組） | 有 (`:80-92`) | **無** | ✗ → F-01 |
| `### Auto-Trigger Safety Net` 子段（三步驟 + 三失敗模式） | 有 (`:94-106`) | **簡化為決策樹下的「三選項格式」，缺失敗模式** | ✗ → F-01 |
| 三級透明度表格 | 有 (`:110-116`) | 有 (`:127-131`) | ✓ |
| Phase Gate 位置（new-feature 與 modify-existing） | 有 (`:120-128`) | 有 (`:133-143`) | ✓ |
| Completion Checklist Execution 子段 | 有 (`:130-144`) | 有 (`:145-159`) | ✓ |
| Confirmation Signals 子段 | 有 (`:146-156`) | 有 (`:161-170`) | ✓ |
| `/dflow:status` 回應格式 | 有 (`:158-181`) | 有 (`:172-195`) | ✓ |
| `/dflow:cancel` 保留物件澄清 | 有 (`:92`) | **無** (`:38`) | ✗ → F-03 |
| **Core SKILL / SKILL_tw** | | | |
| `### Slash Commands` 子段 | 有 (`:95-107`) | 有 (`:82-94`) | ✓ |
| `### Auto-Trigger Safety Net` 子段 + 三失敗模式 | 有 (`:109-121`) | 有 (`:96-108`) | ✓ |
| 三級透明度表格 | 有 (`:125-131`) | 有 (`:110-118`) | ✓ |
| Phase Gate 位置（含 Core 5 步說明） | 有 (`:135-143`) | 有 (`:120-130`) | ✓ |
| Completion Checklist Execution 子段 | 有 (`:145-159`) | 有 (`:132-146`) | ✓ |
| Confirmation Signals | 有 (`:161-171`) | 有 (`:148-158`) | ✓ |
| `/dflow:status` 回應格式 | 有 (`:173-196`) | 有 (`:160-183`) | ✓ |
| `/dflow:cancel` 保留物件澄清 | 有 (`:107`) | 有 (`:94`) | ✓ |
| **new-feature-flow（WebForms / Core）** | | | |
| 頂部 Phase Gates 列表 | 有（兩版皆有） | 有（兩版皆有） | ✓ |
| Step-internal transition 標示（1→2、2→3、3→4、5→6） | 有 | 有 | ✓ |
| Phase Gate 宣告話術（Step 4→5、6→7、7→8） | 有 | 有 | ✓ |
| **modify-existing-flow（WebForms / Core）** | | | |
| 頂部 Phase Gates 列表 | 有 | 有 | ✓ |
| 「步驟數差異」Note（Core 版） | 有 (`:14`) | 有 (`:14`) | ✓ |
| `/dflow:bug-fix` ceremony 調整 Note | 有 | 有 | ✓ |
| Step-internal transition 標示 | 有 | 有 | ✓ |
| Phase Gate 宣告話術（2→3、4→5、5→6 / 2→3、3→4、4→5） | 有 | 有 | ✓ |
| **pr-review-checklist（WebForms / Core）** | | | |
| `/dflow:pr-review` 進入銜接語 | 有 | 有 | ✓ |
| Step 0 標題 + 四（Core：五）要素 | 有 | 有 | ✓ |
| 無 spec 時的引導話術 | 有 | 有 | ✓ |

---

## 跨輪發現（不計入本輪 Findings，寫入 `reviews/cross-round-notes.md`）

本輪過程中發現若干與 P002 / P003 / P004 / P005 / P008 相關的觀察，屬其他 Round 範圍，已另記於 `cross-round-notes.md`，本輪不深查。
