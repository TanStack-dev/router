---
source-updated-at: 2025-02-27T00:19:00.000Z
translation-updated-at: 2025-04-05T03:22:11.000Z
title: 代码分割
---

代码分割与懒加载是一种强大的技术，可显著改善应用的包体积和加载性能。

- 减少初始页面加载时需要下载的代码量
- 按需加载所需代码
- 生成体积更小的代码块，浏览器可更轻松缓存

## TanStack Router 如何实现代码分割？

TanStack Router 将代码分为两类：

- **关键路由配置** - 渲染当前路由和尽早启动数据加载流程所必需的代码。

  - 路径解析/序列化
  - 搜索参数验证
  - 加载器、前置加载
  - 路由上下文
  - 静态数据
  - 链接
  - 脚本
  - 样式
  - 其他未列出的路由配置

- **非关键/懒加载路由配置** - 路由匹配非必需，可按需加载的代码。
  - 路由组件
  - 错误组件
  - 加载中组件
  - 未找到组件

> 🧠 **为何不分割加载器？**
>
> - 加载器本身已是异步边界，若分割则需额外等待代码块加载和加载器执行
> - 相比组件，加载器导致包体积过大的概率更低
> - 加载器是路由最重要的可预加载资源之一（特别是在使用默认预加载行为时，如悬停链接），因此需要确保其可用性而不增加异步开销
>
>   若了解分割加载器的弊端后仍希望实施，请参阅[数据加载器分割](#data-loader-splitting)章节。

## 将路由文件封装至目录

由于 TanStack Router 基于文件的路由系统支持扁平与嵌套结构，无需额外配置即可将路由文件封装至独立目录。

操作步骤：将路由文件移至同名目录内，并重命名为 `route.tsx`。

**改造前**

- `posts.tsx`

**改造后**

- `posts`
  - `route.tsx`

## 代码分割方案

TanStack Router 支持多种代码分割方式。若使用基于代码的路由，请跳转至[基于代码的分割](#code-based-splitting)章节。

使用基于文件的路由时，可选以下方案：

- [✨ 自动代码分割](#using-automatic-code-splitting)
- [使用 `.lazy.tsx` 后缀](#using-the-lazytsx-suffix)
- [使用虚拟路由](#using-virtual-routes)

## ✨ 使用自动代码分割

这是最便捷高效的代码分割方式。

启用 `autoCodeSplitting` 功能后，TanStack Router 会自动根据前述非关键路由配置分割代码。

> [!重要]
> 自动代码分割**仅限**基于文件的路由，且需配合[支持的打包器](../routing/file-based-routing.md#getting-started-with-file-based-routing)使用。
> **不兼容**纯 CLI 模式 (`@tanstack/router-cli`)。

启用方式（在打包器插件配置中添加）：

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
    react(), // 确保该插件位于路由插件之后
  ],
})
```

完成！路由文件将自动按关键/非关键配置分割。更多控制选项详见[自动代码分割指南](./automatic-code-splitting.md)。

## 使用 `.lazy.tsx` 后缀

若无法使用自动分割，可通过 `.lazy.tsx` 后缀手动分割：**将代码移至 `.lazy.tsx` 文件**，并使用 `createLazyFileRoute` 替代 `createFileRoute`。

> [!重要] > `__root.tsx` 根路由文件（使用 `createRootRoute` 或 `createRootRouteWithContext`）**不支持**代码分割。

`createLazyFileRoute` 仅支持以下选项：

| 导出名称            | 说明                   |
| ------------------- | ---------------------- |
| `component`         | 路由渲染组件           |
| `errorComponent`    | 加载错误时渲染的组件   |
| `pendingComponent`  | 加载过程中渲染的组件   |
| `notFoundComponent` | 未找到路由时渲染的组件 |

### `.lazy.tsx` 代码分割示例

**改造前（单文件）**

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

**改造后（拆分为两个文件）**

关键路由配置保留在原文件：

```tsx
// src/routes/posts.tsx
import { createFileRoute } from '@tanstack/react-router'
import { fetchPosts } from './api'

export const Route = createFileRoute('/posts')({
  loader: fetchPosts,
})
```

非关键配置移至 `.lazy.tsx` 文件：

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

## 使用虚拟路由

若分割后原路由文件为空，可直接**删除该文件**！系统将自动生成虚拟路由作为代码分割文件的锚点，该虚拟路由会直接存在于生成的路由树文件中。

**改造前（虚拟路由）**

```tsx
// src/routes/posts.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  // 空文件
})
```

**改造后（虚拟路由）**

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

## 基于代码的分割

使用代码路由时，可通过 `Route.lazy()` 方法和 `createLazyRoute` 函数实现分割：

创建懒加载路由：

```tsx
// src/posts.tsx
export const Route = createLazyRoute('/posts')({
  component: MyComponent,
})

function MyComponent() {
  return <div>My Component</div>
}
```

在 `app.tsx` 中通过 `.lazy` 方法导入：

```tsx
// src/app.tsx
const postsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/posts',
}).lazy(() => import('./posts.lazy').then((d) => d.Route))
```

## 数据加载器分割

**警告！** 分割加载器存在风险。

虽能减小包体积，但代价如[代码分割原理](#how-does-tanstack-router-split-code)章节所述。可通过 `lazyFn` 实现分割，但会弱化类型安全：

```tsx
import { lazyFn } from '@tanstack/react-router'

const route = createRoute({
  path: '/my-route',
  component: MyComponent,
  loader: lazyFn(() => import('./loader'), 'loader'),
})

// 另一文件中
export const loader = async (context: LoaderContext) => {
  // ...
}
```

基于文件的路由需配合[自动代码分割](#using-automatic-code-splitting)的定制打包选项才能分割加载器。

## 通过 `getRouteApi` 跨文件访问路由 API

组件与路由分离时，可使用 `getRouteApi` 安全访问路由 API：

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

`getRouteApi` 支持的类型安全 API：

- `useLoaderData`
- `useLoaderDeps`
- `useMatch`
- `useParams`
- `useRouteContext`
- `useSearch`
