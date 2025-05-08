---
source-updated-at: '2025-02-27T22:43:21.000Z'
translation-updated-at: '2025-05-08T20:15:54.941Z'
title: 從 React Location 遷移
---

在開始從 React Location 遷移之前，請確保您已充分理解 TanStack Router 所使用的[路由概念](./routing/routing-concepts.md)與[設計決策](./decisions-on-dx.md)。

## React Location 與 TanStack Router 的差異

React Location 和 TanStack Router 共享許多設計決策概念，但仍有一些關鍵差異需要注意：

- React Location 使用 _泛型 (generics)_ 來推斷路由類型，而 TanStack Router 使用 _模組宣告合併 (module declaration merging)_ 來推斷類型。
- React Location 的路由配置是透過單一陣列的路由定義完成，而 TanStack Router 則是從[根路由 (root route)](./routing/routing-concepts.md#the-root-route) 開始，使用樹狀結構的路由定義。
- [檔案式路由 (File-based routing)](./routing/file-based-routing.md) 是 TanStack Router 推薦的路由定義方式，而 React Location 僅允許透過程式碼方式在單一檔案中定義路由。
  - TanStack Router 也支援[程式碼式路由 (code-based approach)](./routing/code-based-routing.md)，但大多數情況下不建議使用。詳細原因可參考：[為什麼檔案式路由是推薦的路由定義方式？](./decisions-on-dx.md#3-why-is-file-based-routing-the-preferred-way-to-define-routes)

## 遷移指南

本指南將逐步說明如何將 [React Location Basic 範例](https://github.com/TanStack/router/tree/react-location/examples/basic) 遷移至 TanStack Router，並使用檔案式路由。最終目標是實現與原始範例相同的功能（樣式和其他與路由無關的代碼將省略）。

> [!TIP]
> 若需使用程式碼式路由定義方式，請參閱[程式碼式路由指南](./routing/code-based-routing.md)。

### 步驟 1：更換為 TanStack Router 的依賴套件

首先，安裝 TanStack Router 的依賴套件：

```sh
npm install @tanstack/react-router @tanstack/router-devtools
```

並移除 React Location 的依賴套件：

```sh
npm uninstall @tanstack/react-location @tanstack/react-location-devtools
```

### 步驟 2：使用檔案式路由監聽工具

若您的專案使用 Vite（或其他支援的打包工具），可以使用 TanStack Router 插件來監聽路由檔案的變更，並自動更新路由配置。

安裝 Vite 插件：

```sh
npm install -D @tanstack/router-plugin
```

並將其加入 `vite.config.js`：

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

export default defineConfig({
  // ...
  plugins: [TanStackRouterVite(), react()],
})
```

若您的專案未使用 Vite，可選擇其他[支援的打包工具](./routing/file-based-routing.md#getting-started-with-file-based-routing)，或使用 `@tanstack/router-cli` 套件來監聽路由檔案變更並自動更新配置。

### 步驟 3：新增檔案式路由配置檔案

在專案根目錄建立 `tsr.config.json` 檔案，內容如下：

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts"
}
```

完整的 `tsr.config.json` 選項清單請參考[此處](./routing/file-based-routing.md#options)。

### 步驟 4：建立路由目錄

在專案的 `src` 目錄下建立 `routes` 子目錄：

```sh
mkdir src/routes
```

### 步驟 5：建立根路由檔案

```tsx
// src/routes/__root.tsx
import { createRootRoute, Outlet, Link } from '@tanstack/react-router'
import { TanStackRouterDevtools } from '@tanstack/router-devtools'

export const Route = createRootRoute({
  component: () => {
    return (
      <>
        <div>
          <Link to="/" activeOptions={{ exact: true }}>
            Home
          </Link>
          <Link to="/posts">Posts</Link>
        </div>
        <hr />
        <Outlet />
        <TanStackRouterDevtools />
      </>
    )
  },
})
```

### 步驟 6：建立首頁路由檔案

```tsx
// src/routes/index.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/')({
  component: Index,
})
```

> 需將 `src/index.tsx` 中與首頁路由相關的元件和邏輯移至 `src/routes/index.tsx`。

### 步驟 7：建立文章列表路由檔案

```tsx
// src/routes/posts.tsx
import { createFileRoute, Link, Outlet } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  component: Posts,
  loader: async () => {
    const posts = await fetchPosts()
    return {
      posts,
    }
  },
})

function Posts() {
  const { posts } = Route.useLoaderData()
  return (
    <div>
      <nav>
        {posts.map((post) => (
          <Link
            key={post.id}
            to={`/posts/$postId`}
            params={{ postId: post.id }}
          >
            {post.title}
          </Link>
        ))}
      </nav>
      <Outlet />
    </div>
  )
}
```

> 需將 `src/index.tsx` 中與文章列表路由相關的元件和邏輯移至 `src/routes/posts.tsx`。

### 步驟 8：建立文章列表索引路由檔案

```tsx
// src/routes/posts.index.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/')({
  component: PostsIndex,
})
```

> 需將 `src/index.tsx` 中與文章列表索引路由相關的元件和邏輯移至 `src/routes/posts.index.tsx`。

### 步驟 9：建立文章詳情路由檔案

```tsx
// src/routes/posts.$postId.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  component: PostsId,
  loader: async ({ params: { postId } }) => {
    const post = await fetchPost(postId)
    return {
      post,
    }
  },
})

function PostsId() {
  const { post } = Route.useLoaderData()
  // ...
}
```

> 需將 `src/index.tsx` 中與文章詳情路由相關的元件和邏輯移至 `src/routes/posts.$postId.tsx`。

### 步驟 10：生成路由樹

若使用支援的打包工具，執行開發腳本時會自動生成路由樹。

若未使用支援的打包工具，可執行以下指令生成路由樹：

```sh
npx tsr generate
```

### 步驟 11：更新主入口檔案以渲染路由

生成路由樹後，更新 `src/index.tsx` 以建立路由實例並渲染：

```tsx
// src/index.tsx
import React from 'react'
import ReactDOM from 'react-dom'
import { createRouter, RouterProvider } from '@tanstack/react-router'

// 匯入生成的路由樹
import { routeTree } from './routeTree.gen'

// 建立新的路由實例
const router = createRouter({ routeTree })

// 註冊路由實例以確保類型安全
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}

const domElementId = 'root' // 假設有一個 id 為 'root' 的根元素

// 渲染應用程式
const rootElement = document.getElementById(domElementId)
if (!rootElement) {
  throw new Error(`Element with id ${domElementId} not found`)
}

ReactDOM.createRoot(rootElement).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
)
```

### 完成！

您已成功將應用程式從 React Location 遷移至 TanStack Router，並使用檔案式路由。

React Location 還有一些功能可能是您的應用程式正在使用的，以下是一些遷移指南：

- [搜尋參數 (Search params)](./guide/search-params.md)
- [資料載入 (Data loading)](./guide/data-loading.md)
- [歷史記錄類型 (History types)](./guide/history-types.md)
- [萬用字元 / 全捕捉路由 (Wildcard / Splat / Catch-all routes)](./routing/routing-concepts.md#splat--catch-all-routes)
- [認證路由 (Authenticated routes)](./guide/authenticated-routes.md)

TanStack Router 還有更多功能值得探索：

- [路由上下文 (Router Context)](./guide/router-context.md)
- [預載入 (Preloading)](./guide/preloading.md)
- [無路徑的佈局路由 (Pathless Layout Routes)](./routing/routing-concepts.md#pathless-layout-routes)
- [路由遮罩 (Route masking)](./guide/route-masking.md)
- [伺服器渲染 (SSR)](./guide/ssr.md)
- ... 以及更多！

若遇到任何問題或有疑問，歡迎在 TanStack Discord 中尋求協助。
