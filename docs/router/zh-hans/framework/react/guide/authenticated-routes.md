---
source-updated-at: 2025-02-27T22:43:21.000Z
translation-updated-at: 2025-04-05T03:37:01.000Z
title: 认证路由
id: authenticated-routes
---

认证是Web应用程序中极为常见的需求。在本指南中，我们将介绍如何使用TanStack Router构建受保护路由，并在用户尝试访问时将其重定向至登录页。

## `route.beforeLoad` 选项

`route.beforeLoad` 选项允许指定一个在路由加载前调用的函数。该函数接收与 `route.loader` 相同的参数，是检查用户认证状态并在未认证时重定向至登录页的理想位置。

`beforeLoad` 函数相对于其他路由加载函数的执行顺序如下：

- 路由匹配（自上而下）
  - `route.params.parse`
  - `route.validateSearch`
- 路由加载（包括预加载）
  - **`route.beforeLoad`**
  - `route.onError`
- 并行加载
  - `route.component.preload?`
  - `route.load`

**关键点：路由的 `beforeLoad` 函数会在其所有子路由的 `beforeLoad` 之前调用**，本质上它是该路由及其所有子路由的中间件函数。

**如果在 `beforeLoad` 中抛出错误，其所有子路由都不会尝试加载**。

## 重定向处理

某些认证流程需要重定向至登录页（非强制要求）。可通过在 `beforeLoad` 中**抛出 `redirect()`** 实现：

```tsx
// src/routes/_authenticated.tsx
export const Route = createFileRoute('/_authenticated')({
  beforeLoad: async ({ location }) => {
    if (!isAuthenticated()) {
      throw redirect({
        to: '/login',
        search: {
          // 使用当前location实现登录后重定向
          // （不要使用`router.state.resolvedLocation`，它可能滞后于实际位置）
          redirect: location.href,
        },
      })
    }
  },
})
```

> [!TIP] > `redirect()` 函数接收与 `navigate` 相同的参数，可通过 `replace: true` 等选项替换当前历史记录而非新增记录。

用户认证后，通常需要将其重定向回原试图访问的页面。此时可利用初始重定向时添加的 `redirect` 搜索参数。由于需要完整替换URL，使用 `router.history.push` 比 `router.navigate` 更合适：

```tsx
router.history.push(search.redirect)
```

## 非重定向认证

部分应用选择不重定向用户，而是在当前页展示登录表单（覆盖主内容或通过模态框隐藏）。通过条件渲染 `<Outlet />` 也可实现：

```tsx
// src/routes/_authenticated.tsx
export const Route = createFileRoute('/_authenticated')({
  component: () => {
    if (!isAuthenticated()) {
      return <Login />
    }

    return <Outlet />
  },
})
```

此方式保持用户停留在当前页，认证后渲染 `<Outlet />` 即可展示子路由内容。

## 使用React上下文/钩子的认证

若认证流程依赖React上下文或钩子，需通过 `router.context` 选项将认证状态传递至TanStack Router。

> [!IMPORTANT]
> React钩子不应在组件外使用。如需在React组件外使用钩子，需在包裹 `<RouterProvider /> 的组件中提取钩子返回值，再传递给TanStack Router。

`router.context` 的详细用法将在[路由上下文](./router-context.md)章节介绍。

以下是使用React上下文和钩子保护认证路由的示例（完整实现参见[认证路由示例](../../examples/authenticated-routes)）：

- `src/routes/__root.tsx`

```tsx
import { createRootRouteWithContext } from '@tanstack/react-router'

interface MyRouterContext {
  // 类型为useAuth钩子返回值或AuthContext的值
  auth: AuthState
}

export const Route = createRootRouteWithContext<MyRouterContext>()({
  component: () => <Outlet />,
})
```

- `src/router.tsx`

```tsx
import { createRouter } from '@tanstack/react-router'

import { routeTree } from './routeTree.gen'

export const router = createRouter({
  routeTree,
  context: {
    // 初始auth为undefined
    // 后续会通过React组件传递认证状态
    auth: undefined!,
  },
})
```

- `src/App.tsx`

```tsx
import { RouterProvider } from '@tanstack/react-router'

import { AuthProvider, useAuth } from './auth'

import { router } from './router'

function InnerApp() {
  const auth = useAuth()
  return <RouterProvider router={router} context={{ auth }} />
}

function App() {
  return (
    <AuthProvider>
      <InnerApp />
    </AuthProvider>
  )
}
```

在受保护路由中，可通过 `beforeLoad` 检查认证状态，未登录时**抛出 `redirect()`** 至登录页：

- `src/routes/dashboard.route.tsx`

```tsx
import { createFileRoute, redirect } from '@tanstack/react-router'

export const Route = createFileRoute('/dashboard')({
  beforeLoad: ({ context, location }) => {
    if (!context.auth.isAuthenticated) {
      throw redirect({
        to: '/login',
        search: {
          redirect: location.href,
        },
      })
    }
  },
})
```

也可选择使用[非重定向认证](#non-redirected-authentication)方式展示登录表单而非重定向。

此方案同样适用于无路径路由或布局路由，可保护其下所有子路由。
