---
source-updated-at: 2025-03-26T16:12:50.000Z
translation-updated-at: 2025-04-05T03:13:07.000Z
title: 快速开始
---

如果您迫不及待想跳过我们精彩的文档，以下是使用基于文件的路由生成和基于代码的路由配置快速上手 TanStack Router 的最简指南：

## 使用基于文件的路由生成

基于文件的路由生成（通过 Vite 和其他支持的打包工具）是推荐的使用方式，它能以最小工作量提供最佳体验、性能和人体工学设计。

### 初始化首个 TanStack Router 项目

```sh
npx create-tsrouter-app@latest my-app --framework solid --template file-router
```

更多选项请参阅 [create-tsrouter-app](https://github.com/TanStack/create-tsrouter-app)。

### 手动设置

您也可以按照以下步骤手动设置项目：

#### 安装 TanStack Router、Vite 插件和路由开发工具

```sh
npm install @tanstack/solid-router
npm install -D @tanstack/router-plugin @tanstack/solid-router-devtools
# 或
pnpm add @tanstack/solid-router
pnpm add -D @tanstack/router-plugin @tanstack/solid-router-devtools
# 或
yarn add @tanstack/solid-router
yarn add -D @tanstack/router-plugin @tanstack/solid-router-devtools
# 或
bun add @tanstack/solid-router
bun add -D @tanstack/router-plugin @tanstack/solid-router-devtools
# 或
deno add npm:@tanstack/solid-router npm:@tanstack/router-plugin @tanstack/solid-router-devtools
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
> 如果您不使用 Vite 或其他支持的打包工具，可查阅 [TanStack Router CLI](./routing/installation-with-router-cli.md) 指南获取更多信息。

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
  return <div class="p-2">这里是关于页面！</div>
}
```

#### `src/main.tsx`

无论您使用 `@tanstack/router-plugin` 包运行 `npm run dev`/`npm run build` 脚本，还是从包脚本手动运行 `tsr watch`/`tsr generate` 命令，路由树文件都会生成在 `src/routeTree.gen.ts`。

导入生成的路由树并创建新的路由器实例：

```tsx
import { render } from 'solid-js/web'
import { RouterProvider, createRouter } from '@tanstack/solid-router'

// 导入生成的路由树
import { routeTree } from './routeTree.gen'

// 创建新的路由器实例
const router = createRouter({ routeTree })

// 注册路由器实例以实现类型安全
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

使用此模式时，应将 `index.html` 文件中根 `<div>` 的 `id` 改为 `<div id='root'></div>`。

## 使用基于代码的路由配置

> [!IMPORTANT]
> 以下示例展示如何用代码配置路由，为简化演示所有内容放在单个文件中。虽然基于代码的生成允许您在单个文件中声明多个路由甚至路由器实例，但我们建议随着应用增长，将路由拆分到不同文件中以获得更好的组织和性能。

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
    return <div class="p-2">这里是关于页面！</div>
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

如果您跳过了这些示例或有不明白的地方，我们完全理解，因为要充分掌握 TanStack Router 还有更多内容需要学习！让我们继续深入。
