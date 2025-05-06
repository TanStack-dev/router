---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:52:47.471Z'
id: RegisterType
title: Register type
---

该类型用于向路由器实例注册路由树。这样做可以解锁 TanStack Router 的完整类型安全功能，包括来自 `@tanstack/react-router` 包的所有顶层导出。

```tsx
export type Register = {
  // router: [Your router type here]
}
```

要向路由器实例注册路由树，请使用声明合并 (declaration merging) 将你的路由器实例类型添加到 Register 接口的 `router` 属性下：

## 示例

```tsx
const router = createRouter({
  // ...
})

declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}
```
