---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:21.523Z'
id: ViewTransitionOptionsType
title: ViewTransitionOptions type
---

`ViewTransitionOptions` 型別用於定義 [視圖轉換類型 (viewTransition type)](https://developer.mozilla.org/docs/Web/API/View_Transitions_API#view_transition_types)。

```tsx
interface ViewTransitionOptions {
  types: Array<string>
}
```

## ViewTransitionOptions 屬性

`ViewTransitionOptions` 型別接受一個包含單一屬性的物件：

### `types` 屬性

- 類型：`Array<string>`
- 必填
- 此類型陣列將傳遞給 `document.startViewTransition({update, types})` 呼叫；
