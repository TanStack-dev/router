---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:04.186Z'
id: useMatchesHook
title: useMatches hook
---

`useMatches` 鉤子 (hook) 會回傳路由中所有的 [`RouteMatch`](./RouteMatchType.md) 物件，**無論呼叫者在 React 元件樹中的位置為何**。

> [!TIP]
> 若你只需要父層或子層的匹配結果，可以根據需求使用 [`useParentMatches`](./useParentMatchesHook.md) 或 [`useChildMatches`](./useChildMatchesHook.md)。

## useMatches 選項

`useMatches` 鉤子接受一個 _可選的_ 參數，即 `options` 物件。

### `opts.select` 選項

- 可選
- `(matches: RouteMatch[]) => TSelected`
- 若提供此函式，它會以路由匹配結果作為參數被呼叫，其回傳值將作為 `useMatches` 的回傳值。此值也會用於透過淺層比較 (shallow equality) 判斷是否該觸發父元件的重新渲染。

### `opts.structuralSharing` 選項

- 型別: `boolean`
- 可選
- 設定是否對 `select` 回傳的值啟用結構共享 (structural sharing)。
- 詳情請參閱 [渲染最佳化指南](../../guide/render-optimizations.md)。

## useMatches 回傳值

- 若有提供 `select` 函式，則回傳該函式的執行結果。
- 若未提供 `select` 函式，則回傳 [`RouteMatch`](./RouteMatchType.md) 物件的陣列。

## 範例

```tsx
import { useMatches } from '@tanstack/react-router'

function Component() {
  const matches = useMatches()
  //     ^? [RouteMatch, RouteMatch, ...]
  // ...
}
```
