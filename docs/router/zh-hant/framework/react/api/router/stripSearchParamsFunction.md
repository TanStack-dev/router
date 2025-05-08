---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:46.809Z'
id: stripSearchParams
title: Search middleware to strip search params
---

`stripSearchParams` 是一個用於移除搜尋參數 (search params) 的搜尋中介軟體 (middleware)。

## stripSearchParams 屬性

`stripSearchParams` 接受以下其中一種輸入：

- `true`：若搜尋結構描述 (search schema) 沒有必填參數時，可使用 `true` 來移除所有搜尋參數
- 一個包含要移除之搜尋參數鍵值 (keys) 的列表；僅允許選擇性搜尋參數 (optional search params) 的鍵值
- 一個符合部分輸入搜尋結構描述的物件。搜尋參數會與此物件的值進行比對；若值深度相等 (deeply equal)，則會移除該參數。這特別適用於移除預設搜尋參數 (default search params)

## 範例

```tsx
import { z } from 'zod'
import { createFileRoute, stripSearchParams } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

const defaultValues = {
  one: 'abc',
  two: 'xyz',
}

const searchSchema = z.object({
  one: z.string().default(defaultValues.one),
  two: z.string().default(defaultValues.two),
})

export const Route = createFileRoute('/hello')({
  validateSearch: zodValidator(searchSchema),
  search: {
    // 移除預設值
    middlewares: [stripSearchParams(defaultValues)],
  },
})
```

```tsx
import { z } from 'zod'
import { createRootRoute, stripSearchParams } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

const searchSchema = z.object({
  hello: z.string().default('world'),
  requiredParam: z.string(),
})

export const Route = createRootRoute({
  validateSearch: zodValidator(searchSchema),
  search: {
    // 總是移除 `hello`
    middlewares: [stripSearchParams(['hello'])],
  },
})
```

```tsx
import { z } from 'zod'
import { createFileRoute, stripSearchParams } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

const searchSchema = z.object({
  one: z.string().default('abc'),
  two: z.string().default('xyz'),
})

export const Route = createFileRoute('/hello')({
  validateSearch: zodValidator(searchSchema),
  search: {
    // 移除所有搜尋參數
    middlewares: [stripSearchParams(true)],
  },
})
```
