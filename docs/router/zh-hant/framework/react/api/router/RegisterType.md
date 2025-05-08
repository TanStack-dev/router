---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:47.719Z'
id: RegisterType
title: Register type
---

此類型用於向路由實例 (router instance) 註冊路由樹 (route tree)。這樣做可解鎖 TanStack Router 的完整類型安全功能，包括來自 `@tanstack/react-router` 套件的頂層匯出 (top-level exports)。

```tsx
export type Register = {
  // router: [Your router type here]
}
```

若要向路由實例註冊路由樹，請使用宣告合併 (declaration merging) 將您的路由實例類型添加到 Register 介面 (interface) 的 `router` 屬性下：

## 範例

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
