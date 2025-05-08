---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:52.168Z'
id: historyStateInterface
title: HistoryState interface
---

`HistoryState` 介面是由 `history` 套件匯出的介面，用於描述可與 `history` 套件及 `window.location` API 搭配使用的狀態物件結構。

您可以擴展此介面，為應用程式中的狀態物件添加額外屬性。

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
