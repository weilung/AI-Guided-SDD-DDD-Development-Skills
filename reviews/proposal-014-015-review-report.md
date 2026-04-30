# PROPOSAL-014 + PROPOSAL-015 Design Review

> Reviewer: Codex CLI (GPT-5)  
> 審查日期: 2026-04-30  
> 範圍: 兩 PROPOSAL design review，非 implement review

## 總結
P014 與 P015 的問題定義整體成立，且彼此耦合關係判讀正確：P014 處理 init 入口與 root namespace，P015 處理 structural English 之外的 prose language 規範。兩份草案方向上可行，但目前都還不到可直接 `approve` 的品質門檻，主因是幾個核心設計契約尚未收斂成「可實施且可驗收」的明確規格。

P014 主要 blocker 在於 CLI 入口拓撲與相容策略：`npm create dflow` 的落地形式尚未定義清楚、legacy `specs/` 使用者衝擊揭露不足、與 P009 路徑契約的關聯漏列。P015 主要 blocker 在於 default (B) 的落地完整性：影響範圍未納入 Tutorial 回溯，且「workflow 必讀 `_conventions.md`」尚未被定義為硬性驗收項。

若先修正 Major finding，兩案可以維持分開 proposal 並採「同批實作、共享切面一次改完」策略，不必重新合併成單一 proposal。

## P014 Findings

### F-01
- 嚴重度: **Major**
- 標題: `npm create dflow` 入口設計未定義 package 拓撲
- 位置: `proposals/PROPOSAL-014-cli-init-and-dflow-root-namespace.md:37,101,85-99`
- 描述: 文案同時主張 `npm create dflow` 為替代入口，且應呼叫同一份 `lib/init.js`，但目前 only-one-package skeleton 只描述 `package.json + bin/dflow.js + lib/init.js`。是否需要 `create-*` wrapper package、是否同 repo 雙 package、或直接放棄 V1 `npm create` 未明確，會直接影響實作切分與發布策略。
- 建議處理: 在 proposal 內補「入口拓撲決策」：`npx-only (V1)` 或 `npx + create-wrapper` 二選一，並把對應檔案/發佈流程列入影響範圍。

### F-02
- 嚴重度: **Major**
- 標題: legacy `specs/` 使用者衝擊揭露不足
- 位置: `proposals/PROPOSAL-014-cli-init-and-dflow-root-namespace.md:79,105-113,131-150`
- 描述: 草案明確延後 migration tool，且僅在 init 偵測到 `specs/` 時給提示；但同時又要把 skill/runtime path 全面改成 `dflow/specs/...`。對既有 `specs/` 專案而言，這是實質 breaking change，現稿沒有明確寫「相容模式」、「手動遷移最低步驟」或「版本升級語意」。
- 建議處理: 拍板前至少補一條：`dual-read 過渡`、`明確 major-breaking + 手動 migration 指南`、或 `先不切 runtime 預設` 三選一，並寫入驗收條件。

### F-03
- 嚴重度: **Major**
- 標題: 關聯 Proposal 漏列 P009（feature path 契約）
- 位置: `proposals/PROPOSAL-014-cli-init-and-dflow-root-namespace.md:135-150,181-188`; `proposals/PROPOSAL-009-feature-directory-and-phases.md:41-45,60,200-203`
- 描述: P014 改的是整個 `specs/features/...` 路徑前綴（變為 `dflow/specs/features/...`），這直接觸及 P009 的核心結構契約；但關聯表未列 P009，會讓評估時低估 cross-proposal 影響。
- 建議處理: 在 P014 關聯表新增 P009（依賴/互補皆可），並補一句「僅改 root prefix，不改 P009 目錄語意」。

### F-04
- 嚴重度: **Minor**
- 標題: `/dflow:init-project` 移除清單未涵蓋 SKILL 全部引用面
- 位置: `proposals/PROPOSAL-014-cli-init-and-dflow-root-namespace.md:47-53`
- 描述: 五面向處置表有 cover 主要節點，但目前 SKILL 內對 init 的引用不只 frontmatter/decision tree/slash table，還包含 workflow transparency 與敘述段。若只按五項改，容易殘留文案。
- 建議處理: 增加一條「SKILL.md 全檔 grep `/dflow:init-project` 清零（保留歷史註記除外）」的驗收項。

### F-05
- 嚴重度: **Minor**
- 標題: 工作量估算偏樂觀
- 位置: `proposals/PROPOSAL-014-cli-init-and-dflow-root-namespace.md:170,172-179`
- 描述: 文案估 30-50 檔次，但同檔已宣告 grep-derived 72 檔範圍，且還包含 `tutorial/*/outputs/specs/**` 路徑搬移。若含搬移、連結修正、Tutorial 對話同步，實際 review/verify 負擔可能高於估計。
- 建議處理: 補一個更實務的估算方式（「直接修改檔數 + 搬移檔數 + 驗證檔數」三段估算），避免排程誤差。

## P015 Findings

### F-06
- 嚴重度: **Major**
- 標題: 影響範圍漏列 Tutorial 對話與 outputs 回溯
- 位置: `proposals/PROPOSAL-015-templates-prose-language-convention.md:49-53,81-110,164-180,182-191`
- 描述: P015 default (B) 會新增 init 問題與 `_conventions.md` 新段落，但影響範圍未列任何 `tutorial/` 檔；不在範圍段也未明確排除 Tutorial。這會導致設計與教學資產 drift（特別是 init 劇情中的 Q1-Q5 結構與輸出示例）。
- 建議處理: 二選一明確化：  
  1) 把 Tutorial 對話 + outputs 納入本案；或  
  2) 明寫「Tutorial 回溯延後到 P015-follow-up」並列前置依賴。

### F-07
- 嚴重度: **Major**
- 標題: default (B) 的可靠度前提未被定義為硬性驗收
- 位置: `proposals/PROPOSAL-015-templates-prose-language-convention.md:51,59,173-174,197-199`
- 描述: 草案正確指出可靠度取決於 workflow 是否固定讀 `_conventions.md`，但影響範圍對 flow 僅寫「修改/確認」，且工時估算以可下降為前提。若不把「所有會生成 prose 的 flow 必讀 convention」設為 must-have，default (B) 會退化成弱規範。
- 建議處理: 把 flow 更新改成 mandatory（非可選），並補驗收腳本：至少覆蓋 `new-feature / modify-existing / new-phase / finish-feature` 四條路徑。

### F-08
- 嚴重度: **Minor**
- 標題: fallback「無痛切換」敘述略過 path 過渡細節
- 位置: `proposals/PROPOSAL-015-templates-prose-language-convention.md:75-77,110`
- 描述: 文案稱 (C)→(B) 不衝突，但同檔也指出 pre-P014 與 post-P014 path 不同（`specs/shared` vs `dflow/specs/shared`）。沒有過渡規則時仍會有一次 path 同步成本。
- 建議處理: 補一條 transition rule（例如「若先落地 C，P014 時一併搬移 conventions 檔並更新引用」）。

### F-09
- 嚴重度: **Question**
- 標題: `any` 是否應作為 public default
- 位置: `proposals/PROPOSAL-015-templates-prose-language-convention.md:98-103,142-146`
- 描述: `any` 對初次採用友善，但在多 session / 多代理協作時，仍可能造成 prose 風格漂移。這是產品策略取捨，不是純技術對錯。
- 建議處理: 請拍板預設策略：`any`（低摩擦）或強制 explicit language（高一致性）。

## 合併 Findings

### F-10
- 嚴重度: **Major**
- 標題: P015→P014 有依賴聲明，但 P014 缺雙向對應
- 位置: `proposals/PROPOSAL-015-templates-prose-language-convention.md:213`; `proposals/PROPOSAL-014-cli-init-and-dflow-root-namespace.md:181-188`
- 描述: P015 關聯表已明列對 P014 的依賴/互補；P014 僅在 out-of-scope 提到 P015，未在關聯表建立對應。評估時序與條件容易失焦。
- 建議處理: P014 關聯表新增 P015 一列，標註「P015(B) 依賴 P014 Sub-wave 1；P015(C) 可獨立」。

### F-11
- 嚴重度: **Major**
- 標題: 同期實作的共享切面成本需合併規劃
- 位置: `proposals/PROPOSAL-014-cli-init-and-dflow-root-namespace.md:176-179`; `proposals/PROPOSAL-015-templates-prose-language-convention.md:166-174,203-206`
- 描述: 兩案會同時修改 `lib/init.js`、雙版 `references/init-project-flow.md`、雙版 `SKILL.md`、`_conventions.md` scaffolding、README/Tutorial。若各自獨立排程，會產生連續雙改與高衝突機率。
- 建議處理: 保持 proposal 分開，但實作改採「共享切面單一 wave」：先完成 init contract（含 prose-language）再一次性 cascade 文件。

### F-12
- 嚴重度: **Question**
- 標題: 兩案是否應合併成單一 Proposal
- 位置: `proposals/PROPOSAL-014-cli-init-and-dflow-root-namespace.md:23,164`; `proposals/PROPOSAL-015-templates-prose-language-convention.md:45-53,75-77`
- 描述: 技術上高度耦合，但問題層次不同（入口/命名空間 vs 內容語言規範），且 P015 有可獨立 fallback (C)。
- 建議處理: 建議**不合併**，維持兩案分開；但需在兩案都補齊 shared-cut implementation plan 與雙向 reference。

## 根本性疑慮
目前兩案都缺一條明確的「破壞性升級契約」：從 `/dflow:init-project` + `specs/` 過渡到 `npx dflow init` + `dflow/specs/`，對既有專案是行為改變。若不先定義 compatibility / migration / versioning（至少三選一策略），即使個別設計正確，落地仍可能造成大規模使用者困惑與 adoption 反噬。

## Approve 建議
- **PROPOSAL-014**: `approve with conditions`  
  Conditions: 先解 F-01 / F-02 / F-03（其餘 minor 可在 implement 前修）。
- **PROPOSAL-015**: `approve with conditions`  
  Conditions: 先解 F-06 / F-07，並對 F-09 完成產品策略拍板。

## 給後續 implement 階段的提醒
- 若兩案同批實作，請把 `init contract`（問題集、寫入位置、讀取規則）當作共同主幹，一次改完，避免 `lib/init.js` 與 `init-project-flow.md` 重複返工。
- 在 PR 驗收清單加入兩條硬檢查：  
  1) `specs/` vs `dflow/specs/` path 一致性掃描；  
  2) prose-language 設定對四條主要 workflow 的實際輸出可觀察性。
- Tutorial 若不在同一批更新，必須明確標註「目前劇情與新規格存在已知差異」與追補時點，否則 reviewer 會把教學結果視為 regression。
