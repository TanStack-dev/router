---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:54.204Z'
id: ParsedHistoryStateType
title: ParsedHistoryState type
---

`ParsedHistoryState` 類型代表一個已解析的狀態物件。除了 `HistoryState` 之外，它還包含路由的索引 (index) 和唯一鍵 (unique key)。

```tsx
export type ParsedHistoryState = HistoryState & {
  key?: string
  __TSR_index: number
}
```
