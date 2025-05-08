---
source-updated-at: '2025-02-19T11:57:38.000Z'
translation-updated-at: '2025-05-08T20:21:44.664Z'
id: notFoundFunction
title: notFound function
---

`notFound` 函式會回傳一個新的 `NotFoundError` 物件，該物件可以從像是路由的 `beforeLoad` 或 `loader` 回呼函式中回傳或拋出，以觸發 `notFoundComponent`。

## notFound 選項

`notFound` 函式接受一個可選的參數，即用來建立 not-found 錯誤物件的 `options`。

- 類型: [`Partial<NotFoundError>`](./NotFoundErrorType.md)
- 可選

## notFound 回傳值

- 如果 `options` 物件中的 `throw` 屬性為 `true`，則 `NotFoundError` 物件會在函式呼叫中被拋出。
- 如果 `options` 物件中的 `throw` 屬性為 `false | undefined`，則 `NotFoundError` 物件會被回傳。

## 範例

```tsx
import { notFound, createFileRoute, rootRouteId } from '@tanstack/react-router'

const Route = new createFileRoute('/posts/$postId')({
  // 拋出一個 not-found 物件
  loader: ({ context: { post } }) => {
    if (!post) {
      throw notFound()
    }
  },
  // 或者如果你想在整個頁面顯示 not-found
  loader: ({ context: { team } }) => {
    if (!team) {
      throw notFound({ routeId: rootRouteId })
    }
  },
  // ... 其他路由選項
})
```
