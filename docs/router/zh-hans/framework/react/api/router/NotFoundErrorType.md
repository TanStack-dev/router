---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:53:46.538Z'
id: NotFoundErrorType
title: NotFoundError
---

`NotFoundError` 类型用于表示 TanStack Router 中的未找到路由错误。

```tsx
export type NotFoundError = {
  global?: boolean
  data?: any
  throw?: boolean
  routeId?: string
}
```

## NotFoundError 属性

`NotFoundError` 对象接受/包含以下属性：

### `data` 属性

- 类型: `any`
- 可选
- 当未找到错误被处理时，传递给 `notFoundComponent` 的自定义数据

### `global` 属性

- 类型: `boolean`
- 可选 - `默认值: false`
- 如果为 true，未找到错误将由根路由的 `notFoundComponent` 处理，而不是从抛出该错误的路由向上冒泡。这与导入根路由并调用 `RootRoute.notFound()` 具有相同的行为。

### `route` 属性

- 类型: `string`
- 可选
- 将尝试处理未找到错误的路由 ID。如果该路由没有 `notFoundComponent`，错误将向上冒泡到父路由（必要时由根路由处理）。默认情况下，TanStack Router 会尝试用抛出错误的路由来处理未找到错误。

### `throw` 属性

- 类型: `boolean`
- 可选 - `默认值: false`
- 如果提供，将抛出未找到对象而不是返回它。这在某些情况下很有用，例如函数中 `throwing` 可能导致返回类型为 `never` 时。在这种情况下，你可以使用 `notFound({ throw: true })` 来抛出未找到对象而不是返回它。
