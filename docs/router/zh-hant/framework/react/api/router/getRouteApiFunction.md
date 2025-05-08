---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:01.401Z'
id: getRouteApiFunction
title: getRouteApi function
---

`getRouteApi` 函式提供了一系列類型安全 (type-safe) 的常用掛鉤 (hooks) 版本，例如 `useParams`、`useSearch`、`useRouteContext`、`useNavigate`、`useLoaderData` 和 `useLoaderDeps`，這些掛鉤會預先綁定到特定的路由 ID (route ID) 及其對應的已註冊路由類型。

## getRouteApi 選項

`getRouteApi` 函式接受一個參數，即 `routeId` 字串字面值 (string literal)。

### `routeId` 選項

- 類型: `string`
- 必填
- 指定 [`RouteApi`](./RouteApiClass.md) 實例將綁定的路由 ID (route ID)

## getRouteApi 回傳值

- 回傳一個 [`RouteApi`](./RouteApiType.md) 實例，該實例會預先綁定到呼叫 `getRouteApi` 函式時所指定的路由 ID (route ID)。

## 範例

```tsx
import { getRouteApi } from '@tanstack/react-router'

const routeApi = getRouteApi('/posts')

export function PostsPage() {
  const posts = routeApi.useLoaderData()
  // ...
}
```
