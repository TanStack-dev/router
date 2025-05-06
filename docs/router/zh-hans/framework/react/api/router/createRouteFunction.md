---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:41:17.575Z'
id: createRouteFunction
title: createRoute function
---

`createRoute` 函数会返回一个 [`Route`](./RouteType.md) 实例。该路由实例可以传递给根路由的 children 属性以构建路由树，最终传递给路由器使用。

## createRoute 配置选项

- 类型: [`RouteOptions`](./RouteOptionsType.md)
- 必填
- 用于配置路由实例的选项对象

## createRoute 返回值

返回一个新的 [`Route`](./RouteType.md) 实例。

## 示例代码

```tsx
import { createRoute } from '@tanstack/react-router'
import { rootRoute } from './__root'

const Route = createRoute({
  getParentRoute: () => rootRoute,
  path: '/',
  loader: () => {
    return 'Hello World'
  },
  component: IndexComponent,
})

function IndexComponent() {
  const data = Route.useLoaderData()
  return <div>{data}</div>
}
```
