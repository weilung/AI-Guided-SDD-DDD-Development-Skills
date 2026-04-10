---
context: {ContextName}
chinese-name: {中文名稱}
owner: {負責的開發者或團隊}
created: {YYYY-MM-DD}
---

# {ContextName} Bounded Context

## 職責

> 這個 Context 負責什麼？用 2-3 句話描述。

## 邊界

### 這個 Context 處理：
- {職責 1}
- {職責 2}

### 這個 Context 不處理（由其他 Context 負責）：
- {排除項 1} → 由 {OtherContext} 處理
- {排除項 2} → 由 {OtherContext} 處理

## 核心 Domain 模型

> 詳細定義在 `models.md`，這裡只列出概覽。

### Entities（有唯一識別的物件）
- **{EntityName}** — {一句話描述}

### Value Objects（以值定義的物件）
- **{VOName}** — {一句話描述}

### Domain Services（不屬於特定 Entity 的業務操作）
- **{ServiceName}** — {一句話描述}

## 與其他 Context 的互動

| 對方 Context | 互動方式 | 說明 |
|---|---|---|
| {OtherContext} | 呼叫 / 事件 / 共享資料 | {描述} |

## 關鍵業務規則

> 詳細規則在 `rules.md`，這裡只列出最重要的幾條。

1. {最重要的規則}
2. {第二重要的規則}

## 對應的程式碼位置

### 目前（WebForms）
- Pages: `src/Pages/{相關頁面}.aspx`
- Domain: `src/Domain/{Context}/`

### 未來（ASP.NET Core）
- 預計作為獨立模組/專案
