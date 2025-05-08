---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:41.162Z'
id: rootRouteWithContextFunction
title: rootRouteWithContext function
---

> [!CAUTION]
> 此函式已被棄用，並將在 TanStack Router 的下一個主要版本中移除。
> 請改用 [`createRootRouteWithContext`](./createRootRouteWithContextFunction.md) 函式。

`rootRouteWithContext` 函式是一個輔助函式，可用於建立一個根路由實例，該實例要求在建立路由器時必須提供指定的上下文類型。

## rootRouteWithContext 泛型

`rootRouteWithContext` 函式接受單一泛型參數：

### `TRouterContext` 泛型

- 類型：`TRouterContext`
- 可選，但**強烈建議**使用。
- 此為建立路由器時必須提供的上下文類型

## rootRouteWithContext 回傳值

- 一個工廠函式，可用於建立新的 [`createRootRoute`](./createRootRouteFunction.md) 實例。
- 它接受單一參數，與 [`createRootRoute`](./createRootRouteFunction.md) 函式相同。

## 範例

```tsx
import { rootRouteWithContext, createRouter } from '@tanstack/react-router'
import { QueryClient } from '@tanstack/react-query'

interface MyRouterContext {
  queryClient: QueryClient
}

const rootRoute = rootRouteWithContext<MyRouterContext>()({
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
