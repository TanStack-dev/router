---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:51:00.139Z'
id: RouteClass
title: Route class
---

> [!CAUTION]
> 此类已被弃用，并将在 TanStack Router 的下一个主要版本中移除。
> 请改用 [`createRoute`](./createRouteFunction.md) 函数。

`Route` 类实现了 `RouteApi` 类，可用于创建路由实例。路由实例随后可用于构建路由树。

## `Route` 构造函数

`Route` 构造函数接收一个对象作为其唯一参数。

### 构造函数选项

- 类型: [`RouteOptions`](./RouteOptionsType.md)
- 必填
- 用于配置路由实例的选项

### 构造函数返回值

返回一个新的 [`Route`](./RouteType.md) 实例。

## 示例

```tsx
import { Route } from '@tanstack/react-router'
import { rootRoute } from './__root'

const indexRoute = new Route({
  getParentRoute: () => rootRoute,
  path: '/',
  loader: () => {
    return 'Hello World'
  },
  component: IndexComponent,
})

function IndexComponent() {
  const data = indexRoute.useLoaderData()
  return <div>{data}</div>
}
```
