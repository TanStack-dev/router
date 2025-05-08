---
source-updated-at: '2025-02-27T00:19:00.000Z'
translation-updated-at: '2025-05-08T20:21:41.664Z'
title: 程式碼分割
---

# 程式碼分割 (Code Splitting)

程式碼分割與懶加載 (lazy loading) 是一種強大的技術，可以改善應用程式的打包體積與載入效能。

- 減少初始頁面載入時需要載入的程式碼量
- 程式碼會在需要時才按需載入
- 產生更多體積較小的程式碼塊 (chunk)，瀏覽器能更容易快取這些內容

## TanStack Router 如何分割程式碼？

TanStack Router 將程式碼分為兩大類：

- **關鍵路由配置 (Critical Route Configuration)** - 渲染當前路由並盡早啟動資料載入流程所需的程式碼。

  - 路徑解析/序列化 (Path Parsing/Serialization)
  - 搜尋參數驗證 (Search Param Validation)
  - 載入器 (Loaders)、載入前處理 (Before Load)
  - 路由上下文 (Route Context)
  - 靜態資料 (Static Data)
  - 連結 (Links)
  - 腳本 (Scripts)
  - 樣式 (Styles)
  - 其他未列於下方的所有路由配置

- **非關鍵/懶加載路由配置 (Non-Critical/Lazy Route Configuration)** - 不需要用來匹配路由，可以按需載入的程式碼。
  - 路由元件 (Route Component)
  - 錯誤元件 (Error Component)
  - 載入中元件 (Pending Component)
  - 未找到元件 (Not-found Component)

> 🧠 **為什麼載入器 (loader) 不進行分割？**
>
> - 載入器本身已經是異步邊界，若再分割會導致需要同時等待程式碼塊載入與載入器執行，付出雙重代價。
> - 通常情況下，載入器對打包體積的影響比元件小。
> - 載入器是路由最重要的可預載資源之一，特別是當你使用預設的預載意圖（如懸停在連結上時），因此讓載入器能直接可用而不需額外的異步開銷非常重要。
>
>   若了解分割載入器的缺點後仍想進行，請參考[資料載入器分割](#data-loader-splitting)章節。

## 將路由檔案封裝至目錄中

由於 TanStack Router 的檔案式路由系統 (file-based routing) 同時支援扁平與巢狀檔案結構，你可以無需額外配置就將路由檔案封裝到單一目錄中。

要封裝路由檔案，只需將路由檔案移至與其同名的目錄中，並將檔案重新命名為 `route.tsx` 且放在 `.route` 檔案內。

例如，若你有一個名為 `posts.tsx` 的路由檔案，你會建立一個名為 `posts` 的新目錄，並將 `posts.tsx` 移至該目錄中，重新命名為 `route.tsx`。

**變更前**

- `posts.tsx`

**變更後**

- `posts`
  - `route.tsx`

## 程式碼分割的實現方式

TanStack Router 支援多種程式碼分割方式。若你使用程式碼式路由 (code-based routing)，請直接跳至[程式碼式分割](#code-based-splitting)章節。

使用檔案式路由時，你可以選擇以下方式進行程式碼分割：

- [使用自動程式碼分割 ✨](#using-automatic-code-splitting)
- [使用 `.lazy.tsx` 後綴](#using-the-lazytsx-suffix)
- [使用虛擬路由 (Virtual Routes)](#using-virtual-routes)

## 使用自動程式碼分割 ✨

這是最簡單且強大的路由檔案分割方式。

啟用 `autoCodeSplitting` 功能後，TanStack Router 會根據前述的非關鍵路由配置自動分割你的路由檔案。

> [!IMPORTANT]
> 自動程式碼分割功能**僅限**於使用檔案式路由並搭配[支援的打包工具](../routing/file-based-routing.md#getting-started-with-file-based-routing)時可用。
> 若**僅**使用 CLI (`@tanstack/router-cli`)，此功能**無法**運作。

要啟用自動程式碼分割，只需在 TanStack Router 打包工具插件配置中加入以下設定：

```ts
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

export default defineConfig({
  plugins: [
    TanStackRouterVite({
      // ...
      autoCodeSplitting: true,
    }),
    react(), // 確保此插件添加在 TanStack Router 打包工具插件之後
  ],
})
```

這樣就完成了！TanStack Router 會自動根據關鍵與非關鍵路由配置分割所有路由檔案。

若需要更多控制，請參考[自動程式碼分割](./automatic-code-splitting.md)指南了解可用選項。

## 使用 `.lazy.tsx` 後綴

若無法使用自動程式碼分割功能，你仍可透過 `.lazy.tsx` 後綴來分割路由檔案。**只需將程式碼移至帶有 `.lazy.tsx` 後綴的獨立檔案**，並使用 `createLazyFileRoute` 替代 `createFileRoute` 即可。

> [!IMPORTANT] > `__root.tsx` 路由檔案（使用 `createRootRoute` 或 `createRootRouteWithContext`）不支援程式碼分割，因為它無論當前路由為何都會被渲染。

`createLazyFileRoute` 僅支援以下選項：

| 匯出名稱            | 說明                           |
| ------------------- | ------------------------------ |
| `component`         | 路由渲染的元件。               |
| `errorComponent`    | 載入路由發生錯誤時渲染的元件。 |
| `pendingComponent`  | 路由載入中時渲染的元件。       |
| `notFoundComponent` | 當拋出未找到錯誤時渲染的元件。 |

### 使用 `.lazy.tsx` 的程式碼分割範例

使用 `.lazy.tsx` 時，你可以將路由拆分為兩個檔案來實現程式碼分割：

**變更前（單一檔案）**

```tsx
// src/routes/posts.tsx
import { createFileRoute } from '@tanstack/react-router'
import { fetchPosts } from './api'

export const Route = createFileRoute('/posts')({
  loader: fetchPosts,
  component: Posts,
})

function Posts() {
  // ...
}
```

**變更後（拆分為兩個檔案）**

此檔案包含關鍵路由配置：

```tsx
// src/routes/posts.tsx

import { createFileRoute } from '@tanstack/react-router'
import { fetchPosts } from './api'

export const Route = createFileRoute('/posts')({
  loader: fetchPosts,
})
```

非關鍵路由配置則移至帶有 `.lazy.tsx` 後綴的檔案：

```tsx
// src/routes/posts.lazy.tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/posts')({
  component: Posts,
})

function Posts() {
  // ...
}
```

## 使用虛擬路由

你可能會遇到將路由檔案內容全部分割出去，導致檔案變空的情況！此時只需**直接刪除該路由檔案**！系統會自動生成一個虛擬路由 (virtual route) 作為程式碼分割檔案的錨點。此虛擬路由會直接存在於生成的路由樹檔案中。

**變更前（虛擬路由）**

```tsx
// src/routes/posts.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  // Hello?
})
```

```tsx
// src/routes/posts.lazy.tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/posts')({
  component: Posts,
})

function Posts() {
  // ...
}
```

**變更後（虛擬路由）**

```tsx
// src/routes/posts.lazy.tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/posts')({
  component: Posts,
})

function Posts() {
  // ...
}
```

完成！🎉

## 程式碼式分割

若使用程式碼式路由，你仍可透過 `Route.lazy()` 方法與 `createLazyRoute` 函式來分割路由。你需要將路由配置分為兩部分：

使用 `createLazyRoute` 建立懶加載路由：

```tsx
// src/posts.tsx
export const Route = createLazyRoute('/posts')({
  component: MyComponent,
})

function MyComponent() {
  return <div>My Component</div>
}
```

接著在 `app.tsx` 的路由定義中呼叫 `.lazy` 方法，以導入包含非關鍵路由配置的懶加載/程式碼分割路由。

```tsx
// src/app.tsx
const postsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/posts',
}).lazy(() => import('./posts.lazy').then((d) => d.Route))
```

## 資料載入器分割

**請注意！！！** 分割路由載入器具有風險。

雖然這是減少打包體積的強大工具，但如[TanStack Router 如何分割程式碼？](#how-does-tanstack-router-split-code)章節所述，它會帶來代價。

你可以使用路由的 `loader` 選項來分割資料載入邏輯。此過程會讓傳遞給載入器的參數類型安全難以維護，但你仍可使用泛型 `LoaderContext` 類型來獲得大部分支援：

```tsx
import { lazyFn } from '@tanstack/react-router'

const route = createRoute({
  path: '/my-route',
  component: MyComponent,
  loader: lazyFn(() => import('./loader'), 'loader'),
})

// 在另一個檔案中...
export const loader = async (context: LoaderContext) => {
  /// ...
}
```

若使用檔案式路由，僅在搭配[自動程式碼分割](#using-automatic-code-splitting)與自訂打包選項時才能分割 `loader`。

## 使用 `getRouteApi` 輔助函式手動存取其他檔案中的路由 API

如你所料，將元件程式碼放在與路由不同的檔案中，可能會使路由本身的消費變得困難。為此，TanStack Router 提供了一個方便的 `getRouteApi` 函式，讓你能在無需導入路由本身的情況下，存取路由的類型安全 API。

- `my-route.tsx`

```tsx
import { createRoute } from '@tanstack/react-router'
import { MyComponent } from './MyComponent'

const route = createRoute({
  path: '/my-route',
  loader: () => ({
    foo: 'bar',
  }),
  component: MyComponent,
})
```

- `MyComponent.tsx`

```tsx
import { getRouteApi } from '@tanstack/react-router'

const route = getRouteApi('/my-route')

export function MyComponent() {
  const loaderData = route.useLoaderData()
  //    ^? { foo: string }

  return <div>...</div>
}
```

`getRouteApi` 函式可用於存取其他類型安全 API：

- `useLoaderData`
- `useLoaderDeps`
- `useMatch`
- `useParams`
- `useRouteContext`
- `useSearch`
