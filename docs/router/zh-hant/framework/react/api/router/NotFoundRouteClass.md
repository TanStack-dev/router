---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:23:09.307Z'
id: NotFoundRouteClass
title: NotFoundRoute class
---

> [!CAUTION]
> 此類別已被棄用，並將在 TanStack Router 的下一個主要版本中移除。
> 請改用路由配置時提供的 `notFoundComponent` 選項。
> 更多資訊請參閱 [找不到頁面錯誤指南](../../guide/not-found-errors.md)。

`NotFoundRoute` 類別繼承自 `Route` 類別，可用於建立「找不到頁面」路由實例。此實例可傳遞給 `routerOptions.notFoundRoute` 選項，用於為路由樹的每個分支配置預設的 404 路由。

## 建構函式選項

`NotFoundRoute` 建構函式接受一個物件作為唯一參數。

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

- [RouteOptions](./RouteOptionsType.md)
- 必填
- 用於配置「找不到頁面」路由實例的選項。

## 範例

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

// ... 其他程式碼
```
