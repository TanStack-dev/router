---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:53:00.336Z'
id: RedirectType
title: Redirect type
---

`Redirect` 类型用于表示 TanStack Router 中的重定向操作。

```tsx
export type Redirect = {
  statusCode?: number
  throw?: any
  headers?: HeadersInit
} & NavigateOptions
```

- [`NavigateOptions`](./NavigateOptionsType.md)

## Redirect 属性

`Redirect` 对象接受/包含以下属性：

### `statusCode` 属性

- 类型: `number`
- 可选
- 重定向时使用的 HTTP 状态码

### `throw` 属性

- 类型: `any`
- 可选
- 如果提供该属性，将抛出重定向对象而非返回它。这在某些场景下非常有用，例如当函数内 `throwing` 可能导致返回类型为 `never` 时。此时可以使用 `redirect({ throw: true })` 来抛出重定向对象而非返回它。

### `headers` 属性

- 类型: `HeadersInit`
- 可选
- 重定向时使用的 HTTP 头部信息
