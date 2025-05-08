---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:54.791Z'
id: RedirectType
title: Redirect type
---

`Redirect` 類型用於表示 TanStack Router 中的重新導向動作。

```tsx
export type Redirect = {
  statusCode?: number
  throw?: any
  headers?: HeadersInit
} & NavigateOptions
```

- [`NavigateOptions`](./NavigateOptionsType.md)

## Redirect 屬性

`Redirect` 物件接受/包含以下屬性：

### `statusCode` 屬性

- 類型：`number`
- 可選
- 重新導向時使用的 HTTP 狀態碼

### `throw` 屬性

- 類型：`any`
- 可選
- 若提供此屬性，將會拋出重新導向物件而非回傳它。這在某些情況下很有用，例如當函式中的 `throwing` 可能導致其回傳類型為 `never` 時。此時，你可以使用 `redirect({ throw: true })` 來拋出重新導向物件，而非回傳它。

### `headers` 屬性

- 類型：`HeadersInit`
- 可選
- 重新導向時使用的 HTTP 標頭
