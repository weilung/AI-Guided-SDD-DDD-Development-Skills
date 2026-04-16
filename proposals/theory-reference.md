# SDD/DDD Skill 設計決策的理論依據對照表

> **用途**：向長官或利害關係人報告時，說明本 Skill 的設計決策有哪些業界理論或實踐支撐。
>
> **格式**：每個設計決策 → 對應理論 → 出處 → 一句話解釋為什麼適用。

---

## 一、核心架構

| 我們的設計決策 | 理論 / 業界實踐 | 出處 | 為什麼適用 |
|---|---|---|---|
| Domain 層不依賴外部套件，依賴方向朝內 | **Clean Architecture** | Robert C. Martin,《Clean Architecture》(2017) | 依賴反轉（Dependency Inversion）確保核心業務邏輯不受框架、DB、UI 變動影響 |
| 每個 Bounded Context 有獨立的術語表和規則 | **Bounded Context** | Eric Evans,《Domain-Driven Design》(2003) Ch.14 | 不同業務子域的同一個詞可能意義不同，明確邊界避免語義混淆 |
| 使用 Aggregate 作為一致性邊界 | **Aggregate Pattern** | Eric Evans (2003); Vaughn Vernon,《Implementing Domain-Driven Design》(2013) | 單一交易只修改一個 Aggregate，降低併發衝突、簡化不變條件維護 |
| 程式碼和 spec 使用統一術語 | **Ubiquitous Language** | Eric Evans (2003) Ch.2 | 開發者和領域專家共用語言，消除「翻譯層」的認知損耗和溝通錯誤 |

---

## 二、開發流程（SDD）

| 我們的設計決策 | 理論 / 業界實踐 | 出處 | 為什麼適用 |
|---|---|---|---|
| 先寫規格再寫程式碼（Spec Before Code） | **Specification-Driven Development** | 根植於 BDD（Dan North, 2006）和 TDD（Kent Beck, 2002）的演化 | 規格即可執行的需求文件，減少「做完才發現做錯」的浪費 |
| behavior.md 使用 Given/When/Then 格式 | **Behavior-Driven Development (BDD)** | Dan North, "Introducing BDD" (2006); Gherkin 語法（Cucumber 團隊） | 情境式描述比宣告式規則更容易被非工程師理解和驗證 |
| 依變更影響動態調整流程嚴謹度（Ceremony Scaling） | **Process Tailoring** | SEI/CMMI（Software Engineering Institute）; Alistair Cockburn, "Crystal Methods" (2004) | 不是所有變更都需要相同嚴謹度，過度流程（over-ceremony）和流程不足同樣有害 |
| PR Review 先讀 spec 再看 code（P007b） | **Shift Left** | Larry Smith, IBM (2001); 廣泛用於 DevOps 和品質工程 | 在流程早期發現問題，成本遠低於後期修復。Review 時帶著需求意圖看程式碼，比盲看程式碼更有效 |

---

## 三、文件管理策略

| 我們的設計決策 | 理論 / 業界實踐 | 出處 | 為什麼適用 |
|---|---|---|---|
| behavior.md 作為系統行為的單一權威來源 | **Single Source of Truth (SSOT)** | 資料管理基本原則；廣泛用於 Data Governance 和 Configuration Management | 當同一資訊存在多份拷貝時，版本分歧不可避免。指定一份為權威，其他為衍生 |
| Delta Spec 只在變更 spec 中使用，不累積到 consolidated 文件 | **Event Sourcing vs. State Snapshot** | Martin Fowler (2005); Greg Young, CQRS/ES (2010) | consolidated 文件 = 當前狀態快照（state）；delta spec = 事件記錄（event）。兩者分離，各自清晰 |
| rules.md 當索引、behavior.md 當內容（A+C 結構） | **Separation of Concerns** | Edsger Dijkstra (1974); 軟體設計基本原則 | 「快速查閱規則清單」和「理解完整行為情境」是兩種不同需求，用不同文件各司其職 |
| RENAMED 操作追蹤概念改名 | **Traceability** | IEEE 830; Requirements Engineering（需求工程） | 維持需求追蹤鏈——知道「這條規則從哪來、改過什麼名字」，避免孤立條目 |

---

## 四、信任與驗證策略

| 我們的設計決策 | 理論 / 業界實踐 | 出處 | 為什麼適用 |
|---|---|---|---|
| 信任預設 + 必要時驗證（P008 drift verification） | **Eventually Consistent** | Werner Vogels (Amazon CTO), "Eventually Consistent" (2008) | 分散式系統不追求即時一致，而是「最終會一致」。rules.md 和 behavior.md 之間允許短暫不一致，透過 `/dflow:verify` 定期收斂 |
| 機械層驗證先做，語意層後做 | **Defense in Depth** | 資安原則（NIST）; 用於系統設計的分層防禦 | 先用低成本高確定性的機械檢查（BR-ID 對應、anchor 有效性）攔截明顯問題，再評估是否需要高成本的語意判斷 |
| 驗證分層：AI 可獨立驗證 vs 需開發者確認（P005 B6） | **Automation Boundary** | 人機互動（HCI）領域; Parasuraman et al., "A model for types and levels of human interaction with automation" (2000) | 不是所有驗證都適合自動化——機械性檢查（Domain 純淨度、checklist 勾選）由 AI 做；需要意圖判斷的由人做 |
| Wave 之間設置 Checkpoint 確認採用率 | **Build-Measure-Learn** | Eric Ries,《The Lean Startup》(2011) | 每一波實施後收集數據驗證假設，不成立就調整方向，避免堆疊式失敗 |

---

## 五、導入策略

| 我們的設計決策 | 理論 / 業界實踐 | 出處 | 為什麼適用 |
|---|---|---|---|
| P003 先 pilot 一個 BC，再決定全面 rollout | **Canary Release / Pilot** | 軟體部署策略; Netflix 和 Google 的漸進式部署實踐 | 流程變更和軟體部署一樣有風險——小範圍先驗證，失敗時只需修一個 BC，不是全部 |
| P001 的三級透明化（Flow entry → Phase gate → Step-internal） | **Progressive Disclosure** | UX 設計原則; Jakob Nielsen (1994) | 一次倒出所有資訊會造成認知過載。開發者只在需要時才看到下一層細節 |
| 不強制時間分段導入，靠任務類型動態調整 | **Situated Learning / Task-Driven Learning** | Jean Lave & Etienne Wenger,《Situated Learning》(1991) | 人在實際情境中學習比按時間表學習更有效。做到什麼任務，就學到對應的嚴謹度 |
| brownfield 趁修改時順便建文件（P007a） | **Opportunistic Refactoring** | Martin Fowler, "Opportunistic Refactoring" (2011) | 不排專案做「從零補文件」，而是每次經過時改善一點。累積效應大於一次性運動 |

---

## 六、AI 協作

| 我們的設計決策 | 理論 / 業界實踐 | 出處 | 為什麼適用 |
|---|---|---|---|
| 對話驅動的 spec 填寫（AI 引導問答）而非空白模板自填 | **Scaffolding** | Vygotsky, Zone of Proximal Development (1978); 教育學理論 | AI 扮演「鷹架」角色，透過提問引導開發者完成他獨自無法輕易完成的規格設計 |
| `/dflow:` 指令化入口 + auto-trigger 安全網 | **Explicit Invocation + Safety Net** | 混合模式設計; 類似 Git hooks（pre-commit / pre-push）| 主路徑靠明確指令觸發（可控），安全網靠自動偵測攔截遺漏（防呆） |
| Spec 內容語義耦合，不拆成多檔案 | **Cognitive Locality** | 認知心理學; George Miller, "The Magical Number Seven" (1956) | 相關資訊放在一起減少工作記憶的跳轉負擔。DDD 的領域概念→行為規格→Aggregate→實作是一條語義鏈，拆開會增加 reviewer 的認知成本 |

---

## 七、使用建議

向長官報告時，不需要逐條引用。建議挑選 3-5 個最能引起共鳴的：

**如果長官關注「為什麼要這樣做」（方法論層面）**：
- Clean Architecture + DDD（業界主流架構模式）
- BDD / Specification-Driven（先規格後程式碼不是我們發明的）
- Process Tailoring / Ceremony Scaling（不是教條式流程）

**如果長官關注「風險管理」**：
- Build-Measure-Learn（Wave Checkpoint 有實證基礎）
- Canary / Pilot（不一次全推）
- Defense in Depth（分層驗證）

**如果長官關注「團隊能不能做到」**：
- Situated Learning（任務驅動學習，不是上課式導入）
- Scaffolding（AI 輔助降低門檻）
- Progressive Disclosure（不一次倒出所有複雜度）

---

## 附註

本文件中的理論引用為概念層面的對應關係，不代表我們的實作完全複製了原始理論的所有細節。例如 Eventually Consistent 原本描述分散式資料庫的一致性模型，我們借用其「信任預設 + 最終收斂」的核心思想應用於文件管理。
