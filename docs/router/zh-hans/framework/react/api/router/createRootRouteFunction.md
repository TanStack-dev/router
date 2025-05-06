---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:41:43.613Z'
id: createRootRouteFunction
title: createRootRoute function
---

`createRootRoute` 函数返回一个新的根路由实例。该根路由实例可用于创建路由树。

## createRootRoute 配置选项

用于配置根路由实例的选项。

- 类型：

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
- 可选

## createRootRoute 返回值

返回一个新的 [`Route`](./RouteType.md) 实例。

## 示例

```tsx
import { createRootRoute, createRouter, Outlet } from '@tanstack/react-router'

const rootRoute = createRootRoute({
  component: () => <Outlet />,
  // ... 根路由配置选项
})

const routeTree = rootRoute.addChildren([
  // ... 其他路由
])

const router = createRouter({
  routeTree,
})
```
