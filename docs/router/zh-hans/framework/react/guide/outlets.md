---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:22:25.000Z
title: 出口 (Outlet)
---

嵌套路由是指路由可以嵌套在其他路由中，包括它们的渲染方式。那么我们如何告诉路由在哪里渲染这些嵌套内容呢？

## `Outlet` 组件

`Outlet` 组件用于渲染下一个可能匹配的子路由。`<Outlet />` 不接受任何属性（props），可以在路由组件树的任何位置渲染。如果没有匹配的子路由，`<Outlet />` 将渲染为 `null`。

> [!TIP]
> 如果路由的 `component` 未定义，它将自动渲染一个 `<Outlet />`。

一个很好的例子是配置应用程序的根路由。让我们为根路由设置一个组件，先渲染标题，然后渲染一个 `<Outlet />` 供顶级路由渲染。

```tsx
import { createRootRoute, Outlet } from '@tanstack/react-router'

export const Route = createRootRoute({
  component: RootComponent,
})

function RootComponent() {
  return (
    <div>
      <h1>My App</h1>
      <Outlet /> {/* 子路由将在此处渲染 */}
    </div>
  )
}
```
