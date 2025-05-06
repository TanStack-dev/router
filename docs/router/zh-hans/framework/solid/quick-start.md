---
source-updated-at: '2025-04-14T23:38:51.000Z'
translation-updated-at: '2025-05-06T22:02:04.294Z'
title: 快速开始
---

如果你迫不及待想跳过我们精彩的文档，以下是使用基于文件的路由生成和基于代码的路由配置快速上手 TanStack Router 的最简指南：

## 使用基于文件的路由生成

基于文件的路由生成（通过 Vite 或其他支持的打包工具）是推荐的使用方式，它能以最少的努力提供最佳体验、性能和人体工学设计。

### 初始化你的第一个 TanStack Router 项目

```sh
npx create-tsrouter-app@latest my-app --framework solid --template file-router
```

更多选项请查看 [create-tsrouter-app](https://github.com/TanStack/create-tsrouter-app)。

### 手动设置

你也可以通过以下步骤手动配置项目：

#### 安装 TanStack Router、Vite 插件和路由开发工具

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
> 如果你不使用 Vite 或任何支持的打包工具，可以查看 [TanStack Router CLI](./routing/installation-with-router-cli.md) 指南获取更多信息。

创建以下文件：

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
          首页
        </Link>{' '}
        <Link to="/about" class="[&.active]:font-bold">
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
import { createLazyFileRoute } from '@tanstack/solid-router'

export const Route = createLazyFileRoute('/')({
  component: Index,
})

function Index() {
  return (
    <div class="p-2">
      <h3>欢迎回家！</h3>
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
  return <div class="p-2">你好，这里是关于页面！</div>
}
```

#### `src/main.tsx`

无论你是使用 `@tanstack/router-plugin` 包并运行 `npm run dev`/`npm run build` 脚本，还是从包脚本中手动运行 `tsr watch`/`tsr generate` 命令，路由树文件都会在 `src/routeTree.gen.ts` 生成。

导入生成的路由树并创建新的路由实例：

```tsx
import { render } from 'solid-js/web'
import { RouterProvider, createRouter } from '@tanstack/solid-router'

// 导入生成的路由树
import { routeTree } from './routeTree.gen'

// 创建新的路由实例
const router = createRouter({ routeTree })

// 注册路由实例以确保类型安全
declare module '@tanstack/solid-router' {
  interface Register {
    router: typeof router
  }
}

// 渲染应用
const rootElement = document.getElementById('root')!
if (!rootElement.innerHTML) {
  render(() => <RouterProvider router={router} />, rootElement)
}
```

如果采用这种模式，你应该将 `index.html` 文件中的根 `<div>` 的 `id` 改为 `<div id='root'></div>`。

## 使用基于代码的路由配置

> [!IMPORTANT]
> 以下示例展示了如何通过代码配置路由，为简化演示，所有代码都在单个文件中。虽然基于代码的生成允许你在单个文件中声明多个路由甚至路由实例，但我们建议随着应用规模增长，将路由拆分到不同文件中以获得更好的组织和性能。

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
          首页
        </Link>{' '}
        <Link to="/about" class="[&.active]:font-bold">
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
      <div class="p-2">
        <h3>欢迎回家！</h3>
      </div>
    )
  },
})

const aboutRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/about',
  component: function About() {
    return <div class="p-2">你好，这里是关于页面！</div>
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

如果你略过了这些示例或对某些内容不理解，我们完全理解，因为要真正发挥 TanStack Router 的优势还有很多需要学习！让我们继续深入。
