---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:48:07.879Z'
id: RouterClass
title: Router Class
---

> [!CAUTION]
> 该类已被弃用，并将在 TanStack Router 的下一个主要版本中移除。
> 请改用 [`createRouter`](./createRouterFunction.md) 函数。

`Router` 类用于实例化一个新的路由器实例。

## `Router` 构造函数

`Router` 构造函数接收一个参数：用于配置路由器实例的 `options`。

### 构造函数选项

- 类型: [`RouterOptions`](./RouterOptionsType.md)
- 必填
- 用于配置路由器实例的选项。

### 构造函数返回值

- 返回一个 [`Router`](./RouterType.md) 实例。

## 示例

```tsx
import { Router, RouterProvider } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

const router = new Router({
  routeTree,
  defaultPreload: 'intent',
})

export default function App() {
  return <RouterProvider router={router} />
}
```
