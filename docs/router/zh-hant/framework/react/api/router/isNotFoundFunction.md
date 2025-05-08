---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:54.856Z'
id: isNotFoundFunction
title: isNotFound function
---

`isNotFound` 函式可用於判斷一個物件是否為 [`NotFoundError`](./NotFoundErrorType.md) 物件。

## isNotFound 選項

`isNotFound` 函式接受一個參數 `input`。

### `input` 選項

- 類型: `unknown`
- 必填
- 要檢查的物件，判斷是否為 [`NotFoundError`](./NotFoundErrorType.md)。

## isNotFound 返回值

- 類型: `boolean`
- 如果物件是 [`NotFoundError`](./NotFoundErrorType.md) 則返回 `true`。
- 如果物件不是 [`NotFoundError`](./NotFoundErrorType.md) 則返回 `false`。

## 範例

```tsx
import { isNotFound } from '@tanstack/react-router'

function somewhere(obj: unknown) {
  if (isNotFound(obj)) {
    // ...
  }
}
```
