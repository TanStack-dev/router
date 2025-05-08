---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:26.749Z'
id: useLoaderDataHook
title: useLoaderData hook
---

`useLoaderData` 鉤子 (hook) 會從元件樹中最近的 [`RouteMatch`](./RouteMatchType.md) 返回載入器 (loader) 資料。

## useLoaderData 選項

`useLoaderData` 鉤子 (hook) 接受一個 `options` 物件。

### `opts.from` 選項

- 類型: `string`
- 最接近的父級匹配 (parent match) 的路由 ID
- 可選，但建議設定以確保完整的型別安全 (type safety)
- 若 `opts.strict` 為 `true`，未提供此選項時 TypeScript 會發出警告
- 若 `opts.strict` 為 `false`，TypeScript 會為返回的載入器 (loader) 資料提供較寬鬆的型別

### `opts.strict` 選項

- 類型: `boolean`
- 可選 - `預設值: true`
- 若為 `false`，將忽略 `opts.from` 選項，並放寬型別以反映所有可能載入器 (loader) 資料的共用型別

### `opts.select` 選項

- 可選
- `(loaderData: TLoaderData) => TSelected`
- 若提供此函式，它會接收載入器 (loader) 資料作為參數，其返回值將作為 `useLoaderData` 的結果。此值也會用於透過淺層比較 (shallow equality checks) 決定是否應重新渲染父元件

### `opts.structuralSharing` 選項

- 類型: `boolean`
- 可選
- 設定是否為 `select` 返回的值啟用結構共享 (structural sharing)
- 詳見 [渲染最佳化指南](../../guide/render-optimizations.md)

## useLoaderData 返回值

- 若有提供 `select` 函式，則返回該函式的結果
- 若未提供 `select` 函式，則返回載入器 (loader) 資料；若 `opts.strict` 為 `false` 則返回較寬鬆版本的載入器 (loader) 資料

## 範例

```tsx
import { useLoaderData } from '@tanstack/react-router'

function Component() {
  const loaderData = useLoaderData({ from: '/posts/$postId' })
  //     ^? { postId: string, body: string, ... }
  // ...
}
```
