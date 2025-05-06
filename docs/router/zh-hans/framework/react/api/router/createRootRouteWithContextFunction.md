---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:41:31.506Z'
id: createRootRouteWithContextFunction
title: createRootRouteWithContext function
---

`createRootRouteWithContext` 函数是一个辅助函数，用于创建一个根路由实例，该实例要求在创建路由器时必须提供上下文类型。

## createRootRouteWithContext 泛型

`createRootRouteWithContext` 函数接受一个泛型参数：

### `TRouterContext` 泛型

- 类型: `TRouterContext`
- 可选，**但推荐使用**。
- 该上下文类型将在创建路由器时必须被满足

## createRootRouteWithContext 返回值

- 返回一个工厂函数，可用于创建新的 [`createRootRoute`](./createRootRouteFunction.md) 实例。
- 该工厂函数接受一个参数，与 [`createRootRoute`](./createRootRouteFunction.md) 函数相同。

## 示例

```tsx
import {
  createRootRouteWithContext,
  createRouter,
} from '@tanstack/react-router'
import { QueryClient } from '@tanstack/react-query'

interface MyRouterContext {
  queryClient: QueryClient
}

const rootRoute = createRootRouteWithContext<MyRouterContext>()({
  component: () => <Outlet />,
  // ... 根路由选项
})

const routeTree = rootRoute.addChildren([
  // ... 其他路由
])

const queryClient = new QueryClient()

const router = createRouter({
  routeTree,
  context: {
    queryClient,
  },
})
```
