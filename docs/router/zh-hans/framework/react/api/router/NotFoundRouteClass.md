---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:53:29.624Z'
id: NotFoundRouteClass
title: NotFoundRoute class
---

> [!CAUTION]
> 该类已被弃用，并将在 TanStack Router 的下一个主要版本中移除。
> 请改用路由配置时提供的 `notFoundComponent` 选项。
> 更多信息请参阅 [未找到错误指南](../../guide/not-found-errors.md)。

`NotFoundRoute` 类继承自 `Route` 类，可用于创建未找到路由实例。该实例可传递给 `routerOptions.notFoundRoute` 选项，从而为路由树的每个分支配置默认的未找到/404路由。

## 构造函数选项

`NotFoundRoute` 构造函数接收一个对象作为唯一参数。

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

- [RouteOptions](./RouteOptionsType.md)
- 必填
- 用于配置未找到路由实例的选项。

## 示例

```tsx
import { NotFoundRoute, createRouter } from '@tanstack/react-router'
import { Route as rootRoute } from './routes/__root'
import { routeTree } from './routeTree.gen'

const notFoundRoute = new NotFoundRoute({
  getParentRoute: () => rootRoute,
  component: () => <div>Not found!!!</div>,
})

const router = createRouter({
  routeTree,
  notFoundRoute,
})

// ... 其他代码
```
