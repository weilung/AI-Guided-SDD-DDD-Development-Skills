# OpenSpec 參考文件

> **定位**：本文件是 OpenSpec 的**概覽整理**，用於快速建立比較分析的基準視角。
> 它涵蓋 OpenSpec 的核心概念、架構和功能面貌，但不可能記載所有細節。
>
> **使用原則**：
> - 進行概覽層級的比較分析時，以本文件為基礎即可
> - 當討論**深入觸及某項具體功能或概念的細節**時（例如 delta merge 的衝突解決機制、
>   Custom Schema 的依賴圖運作方式、verify 的具體驗證項目等），必須到網路上查閱
>   OpenSpec 的一手文件（GitHub: Fission-AI/OpenSpec），不能只依賴本文件的概述
> - 如果查閱後發現本文件的描述有誤或重要遺漏，應回來補充或修正
>
> **來源**：OpenSpec GitHub repo (Fission-AI/OpenSpec)、官網 openspec.dev、官方文件 docs/
> **版本基準**：OpenSpec 1.0+（OPSX 架構）
> **最後更新**：2026-04-13

## OpenSpec 是什麼

OpenSpec 是一個開源的 Spec-Driven Development（SDD）框架，設計給 AI coding assistant 使用。GitHub ~19.9k stars，支援 25+ AI 工具（Claude Code、Cursor、Codex、GitHub Copilot、Windsurf、Gemini CLI、Kiro 等）。以 npm 套件發布，需要 Node.js 20.19.0+。

**核心主張**：在寫程式碼前，人和 AI 先對「要蓋什麼」達成共識。Spec 是系統行為的 source of truth，程式碼只是實作細節。

**設計哲學**（四個原則）：
- **Fluid not rigid**：不鎖定階段，隨時可以回頭修改任何 artifact
- **Iterative not waterfall**：擁抱需求變更，理解會隨實作加深而演進
- **Easy not complex**：秒級初始化，最小化設定，只在需要時才自訂
- **Brownfield-first**：為修改現有系統而設計，不只是從零開始

**不處理的事**：架構設計、領域建模（DDD）、Git 策略、遷移規劃、術語管理。

---

## 核心概念

### 1. 雙資料夾模型

```
openspec/
├── config.yaml       # 專案配置（技術棧、慣例、per-artifact 規則）
├── schemas/           # 自訂工作流 schema（可選）
├── specs/             # Source of Truth — 系統現在的完整行為
│   └── auth/
│       └── spec.md
└── changes/           # 進行中的變更 — 每個變更一個資料夾
    ├── add-dark-mode/
    │   ├── .openspec.yaml  # 變更的 metadata
    │   ├── proposal.md
    │   ├── design.md
    │   ├── tasks.md
    │   └── specs/          # Delta specs
    │       └── ui/
    │           └── spec.md
    └── archive/            # 已完成的變更（審計軌跡）
```

**`specs/`** 是「系統行為的當前狀態」，隨時反映系統全貌。按 capability 組織（如 auth/、ui/、payments/）。
**`changes/`** 是「正在進行的修改」，完成後 merge 回 `specs/`。每個變更完全隔離在自己的資料夾中。

這個設計類似 Git 的 main branch vs feature branch，但用在規格文件上。

### 2. Delta Spec（差異規格）

OpenSpec 最核心的概念，也是它 brownfield-first 的關鍵。不要求重寫整份規格，只描述「變了什麼」：

```markdown
# Delta for Auth

## ADDED Requirements
### Requirement: Two-Factor Authentication
The system MUST support TOTP-based two-factor authentication.

#### Scenario: OTP required
- GIVEN a user with 2FA enabled
- WHEN the user submits valid credentials
- THEN an OTP challenge is presented

## MODIFIED Requirements
### Requirement: Session Timeout
The system SHALL expire sessions after 30 minutes of inactivity.
(Previously: 60 minutes)

#### Scenario: Idle timeout
- GIVEN an authenticated session
- WHEN 30 minutes pass without activity
- THEN the session is invalidated

## REMOVED Requirements
### Requirement: Remember Me
(Deprecated in favor of 2FA)
```

**Delta 操作類型**：ADDED、MODIFIED、REMOVED、RENAMED（重新命名 requirement）。

**Merge 機制**：Archive 或 Sync 時，CLI 自動把 ADDED 新增到主 spec、MODIFIED 覆蓋舊版本、REMOVED 刪除、RENAMED 更新名稱。如果 delta 引用的 capability 在 `specs/` 中不存在，會自動建立新的 spec 檔案（僅限 ADDED）。

**驗證系統**：`openspec validate` 可檢查 delta 格式是否正確、MODIFIED 引用的 requirement 是否存在於主 spec 中、是否有衝突。支援 `--strict` 模式和 CI/CD 整合。

**規格語言**：使用 RFC 2119 關鍵字（SHALL、MUST、SHOULD 等），搭配 Given/When/Then 情境。

### 3. 四個核心 Artifact

每個 change 資料夾包含四個 artifact，依序建立：

```
proposal ──► specs ──► design ──► tasks
  why         what       how       steps
  + scope     changes    approach  to take
```

每個 artifact 為下一個提供上下文。依賴關係由 artifact state machine 管理。

| Artifact | 職責 | 狀態管理 |
|---|---|---|
| `proposal.md` | Why（Intent）+ What（Scope）+ Approach | PENDING → READY → DONE |
| `specs/*.md` | 行為規格（Delta format: ADDED/MODIFIED/REMOVED） | 依賴 proposal |
| `design.md` | 技術方案（How） | 依賴 proposal |
| `tasks.md` | 可勾選的實作清單，每個任務 ≈ 一個可驗證單元 | 依賴 specs + design（BLOCKED 直到兩者 DONE）|

**Artifact State Machine**：每個 artifact 有三個狀態——PENDING（尚未就緒）、READY（依賴已滿足，可以建立）、DONE（已完成）。`openspec status` 命令會計算當前狀態並告訴 AI 下一步可以做什麼。

### 4. 工作流程（OPSX 1.0 架構）

OpenSpec 1.0 從「Phase-Locked」轉型為「Action-Based」。關鍵轉變：不再是線性的階段鎖定，而是流動的動作——你可以隨時回頭修改任何 artifact。

```
傳統（階段鎖定）：
PLANNING ────────► IMPLEMENTING ────────► DONE
    │                                       │
    │            "不能回頭"                  │
    └───────────────────────────────────────┘

OPSX（流動動作）：
proposal ──► specs ──► design ──► tasks ──► implement
    ▲           ▲         ▲         ▲
    └───────────┴─────────┴─────────┘
              隨時可以回頭修改
```

**兩種 Profile**：

| Profile | 包含的工作流 | 適用場景 |
|---|---|---|
| **Core**（預設） | propose, explore, apply, archive（4 個） | 大多數團隊，快速上手 |
| **Custom** | 從 11 個工作流中任選子集 | 需要更精細控制的團隊 |

**完整的 11 個工作流命令**：

| 命令 | 功能 | 說明 |
|---|---|---|
| `/opsx:explore` | 思考階段 | 在承諾變更前自由探索——調查問題、比較方案、分析程式碼。想法成熟後轉入 propose |
| `/opsx:propose` | 一步建立 | 一次建立 change + 產生所有 planning artifacts（Core profile 的入口） |
| `/opsx:new` | 建立 change | 只建立 change 資料夾和 metadata（Expanded profile 用） |
| `/opsx:continue` | 逐步建立 | 一次建立一個 artifact，依照依賴圖順序 |
| `/opsx:ff` | 快轉建立 | 一次產生所有 planning artifacts（proposal + specs + design + tasks） |
| `/opsx:apply` | 實作 | AI 按 tasks 逐項實作，完成一項勾一項。實作中發現需要調整時，可以直接更新 artifact |
| `/opsx:verify` | 驗證閘門 | 檢查所有 artifact 格式正確、tasks 全部完成、delta spec 有效。支援 CI/CD |
| `/opsx:sync` | 中途同步 | 把 delta specs 同步到主 `specs/`，但不歸檔 change。允許開發中的 checkpoint commit |
| `/opsx:archive` | 歸檔 | delta merge 回主 spec，change 移到 `archive/`。會檢查 specs 是否已 sync |
| `/opsx:bulk-archive` | 批次歸檔 | 一次歸檔多個已完成的 change，自動偵測 spec 衝突並按時間順序解決 |
| `/opsx:onboard` | 引導上手 | 掃描現有程式碼，找出一個小改進，引導開發者走完完整流程 |

**典型使用模式**：

快速模式（大多數情境）：
```
/opsx:propose "add dark mode" → /opsx:apply → /opsx:archive
```

探索模式（不確定怎麼做時）：
```
/opsx:explore → /opsx:propose → /opsx:apply → /opsx:archive
```

精細模式（需要逐步控制）：
```
/opsx:new → /opsx:continue → /opsx:continue → ... → /opsx:apply → /opsx:verify → /opsx:sync → /opsx:archive
```

### 5. Project Config（專案配置）

`openspec/config.yaml` 注入專案層級的上下文和規則到所有 AI 產生的 artifact：

```yaml
schema: spec-driven    # 或自訂 schema 名稱

context: |
  Tech stack: TypeScript, React, Node.js
  API conventions: RESTful, JSON responses
  Testing: Vitest for unit tests, Playwright for e2e

rules:
  proposal:
    - Include rollback plan
    - Identify affected teams
  specs:
    - Use Given/When/Then format for scenarios
  design:
    - Include sequence diagrams for complex flows
```

AI 在產生每個 artifact 時，會自動讀取對應的 context + rules + template，組裝成結構化的指令。這個「Dynamic Instruction Generation」機制讓不同專案可以有不同的規範要求，而不需要修改 OpenSpec 本身。

### 6. Custom Schemas（自訂工作流）

團隊可以定義完全自訂的 artifact 工作流：

```yaml
# openspec/schemas/my-workflow/schema.yaml
name: my-workflow
version: 1
description: My team's custom workflow
artifacts:
  - id: proposal
    generates: proposal.md
    description: Initial proposal document
    template: proposal.md
    instruction: |
      Create a proposal that explains WHY this change is needed...
  - id: tasks
    generates: tasks.md
    depends_on: [proposal]
    description: Implementation tasks
    template: tasks.md
```

可以新增、移除、重新排列 artifact，自訂 template 和 AI 指令。支援從內建 schema fork 後修改。

### 7. Ceremony Scaling

OpenSpec 透過 Profile + Custom Schema 兩層來實現彈性：

- **Core Profile**：4 個命令，最低限度的流程
- **Custom Profile**：從 11 個命令中挑選適合的子集
- **Custom Schema**：定義最小化的 artifact 集（例如只要 proposal + tasks，跳過 design 和 specs）
- **Rapid Schema 範例**：只產生 proposal + tasks，跳過 specs 和 design，適合極小變更

原則：「避免官僚化，用能讓變更可驗證的最低限度」。

### 8. Brownfield 支援

OpenSpec 明確定位為 brownfield-first，有幾個相關功能：

**逆向工程模式**：建立一個叫 `baseline` 的 change proposal，讓 AI 讀現有程式碼並反向產生 spec，描述系統當前行為。產生的 spec archive 後成為 source of truth 的起點。

**漸進累積**：不需要一次為整個系統產生 spec。每次修改時才為被觸及的部分建立 spec，隨時間逐步建立完整的系統文件。

**Delta-first**：所有變更描述的都是「差異」，不需要理解整個系統才能開始使用。

### 9. 多工具整合

OpenSpec 不綁定特定 AI 工具。`openspec init` 時選擇使用的工具，CLI 會產生對應格式的檔案：

- Claude Code：`.claude/skills/` + `.claude/commands/opsx/`
- Cursor：`.cursor/skills/` + `.cursor/rules/`
- GitHub Copilot：`.github/prompts/`
- 其他工具：透過 AGENTS.md 或自然語言指令

`openspec update` 會重新產生這些檔案，確保指令是最新版。支援同一專案中不同成員使用不同 AI 工具。

---

## 與我們的 SDD/DDD 的關鍵差異

### 我們比 OpenSpec 強的地方

1. **DDD 領域建模**：Aggregate、Value Object、Domain Events、Context Mapping——OpenSpec 完全不涉及。這是我們最大的差異化優勢。
2. **架構規範**：Clean Architecture 四層規則、Domain 層純淨度檢查、層間依賴方向約束。
3. **深度引導問答**：Decision Tree + 分階段引導問題，根據開發者回答動態調整。OpenSpec 的引導是「產生 artifact + 執行 tasks」，比較被動。
4. **Git Flow 整合**：branch naming、commit message conventions、gate checks。OpenSpec 不管版本控制。
5. **遷移策略**：WebForms → Core 的漸進遷移規劃、遷移準備度評分（A~F）。
6. **術語管理**：Ubiquitous Language / Glossary 機制，確保團隊和 AI 使用一致的業務術語。
7. **PR Review 品質**：Architecture Score、四層各自的 checklist、全鏈路驗證。

### OpenSpec 比我們強的地方

1. **Delta Spec 系統**：結構化的 ADDED/MODIFIED/REMOVED/RENAMED，搭配自動驗證和衝突偵測。比我們的 Current/Changed Behavior 自由文字更嚴謹。
2. **系統現狀 Source of Truth**：`specs/` 隨時反映系統全貌，透過 archive/sync 自動維護。我們的 `completed/` 只是歸檔。
3. **Artifact State Machine**：依賴圖驅動的狀態管理（PENDING/READY/DONE/BLOCKED），確保 artifact 按正確順序建立。
4. **完成時自動更新**：archive + sync 自動 merge delta，bulk-archive 處理衝突。我們靠人工。
5. **Explore 階段**：在承諾變更前有正式的探索階段，適合不確定怎麼做的情境。我們沒有對應物。
6. **Custom Schema**：完全可自訂的工作流引擎，比我們的 Ceremony Scaling 表格靈活得多。
7. **Project Config**：專案層級的 context + per-artifact rules 注入機制。我們的 CLAUDE.md 類似但不如它結構化。
8. **中途同步（sync）**：可以在不歸檔的情況下把 delta 同步到主 spec，做為 checkpoint。
9. **導入門檻低**：一個 npm install + openspec init，Core profile 4 個命令。`/opsx:onboard` 引導新手。
10. **格式驗證**：`openspec validate` + CI/CD 整合，機器檢查 spec 格式正確性。
11. **逆向工程模式**：有文件化的 baseline 做法，讓 AI 從現有程式碼反向產生 spec。

### 需要重新評估的差距

舊版參考文件低估了 OpenSpec 的 AI 引導能力。1.0 之後的 Dynamic Instruction Generation（從 schema + config + template 組裝上下文）已經相當成熟。我們的引導仍然更「深」（DDD 問答、架構決策引導），但 OpenSpec 的引導更「結構化」（狀態機、依賴圖、可自訂指令）。兩者是不同維度的優勢。

---

## 我們的借鏡策略

**不是取代，是融合**：把 OpenSpec 的變更管理優勢融入我們的 DDD 引導框架。

**可以借鏡的**：
- Delta spec 格式 + 驗證（→ P002）
- Source of truth 機制（→ P003）
- Artifact 依賴和狀態管理的概念（→ P004）
- 完成流程的 verify + sync 組合（→ P005）
- Custom schema 的靈活度思維（→ P006）
- 逆向工程的標準做法（→ P007）
- Explore 階段的概念（→ 可能需要新的 Proposal）
- Project config 的 per-artifact rules 注入（→ 可能需要新的 Proposal）

**不需要借鏡的**：
- 多工具整合（我們只用 Claude Code）
- npm CLI 工具鏈（我們的 AI 引導不需要獨立的 CLI）
- Slash command 適配器系統（同上）

具體借鏡項目見各 Proposal（PROPOSAL-001 至 PROPOSAL-007）。
