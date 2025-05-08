---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:57.144Z'
id: RootRouteClass
title: RootRoute class
---

> [!CAUTION]
> 此類別已被棄用，並將在 TanStack Router 的下一個主要版本中移除。
> 請改用 [`createRootRoute`](./createRootRouteFunction.md) 函式。

`RootRoute` 類別繼承自 `Route` 類別，可用於建立根路由 (root route) 實例。根路由實例隨後可用於建立路由樹 (route tree)。

## `RootRoute` 建構函式

`RootRoute` 建構函式接受一個物件作為其唯一參數。

### 建構函式選項

用於配置根路由實例的選項。

- 類型：

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

## 建構函式回傳值

一個新的 [`Route`](./RouteType.md) 實例。

## 範例

```tsx
import { RootRoute, createRouter, Outlet } from '@tanstack/react-router'

const rootRoute = new RootRoute({
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
