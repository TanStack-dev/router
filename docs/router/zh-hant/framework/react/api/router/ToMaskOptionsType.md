---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:26.617Z'
id: ToMaskOptionsType
title: ToMaskOptions type
---

`ToMaskOptions` 型別 (type) 繼承自 [`ToOptions`](./ToOptionsType.md) 型別 (type)，用於描述使用路由遮罩 (route mask) 時可用的額外選項。

```tsx
type ToMaskOptions = ToOptions & {
  unmaskOnReload?: boolean
}
```

- [`ToOptions`](./ToOptionsType.md)
