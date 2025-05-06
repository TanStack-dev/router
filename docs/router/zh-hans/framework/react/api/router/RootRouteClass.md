---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:52:37.064Z'
id: RootRouteClass
title: RootRoute class
---

> [!CAUTION]
> 此类已被弃用，并将在 TanStack Router 的下一个主要版本中移除。
> 请改用 [`createRootRoute`](./createRootRouteFunction.md) 函数。

`RootRoute` 类继承自 `Route` 类，可用于创建根路由 (root route) 实例。根路由实例随后可用于创建路由树。

## `RootRoute` 构造函数

`RootRoute` 构造函数接收一个对象作为其唯一参数。

### 构造函数选项

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

## 构造函数返回值

一个新的 [`Route`](./RouteType.md) 实例。

## 示例

```tsx
import { RootRoute, createRouter, Outlet } from '@tanstack/react-router'

const rootRoute = new RootRoute({
  component: () => <Outlet />,
  // ... 根路由选项
})

const routeTree = rootRoute.addChildren([
  // ... 其他路由
])

const router = createRouter({
  routeTree,
})
```
