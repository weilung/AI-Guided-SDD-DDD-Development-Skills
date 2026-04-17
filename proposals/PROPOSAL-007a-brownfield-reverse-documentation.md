## PROPOSAL-007a: Brownfield 系統性逆向文件化

**狀態**: `implemented`

**提出日期**: 2026-04-11（從 PROPOSAL-007 拆分）

**優先級**: `低`

**來源**: PROPOSAL-007 拆分。原 P007 捆綁三個影響不同檔案、不同概念的子項，整合評估（2026-04-15）決定拆分為獨立 proposal。

**實施排程**: Wave C（獨立微調，無前置依賴）

---

### 問題描述

WebForms 版的 modify-existing-flow Step 2 說「capture what currently exists」，但只是記錄單一功能的行為。沒有引導 AI 主動掃描相關的 Code-Behind，系統性地抽取業務規則和領域概念。

### 提議的解法

WebForms 版 modify-existing-flow 在 Step 2 和 Step 3 之間加入「系統性 baseline capture」指引——當 AI 發現被修改的功能沒有現有 spec 時，主動：

1. 讀取相關 Code-Behind 檔案
2. 抽取所有業務規則（if/else、計算、驗證）
3. 識別領域概念（潛在的 Entity、VO、Service）
4. 檢查是否有跨頁面的重複邏輯
5. 將發現記錄到對應的 domain docs 和 tech-debt.md

這是「趁修改時順便做」的機會主義策略，不是每次都完整掃描。

### 影響範圍

| 檔案 | 變更類型 | 說明 |
|---|---|---|
| `sdd-ddd-webforms-skill/references/modify-existing-flow.md` | 修改 | Step 2-3 之間加入系統性 baseline capture 指引 |

### 預估工作量

小

### 關聯的 Proposal

| Proposal | 關係 | 說明 |
|---|---|---|
| PROPOSAL-003 | 互補 | 逆向文件化的結果可存入 behavior.md |
| PROPOSAL-007（原） | 拆分自 | 本 proposal 是原 P007 的第一個子項 |

### 關鍵假設

- 假設：「趁修改時 baseline」能累積出有用的文件（而非只是散點）
- 驗證點：實施後觀察 baseline capture 的產出是否被後續修改引用

### 評估紀錄

**評估日期**: 2026-04-15

**結論**: approved

**理由**: 工作量極小，只在 WebForms 版 modify-existing-flow 加入一段指引。機會主義策略的風險低——最差情況是「順便做了但沒人引用」，不會增加必要工作量。

### 實施紀錄

**Wave**: C

**實施日期**: 2026-04-17

**實際變更**:

1. **`sdd-ddd-webforms-skill/references/modify-existing-flow.md`** Step 2 新增子段「Systematic Baseline Capture (when no prior spec exists)」：
   - 5 步機會主義掃描指引（讀相關 Code-Behind、提取業務規則、識別領域概念、檢查重複邏輯、記錄到 domain docs + tech-debt）
   - 明確標註為 opportunistic，範圍限定在被修改功能和鄰近頁面
   - 附 AI 話術範例

**影響檔案**（1 個英文 + 1 個繁中）：

- `sdd-ddd-webforms-skill/references/modify-existing-flow.md`
- `sdd-ddd-webforms-skill-tw/modify-existing-flow_tw.md`（繁中同步）
