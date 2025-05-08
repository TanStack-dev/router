---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:23:12.120Z'
id: NotFoundErrorType
title: NotFoundError
---

`NotFoundError` 類型用於表示 TanStack Router 中的「找不到路徑」錯誤。

```tsx
export type NotFoundError = {
  global?: boolean
  data?: any
  throw?: boolean
  routeId?: string
}
```

## NotFoundError 屬性

`NotFoundError` 物件接受/包含以下屬性：

### `data` 屬性

- 類型：`any`
- 可選
- 當處理「找不到路徑」錯誤時，傳遞給 `notFoundComponent` 的自訂資料

### `global` 屬性

- 類型：`boolean`
- 可選 - `預設值：false`
- 若為 true，「找不到路徑」錯誤將由根路由的 `notFoundComponent` 處理，而非從拋出錯誤的路由向上冒泡。此行為與導入根路由並呼叫 `RootRoute.notFound()` 相同。

### `route` 屬性

- 類型：`string`
- 可選
- 將嘗試處理「找不到路徑」錯誤的路由 ID。若該路由沒有 `notFoundComponent`，錯誤將向上冒泡至父路由（並在必要時由根路由處理）。預設情況下，TanStack Router 會嘗試由拋出錯誤的路逕自行處理「找不到路徑」錯誤。

### `throw` 屬性

- 類型：`boolean`
- 可選 - `預設值：false`
- 若提供此屬性，將直接拋出「找不到路徑」物件而非回傳。這在函式中「拋出」可能導致回傳類型為 `never` 的情境下特別有用。此時可使用 `notFound({ throw: true })` 來拋出「找不到路徑」物件而非回傳。
