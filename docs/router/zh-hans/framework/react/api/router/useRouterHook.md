---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:30:47.595Z'
id: useRouterHook
title: useRouter hook
---

`useRouter` 方法是一个钩子，用于从上下文中返回当前 [`Router`](./RouterType.md) 实例。该钩子在组件中访问路由器实例时非常有用。

## useRouter 返回值

- 当前的 [`Router`](./RouterType.md) 实例。

> ⚠️⚠️⚠️ **`router.state` 始终是最新的，但**不具备响应式 (Reactive)**。如果在组件中使用 `router.state`，当路由器状态变化时，组件不会重新渲染。要获取路由器状态的响应式版本，请使用 [`useRouterState`](./useRouterStateHook.md) 钩子。**

## 示例

```tsx
import { useRouter } from '@tanstack/react-router'

function Component() {
  const router = useRouter()
  //    ^ Router

  // ...
}
```
