---
source-updated-at: 2025-03-21T04:05:46.000Z
translation-updated-at: 2025-04-05T03:12:28.000Z
title: 常见问题解答
---

欢迎来到 TanStack Router 常见问题解答！在这里，您将找到关于 TanStack Router 常见问题的答案。如果您的疑问未在此得到解答，欢迎在 [TanStack Discord](https://tlinz.com/discord) 中提问。

## 我应该将 `routeTree.gen.ts` 文件提交到 git 吗？

是的！尽管路由树文件（即 `routeTree.gen.ts`）是由 TanStack Router 生成的，但它本质上是应用程序运行时的一部分，而非构建产物。该文件是应用程序源代码的关键组成部分，TanStack Router 会在运行时使用它来构建应用的路由。

您应该将此文件提交到 git，以便其他开发者能够使用它来构建您的应用程序。

## 我可以有条件地渲染根路由组件吗？

不可以，根路由始终会被渲染，因为它是应用程序的入口点。

如果您需要根据条件渲染某个路由的组件，通常意味着页面内容需要基于某些条件（例如用户认证状态）而变化。针对这种场景，您应该使用[布局路由](./routing/routing-concepts.md#layout-routes)或[无路径布局路由](./routing/routing-concepts.md#pathless-layout-routes)来有条件地渲染内容。

您可以通过在路由的 `beforeLoad` 函数中添加条件检查来限制对这些路由的访问。

<details>
<summary>示例代码</summary>

```tsx
// src/routes/_pathless-layout.tsx
import { createFileRoute, Outlet } from '@tanstack/react-router'
import { isAuthenticated } from '../utils/auth'

export const Route = createFileRoute('/_pathless-layout', {
  beforeLoad: async () => {
    // 检查用户是否已认证
    const authed = await isAuthenticated()
    if (!authed) {
      // 将用户重定向至登录页
      return '/login'
    }
  },
  component: PathlessLayoutRouteComponent,
  // ...
})

function PathlessLayoutRouteComponent() {
  return (
    <div>
      <h1>您已通过认证</h1>
      <Outlet />
    </div>
  )
}
```

</details>
