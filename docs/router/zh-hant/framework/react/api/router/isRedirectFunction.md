---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:51.647Z'
id: isRedirectFunction
title: isRedirect function
---

`isRedirect` 函式可用於判斷一個物件是否為重新導向 (redirect) 物件。

## isRedirect 選項

`isRedirect` 函式接受單一參數 `input`。

#### `input`

- 類型: `unknown`
- 必填
- 要檢查是否為重新導向 (redirect) 物件的物件

## isRedirect 返回值

- 類型: `boolean`
- 若物件為重新導向 (redirect) 物件則返回 `true`
- 若物件不是重新導向 (redirect) 物件則返回 `false`

## 範例

```tsx
import { isRedirect } from '@tanstack/react-router'

function somewhere(obj: unknown) {
  if (isRedirect(obj)) {
    // ...
  }
}
```
