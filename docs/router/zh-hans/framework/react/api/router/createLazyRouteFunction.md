---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:42:05.113Z'
id: createLazyRouteFunction
title: createLazyRoute function
---

`createLazyRoute` 函数用于创建一个基于代码的部分路由实例，该实例会在匹配时延迟加载。此路由实例仅可用于配置路由的 [非关键属性](../../guide/code-splitting.md#how-does-tanstack-router-split-code)，例如 `component`、`pendingComponent`、`errorComponent` 和 `notFoundComponent`。

## createLazyRoute 选项

`createLazyRoute` 函数接受一个类型为 `string` 的参数，表示路由的 `id`。

### `id`

- 类型: `string`
- 必填
- 路由的标识符。

### createLazyRoute 返回值

返回一个新函数，该函数接受一个类型为 [`RouteOptions`](./RouteOptionsType.md) 部分属性的参数，用于配置文件 [`Route`](./RouteType.md) 实例。

- 类型:

```tsx
Pick<
  RouteOptions,
  'component' | 'pendingComponent' | 'errorComponent' | 'notFoundComponent'
>
```

- [`RouteOptions`](./RouteOptionsType.md)

> ⚠️ 注意：此路由实例必须使用 `createRoute` 函数返回的 `lazy` 方法手动延迟加载其关键路由实例。

### 示例

```tsx
// src/route-pages/index.tsx
import { createLazyRoute } from '@tanstack/react-router'

export const Route = createLazyRoute('/')({
  component: IndexComponent,
})

function IndexComponent() {
  const data = Route.useLoaderData()
  return <div>{data}</div>
}

// src/routeTree.tsx
import {
  createRootRouteWithContext,
  createRoute,
  Outlet,
} from '@tanstack/react-router'

interface MyRouterContext {
  foo: string
}

const rootRoute = createRootRouteWithContext<MyRouterContext>()({
  component: () => <Outlet />,
})

const indexRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/',
}).lazy(() => import('./route-pages/index').then((d) => d.Route))

export const routeTree = rootRoute.addChildren([indexRoute])
```
