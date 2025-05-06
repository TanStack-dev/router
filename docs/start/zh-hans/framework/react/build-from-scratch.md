---
source-updated-at: '2025-04-23T01:22:58.000Z'
translation-updated-at: '2025-05-06T22:19:46.779Z'
id: build-from-scratch
title: 从零开始构建
---

> [!NOTE]
> 如果选择通过示例或克隆项目快速启动，可以跳过本指南，直接进入[基础知识学习](../learn-the-basics)指南。

_想要从零开始构建一个 TanStack Start 项目吗？_

本指南将帮助你构建一个**非常**基础的 TanStack Start 网络应用。我们将一起使用 TanStack Start 完成以下功能：

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
> 示例中使用的是 `npm`，但你可以选择自己喜欢的包管理器替代。

## TypeScript 配置

强烈建议在 TanStack Start 中使用 TypeScript。创建一个 `tsconfig.json` 文件，至少包含以下配置：

```json
{
  "compilerOptions": {
    "jsx": "react-jsx",
    "moduleResolution": "Bundler",
    "module": "ESNext",
    "target": "ES2022",
    "skipLibCheck": true,
    "strictNullChecks": true
  }
}
```

> [!NOTE]
> 启用 `verbatimModuleSyntax` 可能导致服务端代码泄漏到客户端包中，建议保持此选项禁用。

## 安装依赖

TanStack Start（当前版本\*）基于 [Vinxi](https://vinxi.vercel.app/) 和 [TanStack Router](https://tanstack.com/router)，需要安装这些依赖。

> [!NOTE] \*在 1.0.0 版本发布前，Vinxi 将被移除，TanStack 将仅依赖 Vite 和 Nitro。使用 Vinxi 的命令和 API 可能会被 Vite 插件或专用的 TanStack Start CLI 替代。

运行以下命令安装：

```shell
npm i @tanstack/react-start @tanstack/react-router vinxi
```

还需要安装 React 和 Vite React 插件：

```shell
npm i react react-dom
npm i -D @vitejs/plugin-react vite-tsconfig-paths
```

以及 TypeScript 相关依赖：

```shell
npm i -D typescript @types/react @types/react-dom
```

## 更新配置文件

更新 `package.json`，使用 Vinxi 的 CLI 并设置 `"type": "module"`：

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

1. 路由配置
2. 服务端入口
3. 客户端入口
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

## 路由配置

此文件定义 Start 中使用的 TanStack Router 行为，可配置从默认的[预加载功能](/router/latest/docs/framework/react/guide/preloading)到[缓存过期策略](/router/latest/docs/framework/react/guide/data-loading)等所有内容。

> [!NOTE]
> 初始时不会有 `routeTree.gen.ts` 文件，首次运行 TanStack Start 时会自动生成。

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

## 服务端入口

由于 TanStack Start 是[服务端渲染 (SSR)](https://unicorn-utterances.com/posts/what-is-ssr-and-ssg)框架，需要将路由信息传递到服务端入口：

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

## 客户端入口

现在需要一种方式在路由解析到客户端后激活客户端 JavaScript：

```tsx
// app/client.tsx
/// <reference types="vinxi/types/client" />
import { hydrateRoot } from 'react-dom/client'
import { StartClient } from '@tanstack/react-start'
import { createRouter } from './router'

const router = createRouter()

hydrateRoot(document, <StartClient router={router} />)
```

## 应用根组件

最后创建应用的根组件，它将包裹所有其他路由：

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

基础模板设置完成后，可以在 `app/routes` 目录中创建第一个路由：

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
      当前值 {state}，点击加 1？
    </button>
  )
}
```

大功告成！🤯 你已经成功搭建了 TanStack Start 项目并编写了第一个路由。🎉

现在可以运行 `npm run dev` 启动服务器，访问 `http://localhost:3000` 查看效果。

想要部署应用？请查看[托管指南](./hosting.md)。
