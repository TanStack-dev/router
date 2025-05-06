---
source-updated-at: '2025-03-09T10:56:27.000Z'
translation-updated-at: '2025-05-06T17:54:16.033Z'
id: NavigateOptionsType
title: NavigateOptions type
---

`NavigateOptions` 类型用于描述在 TanStack Router 中进行导航操作时可用的选项。

```tsx
type NavigateOptions = ToOptions & {
  replace?: boolean
  resetScroll?: boolean
  hashScrollIntoView?: boolean | ScrollIntoViewOptions
  viewTransition?: boolean | ViewTransitionOptions
  ignoreBlocker?: boolean
  reloadDocument?: boolean
  href?: string
}
```

## NavigateOptions 属性

`NavigateOptions` 对象接受以下属性：

### `replace`

- 类型：`boolean`
- 可选
- 默认值为 `false`
- 如果为 `true`，将使用 `history.replace` 而非 `history.push` 将位置提交至浏览器历史记录

### `resetScroll`

- 类型：`boolean`
- 可选
- 默认值为 `true`，在位置提交至浏览器历史记录后，滚动位置会重置为 0,0
- 如果为 `false`，位置提交至历史记录后不会重置滚动位置

### `hashScrollIntoView`

- 类型：`boolean | ScrollIntoViewOptions`
- 可选
- 默认值为 `true`，在位置提交至历史记录后，会将与哈希值匹配 ID 的元素滚动至可视区域
- 如果为 `false`，则不会滚动至匹配元素
- 如果传入对象，该对象将作为选项传递给 `scrollIntoView` 方法
- 有关 `ScrollIntoViewOptions` 的更多信息，请参阅 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView)

### `viewTransition`

- 类型：`boolean | ViewTransitionOptions`
- 可选
- 默认值为 `false`
- 如果为 `true`，导航将通过 `document.startViewTransition()` 调用
- 如果传入 [`ViewTransitionOptions`](./ViewTransitionOptionsType.md)，路由导航将通过 `document.startViewTransition({update, types})` 调用，其中 `types` 为 `ViewTransitionOptions["types"]` 传入的字符串数组。如果浏览器不支持 viewTransition 类型，导航将回退至普通的 `document.startTransition()`，效果等同于传入 `true`
- 如果浏览器不支持此 API，该选项将被忽略
- 关于此函数的详细信息，请参阅 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Document/startViewTransition)
- 关于 viewTransition 类型的更多信息，请参阅 [Google](https://developer.chrome.com/docs/web-platform/view-transitions/same-document#view-transition-types)

### `ignoreBlocker`

- 类型：`boolean`
- 可选
- 默认值为 `false`
- 如果为 `true`，导航将忽略可能阻止它的所有拦截器

### `reloadDocument`

- 类型：`boolean`
- 可选
- 默认值为 `false`
- 如果为 `true`，路由内的导航将触发整页加载，而非传统的 SPA 导航

### `href`

- 类型：`string`
- 可选
- 可替代 `to` 使用，用于导航至完整构建的 href（例如指向外部目标）

- [`ToOptions`](./ToOptionsType.md)
