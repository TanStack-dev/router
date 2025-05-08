---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:20:51.390Z'
id: useRouteContextHook
title: useRouteContext hook
---

`useRouteContext` 方法是一個鉤子 (hook)，用於回傳當前路由的上下文。此鉤子 (hook) 適用於在元件中存取當前路由的上下文。

## useRouteContext 選項

`useRouteContext` 鉤子 (hook) 接受一個 `options` 物件。

### `opts.from` 選項

- 類型: `string`
- 必填
- 用於匹配路由上下文的 RouteID。

### `opts.select` 選項

- 類型: `(context: RouteContext) => TSelected`
- 選填
- 若提供此函式，它會以路由上下文物件作為參數被呼叫，其回傳值將作為 `useRouteContext` 的回傳結果。

## useRouteContext 回傳值

- 當前路由的上下文，若提供了 `select` 函式則回傳 `TSelected`。

## 範例

```tsx
import { useRouteContext } from '@tanstack/react-router'

function Component() {
  const context = useRouteContext({ from: '/posts/$postId' })
  //    ^ RouteContext

  // 或

  const selected = useRouteContext({
    from: '/posts/$postId',
    select: (context) => context.postId,
  })
  //    ^ string

  // ...
}
```
