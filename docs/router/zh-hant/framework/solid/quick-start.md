---
source-updated-at: '2025-04-25T07:09:37.000Z'
translation-updated-at: '2025-05-08T20:15:39.249Z'
title: 快速開始
---

如果你感到不耐煩，想跳過我們精彩的說明文件，以下是使用基於檔案的 路由生成 (file-based route generation) 和基於程式碼的路由配置 (code-based route configuration) 來快速開始使用 TanStack Router 的最低需求：

## 使用基於檔案的路由生成

基於檔案的路由生成（透過 Vite 和其他支援的打包工具）是推薦使用 TanStack Router 的方式，因為它能以最少的努力提供最佳的使用體驗、效能和人體工學設計。

### 建立你的第一個 TanStack Router 專案

```sh
npx create-tsrouter-app@latest my-app --framework solid --template file-router
```

更多選項請參閱 [create-tsrouter-app](https://github.com/TanStack/create-tsrouter-app/tree/main/cli/create-tsrouter-app)。

### 手動設定

或者，你可以按照以下步驟手動設定專案：

#### 安裝 TanStack Router、Vite 插件和路由開發工具

```sh
npm install @tanstack/solid-router @tanstack/solid-router-devtools
npm install -D @tanstack/router-plugin
# 或
pnpm add @tanstack/solid-router @tanstack/solid-router-devtools
pnpm add -D @tanstack/router-plugin
# 或
yarn add @tanstack/solid-router @tanstack/solid-router-devtools
yarn add -D @tanstack/router-plugin
# 或
bun add @tanstack/solid-router @tanstack/solid-router-devtools
bun add -D @tanstack/router-plugin
# 或
deno add npm:@tanstack/solid-router npm:@tanstack/router-plugin npm:@tanstack/solid-router-devtools
```

#### 配置 Vite 插件

```tsx
// vite.config.ts
import { defineConfig } from 'vite'
import solid from 'vite-plugin-solid'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    TanStackRouterVite({ target: 'solid', autoCodeSplitting: true }),
    solid(),
    // ...,
  ],
})
```

> [!TIP]
> 如果你沒有使用 Vite 或任何支援的打包工具，可以查看 [TanStack Router CLI](./routing/installation-with-router-cli.md) 指南以獲取更多資訊。

建立以下檔案：

- `src/routes/__root.tsx`
- `src/routes/index.tsx`
- `src/routes/about.tsx`
- `src/main.tsx`

#### `src/routes/__root.tsx`

```tsx
import { createRootRoute, Link, Outlet } from '@tanstack/solid-router'
import { TanStackRouterDevtools } from '@tanstack/solid-router-devtools'

export const Route = createRootRoute({
  component: () => (
    <>
      <div class="p-2 flex gap-2">
        <Link to="/" class="[&.active]:font-bold">
          首頁
        </Link>{' '}
        <Link to="/about" class="[&.active]:font-bold">
          關於
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
import { createLazyFileRoute } from '@tanstack/solid-router'

export const Route = createLazyFileRoute('/')({
  component: Index,
})

function Index() {
  return (
    <div class="p-2">
      <h3>歡迎回家！</h3>
    </div>
  )
}
```

#### `src/routes/about.tsx`

```tsx
import { createLazyFileRoute } from '@tanstack/solid-router'

export const Route = createLazyFileRoute('/about')({
  component: About,
})

function About() {
  return <div class="p-2">來自「關於」頁面的問候！</div>
}
```

#### `src/main.tsx`

無論你是使用 `@tanstack/router-plugin` 套件並執行 `npm run dev`/`npm run build` 腳本，還是手動從套件腳本執行 `tsr watch`/`tsr generate` 命令，路由樹檔案都會在 `src/routeTree.gen.ts` 生成。

導入生成的路由樹並建立新的路由器實例：

```tsx
import { render } from 'solid-js/web'
import { RouterProvider, createRouter } from '@tanstack/solid-router'

// 導入生成的路由樹
import { routeTree } from './routeTree.gen'

// 建立新的路由器實例
const router = createRouter({ routeTree })

// 註冊路由器實例以確保型別安全
declare module '@tanstack/solid-router' {
  interface Register {
    router: typeof router
  }
}

// 渲染應用程式
const rootElement = document.getElementById('root')!
if (!rootElement.innerHTML) {
  render(() => <RouterProvider router={router} />, rootElement)
}
```

如果你採用這種模式，應該將 `index.html` 檔案中的根 `<div>` 的 `id` 改為 `<div id='root'></div>`。

## 使用基於程式碼的路由配置

> [!IMPORTANT]
> 以下範例展示如何使用程式碼配置路由，為了簡化演示，此範例將所有內容放在單一檔案中。雖然基於程式碼的生成允許你在單一檔案中宣告多個路由甚至路由器實例，但我們建議隨著應用程式的增長，將路由拆分到不同的檔案中以獲得更好的組織和效能。

```tsx
import { render } from 'solid-js/web'
import {
  Outlet,
  RouterProvider,
  Link,
  createRouter,
  createRoute,
  createRootRoute,
} from '@tanstack/solid-router'
import { TanStackRouterDevtools } from '@tanstack/solid-router-devtools'

const rootRoute = createRootRoute({
  component: () => (
    <>
      <div class="p-2 flex gap-2">
        <Link to="/" class="[&.active]:font-bold">
          首頁
        </Link>{' '}
        <Link to="/about" class="[&.active]:font-bold">
          關於
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
      <div class="p-2">
        <h3>歡迎回家！</h3>
      </div>
    )
  },
})

const aboutRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/about',
  component: function About() {
    return <div class="p-2">來自「關於」頁面的問候！</div>
  },
})

const routeTree = rootRoute.addChildren([indexRoute, aboutRoute])

const router = createRouter({ routeTree })

declare module '@tanstack/solid-router' {
  interface Register {
    router: typeof router
  }
}

const rootElement = document.getElementById('app')!
if (!rootElement.innerHTML) {
  render(() => <RouterProvider router={router} />, rootElement)
}
```

如果你匆匆瀏覽了這些範例或對某些內容不理解，我們不會責怪你，因為要真正發揮 TanStack Router 的優勢，還有更多需要學習的內容！讓我們繼續前進。
