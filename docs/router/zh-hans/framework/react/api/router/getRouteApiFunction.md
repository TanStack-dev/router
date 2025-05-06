---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:40:10.111Z'
id: getRouteApiFunction
title: getRouteApi function
---

`getRouteApi` 函数为常见钩子如 `useParams`、`useSearch`、`useRouteContext`、`useNavigate`、`useLoaderData` 和 `useLoaderDeps` 提供了类型安全 (type-safe) 的版本，这些钩子会预先绑定到特定的路由 ID 及对应的已注册路由类型。

## getRouteApi 选项

`getRouteApi` 函数接收一个参数，即 `routeId` 字符串字面量。

### `routeId` 选项

- 类型：`string`
- 必需
- 指定 [`RouteApi`](./RouteApiClass.md) 实例将绑定到的路由 ID

## getRouteApi 返回值

- 返回一个 [`RouteApi`](./RouteApiType.md) 实例，该实例会预先绑定到调用 `getRouteApi` 函数时传入的路由 ID。

## 示例

```tsx
import { getRouteApi } from '@tanstack/react-router'

const routeApi = getRouteApi('/posts')

export function PostsPage() {
  const posts = routeApi.useLoaderData()
  // ...
}
```
