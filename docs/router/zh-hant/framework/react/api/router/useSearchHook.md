---
source-updated-at: '2025-02-08T21:36:40.000Z'
translation-updated-at: '2025-05-08T20:20:36.914Z'
id: useSearchHook
title: useSearch hook
---

`useSearch` 方法是一個鉤子 (hook)，它會返回當前位置的搜尋查詢參數作為一個物件。這個鉤子 (hook) 在元件中存取當前搜尋字串和查詢參數時非常有用。

## useSearch 選項

`useSearch` 鉤子 (hook) 接受一個 `options` 物件。

### `opts.from` 選項

- 類型: `string`
- 必填
- 要匹配搜尋查詢參數的路由 ID (RouteID)。

### `opts.shouldThrow` 選項

- 類型: `boolean`
- 選填
- `預設值: true`
- 如果設為 `false`，當在當前渲染的匹配中找不到對應項目時，`useSearch` 不會拋出例外 (invariant exception)；在這種情況下，它會返回 `undefined`。

### `opts.select` 選項

- 類型: `(search: SelectedSearchSchema) => TSelected`
- 選填
- 如果提供此函數，它會以搜尋物件作為參數被呼叫，並且其返回值會從 `useSearch` 返回。

### `opts.structuralSharing` 選項

- 類型: `boolean`
- 選填
- 配置是否為 `select` 返回的值啟用結構共享 (structural sharing)。
- 更多資訊請參閱 [渲染最佳化指南](../../guide/render-optimizations.md)。

### `opts.strict` 選項

- 類型: `boolean`
- 選填 - `預設值: true`
- 如果設為 `false`，將忽略 `opts.from` 選項，並且類型會放寬為 `Partial<FullSearchSchema>` 以反映所有搜尋查詢參數的共享類型。

## useSearch 返回值

- 如果提供 `opts.from`，則返回當前位置的搜尋查詢參數物件，或者如果提供了 `select` 函數，則返回 `TSelected`。
- 如果 `opts.strict` 設為 `false`，則返回當前位置的搜尋查詢參數物件，或者如果提供了 `select` 函數，則返回 `TSelected`。

## 範例

```tsx
import { useSearch } from '@tanstack/react-router'

function Component() {
  const search = useSearch({ from: '/posts/$postId' })
  //    ^ FullSearchSchema

  // 或者

  const selected = useSearch({
    from: '/posts/$postId',
    select: (search) => search.postView,
  })
  //    ^ string

  // 或者

  const looseSearch = useSearch({ strict: false })
  //    ^ Partial<FullSearchSchema>

  // ...
}
```
