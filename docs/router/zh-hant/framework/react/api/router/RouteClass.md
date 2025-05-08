---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:44.537Z'
id: RouteClass
title: Route class
---

> [!CAUTION]
> 此類別已被棄用，並將在 TanStack Router 的下一個主要版本中移除。
> 請改用 [`createRoute`](./createRouteFunction.md) 函式。

`Route` 類別實作了 `RouteApi` 類別，可用於建立路由實例。路由實例隨後可用於建立路由樹。

## `Route` 建構函式

`Route` 建構函式接受一個物件作為其唯一參數。

### 建構函式選項

- 類型: [`RouteOptions`](./RouteOptionsType.md)
- 必填
- 用於配置路由實例的選項

### 建構函式回傳值

一個新的 [`Route`](./RouteType.md) 實例。

## 範例

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
