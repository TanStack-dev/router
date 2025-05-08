---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:54.002Z'
id: RouteApiClass
title: RouteApi class
---

> [!CAUTION]
> 此類別已被棄用，將在 TanStack Router 的下一個主要版本中移除。
> 請改用 [`getRouteApi`](./getRouteApiFunction.md) 函式。

`RouteApi` 類別提供了類型安全版本的常用鉤子 (hooks)，例如 `useParams`、`useSearch`、`useRouteContext`、`useNavigate`、`useLoaderData` 和 `useLoaderDeps`，這些鉤子會預先綁定到特定的路由 ID 和對應的已註冊路由類型。

## 建構函式選項

`RouteApi` 建構函式接受單一參數：用於配置 `RouteApi` 實例的 `options`。

### `opts.routeId` 選項

- 類型：`string`
- 必填
- `RouteApi` 實例將綁定的路由 ID

## 建構函式回傳值

- 一個 [`RouteApi`](./RouteApiType.md) 實例，該實例會預先綁定到呼叫時指定的路由 ID。

## 範例

```tsx
import { RouteApi } from '@tanstack/react-router'

const routeApi = new RouteApi({ id: '/posts' })

export function PostsPage() {
  const posts = routeApi.useLoaderData()
  // ...
}
```
