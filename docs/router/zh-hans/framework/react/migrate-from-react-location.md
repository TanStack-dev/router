---
source-updated-at: 2025-02-27T22:43:21.000Z
translation-updated-at: 2025-04-05T03:09:45.000Z
title: 从 React Location 迁移
---

在开始从 React Location 迁移之前，理解 TanStack Router 采用的[路由概念](./routing/routing-concepts.md)和[设计决策](./decisions-on-dx.md)非常重要。

## React Location 与 TanStack Router 的区别

React Location 和 TanStack Router 在设计理念上有许多共同点，但也存在一些关键差异需要注意：

- React Location 使用**泛型**来推断路由类型，而 TanStack Router 采用**模块声明合并**进行类型推断。
- React Location 通过单一的路由定义数组配置路由，而 TanStack Router 使用以[根路由](./routing/routing-concepts.md#the-root-route)为起点的树形结构定义路由。
- [基于文件的路由](./routing/file-based-routing.md)是 TanStack Router 推荐的定义方式，而 React Location 仅支持通过代码方式在单一文件中定义路由。
  - TanStack Router 也支持[代码方式](./routing/code-based-routing.md)定义路由，但不推荐大多数场景使用。详细原因可阅读：[为何推荐基于文件的路由？](./decisions-on-dx.md#3-why-is-file-based-routing-the-preferred-way-to-define-routes)

## 迁移指南

本指南将以[React Location 基础示例](https://github.com/TanStack/router/tree/react-location/examples/basic)为例，演示如何通过基于文件的路由迁移至 TanStack Router，最终实现与原示例相同的功能（样式等非路由相关代码将省略）。

> [!TIP]
> 若需使用代码方式定义路由，请参阅[代码路由指南](./routing/code-based-routing.md)。

### 步骤1：更换依赖包

首先安装 TanStack Router 的依赖：

```sh
npm install @tanstack/react-router @tanstack/router-devtools
```

同时移除 React Location 的依赖：

```sh
npm uninstall @tanstack/react-location @tanstack/react-location-devtools
```

### 步骤2：配置基于文件的路由监听

如果项目使用 Vite（或其他支持的打包工具），可通过 TanStack Router 插件自动监听路由文件变化并更新配置。

安装 Vite 插件：

```sh
npm install -D @tanstack/router-plugin
```

在 `vite.config.js` 中添加配置：

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

export default defineConfig({
  // ...
  plugins: [TanStackRouterVite(), react()],
})
```

若未使用 Vite，可选用其他[支持的打包工具](./routing/file-based-routing.md#getting-started-with-file-based-routing)，或通过 `@tanstack/router-cli` 包实现路由文件监听。

### 步骤3：添加配置文件

在项目根目录创建 `tsr.config.json` 文件：

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts"
}
```

完整配置选项详见[文档](./routing/file-based-routing.md#options)。

### 步骤4：创建路由目录

在 `src` 目录下创建路由文件夹：

```sh
mkdir src/routes
```

### 步骤5：创建根路由文件

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

### 步骤6：创建首页路由文件

```tsx
// src/routes/index.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/')({
  component: Index,
})
```

> 需将原 `src/index.tsx` 中与首页相关的组件和逻辑迁移至此文件。

### 步骤7：创建文章列表路由文件

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

> 需迁移原 `src/index.tsx` 中与文章列表相关的组件和逻辑。

### 步骤8：创建文章列表索引路由文件

```tsx
// src/routes/posts.index.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/')({
  component: PostsIndex,
})
```

> 需迁移原 `src/index.tsx` 中与文章列表索引相关的组件和逻辑。

### 步骤9：创建文章详情路由文件

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

> 需迁移原 `src/index.tsx` 中与文章详情相关的组件和逻辑。

### 步骤10：生成路由树

使用支持的打包工具时，路由树会在开发脚本运行时自动生成。

若未使用支持的工具，可运行以下命令生成：

```sh
npx tsr generate
```

### 步骤11：更新入口文件渲染路由

生成路由树后，更新 `src/index.tsx` 创建路由实例并渲染：

```tsx
// src/index.tsx
import React from 'react'
import ReactDOM from 'react-dom'
import { createRouter, RouterProvider } from '@tanstack/react-router'

// 导入生成的路由树
import { routeTree } from './routeTree.gen'

// 创建路由实例
const router = createRouter({ routeTree })

// 注册路由实例以获得类型安全
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}

const domElementId = 'root' // 假设存在 id 为 'root' 的根元素

// 渲染应用
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

至此已成功通过基于文件的路由将应用从 React Location 迁移至 TanStack Router。

若原应用使用了 React Location 的其他功能，可参考以下迁移指南：

- [搜索参数](./guide/search-params.md)
- [数据加载](./guide/data-loading.md)
- [历史记录类型](./guide/history-types.md)
- [通配路由](./routing/routing-concepts.md#splat--catch-all-routes)
- [认证路由](./guide/authenticated-routes.md)

TanStack Router 还提供更多功能可供探索：

- [路由上下文](./guide/router-context.md)
- [预加载](./guide/preloading.md)
- [无路径布局路由](./routing/routing-concepts.md#pathless-layout-routes)
- [路由掩码](./guide/route-masking.md)
- [服务端渲染](./guide/ssr.md)
- 以及其他功能！

如遇问题或有疑问，欢迎在 TanStack Discord 社区寻求帮助。
