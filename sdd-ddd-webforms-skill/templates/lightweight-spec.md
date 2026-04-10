---
id: BUG-{NUMBER}
title: {簡述問題}
status: in-progress
bounded-context: {ContextName}
created: {YYYY-MM-DD}
branch: bugfix/BUG-{NUMBER}-{short-description}
---

# {問題簡述}

## 問題

{什麼東西壞了？或什麼行為不正確？}

## 現有行為

```gherkin
Given {目前的狀態}
When {操作}
Then {目前的（錯誤）結果}
```

## 預期行為

```gherkin
Given {相同狀態}
When {相同操作}
Then {正確的結果}
```

## 根因

{為什麼會這樣？是邏輯錯誤？資料問題？還是需求理解有誤？}

## 修復方式

{怎麼修？有沒有抽到 Domain 層的機會？}

## 發現的技術債（如有）

{在修這個 bug 時發現的其他問題，記錄到 tech-debt.md}
