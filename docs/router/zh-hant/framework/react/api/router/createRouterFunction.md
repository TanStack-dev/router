---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:03.016Z'
id: createRouterFunction
title: createRouter function
---

`createRouter` 函式接受一個 [`RouterOptions`](./RouterOptionsType.md) 物件並建立一個新的 [`Router`](./RouterClass.md) 實例。

## createRouter 選項

- 類型: [`RouterOptions`](./RouterOptionsType.md)
- 必填
- 用於配置路由器實例的選項。

## createRouter 回傳值

- 一個 [`Router`](./RouterType.md) 的實例。

## 範例

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
