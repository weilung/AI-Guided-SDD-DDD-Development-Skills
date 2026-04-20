# Cross-Round Notes — 跨輪發現累積

> 各輪 review 期間發現的問題若不屬本輪範圍，寫在這裡，於進入對應輪的 Review 階段時讀入。
> 格式：位置 + 描述 + 建議轉交輪次。這些備忘不計入該輪 Finding 數量。

---

## R1 發現、屬於 R{N} 的備忘

### N-01：`templates/behavior.md` 在兩版繁中 SKILL_tw 的「模板」表缺漏

- **位置**：`sdd-ddd-webforms-skill-tw/SKILL_tw.md:283-290`、`sdd-ddd-core-skill-tw/SKILL_tw.md:386-394`
- **描述**：英文兩版都把 `templates/behavior.md`（Consolidated behavior spec for a Bounded Context）列在 Templates 表；繁中兩版的 Templates 表都缺這一列。`behavior.md` 的內文章節（§ Behavior Source of Truth）在繁中版有保留，只有模板清單沒同步。
- **建議轉交**：R4（Source of Truth 與漂移驗證），與 P003 實作一併審。

---

### N-02：「Completion Checklist Execution」子段夾帶 P005a / P005b 實質內容

- **位置**：`sdd-ddd-webforms-skill/SKILL.md:130-144`、`sdd-ddd-core-skill/SKILL.md:145-159`（以及兩個 tw 對應段）
- **描述**：此子段放在 § Workflow Transparency 內，但內容大半是 P005a/P005b 的材料（Pre-merge / Post-merge 分層、Section 8.1/6.1/5.1 子段結構、P005b 追加的兩題、B3/B4 標記）。R1 已將此段的**歸屬問題**列為 Finding F-04（Question），但此段的**內部細節正確性**（驗證項目切分、post-merge 時機標記、B3/B4 連結）屬 R3 範圍。
- **建議轉交**：R3（完成流程 Checklist），審查驗證分層的正確性與 flow 檔 Section 8.1 / 6.1 / 5.1 的對應。

---

### N-03：new-feature-flow Step 8 / modify-existing-flow Step 6 / Step 5 的 checklist 內容

- **位置**：
  - `sdd-ddd-webforms-skill/references/new-feature-flow.md:212-258`
  - `sdd-ddd-core-skill/references/new-feature-flow.md:224-276`
  - `sdd-ddd-webforms-skill/references/modify-existing-flow.md:218-261`
  - `sdd-ddd-core-skill/references/modify-existing-flow.md:144-195`
  - 以及四個繁中對應檔
- **描述**：完成流程 checklist 的具體條目、`實作任務` 段處理、`behavior.md` 草稿轉正式、last-updated 日期、`## 提案中變更` 清理——全部是 P005a / P005b 加 P003 (B3 中途同步) 加 P004（實作任務段）的合力產物。R1 只對其「是否被 Phase Gate 正確觸發」做了確認，條目內容正確性未深查。
- **建議轉交**：R3（完成流程 Checklist），與 P003 相關的 behavior.md 子步驟也可在 R4 交叉檢查。

---

### N-04：modify-existing-flow 的 Delta 格式規範（ADDED/MODIFIED/REMOVED/RENAMED + 可選 UNCHANGED）

- **位置**：
  - `sdd-ddd-webforms-skill/references/modify-existing-flow.md:52-89`
  - `sdd-ddd-core-skill/references/modify-existing-flow.md:39-78`
  - 以及繁中對應
- **描述**：P002 的 Delta 規範已落地在 modify-existing-flow 的 Step 2 內。R1 審查時看到 Core 版的 Step 2 多了一條「Aggregate 狀態轉換和 Domain Events 要寫進 Given/When/Then 裡——`/dflow:pr-review` Step 0 會靠這些讀出變更語義」（line 78），這是 P007b ↔ P002 的橋樑說明，屬 P002 範圍。
- **建議轉交**：R2（變更描述格式），檢查 Delta 的五種段落（ADDED/MODIFIED/REMOVED/RENAMED/UNCHANGED）規則、`## Reason for Change` 強制要求、以及 Core 版與 WebForms 版差異（Core 多 Aggregate/Event 補充）是否與 P002 提案意圖完全對齊。

---

### N-05：`/dflow:verify` 指令的完整分類與 drift-verification.md 的存在

- **位置**：
  - `sdd-ddd-webforms-skill/SKILL.md:48, 91, 319`
  - `sdd-ddd-core-skill/SKILL.md:65, 106, 388`
  - 以及繁中對應 + `references/drift-verification.md`
- **描述**：`/dflow:verify` 屬 P008 範圍，R1 只驗證它在 SKILL.md 的指令清單中與其他 /dflow 指令並存、不干擾 P001 的分級（被分類為「控制指令：獨立，不需進行中的流程」）。drift-verification.md 的完整內容與 rules.md ↔ behavior.md 的驗證邏輯未深查。
- **建議轉交**：R4（Source of Truth 與漂移驗證）。

---

### N-06：`實作任務` 段（HTML comment Phase 標記）

- **位置**：
  - 多處 SKILL.md：`sdd-ddd-webforms-skill/SKILL.md:257`、`sdd-ddd-core-skill/SKILL.md:342`（Guiding Questions by Phase 段的引言）
  - new-feature-flow / modify-existing-flow 中 Step 5 / Step 4 / Step 3 的「Generate Implementation Tasks List」子段
- **描述**：P004 的 Phase 標記與 `實作任務` 段已落地。R1 觀察到四份 flow 檔的 Generate Tasks 子段一致提到「寫入 spec 的 `實作任務` 段」、"[LAYER]-[NUMBER]" 格式。feature-spec.md / lightweight-spec.md 模板的實際 HTML comment 與 tasks 清單未深查。
- **建議轉交**：R2（變更描述格式，P004 範圍）。

---

### N-07：WebForms 版決策樹含 `specs/features/backlog/` / Sprint planning 分支，Core 版不含

- **位置**：
  - `sdd-ddd-webforms-skill/SKILL.md:63-64`（`"What should I work on next?" / Sprint planning` 分支）
  - `sdd-ddd-core-skill/SKILL.md` 決策樹沒有對應分支
- **描述**：這是兩版的結構性差異。嚴格說不屬 P001 要求的指令化或透明度範疇（P001 只改 /dflow 入口與透明度機制），但這個差異存在於 P001 重寫過的決策樹內。R1 判斷為既有設計（pre-P001）保留，未列 Finding；若 R5 或後續輪次發現此差異影響 Ceremony Scaling / Brownfield 判讀可再檢視。
- **建議轉交**：R5（Ceremony 與逆向文件化），或 Wave D 精簡化審查時一併檢視。
