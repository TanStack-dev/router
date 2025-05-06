---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:40:52.361Z'
id: createRouterFunction
title: createRouter function
---

`createRouter` 函数接收一个 [`RouterOptions`](./RouterOptionsType.md) 对象并创建一个新的 [`Router`](./RouterClass.md) 实例。

## createRouter 配置选项

- 类型: [`RouterOptions`](./RouterOptionsType.md)
- 必填
- 用于配置路由实例的选项对象。

## createRouter 返回值

- 返回一个 [`Router`](./RouterType.md) 实例。

## 示例代码

```tsx
import { createRouter, RouterProvider } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

const router = createRouter({
  routeTree,
  defaultPreload: 'intent',
})

export default function App() {
  return <RouterProvider router={router} />
}
```
