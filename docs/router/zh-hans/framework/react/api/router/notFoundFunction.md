---
source-updated-at: '2025-02-19T11:57:38.000Z'
translation-updated-at: '2025-05-06T17:38:30.782Z'
id: notFoundFunction
title: notFound function
---

`notFound` 函数返回一个新的 `NotFoundError` 对象，该对象可以从路由的 `beforeLoad` 或 `loader` 回调等位置返回或抛出，以触发 `notFoundComponent`。

## notFound 配置选项

`notFound` 函数接受一个可选参数 `options`，用于创建未找到错误对象。

- 类型: [`Partial<NotFoundError>`](./NotFoundErrorType.md)
- 可选

## notFound 返回值

- 如果 `options` 对象中的 `throw` 属性为 `true`，则 `NotFoundError` 对象将在函数调用内部抛出。
- 如果 `options` 对象中的 `throw` 属性为 `false | undefined`，则 `NotFoundError` 对象将被返回。

## 示例

```tsx
import { notFound, createFileRoute, rootRouteId } from '@tanstack/react-router'

const Route = new createFileRoute('/posts/$postId')({
  // 抛出未找到对象
  loader: ({ context: { post } }) => {
    if (!post) {
      throw notFound()
    }
  },
  // 或者如果你想在整个页面显示未找到状态
  loader: ({ context: { team } }) => {
    if (!team) {
      throw notFound({ routeId: rootRouteId })
    }
  },
  // ... 其他路由选项
})
```
