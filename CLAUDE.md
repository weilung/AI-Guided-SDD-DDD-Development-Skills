# SDD/DDD Skill 開發環境 — Claude Code 協作規範

> 本文件供 Claude Code CLI 使用。
> 議題討論和 Proposal 評估的完整背景請見 Claude.ai Project（內含 Project Instructions 和歷史對話）。

---

## 這個 Repo 是什麼

本 Repo 維護一套 AI 引導的 SDD（Specification-Driven Development）/ DDD（Domain-Driven Design）工作流程規範，設計用於 Claude Code 協作開發。

**兩個版本**：
- `sdd-ddd-webforms-skill/`：針對運行中的 ASP.NET WebForms 專案，漸進式抽離業務邏輯
- `sdd-ddd-core-skill/`：針對 ASP.NET Core 新專案，完整 Clean Architecture + DDD 戰術模式

> **註（2026-04-30）**：原本另有 `sdd-ddd-webforms-skill-tw/` / `sdd-ddd-core-skill-tw/` 繁中版供人閱讀，已於本日棄用刪除（Tutorial 已涵蓋人讀需求 + Path A 全英化路線）。未來若有重起翻譯需求另起 PROPOSAL。

---

## Repo 結構

```
/
├── CLAUDE.md                          ← 本文件
├── CHANGELOG.md                       ← 版本變更記錄
├── README.md
├── proposals/                         ← Proposal 管理目錄
│   ├── PROPOSAL-000-template.md       ← 格式範本（不是實際 Proposal）
│   ├── PROPOSAL-001-*.md
│   ├── PROPOSAL-002-*.md
│   ├── ...
│   ├── comparison-analysis.md         ← 比較分析摘要
│   └── openspec-reference.md          ← OpenSpec 參考文件
├── sdd-ddd-webforms-skill/
│   ├── SKILL.md                       ← 主文件：決策樹、核心原則、架構規範
│   ├── references/
│   │   ├── new-feature-flow.md
│   │   ├── modify-existing-flow.md
│   │   ├── pr-review-checklist.md
│   │   └── git-flow-integration.md
│   └── templates/
│       ├── feature-spec.md
│       ├── lightweight-spec.md
│       ├── context-definition.md
│       └── CLAUDE.md
└── sdd-ddd-core-skill/
    ├── SKILL.md
    ├── PRACTICE_PLAN_tw.md
    ├── references/
    │   ├── new-feature-flow.md
    │   ├── modify-existing-flow.md
    │   ├── pr-review-checklist.md
    │   ├── git-flow-integration.md
    │   └── ddd-modeling-guide.md
    └── templates/
        ├── feature-spec.md
        ├── lightweight-spec.md
        ├── context-definition.md
        ├── aggregate-design.md
        └── CLAUDE.md
```

---

## Claude Code 在這裡的角色

本環境的主要工作是**實施已確認的 Skill 改進**，以及**review Skill 內容的品質和一致性**。

### 主要任務

1. **實施 approved Proposal**
   - 讀取 `proposals/PROPOSAL-NNN-*.md`，確認狀態為 `approved`
   - 按照 Proposal 的「影響範圍」修改對應檔案
   - 修改完成後，更新 Proposal 狀態為 `implemented`
   - 產出 CHANGELOG.md 的更新內容

2. **Review Skill 內容**
   - 檢查 WebForms 版和 Core 版的共通部分是否一致
   - 檢查英文版和繁體中文版是否同步
   - 指出格式不一致、引用錯誤、邏輯矛盾的地方

3. **協助起草或細化 Proposal**
   - 使用 `proposals/PROPOSAL-000-template.md` 格式
   - 狀態從 `draft` 開始
   - 主要評估和優先排序在 Claude.ai Project 進行

### 不在這裡做的事

- Proposal 的整合評估和優先排序（在 Claude.ai Project 進行）
- 大方向的架構決策討論（在 Claude.ai Project 進行）

---

## Proposal 工作規則

### 狀態流轉

```
draft → evaluating → approved → implemented
                   → rejected（記錄原因於 Proposal 內）
```

### 實施一個 approved Proposal 的步驟

1. 讀取 Proposal 檔案，確認狀態為 `approved`
2. 讀取「影響範圍」表格，列出所有要修改的檔案
3. 讀取每個目標檔案的現有內容
4. 按 Proposal 描述的解法進行修改
5. 確認修改符合 Proposal 意圖後，更新 Proposal 中的「評估紀錄」欄位，狀態改為 `implemented`
6. 產出 CHANGELOG.md 新增段落（格式見現有 CHANGELOG.md）

### WebForms 版 / Core 版的同步原則

兩版有共通的 SDD 流程部分（`new-feature-flow`、`modify-existing-flow`、`pr-review-checklist`、`git-flow-integration`）。修改共通部分時，**兩版必須同步修改**，除非 Proposal 明確說明只改其中一版。

---

## 核心設計原則（改動時不能違背）

1. **先規格後程式碼**：任何功能變更都需要先有 spec，不直接跳進實作
2. **務實不教條**：流程嚴謹度依變更影響動態調整（Ceremony Scaling），不是所有變更都需要走完整流程
3. **WebForms 版與 Core 版相容**：WebForms 版抽離出的 Domain 層程式碼，應能直接遷移到 Core 版專案
4. **英文版供 AI 使用**：英文版（無 `_tw` 後綴）是 Claude Code 實際引導開發時讀的檔案，必須精確、不模糊
5. **中文版供人閱讀**：繁體中文版是給團隊成員理解流程用的，語氣可以更口語

---

## OpenSpec 參考文件的使用原則

`proposals/openspec-reference.md` 是 OpenSpec 的概覽整理，用於快速建立比較分析的基準，不是完整知識庫。

- **概覽層級的比較**：直接使用本文件即可
- **深入某項功能或概念的細節時**：必須用 web search 查閱 OpenSpec 一手文件（GitHub: Fission-AI/OpenSpec），不能只依賴概述。例如：delta merge 的衝突解決機制、Custom Schema 的依賴圖運作方式、verify 的具體驗證項目等
- **發現本文件有誤或重要遺漏時**：提出修正建議，回來補充或修正 `openspec-reference.md`

---

## 團隊背景（供分析參考）

- **技術棧**：ASP.NET WebForms + Entity Framework，規劃遷移到 ASP.NET Core
- **團隊**：全端工程師，DDD 經驗尚淺，透過模擬專案學習中
- **規模**：約 100 人組織中的全端工程團隊
- **AI 工具**：Claude Code CLI 用於日常開發
- **語言慣例**：程式碼和 AI Skill 用英文；團隊溝通和人讀文件用繁體中文
