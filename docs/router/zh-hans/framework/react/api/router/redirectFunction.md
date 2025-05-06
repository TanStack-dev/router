---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:38:07.804Z'
id: redirectFunction
title: redirect function
---

`redirect` 函数返回一个新的 `Redirect` 对象，该对象可以从路由的 `beforeLoad` 或 `loader` 回调等位置返回或抛出，以触发重定向 (redirect) 到新位置。

## redirect 选项

`redirect` 函数接受一个参数 `options`，用于确定重定向行为。

- 类型: [`Redirect`](./RedirectType.md)
- 必填

## redirect 返回值

- 如果 `options` 对象中的 `throw` 属性为 `true`，`Redirect` 对象将在函数调用内部被抛出。
- 如果 `options` 对象中的 `throw` 属性为 `false | undefined`，`Redirect` 对象将被返回。

## 示例

```tsx
import { redirect } from '@tanstack/react-router'

const route = createRoute({
  // 抛出重定向对象
  loader: () => {
    if (!user) {
      throw redirect({
        to: '/login',
      })
    }
  },
  // 或强制 `redirect` 自行抛出
  loader: () => {
    if (!user) {
      redirect({
        to: '/login',
        throw: true,
      })
    }
  },
  // ... 其他路由选项
})
```
