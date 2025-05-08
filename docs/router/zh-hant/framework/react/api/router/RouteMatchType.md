---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:35.834Z'
id: RouteMatchType
title: RouteMatch type
---

`RouteMatch` 類型代表 TanStack Router 中的路由匹配 (route match)。

```tsx
interface RouteMatch {
  id: string
  routeId: string
  pathname: string
  params: Route['allParams']
  status: 'pending' | 'success' | 'error'
  isFetching: boolean
  showPending: boolean
  error: unknown
  paramsError: unknown
  searchError: unknown
  updatedAt: number
  loadPromise?: Promise<void>
  loaderData?: Route['loaderData']
  context: Route['allContext']
  search: Route['fullSearchSchema']
  fetchedAt: number
  abortController: AbortController
  cause: 'enter' | 'stay'
}
```
