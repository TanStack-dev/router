---
source-updated-at: 2025-03-26T16:12:50.000Z
translation-updated-at: 2025-04-05T04:23:46.293Z
title: 快速开始
---

如果你感到不耐烦，想跳过我们精彩的文档，这里提供了使用基于文件的路由生成和基于代码的路由配置来快速上手 TanStack Router 的最简指南：

## 使用基于文件的路由生成

基于文件的路由生成（通过 Vite 及其他支持的打包工具）是推荐的使用方式，因为它能以最少的努力提供最佳体验、性能和易用性。

### 初始化你的第一个 TanStack Router 项目

```sh
npx create-tsrouter-app@latest my-app --template file-router
```

更多选项请查看 [create-tsrouter-app](https://github.com/TanStack/create-tsrouter-app)。

### 手动设置

你也可以按照以下步骤手动设置项目：

#### 安装 TanStack Router、Vite 插件及路由开发工具

```sh
npm install @tanstack/react-router
npm install -D @tanstack/router-plugin @tanstack/react-router-devtools
# 或
pnpm add @tanstack/react-router
pnpm add -D @tanstack/router-plugin @tanstack/react-router-devtools
# 或
yarn add @tanstack/react-router
yarn add -D @tanstack/router-plugin @tanstack/react-router-devtools
# 或
bun add @tanstack/react-router
bun add -D @tanstack/router-plugin @tanstack/react-router-devtools
# 或
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
    TanStackRouterVite({ target: 'react', autoCodeSplitting: true }),
    react(),
    // ...,
  ],
})
```

> [!TIP]
> 如果你不使用 Vite 或任何支持的打包工具，可以查看 [TanStack Router CLI](./routing/installation-with-router-cli.md) 指南获取更多信息。

创建以下文件：

- `src/routes/__root.tsx`（注意有两个 '`_`' 字符）
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
          首页
        </Link>{' '}
        <Link to="/about" className="[&.active]:font-bold">
          关于
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
      <h3>欢迎回家！</h3>
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
  return <div className="p-2">你好，这是关于页面！</div>
}
```

#### `src/main.tsx`

无论你是使用 `@tanstack/router-plugin` 包并运行 `npm run dev`/`npm run build` 脚本，还是手动从包脚本中运行 `tsr watch`/`tsr generate` 命令，路由树文件都会在 `src/routeTree.gen.ts` 生成。

导入生成的路由树并创建新的路由器实例：

```tsx
import { StrictMode } from 'react'
import ReactDOM from 'react-dom/client'
import { RouterProvider, createRouter } from '@tanstack/react-router'

// 导入生成的路由树
import { routeTree } from './routeTree.gen'

// 创建新的路由器实例
const router = createRouter({ routeTree })

// 注册路由器实例以确保类型安全
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}

// 渲染应用
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

如果采用此模式，你应将 `index.html` 文件中的根 `<div>` 的 `id` 改为 `<div id='root'></div>`。

## 使用基于代码的路由配置

> [!IMPORTANT]
> 以下示例展示了如何使用代码配置路由，为简化演示，所有内容放在单个文件中。虽然基于代码的生成允许你在单个文件中声明多个路由甚至路由器实例，但我们建议随着应用规模增长，将路由拆分为单独的文件以获得更好的组织和性能。

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
          首页
        </Link>{' '}
        <Link to="/about" className="[&.active]:font-bold">
          关于
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
        <h3>欢迎回家！</h3>
      </div>
    )
  },
})

const aboutRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/about',
  component: function About() {
    return <div className="p-2">你好，这是关于页面！</div>
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

如果你匆匆浏览了这些示例或有不明白的地方，我们不会责怪你，因为要真正充分利用 TanStack Router，还有很多需要学习的内容！让我们继续吧。
