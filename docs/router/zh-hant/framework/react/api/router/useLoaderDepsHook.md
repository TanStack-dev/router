---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:21.370Z'
id: useLoaderDepsHook
title: useLoaderDeps hook
---

`useLoaderDeps` 鉤子 (hook) 是一個會回傳包含觸發指定路由 `loader` 所需依賴項 (dependencies) 物件的鉤子。

## useLoaderDepsHook 選項

`useLoaderDepsHook` 鉤子接受一個 `options` 物件。

### `opts.from` 選項

- 類型: `string`
- 必填
- 指定要獲取載入器依賴項的路由 ID 或路徑。

### `opts.select` 選項

- 類型: `(deps: TLoaderDeps) => TSelected`
- 選填
- 若提供此函式，它將被呼叫並傳入載入器依賴項物件，其回傳值將作為 `useLoaderDeps` 的回傳值。

### `opts.structuralSharing` 選項

- 類型: `boolean`
- 選填
- 設定是否為 `select` 回傳的值啟用結構共享 (structural sharing)。
- 詳情請參閱 [渲染最佳化指南](../../guide/render-optimizations.md)。

## useLoaderDeps 回傳值

- 載入器依賴項物件，若提供了 `select` 函式則回傳 `TSelected`。

## 範例

```tsx
import { useLoaderDeps } from '@tanstack/react-router'

const routeApi = getRouteApi('/posts/$postId')

function Component() {
  const deps = useLoaderDeps({ from: '/posts/$postId' })

  // 或

  const routeDeps = routeApi.useLoaderDeps()

  // 或

  const postId = useLoaderDeps({
    from: '/posts',
    select: (deps) => deps.view,
  })

  // ...
}
```
