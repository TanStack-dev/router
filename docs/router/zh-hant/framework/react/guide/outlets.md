---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:18:46.783Z'
title: 出口 (Outlets)
---

# 出口點 (Outlets)

嵌套路由 (Nested Routing) 意味著路由可以嵌套在其他路由中，包括它們的渲染方式。那麼我們該如何告訴路由在哪裡渲染這些嵌套內容呢？

## `Outlet` 元件

`Outlet` 元件用於渲染下一個可能匹配的子路由。`<Outlet />` 不接受任何屬性 (props)，並且可以在路由元件樹中的任何位置渲染。如果沒有匹配的子路由，`<Outlet />` 將渲染為 `null`。

> [!TIP]
> 如果路由的 `component` 未定義，它會自動渲染一個 `<Outlet />`。

一個很好的例子是配置應用程式的根路由 (Root Route)。讓我們為根路由設置一個元件，該元件會渲染標題，然後是一個 `<Outlet />` 供頂層路由渲染。

```tsx
import { createRootRoute, Outlet } from '@tanstack/react-router'

export const Route = createRootRoute({
  component: RootComponent,
})

function RootComponent() {
  return (
    <div>
      <h1>My App</h1>
      <Outlet /> {/* 子路由將在此處渲染 */}
    </div>
  )
}
```
