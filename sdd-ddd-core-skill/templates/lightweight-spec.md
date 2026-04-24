---
id: BUG-{NUMBER}
title: {簡述問題}
status: in-progress
bounded-context: {ContextName}
created: {YYYY-MM-DD}
branch: bugfix/BUG-{NUMBER}-{short-description}
---

<!--
Template note (for AI):
  This is the **lightweight-spec** template — it corresponds to T2 Light
  ceremony in the three-tier Ceremony Scaling (T1 Heavy / T2 Light /
  T3 Trivial; see SKILL.md § Ceremony Scaling for the tier criteria).

  - T1 Heavy → use templates/phase-spec.md instead
  - T2 Light → THIS template; produces an independent file
  - T3 Trivial → no independent file; just one inline row in _index.md
                 輕量修改紀錄 with a tag like [cosmetic] / [text] / [format]

  Instance file location and naming:
    Place the instantiated file inside the corresponding feature directory:
      specs/features/active/{SPEC-ID}-{slug}/lightweight-{YYYY-MM-DD}-{slug}.md
    or, when the lightweight change is a tracked bug:
      specs/features/active/{SPEC-ID}-{slug}/BUG-{NUMBER}-{slug}.md

    If the change is a standalone bug not yet attached to any existing
    feature, /dflow:bug-fix must first create a feature directory (with a
    minimal _index.md) before placing the lightweight-spec instance inside.
    This keeps the structure invariant: every spec file lives under some
    feature directory.

  After finalizing this lightweight-spec, AI must:
    1. Add an outbound-link row to the feature's _index.md 輕量修改紀錄 table
       (Tier = T2; description includes the link to this file)
    2. Refresh the feature's _index.md Current BR Snapshot table to reflect
       any BR ADDED / MODIFIED / REMOVED / RENAMED in this lightweight-spec
-->

# {問題簡述}

## 問題

{什麼東西壞了？或什麼行為不正確？}

## 行為變更（Delta）

> 精簡 delta 格式：bug fix 多數只需 MODIFIED；若確實是新增規則可改用 ADDED、移除用 REMOVED、改名用 RENAMED。多項變更時照類別列。

### MODIFIED — 修改的行為
#### 規則：BR-NN {規則名稱}
**原本**：Given {Aggregate 目前狀態} When {操作} Then {目前的（錯誤）結果} / {事件}
**改為**：Given {相同狀態} When {相同操作} Then {正確的結果} / {事件}
**原因**：{為什麼改 — bug / 不變條件違反 / 需求澄清}

<!-- 若需要 ADDED / REMOVED / RENAMED / UNCHANGED 請比照 references/modify-existing-flow.md 的 Delta 格式 -->


## 根因

{為什麼會這樣？是邏輯錯誤？資料問題？還是需求理解有誤？}

## 修復方式

{怎麼修？有沒有抽到 Domain 層的機會？}

## 發現的技術債（如有）

{在修這個 bug 時發現的其他問題，記錄到 tech-debt.md}
