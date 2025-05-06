---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:39:56.585Z'
id: historyStateInterface
title: HistoryState interface
---

`HistoryState` 接口是由 `history` 包导出的一个接口，用于描述可与 `history` 包及 `window.location` API 配合使用的状态对象的结构。

您可以通过扩展此接口，为应用程序中的状态对象添加额外属性。

```tsx
// src/main.tsx
declare module '@tanstack/react-router' {
  // ...

  interface HistoryState {
    additionalRequiredProperty: number
    additionalProperty?: string
  }
}
```
