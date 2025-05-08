---
source-updated-at: '2025-04-25T07:09:37.000Z'
translation-updated-at: '2025-05-08T20:15:37.638Z'
title: 快速開始
---

如果你已經等不及想跳過我們精彩的說明文件，以下是使用 TanStack Router 的兩種基本方式：基於檔案的路由生成和基於程式碼的路由配置。

## 使用基於檔案的路由生成

基於檔案的路由生成（透過 Vite 或其他支援的打包工具）是推薦使用 TanStack Router 的方式，它能以最少的努力提供最佳的使用體驗、效能和人體工學設計。

### 建立你的第一個 TanStack Router 專案

```sh
npx create-tsrouter-app@latest my-app --template file-router
```

更多選項請參閱 [create-tsrouter-app](https://github.com/TanStack/create-tsrouter-app/tree/main/cli/create-tsrouter-app)。

### 手動設定

你也可以按照以下步驟手動設定專案：

#### 安裝 TanStack Router、Vite 插件和路由開發工具

```sh
npm install @tanstack/react-router @tanstack/react-router-devtools
npm install -D @tanstack/router-plugin
# or
pnpm add @tanstack/react-router @tanstack/react-router-devtools
pnpm add -D @tanstack/router-plugin
# or
yarn add @tanstack/react-router @tanstack/react-router-devtools
yarn add -D @tanstack/router-plugin
# or
bun add @tanstack/react-router @tanstack/react-router-devtools
bun add -D @tanstack/router-plugin
# or
deno add npm:@tanstack/react-router npm:@tanstack/router-plugin npm:@tanstack/react-router-devtools
```

#### 配置 Vite 插件

```tsx
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    // 請確保 '@tanstack/router-plugin' 在 '@vitejs/plugin-react' 之前
    TanStackRouterVite({ target: 'react', autoCodeSplitting: true }),
    react(),
    // ...,
  ],
})
```

> [!TIP]
> 如果你沒有使用 Vite 或任何支援的打包工具，可以查看 [TanStack Router CLI](./routing/installation-with-router-cli.md) 指南以獲取更多資訊。

建立以下檔案：

- `src/routes/__root.tsx`（包含兩個 '`_`' 字元）
- `src/routes/index.tsx`
- `src/routes/about.tsx`
- `src/main.tsx`

#### `src/routes/__root.tsx`

```tsx
import { createRootRoute, Link, Outlet } from '@tanstack/react-router'
import { TanStackRouterDevtools } from '@tanstack/react-router-devtools'

export const Route = createRootRoute({
  component: () => (
    <>
      <div className="p-2 flex gap-2">
        <Link to="/" className="[&.active]:font-bold">
          Home
        </Link>{' '}
        <Link to="/about" className="[&.active]:font-bold">
          About
        </Link>
      </div>
      <hr />
      <Outlet />
      <TanStackRouterDevtools />
    </>
  ),
})
```

#### `src/routes/index.tsx`

```tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/')({
  component: Index,
})

function Index() {
  return (
    <div className="p-2">
      <h3>Welcome Home!</h3>
    </div>
  )
}
```

#### `src/routes/about.tsx`

```tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/about')({
  component: About,
})

function About() {
  return <div className="p-2">Hello from About!</div>
}
```

#### `src/main.tsx`

無論你是使用 `@tanstack/router-plugin` 套件並執行 `npm run dev`/`npm run build` 腳本，還是從套件腳本手動執行 `tsr watch`/`tsr generate` 命令，路由樹檔案都會生成在 `src/routeTree.gen.ts`。

導入生成的路由樹並建立新的路由器實例：

```tsx
import { StrictMode } from 'react'
import ReactDOM from 'react-dom/client'
import { RouterProvider, createRouter } from '@tanstack/react-router'

// 導入生成的路由樹
import { routeTree } from './routeTree.gen'

// 建立新的路由器實例
const router = createRouter({ routeTree })

// 註冊路由器實例以確保型別安全
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}

// 渲染應用程式
const rootElement = document.getElementById('root')!
if (!rootElement.innerHTML) {
  const root = ReactDOM.createRoot(rootElement)
  root.render(
    <StrictMode>
      <RouterProvider router={router} />
    </StrictMode>,
  )
}
```

如果你採用這種模式，應該將 `index.html` 檔案中的根 `<div>` 的 `id` 改為 `<div id='root'></div>`。

## 使用基於程式碼的路由配置

> [!IMPORTANT]
> 以下範例展示如何使用程式碼配置路由，為了簡化演示，所有內容都放在單一檔案中。雖然基於程式碼的生成允許你在單一檔案中宣告多個路由甚至路由器實例，但我們建議隨著應用程式成長，將路由拆分到不同檔案中以獲得更好的組織和效能。

```tsx
import { StrictMode } from 'react'
import ReactDOM from 'react-dom/client'
import {
  Outlet,
  RouterProvider,
  Link,
  createRouter,
  createRoute,
  createRootRoute,
} from '@tanstack/react-router'
import { TanStackRouterDevtools } from '@tanstack/react-router-devtools'

const rootRoute = createRootRoute({
  component: () => (
    <>
      <div className="p-2 flex gap-2">
        <Link to="/" className="[&.active]:font-bold">
          Home
        </Link>{' '}
        <Link to="/about" className="[&.active]:font-bold">
          About
        </Link>
      </div>
      <hr />
      <Outlet />
      <TanStackRouterDevtools />
    </>
  ),
})

const indexRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/',
  component: function Index() {
    return (
      <div className="p-2">
        <h3>Welcome Home!</h3>
      </div>
    )
  },
})

const aboutRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/about',
  component: function About() {
    return <div className="p-2">Hello from About!</div>
  },
})

const routeTree = rootRoute.addChildren([indexRoute, aboutRoute])

const router = createRouter({ routeTree })

declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}

const rootElement = document.getElementById('app')!
if (!rootElement.innerHTML) {
  const root = ReactDOM.createRoot(rootElement)
  root.render(
    <StrictMode>
      <RouterProvider router={router} />
    </StrictMode>,
  )
}
```

如果你只是略讀這些範例或對某些部分不理解，我們不會怪你，因為要充分利用 TanStack Router 還有很多需要學習的地方！讓我們繼續吧。
