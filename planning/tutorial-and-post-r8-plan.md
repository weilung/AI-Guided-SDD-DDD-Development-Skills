# Tutorial 重建規劃 + Post-R8 整體計畫

> **目的**：規劃 Tutorial 重建的結構與生產方式（Subagent 自動化）+ Post-R8 整體執行時程
> **建立日期**：2026-04-28
> **前置文件**：`planning/r8-overview.md`（R8 全程脈絡）
> **狀態更新（2026-05-01）**：Closeout C2 tutorial 收尾已完成。現行 tutorial 入口為 `tutorial/README.md`；雙劇情分別從 `tutorial/01-greenfield-core/00-setup.md` 與 `tutorial/02-brownfield-webforms/00-setup.md` 開始。P001 前 / P001 hybrid 單檔 tutorial 已刪除，避免誤用。

---

## 一、Tutorial 為什麼要重建

### 現狀

`tutorial/` 目錄在 R7 期已被標為「重建範疇」，原因：

- R7-R8 期間 `/dflow:` 命令集大幅變動（新增 init-project / new-phase / finish-feature）
- R7-R8 引入 feature directory + multi-phase + Lightweight Changes 結構
- R8b canonical English structural language + section anchors 改變了整個 spec 文件外觀
- 既有 tutorial 已嚴重過時，與當前 skill 不對齊

### 使用者需求（2026-04-28 釐清）

> 「Tutorial 的重寫，我想是大量工作，我想要的是完整劇情，與產生的文件顯示，這樣可以讓同事沒實際使用前來審視有沒有問題。」

**關鍵需求**：
1. **完整劇情驅動**：不是 reference 風格的「點到為止」說明，而是「Alice 想做 X，於是觸發 /dflow:Y，Dflow 引導她…」的故事敘事
2. **生成的 spec 檔可瀏覽**：同事能 read-through 看到實際 specs/features/active/{SPEC-ID}-{slug}/_index.md 等檔的真實內容
3. **無需實際使用就能審視**：適合內部 review、stakeholder 簡報、新人 onboarding

---

## 二、Tutorial 結構規劃

### 目錄結構（雙劇情）

```
tutorial/
├── README.md                                # 索引：劇情列表 + 適用對象 + 閱讀順序建議
├── 01-greenfield-core/                      # 劇情 1：Core 新專案（greenfield）
│   ├── 00-setup.md                          # 假想專案介紹 + 角色設定
│   ├── 01-init-project.md                   # /dflow:init-project 全程對話
│   ├── 02-new-feature.md                    # /dflow:new-feature 第一個 feature
│   ├── 03-new-phase.md                      # /dflow:new-phase 加 phase 2
│   ├── 04-modify-existing.md                # /dflow:modify-existing T2 修改
│   ├── 05-bug-fix.md                        # /dflow:bug-fix
│   ├── 06-finish-feature.md                 # /dflow:finish-feature 收尾
│   └── outputs/                             # 完整生成的 specs/ 結構
│       ├── CLAUDE.md
│       └── specs/
│           ├── shared/_overview.md
│           ├── shared/_conventions.md
│           ├── domain/glossary.md
│           ├── domain/context-map.md
│           ├── domain/expense/{models,rules,behavior,events}.md
│           ├── architecture/tech-debt.md
│           ├── architecture/decisions/README.md
│           └── features/
│               ├── active/SPEC-20260501-002-{slug}/...
│               └── completed/SPEC-20260428-001-{slug}/...
└── 02-brownfield-webforms/                  # 劇情 2：WebForms brownfield
    ├── 00-setup.md                          # 假想專案：既有 WebForms 訂單系統，要導入 Dflow
    ├── 01-init-project.md
    ├── 02-modify-existing.md                # brownfield 主要進入點
    ├── 03-baseline-capture.md               # 抽 domain 邏輯時的 baseline 紀錄
    ├── 04-new-feature.md                    # 新需求進來的處理
    ├── 05-bug-fix.md
    ├── 06-finish-feature.md
    └── outputs/
        └── specs/
            ├── shared/...
            ├── domain/...
            ├── migration/tech-debt.md
            └── features/...
```

### 每個劇情段（XX-yyy.md）的內部結構

```markdown
# {命令} — {一句話描述劇情段}

## 劇情背景
{Alice 為什麼此刻要觸發這個命令；前情提要}

## 完整對話

> **Alice**: {自然語言觸發}

> **Dflow**: {第一步引導}

> **Alice**: {回應 / 提供資訊}

> **Dflow**: {下一步}

...（完整對話直到該命令完成）

## 本段產出的檔案

- `{檔案路徑}`：{一句話說明用途；連結到 outputs/ 對應檔}
- `{檔案路徑}`：...

## 觀察重點

- {3-5 個適合同事 review 時關注的點}
- {例如：「注意 BR-ID 命名約定」「注意 _index.md 的 6 個 section 順序」「注意 `git mv` 的時機」}

## 下一個劇情段

→ `02-new-feature.md`：Alice 開始第一個 feature
```

### 雙劇情的核心差異

| 面向 | 劇情 1 (Core greenfield) | 劇情 2 (WebForms brownfield) |
|---|---|---|
| 角色 | Alice — 新專案 PO + 工程師 | Bob — 維護既有 WebForms 系統的工程師 |
| 假想專案 | ExpenseTracker（差旅費用申報）| OrderManager（既有訂單系統） |
| init-project 重點 | mandatory baseline 含 context-map.md / ADR README | 含 migration/tech-debt.md；baseline capture 流程 |
| 主要 entry point | /dflow:new-feature | /dflow:modify-existing + baseline capture |
| Domain 處理 | 直接 DDD：Aggregate / Value Object / Domain Event | 從 Code-Behind 抽離到 src/Domain/ |
| Tech debt 處理 | 偶爾記 ADR | 每次 modify 都記 migration tech-debt |

---

## 三、Subagent 自動化生產 Tutorial

### 構想（使用者 2026-04-28 提案）

> 「是否有可能跑 subagent，讓 AI 自己實際使用 skill 開發專案，歷程寫成 Tutorial？」

**評估**：技術上可行，且是「Tutorial fidelity = 100%」的最佳途徑（內容是 skill 真實 output，不是編造）。

### Subagent 模式

```
每個 Tutorial 段（如 01-init-project.md）= 1 次 subagent 呼叫

Agent({
  description: "Run skill end-to-end as Alice + Dflow simulator",
  isolation: "worktree",
  subagent_type: "general-purpose",
  prompt: "..."  # 見下方範本
})
```

### 角色分工

Subagent 在單次呼叫內扮演**雙角色**：

1. **Alice（開發者）**：發起需求、回答 Dflow 的問題、做選擇
2. **Dflow guardian**：讀 `sdd-ddd-core-skill/SKILL.md` + `references/*.md`，依決策樹引導 Alice

### Worktree 隔離

每個劇情段在獨立 worktree 跑，避免污染 main 分支：

- 主 repo 提供 skill 內容（read-only context）
- Worktree 內模擬「目標專案」的 specs/ 結構（write target）
- Subagent 完成後，把產出（對話 .md + specs/ 檔）merge 回 main 的 `tutorial/` 目錄

### Subagent prompt 範本（劇情 1 段 1：init-project）

```
你扮演兩個角色：
1. Alice — Core 新專案 ExpenseTracker 的 PO + 工程師
2. Dflow guardian — 讀 sdd-ddd-core-skill/SKILL.md 引導 Alice

劇情：
Alice 剛建好一個 ASP.NET Core 9 + EF Core + MediatR 的空專案
（src/ExpenseTracker.{Domain,Application,Infrastructure,WebAPI}）
要導入 Dflow。她在 IDE 中下指令 `/dflow:init-project`。

任務：
模擬 Alice 與 Dflow 的完整對話（從 Alice 觸發到 init-project 流程結束），
依 sdd-ddd-core-skill/references/init-project-flow.md 的 Step 1-5 全程執行。

Alice 的選擇：
- Q1: greenfield
- Q2: ASP.NET Core 9 / EF Core 8 / MediatR 12 / xUnit
- Q3: 新建（無 migration）
- Q4: English scaffolding
- Q5: 全部選（_overview, _conventions, Git-principles-trunk, CLAUDE.md snippet）

產出：
1. 寫對話 markdown 到 `tutorial/01-greenfield-core/01-init-project.md`，
   依 plan B-2 的「劇情段內部結構」格式撰寫（含「劇情背景」「完整對話」
   「本段產出的檔案」「觀察重點」「下一個劇情段」5 個小節）
2. 在 worktree 內實際建立 specs/ 結構與所有 init flow 應產生的檔案
   （`specs/shared/_overview.md`、`specs/shared/_conventions.md`、
   `specs/shared/Git-principles-trunk.md`、`specs/domain/glossary.md`、
   `specs/domain/context-map.md`、`specs/architecture/tech-debt.md`、
   `specs/architecture/decisions/README.md` + `specs/features/{active,completed,backlog}/.gitkeep`），
   內容用對應 templates 填充並做合理 placeholder 替換（如 {系統名稱} → ExpenseTracker）
3. 產出位置：worktree 內 `tutorial-output/` 目錄
   （tutorial/01-greenfield-core/01-init-project.md + tutorial-output/specs/...）

完成後回報：
- 對話 markdown 字數
- worktree 內生成的檔案清單
- 任何需要使用者拍板的設計選擇（例如假想專案的「業務領域」描述細節）
```

### 試水溫策略

**先跑 1 個段（init-project）驗證品質**：

- 評估產出的對話品質（自然度、忠於 skill flow）
- 評估生成 specs 檔的完整性（與 templates 對齊）
- 若品質高 → 擴展到劇情 1 全部 6 段
- 若品質低 → 調整 prompt 範本（角色描述、對話風格、輸出格式）

**全劇本估算**（雙劇情 × 6-7 段）：~14 次 subagent 呼叫，每次預估 15-30 分鐘 subagent 工作量。

---

## 四、Post-R8 整體執行時程

### 階段 1：本主 session 內（2026-04-28，今日）

| 步驟 | 預估 | 狀態 |
|---|---|---|
| A. R8 全程總覽（`planning/r8-overview.md`） | 5 min | ✅ 完成 |
| B. Tutorial 規劃 + Subagent 計畫（本檔） | 15 min | 🟢 處理中 |
| C. 跑 Tutorial Subagent 第 1 段（劇情 1 / init-project，試水溫） | 20-30 min | ⏳ 等本檔完成 |
| D. Commit + handoff notes（`planning/post-r8-handoff.md`） | 5 min | ⏳ |

### 階段 2：Debian 新 session（連假期間，~2-4 天）

從 handoff notes 接續：

| 步驟 | 預估 |
|---|---|
| 跑 Tutorial Subagent 劇情 1 剩餘段（5-6 段）| 2-3 hr |
| 跑 Tutorial Subagent 劇情 2 全部段（6-7 段）| 2-3 hr |
| Tutorial README.md + 索引整理 | 30 min |
| Tutorial 全程 review（人工或 Subagent）| 1 hr |

**完成判定**：Tutorial 雙劇情齊備、可瀏覽、同事可 review。

### 階段 3：連假後（Closeout 全程）

| 步驟 | 預估 |
|---|---|
| Closeout C1：繁中版 skill 同步（視 Tutorial 中文版產出再決定範圍）| 1-3 day |
| Closeout C2：Tutorial 收尾（補圖、調整劇情段）| ✅ 2026-05-01 完成 |
| Global Sweep（仿 R6 後做法，可選）| 0.5 day |
| 改名遷移：dev repo + dist repo 切割（per r8-overview.md §關鍵設計決定 #2）| 1-2 day |
| Dflow V1 ships（README、LICENSE、CHANGELOG release notes、Git tag v1.0.0）| 0.5 day |

**Dflow V1 ships 預估時程**：連假後約 1-2 週內。

---

## 五、Tutorial 與 Closeout C1 / 改名遷移的依賴順序

```
Tutorial 優先（多劇情產出）
    ↓
Closeout C1 評估：Tutorial 中文版是否需要 + skill 中文版必要性是否降低
    ↓
（視評估）C1 範圍可能大幅縮減
    ↓
Closeout C2 (Tutorial 收尾)
    ↓
Global Sweep (optional)
    ↓
改名遷移 (dev + dist repo 切割)
    ↓
Dflow V1 ships
```

**關鍵 hinge**：Tutorial 完成後，C1 範圍可能從「全 skill 中文化」縮減到「只 Tutorial 中文化」，省下大量翻譯工作。

---

## 六、要派給 Codex 的工作（weekly reset 後可用）

Codex 適合「處理到完」型任務。Post-R8 適合派 Codex 的：

1. **Tutorial Subagent 各段執行**：每次一個劇情段，明確輸入（prompt + worktree）+ 明確輸出（對話 md + specs/ 檔）。Codex 6%/週 可跑 ~6-8 段（視 token）
2. **Closeout C1 中文翻譯**：機械翻譯型，scope 大但不需設計判斷
3. **Global Sweep**：grep + 一致性確認，純機械
4. **改名遷移腳本**：sed-like 替換 + git mv，純機械

不適合派 Codex 的（需要 Cursor / Claude Code）：

- Tutorial 整體規劃 / 劇情設計（需設計判斷）
- C1 範圍評估（需 trade-off 思考）
- 改名遷移策略決定（需 architecture 判斷）

---

## 七、開放問題（待後續討論）

| 議題 | 影響 | 建議處理時點 |
|---|---|---|
| Tutorial 寫中文還是英文？ | 影響 C1 範圍 | C1 啟動前拍板 |
| 假想專案名稱 / 業務領域具體設計 | Subagent prompt 細節 | 第 1 段試水溫時順便拍板 |
| Subagent worktree 內若失敗如何 fallback | 影響執行可靠性 | 第 1 段試水溫時觀察 |
| dev repo 名稱（保留現名 vs 改 `dflow-dev`）| 改名遷移策略 | 改名遷移啟動前拍板 |
| dist repo 結構（單一 skill / 雙 skill / 含 scaffolding 與否）| 影響使用者首日體驗 | 改名遷移啟動前拍板 |
| Dflow V1 是否同時兼容多 AI 工具（Claude Code / Codex / Gemini）| 影響 dist repo 結構 | V1 ships 前拍板（per backlog § 2）|

> **2026-05-01 update**：dist repo 初步策略已整理於 `planning/dist-repo-strategy.md`。目前建議本 repo 維持 dev repo、不立即改名；dist repo 以手動 export 的 public projection 起步。
