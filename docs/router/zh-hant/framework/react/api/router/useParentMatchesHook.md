---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:20:55.808Z'
id: useParentMatchesHook
title: useParentMatches hook
---

`useParentMatches` hook 會回傳從根路由到當前匹配路由的直屬父路由之間的所有父級 [`RouteMatch`](./RouteMatchType.md) 物件。**它不包含當前匹配的路由，該路由可透過 `useMatch` hook 取得。**

> [!IMPORTANT]
> 若路由器有等待中的匹配路由且正在顯示其等待中的元件備用內容時，將會使用 `router.state.pendingMatches` 而非 `router.state.matches`。

## useParentMatches 選項

`useParentMatches` hook 接受一個可選的 `options` 物件。

### `opts.select` 選項

- 可選
- `(matches: RouteMatch[]) => TSelected`
- 若提供此函式，它會以路由匹配陣列作為參數被呼叫，其回傳值將作為 `useParentMatches` 的回傳值。此值也會用於透過淺層比較 (shallow equality) 判斷 hook 是否應重新渲染其父元件。

### `opts.structuralSharing` 選項

- 類型: `boolean`
- 可選
- 設定是否對 `select` 回傳的值啟用結構共享 (structural sharing)。
- 詳情請參閱[渲染最佳化指南](../../guide/render-optimizations.md)。

## useParentMatches 回傳值

- 若有提供 `select` 函式，則回傳該函式的回傳值。
- 若未提供 `select` 函式，則回傳 [`RouteMatch`](./RouteMatchType.md) 物件的陣列。

## 範例

```tsx
import { useParentMatches } from '@tanstack/react-router'

function Component() {
  const parentMatches = useParentMatches()
  //    ^ [RouteMatch, RouteMatch, ...]
}
```
