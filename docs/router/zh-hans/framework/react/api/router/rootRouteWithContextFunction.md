---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:37:39.219Z'
id: rootRouteWithContextFunction
title: rootRouteWithContext function
---

> [!CAUTION]
> 此函数已弃用，将在 TanStack Router 的下一个主要版本中移除。
> 请改用 [`createRootRouteWithContext`](./createRootRouteWithContextFunction.md) 函数。

`rootRouteWithContext` 是一个辅助函数，用于创建一个需要在路由器创建时提供上下文类型的根路由实例。

## rootRouteWithContext 泛型参数

`rootRouteWithContext` 函数接受一个泛型参数：

### `TRouterContext` 泛型

- 类型: `TRouterContext`
- 可选，但**强烈推荐**指定。
- 该上下文类型将在路由器创建时必须被满足

## rootRouteWithContext 返回值

- 返回一个工厂函数，可用于创建新的 [`createRootRoute`](./createRootRouteFunction.md) 实例。
- 该工厂函数接收与 [`createRootRoute`](./createRootRouteFunction.md) 函数相同的参数。

## 示例代码

```tsx
import { rootRouteWithContext, createRouter } from '@tanstack/react-router'
import { QueryClient } from '@tanstack/react-query'

interface MyRouterContext {
  queryClient: QueryClient
}

const rootRoute = rootRouteWithContext<MyRouterContext>()({
  component: () => <Outlet />,
  // ... 根路由配置项
})

const routeTree = rootRoute.addChildren([
  // ... 其他子路由
])

const queryClient = new QueryClient()

const router = createRouter({
  routeTree,
  context: {
    queryClient,
  },
})
```
