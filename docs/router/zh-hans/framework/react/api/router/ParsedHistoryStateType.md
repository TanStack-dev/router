---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:53:14.450Z'
id: ParsedHistoryStateType
title: ParsedHistoryState type
---

`ParsedHistoryState` 类型表示一个已解析的状态对象。除了 `HistoryState` 之外，它还包含路由的索引和唯一键 (unique key)。

```tsx
export type ParsedHistoryState = HistoryState & {
  key?: string
  __TSR_index: number
}
```
