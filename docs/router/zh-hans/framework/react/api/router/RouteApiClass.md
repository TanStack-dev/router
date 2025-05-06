---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:52:22.982Z'
id: RouteApiClass
title: RouteApi class
---

> [!CAUTION]
> 此类已被弃用，并将在 TanStack Router 的下一个主要版本中移除。
> 请改用 [`getRouteApi`](./getRouteApiFunction.md) 函数。

`RouteApi` 类提供了类型安全版本的常用钩子，如 `useParams`、`useSearch`、`useRouteContext`、`useNavigate`、`useLoaderData` 和 `useLoaderDeps`，这些钩子已预先绑定到特定路由 ID 及其对应的已注册路由类型。

## 构造函数选项

`RouteApi` 构造函数接受一个参数：用于配置 `RouteApi` 实例的 `options`。

### `opts.routeId` 选项

- 类型：`string`
- 必填
- `RouteApi` 实例将绑定到的路由 ID

## 构造函数返回值

- 一个 [`RouteApi`](./RouteApiType.md) 实例，该实例已预先绑定到调用时指定的路由 ID。

## 示例

```tsx
import { RouteApi } from '@tanstack/react-router'

const routeApi = new RouteApi({ id: '/posts' })

export function PostsPage() {
  const posts = routeApi.useLoaderData()
  // ...
}
```
