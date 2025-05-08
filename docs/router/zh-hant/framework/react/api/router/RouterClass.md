---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:34.797Z'
id: RouterClass
title: Router Class
---

> [!CAUTION]
> 此類別已被棄用，將在 TanStack Router 的下一個主要版本中移除。
> 請改用 [`createRouter`](./createRouterFunction.md) 函式。

`Router` 類別用於實例化一個新的路由器實例。

## `Router` 建構函式

`Router` 建構函式接受單一參數：用於配置路由器實例的 `options`。

### 建構函式選項

- 類型: [`RouterOptions`](./RouterOptionsType.md)
- 必填
- 用於配置路由器實例的選項。

### 建構函式回傳值

- 一個 [`Router`](./RouterType.md) 的實例。

## 範例

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
