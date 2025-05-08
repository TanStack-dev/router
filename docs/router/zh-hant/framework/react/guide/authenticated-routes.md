---
source-updated-at: '2025-04-05T00:53:14.000Z'
translation-updated-at: '2025-05-08T20:21:10.380Z'
id: authenticated-routes
title: 認證路由
---

# 驗證路由 (Authenticated Routes)

驗證 (Authentication) 是網頁應用程式中非常常見的需求。本指南將介紹如何使用 TanStack Router 來建立受保護的路由，以及當使用者嘗試存取這些路由時如何將他們重新導向至登入頁面。

## `route.beforeLoad` 選項

`route.beforeLoad` 選項允許你指定一個在路由載入前會被呼叫的函式。它接收與 `route.loader` 函式相同的所有參數。這是檢查使用者是否已驗證 (authenticated) 的絕佳位置，如果未驗證則將他們重新導向至登入頁面。

`beforeLoad` 函式與其他路由載入函式的相對執行順序如下：

- 路由匹配 (由上至下)
  - `route.params.parse`
  - `route.validateSearch`
- 路由載入 (包含預載入)
  - **`route.beforeLoad`**
  - `route.onError`
- 路由載入 (平行處理)
  - `route.component.preload?`
  - `route.load`

**重要須知：路由的 `beforeLoad` 函式會在其所有子路由的 `beforeLoad` 函式之前被呼叫**。它本質上是該路由及其所有子路由的中介層 (middleware) 函式。

**如果在 `beforeLoad` 中拋出錯誤，其所有子路由都不會嘗試載入**。

## 重新導向 (Redirecting)

雖然不是必須的，但某些驗證流程需要重新導向至登入頁面。為此，你可以從 `beforeLoad` 中**拋出 `redirect()`**：

```tsx
// src/routes/_authenticated.tsx
export const Route = createFileRoute('/_authenticated')({
  beforeLoad: async ({ location }) => {
    if (!isAuthenticated()) {
      throw redirect({
        to: '/login',
        search: {
          // 使用當前位置來實現登入後重新導向
          // (不要使用 `router.state.resolvedLocation`，因為它可能
          // 會落後於實際的當前位置)
          redirect: location.href,
        },
      })
    }
  },
})
```

> [!TIP] > `redirect()` 函式接受與 `navigate` 函式相同的所有選項，因此如果你想要替換當前的歷史記錄條目而不是新增一個，可以傳遞像 `replace: true` 這樣的選項。

一旦使用者通過驗證，通常會將他們重新導向回他們原本嘗試存取的頁面。為此，你可以利用我們在原始重新導向中新增的 `redirect` 搜尋參數。由於我們將用原本的 URL 替換整個 URL，`router.history.push` 比 `router.navigate` 更適合此用途：

```tsx
router.history.push(search.redirect)
```

## 非重新導向驗證 (Non-Redirected Authentication)

某些應用程式選擇不將使用者重新導向至登入頁面，而是讓使用者停留在同一頁面，並顯示一個登入表單，該表單要麼替換主要內容，要麼透過模態框 (modal) 隱藏它。透過簡單地短路渲染通常會渲染子路由的 `<Outlet />`，這在 TanStack Router 中也是可行的：

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

這讓使用者停留在同一頁面，但仍允許你渲染登入表單。一旦使用者通過驗證，你可以簡單地渲染 `<Outlet />`，子路由就會被渲染。

## 使用 React 上下文/鉤子進行驗證 (Authentication using React context/hooks)

如果你的驗證流程依賴於與 React 上下文 (context) 和/或鉤子 (hooks) 的互動，你需要使用 `router.context` 選項將你的驗證狀態傳遞給 TanStack Router。

> [!IMPORTANT]
> React 鉤子不應該在 React 元件之外使用。如果你需要在 React 元件之外使用鉤子，你需要在包裹你的 `<RouterProvider />` 的元件中提取鉤子返回的狀態，然後將返回的值傳遞給 TanStack Router。

我們將在[路由器上下文 (Router Context)](./router-context.md) 章節詳細介紹 `router.context` 選項。

以下是一個使用 React 上下文和鉤子來保護 TanStack Router 中驗證路由的範例。完整的設定請參閱[驗證路由範例 (Authenticated Routes example)](../examples/authenticated-routes)。

- `src/routes/__root.tsx`

```tsx
import { createRootRouteWithContext } from '@tanstack/react-router'

interface MyRouterContext {
  // 你的 useAuth 鉤子的 ReturnType 或你的 AuthContext 的值
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
    // auth 初始會是 undefined
    // 我們將從 React 元件中傳遞 auth 狀態
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

然後在驗證路由中，你可以使用 `beforeLoad` 函式檢查驗證狀態，如果使用者未登入，則**拋出 `redirect()`** 到你的**登入路由**。

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

你也可以選擇使用[非重新導向驗證](#non-redirected-authentication)方法來顯示登入表單，而不是呼叫**重新導向**。

這種方法也可以與無路徑 (Pathless) 或佈局路由 (Layout Route) 一起使用，以保護其父路由下的所有路由。
