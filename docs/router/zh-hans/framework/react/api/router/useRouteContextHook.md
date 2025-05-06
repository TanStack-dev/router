---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:30:49.862Z'
id: useRouteContextHook
title: useRouteContext hook
---

`useRouteContext` 方法是一个钩子函数，用于返回当前路由的上下文。该钩子在组件中访问当前路由上下文时非常有用。

## useRouteContext 选项

`useRouteContext` 钩子接受一个 `options` 配置对象。

### `opts.from` 选项

- 类型: `string`
- 必填
- 用于匹配路由上下文的 RouteID。

### `opts.select` 选项

- 类型: `(context: RouteContext) => TSelected`
- 可选
- 如果提供此函数，它将被传入路由上下文对象并调用，其返回值将作为 `useRouteContext` 的返回结果。

## useRouteContext 返回值

- 返回当前路由的上下文；如果提供了 `select` 函数，则返回 `TSelected` 类型的结果。

## 示例

```tsx
import { useRouteContext } from '@tanstack/react-router'

function Component() {
  const context = useRouteContext({ from: '/posts/$postId' })
  //    ^ RouteContext

  // 或者

  const selected = useRouteContext({
    from: '/posts/$postId',
    select: (context) => context.postId,
  })
  //    ^ string

  // ...
}
```
