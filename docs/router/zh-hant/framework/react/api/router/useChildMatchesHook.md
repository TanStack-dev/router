---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:23.790Z'
id: useChildMatchesHook
title: useChildMatches hook
---

`useChildMatches` 鉤子 (hook) 會返回從最接近的匹配到最末端匹配的所有子 [`RouteMatch`](./RouteMatchType.md) 物件。**它不包含當前匹配，當前匹配可以透過 `useMatch` 鉤子 (hook) 取得。**

> [!IMPORTANT]
> 如果路由器 (router) 有待處理的匹配且正在顯示其待處理元件後備 (fallback) 內容，則會使用 `router.state.pendingMatches` 而非 `router.state.matches`。

## useChildMatches 選項

`useChildMatches` 鉤子 (hook) 接受一個 _可選的_ 參數，即 `options` 物件。

### `opts.select` 選項

- 可選
- `(matches: RouteMatch[]) => TSelected`
- 如果提供，此函式將以路由匹配 (route matches) 為參數呼叫，其返回值將作為 `useChildMatches` 的返回結果。此值也會用於透過淺層比較 (shallow equality checks) 來判斷鉤子 (hook) 是否應重新渲染其父元件。

### `opts.structuralSharing` 選項

- 類型：`boolean`
- 可選
- 配置是否為 `select` 返回的值啟用結構共享 (structural sharing)。
- 更多資訊請參閱 [渲染最佳化指南](../../guide/render-optimizations.md)。

## useChildMatches 返回值

- 如果提供了 `select` 函式，則返回 `select` 函式的返回值。
- 如果未提供 `select` 函式，則返回 [`RouteMatch`](./RouteMatchType.md) 物件的陣列。

## 範例

```tsx
import { useChildMatches } from '@tanstack/react-router'

function Component() {
  const childMatches = useChildMatches()
  // ...
}
```
