---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:20:37.931Z'
id: useRouterStateHook
title: useRouterState hook
---

`useRouterState` 方法是一個鉤子 (hook)，用於回傳路由器的當前內部狀態。這個鉤子 (hook) 在元件中存取路由器的當前狀態時非常有用。

> [!TIP]
> 如果你想存取當前位置 (location) 或當前匹配 (matches)，應該先嘗試使用 [`useLocation`](./useLocationHook.md) 和 [`useMatches`](./useMatchesHook.md) 鉤子 (hooks)。這些鉤子 (hooks) 的設計比直接存取路由器狀態更符合人體工學 (ergonomic) 且更容易使用。

## useRouterState 選項

`useRouterState` 鉤子 (hook) 接受一個可選的 `options` 物件。

### `opts.select` 選項

- 類型: `(state: RouterState) => TSelected`
- 可選
- 如果提供，此函式將以 [`RouterState`](./RouterStateType.md) 物件作為參數呼叫，其回傳值將從 `useRouterState` 回傳。

### `opts.structuralSharing` 選項

- 類型: `boolean`
- 可選
- 配置是否為 `select` 回傳的值啟用結構共享 (structural sharing)。
- 更多資訊請參閱 [渲染最佳化指南](../../guide/render-optimizations.md)。

## useRouterState 回傳值

- 當前的 [`RouterState`](./RouterStateType.md) 物件，如果提供了 `select` 函式則回傳 `TSelected`。

## 範例

```tsx
import { useRouterState } from '@tanstack/react-router'

function Component() {
  const state = useRouterState()
  //    ^ RouterState

  // 或

  const selected = useRouterState({
    select: (state) => state.location,
  })
  //    ^ ParsedLocation

  // ...
}
```
