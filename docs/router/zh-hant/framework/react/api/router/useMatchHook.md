---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:28.349Z'
id: useMatchHook
title: useMatch hook
---

`useMatch` 鉤子 (hook) 會回傳元件樹中的一個 [`RouteMatch`](./RouteMatchType.md)。原始的路由匹配 (route match) 包含路由器中關於路由匹配的所有資訊，同時也驅動了許多其他鉤子 (hooks) 的底層運作，例如 `useParams`、`useLoaderData`、`useRouteContext` 和 `useSearch`。

## useMatch 選項

`useMatch` 鉤子 (hook) 接受一個參數，即 `options` 物件。

### `opts.from` 選項

- 類型: `string`
- 路由匹配 (route match) 的路由 ID
- 可選，但建議設定以獲得完整的型別安全 (type safety)。
- 如果 `opts.strict` 為 `true`，則 `from` 為必填，且 TypeScript 會在此選項未提供時發出警告。
- 如果 `opts.strict` 為 `false`，則不得設定 `from`，且 TypeScript 會為回傳的 [`RouteMatch`](./RouteMatchType.md) 提供較寬鬆的型別。

### `opts.strict` 選項

- 類型: `boolean`
- 可選
- `預設值: true`
- 如果為 `false`，則不得設定 `opts.from`，且型別會放寬為 `Partial<RouteMatch>`，以反映所有路由匹配 (route matches) 的共享型別。

### `opts.select` 選項

- 可選
- `(match: RouteMatch) => TSelected`
- 如果提供，此函式會以路由匹配 (route match) 作為參數呼叫，並將其回傳值作為 `useMatch` 的回傳值。此值也會用於透過淺層比較 (shallow equality checks) 決定鉤子 (hook) 是否應重新渲染其父元件。

### `opts.structuralSharing` 選項

- 類型: `boolean`
- 可選
- 設定是否為 `select` 回傳的值啟用結構共享 (structural sharing)。
- 詳情請參閱 [渲染最佳化指南](../../guide/render-optimizations.md)。

### `opts.shouldThrow` 選項

- 類型: `boolean`
- 可選
- `預設值: true`
- 如果為 `false`，`useMatch` 在目前渲染的路由匹配 (route matches) 中找不到匹配時，不會拋出例外 (invariant exception)；此時會回傳 `undefined`。

## useMatch 回傳值

- 如果有提供 `select` 函式，則回傳 `select` 函式的回傳值。
- 如果沒有提供 `select` 函式，則回傳 [`RouteMatch`](./RouteMatchType.md) 物件；如果 `opts.strict` 為 `false`，則回傳較寬鬆版本的 `RouteMatch` 物件。

## 範例

### 存取路由匹配 (route match)

```tsx
import { useMatch } from '@tanstack/react-router'

function Component() {
  const match = useMatch({ from: '/posts/$postId' })
  //     ^? strict match for RouteMatch
  // ...
}
```

### 存取根路由 (root route) 的匹配

```tsx
import {
  useMatch,
  rootRouteId, // <<<< 使用此標記！
} from '@tanstack/react-router'

function Component() {
  const match = useMatch({ from: rootRouteId })
  //     ^? strict match for RouteMatch
  // ...
}
```

### 檢查特定路由是否正在渲染中

```tsx
import { useMatch } from '@tanstack/react-router'

function Component() {
  const match = useMatch({ from: '/posts', shouldThrow: false })
  //     ^? RouteMatch | undefined
  if (match !== undefined) {
    // ...
  }
}
```
