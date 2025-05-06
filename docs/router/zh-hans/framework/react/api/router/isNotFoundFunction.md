---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:39:49.724Z'
id: isNotFoundFunction
title: isNotFound function
---

`isNotFound` 函数可用于判断一个对象是否为 [`NotFoundError`](./NotFoundErrorType.md) 对象。

## isNotFound 选项

`isNotFound` 函数接受一个参数 `input`。

### `input` 选项

- 类型: `unknown`
- 必填
- 需要检查是否为 [`NotFoundError`](./NotFoundErrorType.md) 的对象。

## isNotFound 返回值

- 类型: `boolean`
- 如果对象是 [`NotFoundError`](./NotFoundErrorType.md) 则返回 `true`。
- 如果对象不是 [`NotFoundError`](./NotFoundErrorType.md) 则返回 `false`。

## 示例

```tsx
import { isNotFound } from '@tanstack/react-router'

function somewhere(obj: unknown) {
  if (isNotFound(obj)) {
    // ...
  }
}
```
