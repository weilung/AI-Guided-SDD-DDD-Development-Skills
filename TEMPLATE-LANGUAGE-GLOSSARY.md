<!-- Maintenance contract for Dflow. See proposals/PROPOSAL-013 §1.1 for origin. -->

# Template Language Glossary

This file is a human reading aid and review reference for Dflow template terminology. It is not a second template set.

Template headings, field labels, anchors, and placeholder names use canonical English. Developer-authored content inside those sections may use the project language.

## Glossary

| English term | 繁體中文對照 | 使用位置 | 說明 |
|---|---|---|---|
| Implementation Tasks | 實作任務 | `phase-spec.md`, `lightweight-spec.md` | AI 產生與追蹤 task checklist 的段落 |
| Behavior Scenarios | 行為情境 | `phase-spec.md`, `behavior.md` | Given/When/Then 行為規格 |
| Business Rules | 業務規則 | `rules.md`, `_index.md` | BR-ID declarative rules |
| Current BR Snapshot | 目前業務規則快照 | `_index.md` | feature-level rules snapshot |
| Domain Models | 領域模型 | `models.md` | Entities / Value Objects / Services 等模型索引 |
| Change Scope | 變動範圍 | `Git-principles-*.md`, spec templates | 描述本次變更涵蓋的功能 / 文件 / 程式碼範圍 |
| Feature Goal | 功能目標 | `Git-principles-*.md`, `finish-feature-flow.md` | Integration Summary 與整合 commit message 的主目標段落 |
| Related BR-IDs | 關聯 BR-ID 清單 | `Git-principles-*.md`, `finish-feature-flow.md` | 統整本次變更涉及的 ADDED / MODIFIED / REMOVED BR-ID |
| Phase Count | Phase 數 | `Git-principles-*.md`, `finish-feature-flow.md` | 整合摘要中描述本次 feature 涵蓋的 phase-spec 數量 |
| Lightweight Change | 輕量修改 | `_index.md`, `lightweight-spec.md`, Git principles | T2 / small change 類型的固定術語 |
| Lightweight Changes | 輕量修改紀錄 | `_index.md` | `_index.md` 中登記 T2 外連 + T3 inline 的 section heading |
| Resume Pointer | 接續入口 | `_index.md` | `_index.md` 末段「目前進展 + 下一動作」的 section heading |
| Behavior Delta | 行為變更 | `lightweight-spec.md` | lightweight-spec 中 BR delta 段的 section heading |
