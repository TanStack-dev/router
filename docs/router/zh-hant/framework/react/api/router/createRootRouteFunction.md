---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:13.111Z'
id: createRootRouteFunction
title: createRootRoute function
---

`createRootRoute` 函式會回傳一個新的根路由 (root route) 實例。此根路由實例可用於建立路由樹 (route-tree)。

## createRootRoute 選項

用於設定根路由實例的選項。

- 型別：

```tsx
Omit<
  RouteOptions,
  | 'path'
  | 'id'
  | 'getParentRoute'
  | 'caseSensitive'
  | 'parseParams'
  | 'stringifyParams'
>
```

- [`RouteOptions`](./RouteOptionsType.md)
- 可選

## createRootRoute 回傳值

一個新的 [`Route`](./RouteType.md) 實例。

## 範例

```tsx
import { createRootRoute, createRouter, Outlet } from '@tanstack/react-router'

const rootRoute = createRootRoute({
  component: () => <Outlet />,
  // ... 根路由選項
})

const routeTree = rootRoute.addChildren([
  // ... 其他路由
])

const router = createRouter({
  routeTree,
})
```
