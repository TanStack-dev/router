---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:14.578Z'
id: createRootRouteWithContextFunction
title: createRootRouteWithContext function
---

`createRootRouteWithContext` 函式是一個輔助函式，可用於建立需要滿足特定上下文類型的根路由 (root route) 實例，該上下文類型會在路由器建立時被要求提供。

## createRootRouteWithContext 泛型 (generics)

`createRootRouteWithContext` 函式接受單一泛型參數：

### `TRouterContext` 泛型

- 類型：`TRouterContext`
- 可選，但**強烈建議**使用。
- 此為建立路由器時需要滿足的上下文類型

## createRootRouteWithContext 回傳值

- 一個工廠函式 (factory function)，可用於建立新的 [`createRootRoute`](./createRootRouteFunction.md) 實例。
- 它接受單一參數，與 [`createRootRoute`](./createRootRouteFunction.md) 函式相同。

## 範例

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
  // ... 根路由選項
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
