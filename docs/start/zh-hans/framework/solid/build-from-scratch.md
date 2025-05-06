---
source-updated-at: '2025-04-23T01:22:58.000Z'
translation-updated-at: '2025-05-06T22:19:40.177Z'
id: build-from-scratch
title: 从零开始构建
---

> [!NOTE]
> 若您选择通过示例或克隆项目快速启动，可跳过本指南，直接进入[基础知识学习](../learn-the-basics)指南。

_想要从零开始构建一个 TanStack Start 项目？_

本指南将帮助您构建一个**极其**基础的 TanStack Start 网络应用。我们将共同使用 TanStack Start 实现：

- 提供一个首页...
- 展示计数器...
- 包含可持久化递增计数器的按钮。

[效果预览](https://stackblitz.com/github/tanstack/router/tree/main/examples/solid/start-bare)

首先创建并初始化项目目录：

```shell
mkdir myApp
cd myApp
npm init -y
```

> [!NOTE] > 示例中使用的是 `npm`，但您可以选择其他包管理工具。

## TypeScript 配置

强烈推荐在 TanStack Start 中使用 TypeScript。创建 `tsconfig.json` 文件并至少包含以下配置：

```json
{
  "compilerOptions": {
    "jsx": "preserve",
    "jsxImportSource": "solid-js",
    "moduleResolution": "Bundler",
    "module": "ESNext",
    "target": "ES2022",
    "skipLibCheck": true,
    "strictNullChecks": true
  }
}
```

> [!NOTE] > 启用 `verbatimModuleSyntax` 可能导致服务端代码泄漏至客户端，建议保持关闭状态。

## 安装依赖

TanStack Start（当前版本\*）基于 [Vinxi](https://vinxi.vercel.app/) 和 [TanStack Router](https://tanstack.com/router)，需安装这些依赖。

> [!NOTE] > \*在 1.0.0 版本发布前将移除 Vinxi，TanStack 将仅依赖 Vite 和 Nitro。使用 Vinxi 的命令和 API 可能会被 Vite 插件或专属 TanStack Start CLI 替代。

执行以下命令安装：

```shell
npm i @tanstack/solid-start @tanstack/solid-router vinxi
```

还需安装 Solid 和 Vite Solid 插件：

```shell
npm i solid-js
npm i -D vite-plugin-solid vite-tsconfig-paths
```

以及 TypeScript：

```shell
npm i -D typescript
```

## 更新配置文件

修改 `package.json` 使用 Vinxi CLI 并设置 `"type": "module"`：

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
import { defineConfig } from '@tanstack/solid-start/config'
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

该文件定义 TanStack Router 在 Start 中的行为，可配置[预加载功能](/router/latest/docs/framework/solid/guide/preloading)到[缓存过期策略](/router/latest/docs/framework/solid/guide/data-loading)等所有设置。

> [!NOTE]
> 初始状态下不会生成 `routeTree.gen.ts` 文件，首次运行 TanStack Start 时会自动创建。

```tsx
// app/router.tsx
import { createRouter as createTanStackRouter } from '@tanstack/solid-router'
import { routeTree } from './routeTree.gen'

export function createRouter() {
  const router = createTanStackRouter({
    routeTree,
    scrollRestoration: true,
  })

  return router
}

declare module '@tanstack/solid-router' {
  interface Register {
    router: ReturnType<typeof createRouter>
  }
}
```

## 服务端入口

由于 TanStack Start 是[服务端渲染 (SSR)](https://unicorn-utterances.com/posts/what-is-ssr-and-ssg)框架，需将路由信息传递至服务端入口：

```tsx
// app/ssr.tsx
import {
  createStartHandler,
  defaultStreamHandler,
} from '@tanstack/solid-start/server'
import { getRouterManifest } from '@tanstack/solid-start/router-manifest'

import { createRouter } from './router'

export default createStartHandler({
  createRouter,
  getRouterManifest,
})(defaultStreamHandler)
```

## 客户端入口

通过相同路由信息实现客户端 JavaScript 的水合：

```tsx
// app/client.tsx
/// <reference types="vinxi/types/client" />
import { hydrate } from 'solid-js/web'
import { StartClient } from '@tanstack/solid-start'
import { createRouter } from './router'

const router = createRouter()

hydrate(() => <StartClient router={router} />, document.body)
```

## 应用根组件

创建包裹所有路由的根组件：

```tsx
// app/routes/__root.tsx
import {
  Outlet,
  createRootRoute,
  HeadContent,
  Scripts,
} from '@tanstack/solid-router'

export const Route = createRootRoute({
  head: () => ({
    meta: [
      {
        charset: 'utf-8',
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
  return <Outlet />
}
```

## 创建首个路由

在 `app/routes` 目录下创建新文件：

```tsx
// app/routes/index.tsx
import * as fs from 'node:fs'
import { createFileRoute, useRouter } from '@tanstack/solid-router'
import { createServerFn } from '@tanstack/solid-start'

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
      当前值：{state()}，点击+1
    </button>
  )
}
```

大功告成！🤯 您已成功搭建 TanStack Start 项目并创建首个路由。🎉

执行 `npm run dev` 启动服务，访问 `http://localhost:3000` 即可查看效果。

需要部署应用？请参阅[托管指南](./hosting.md)。
