---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:43:33.919Z'
id: ViewTransitionOptionsType
title: ViewTransitionOptions type
---

`ViewTransitionOptions` 类型用于定义 [视图过渡类型 (viewTransition type)](https://developer.chrome.com/docs/web-platform/view-transitions/same-document#view-transition-types)。

```tsx
interface ViewTransitionOptions {
  types: Array<string>
}
```

## ViewTransitionOptions 属性

`ViewTransitionOptions` 类型接收一个包含以下属性的对象：

### `types` 属性

- 类型: `Array<string>`
- 必填
- 该类型数组将传递给 `document.startViewTransition({update, types})` 调用；
