---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:20:37.966Z'
id: useRouterHook
title: useRouter hook
---

`useRouter` 方法是一個鉤子 (hook)，它會從上下文中返回當前的 [`Router`](./RouterType.md) 實例。這個鉤子 (hook) 在需要於組件中存取路由實例時非常有用。

## useRouter 返回

- 當前的 [`Router`](./RouterType.md) 實例。

> ⚠️⚠️⚠️ **`router.state` 始終是最新的，但並非響應式 (reactive)。如果你在組件中使用 `router.state`，當路由狀態變化時，組件不會重新渲染。要獲取路由狀態的響應式 (reactive) 版本，請使用 [`useRouterState`](./useRouterStateHook.md) 鉤子 (hook)。**

## 範例

```tsx
import { useRouter } from '@tanstack/react-router'

function Component() {
  const router = useRouter()
  //    ^ Router

  // ...
}
```
