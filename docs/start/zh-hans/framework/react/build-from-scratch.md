---
source-updated-at: 2025-03-26T16:13:53.000Z
translation-updated-at: 2025-04-07T03:52:09.000Z
title: 从零开始构建
id: build-from-scratch
---

> [!NOTE]
> 如果你选择通过示例项目或克隆项目快速开始，可以跳过本指南，直接进入[基础知识学习](../learn-the-basics)指南。

_你想从零开始构建一个 TanStack Start 项目吗？_

本指南将帮助你构建一个**非常**基础的 TanStack Start 网络应用。我们将共同使用 TanStack Start 实现以下功能：

- 提供一个首页...
- 显示一个计数器...
- 包含一个按钮，用于持久化递增计数器。

[这是最终效果示例](https://stackblitz.com/github/tanstack/router/tree/main/examples/react/start-counter)

首先创建一个新项目目录并初始化：

```shell
mkdir myApp
cd myApp
npm init -y
```

> [!NOTE]
> 这些示例中我们使用 `npm`，但你可以选择自己喜欢的包管理器替代。

## TypeScript 配置

我们强烈推荐在 TanStack Start 中使用 TypeScript。创建一个 `tsconfig.json` 文件，至少包含以下配置：

```jsonc
{
  "compilerOptions": {
    "jsx": "react-jsx",
    "moduleResolution": "Bundler",
    "module": "ESNext",
    "target": "ES2022",
    "skipLibCheck": true,
    "strictNullChecks": true,
  },
}
```

> [!NOTE]
> 启用 `verbatimModuleSyntax` 可能导致服务端打包内容泄漏到客户端打包中，建议保持此选项禁用。

## 安装依赖项

TanStack Start（当前版本\*）基于 [Vinxi](https://vinxi.vercel.app/) 和 [TanStack Router](https://tanstack.com/router)，需要安装这些依赖项。

> [!NOTE] \*在 1.0.0 版本发布前，Vinxi 将被移除，TanStack 将仅依赖 Vite 和 Nitro。使用 Vinxi 的命令和 API 可能会被 Vite 插件或专用的 TanStack Start 命令行工具替代。

运行以下命令安装：

```shell
npm i @tanstack/react-start @tanstack/react-router vinxi
```

同时需要安装 React 和 Vite React 插件：

```shell
npm i react react-dom
npm i -D @vitejs/plugin-react vite-tsconfig-paths
```

以及 TypeScript 相关依赖：

```shell
npm i -D typescript @types/react @types/react-dom
```

## 更新配置文件

更新 `package.json` 以使用 Vinxi 的命令行工具并设置 `"type": "module"`：

```json
{
  // ...
  "type": "module",
  "scripts": {
    "dev": "vinxi dev",
    "build": "vinxi build",
    "start": "vinxi start"
  }
}
```

然后配置 TanStack Start 的 `app.config.ts` 文件：

```typescript
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'
import tsConfigPaths from 'vite-tsconfig-paths'

export default defineConfig({
  vite: {
    plugins: [
      tsConfigPaths({
        projects: ['./tsconfig.json'],
      }),
    ],
  },
})
```

## 添加基础模板

TanStack Start 需要四个核心文件：

1. 路由器配置
2. 服务端入口文件
3. 客户端入口文件
4. 应用根组件

配置完成后，文件结构如下：

```
.
├── app/
│   ├── routes/
│   │   └── `__root.tsx`
│   ├── `client.tsx`
│   ├── `router.tsx`
│   ├── `routeTree.gen.ts`
│   └── `ssr.tsx`
├── `.gitignore`
├── `app.config.ts`
├── `package.json`
└── `tsconfig.json`
```

## 路由器配置

此文件用于配置 TanStack Router 在 Start 中的行为。你可以在此配置从默认的[预加载功能](/router/latest/docs/framework/react/guide/preloading)到[缓存过期策略](/router/latest/docs/framework/react/guide/data-loading)等所有内容。

> [!NOTE]
> 初始时不会有 `routeTree.gen.ts` 文件，该文件将在首次运行 TanStack Start 时生成。

```tsx
// app/router.tsx
import { createRouter as createTanStackRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

export function createRouter() {
  const router = createTanStackRouter({
    routeTree,
    scrollRestoration: true,
  })

  return router
}

declare module '@tanstack/react-router' {
  interface Register {
    router: ReturnType<typeof createRouter>
  }
}
```

## 服务端入口文件

由于 TanStack Start 是[服务端渲染 (SSR)](https://unicorn-utterances.com/posts/what-is-ssr-and-ssg) 框架，我们需要将路由器信息传递到服务端入口：

```tsx
// app/ssr.tsx
import {
  createStartHandler,
  defaultStreamHandler,
} from '@tanstack/react-start/server'
import { getRouterManifest } from '@tanstack/react-start/router-manifest'

import { createRouter } from './router'

export default createStartHandler({
  createRouter,
  getRouterManifest,
})(defaultStreamHandler)
```

这让我们能在用户访问特定路由时知道需要执行哪些路由和加载器。

## 客户端入口文件

现在我们需要在路由解析到客户端后，注入客户端 JavaScript 进行水合 (Hydration)：

```tsx
// app/client.tsx
/// <reference types="vinxi/types/client" />
import { hydrateRoot } from 'react-dom/client'
import { StartClient } from '@tanstack/react-start'
import { createRouter } from './router'

const router = createRouter()

hydrateRoot(document, <StartClient router={router} />)
```

这使得用户初始服务端请求完成后能启动客户端路由。

## 应用根组件

最后创建应用的根组件，这是所有其他路由的入口点：

```tsx
// app/routes/__root.tsx
import type { ReactNode } from 'react'
import {
  Outlet,
  createRootRoute,
  HeadContent,
  Scripts,
} from '@tanstack/react-router'

export const Route = createRootRoute({
  head: () => ({
    meta: [
      {
        charSet: 'utf-8',
      },
      {
        name: 'viewport',
        content: 'width=device-width, initial-scale=1',
      },
      {
        title: 'TanStack Start Starter',
      },
    ],
  }),
  component: RootComponent,
})

function RootComponent() {
  return (
    <RootDocument>
      <Outlet />
    </RootDocument>
  )
}

function RootDocument({ children }: Readonly<{ children: ReactNode }>) {
  return (
    <html>
      <head>
        <HeadContent />
      </head>
      <body>
        {children}
        <Scripts />
      </body>
    </html>
  )
}
```

## 编写第一个路由

基础模板搭建完成后，可以在 `app/routes` 目录下创建第一个路由文件：

```tsx
// app/routes/index.tsx
import * as fs from 'node:fs'
import { createFileRoute, useRouter } from '@tanstack/react-router'
import { createServerFn } from '@tanstack/react-start'

const filePath = 'count.txt'

async function readCount() {
  return parseInt(
    await fs.promises.readFile(filePath, 'utf-8').catch(() => '0'),
  )
}

const getCount = createServerFn({
  method: 'GET',
}).handler(() => {
  return readCount()
})

const updateCount = createServerFn({ method: 'POST' })
  .validator((d: number) => d)
  .handler(async ({ data }) => {
    const count = await readCount()
    await fs.promises.writeFile(filePath, `${count + data}`)
  })

export const Route = createFileRoute('/')({
  component: Home,
  loader: async () => await getCount(),
})

function Home() {
  const router = useRouter()
  const state = Route.useLoaderData()

  return (
    <button
      type="button"
      onClick={() => {
        updateCount({ data: 1 }).then(() => {
          router.invalidate()
        })
      }}
    >
      当前值：{state}，点击加1
    </button>
  )
}
```

大功告成！🤯 你现在已经成功设置了一个 TanStack Start 项目并编写了第一个路由。🎉

运行 `npm run dev` 启动服务器，访问 `http://localhost:3000` 查看效果。

想部署应用？查看[托管指南](./hosting.md)。
