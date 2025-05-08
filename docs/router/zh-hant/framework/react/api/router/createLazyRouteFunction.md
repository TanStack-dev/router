---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:23.853Z'
id: createLazyRouteFunction
title: createLazyRoute function
---

`createLazyRoute` 函式用於建立一個基於程式碼的部分路由實例，該實例會在匹配時延遲載入。此路由實例僅能用於配置路由的 [非關鍵屬性](../../guide/code-splitting.md#how-does-tanstack-router-split-code)，例如 `component`、`pendingComponent`、`errorComponent` 和 `notFoundComponent`。

## createLazyRoute 選項

`createLazyRoute` 函式接受一個型別為 `string` 的參數，代表路由的 `id`。

### `id`

- 型別: `string`
- 必填
- 路由的識別碼。

### createLazyRoute 回傳值

一個新的函式，該函式接受一個部分 [`RouteOptions`](./RouteOptionsType.md) 型別的參數，用於配置檔案 [`Route`](./RouteType.md) 實例。

- 型別:

```tsx
Pick<
  RouteOptions,
  'component' | 'pendingComponent' | 'errorComponent' | 'notFoundComponent'
>
```

- [`RouteOptions`](./RouteOptionsType.md)

> ⚠️ 注意：此路由實例必須使用 `createRoute` 函式回傳的 `lazy` 方法，手動針對其關鍵路由實例進行延遲載入。

### 範例

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
