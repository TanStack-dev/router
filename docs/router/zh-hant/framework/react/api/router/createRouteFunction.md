---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:07.258Z'
id: createRouteFunction
title: createRoute function
---

`createRoute` 函式會回傳一個 [`Route`](./RouteType.md) 實例。該路由實例可傳遞給根路由的 children 屬性來建立路由樹，接著再傳遞給路由器使用。

## createRoute 選項

- 類型: [`RouteOptions`](./RouteOptionsType.md)
- 必填
- 用於配置路由實例的選項

## createRoute 回傳值

一個新的 [`Route`](./RouteType.md) 實例。

## 範例

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
