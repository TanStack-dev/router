---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:39:39.873Z'
id: isRedirectFunction
title: isRedirect function
---

`isRedirect` 函数可用于判断一个对象是否为重定向对象。

## isRedirect 选项

`isRedirect` 函数接收一个参数 `input`。

#### `input`

- 类型: `unknown`
- 必填
- 需要检查是否为重定向对象的对象

## isRedirect 返回值

- 类型: `boolean`
- 如果对象是重定向对象，返回 `true`
- 如果对象不是重定向对象，返回 `false`

## 示例

```tsx
import { isRedirect } from '@tanstack/react-router'

function somewhere(obj: unknown) {
  if (isRedirect(obj)) {
    // ...
  }
}
```
