---
source-updated-at: '2025-03-21T04:05:46.000Z'
translation-updated-at: '2025-05-08T20:14:57.088Z'
title: 常見問題
---

歡迎來到 TanStack Router 的常見問題集！在這裡，您將找到關於 TanStack Router 的常見問題解答。如果您的問題未在此處獲得解答，請隨時在 [TanStack Discord](https://tlinz.com/discord) 中提問。

## 我應該將 `routeTree.gen.ts` 檔案提交到 git 嗎？

是的！雖然路由樹檔案（即 `routeTree.gen.ts`）是由 TanStack Router 生成的，但它本質上是應用程式運行時的一部分，並非建構產物。路由樹檔案是應用程式原始碼的關鍵部分，TanStack Router 會使用它在運行時建立應用程式的路由。

您應該將此檔案提交到 git，以便其他開發人員可以使用它來建構您的應用程式。

## 我可以條件式渲染根路由元件嗎？

不行，根路由始終會被渲染，因為它是應用程式的入口點。

如果您需要條件式渲染某個路由的元件，這通常意味著頁面內容需要根據某些條件（例如使用者驗證狀態）而有所不同。針對這種使用情境，您應該使用 [佈局路由 (Layout Route)](./routing/routing-concepts.md#layout-routes) 或 [無路徑佈局路由 (Pathless Layout Route)](./routing/routing-concepts.md#pathless-layout-routes) 來條件式渲染內容。

您可以在路由的 `beforeLoad` 函式中使用條件檢查來限制對這些路由的存取。

<details>
<summary>這看起來是什麼樣子？</summary>

```tsx
// src/routes/_pathless-layout.tsx
import { createFileRoute, Outlet } from '@tanstack/react-router'
import { isAuthenticated } from '../utils/auth'

export const Route = createFileRoute('/_pathless-layout', {
  beforeLoad: async () => {
    // 檢查使用者是否已驗證
    const authed = await isAuthenticated()
    if (!authed) {
      // 將使用者重新導向至登入頁面
      return '/login'
    }
  },
  component: PathlessLayoutRouteComponent,
  // ...
})

function PathlessLayoutRouteComponent() {
  return (
    <div>
      <h1>您已通過驗證</h1>
      <Outlet />
    </div>
  )
}
```

</details>
