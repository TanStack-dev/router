---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:44.745Z'
id: redirectFunction
title: redirect function
---

`redirect` 函式會回傳一個新的 `Redirect` 物件，該物件可以從 Route 的 `beforeLoad` 或 `loader` 回呼函式中回傳或拋出，以觸發重新導向 (redirect) 至新位置。

## redirect 選項

`redirect` 函式接受一個參數，即決定重新導向行為的 `options`。

- 類型: [`Redirect`](./RedirectType.md)
- 必填

## redirect 回傳結果

- 如果 `options` 物件中的 `throw` 屬性為 `true`，`Redirect` 物件會在函式呼叫中被拋出。
- 如果 `options` 物件中的 `throw` 屬性為 `false | undefined`，`Redirect` 物件會被回傳。

## 範例

```tsx
import { redirect } from '@tanstack/react-router'

const route = createRoute({
  // 拋出 redirect 物件
  loader: () => {
    if (!user) {
      throw redirect({
        to: '/login',
      })
    }
  },
  // 或強制 `redirect` 自行拋出
  loader: () => {
    if (!user) {
      redirect({
        to: '/login',
        throw: true,
      })
    }
  },
  // ... 其他 route 選項
})
```
