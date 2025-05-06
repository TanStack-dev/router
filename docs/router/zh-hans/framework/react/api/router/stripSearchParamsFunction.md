---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:37:21.416Z'
id: stripSearchParams
title: Search middleware to strip search params
---

`stripSearchParams` 是一个用于移除搜索参数 (search params) 的搜索中间件 (search middleware)。

## stripSearchParams 属性

`stripSearchParams` 接受以下任一输入：

- `true`：如果搜索模式 (search schema) 没有必填参数，可以使用 `true` 来移除所有搜索参数
- 需要移除的搜索参数键名列表；仅允许可选搜索参数的键名
- 符合部分输入搜索模式的对象。搜索参数会与该对象的值进行深度比较；若值完全相等，则会被移除。这对于移除默认搜索参数特别有用

## 示例

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
    // 移除默认值
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
    // 始终移除 `hello`
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
    // 移除所有搜索参数
    middlewares: [stripSearchParams(true)],
  },
})
```
