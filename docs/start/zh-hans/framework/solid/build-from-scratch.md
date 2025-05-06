---
source-updated-at: 2025-03-27T00:19:57.000Z
translation-updated-at: 2025-04-05T03:43:33.000Z
title: 从零开始构建
id: build-from-scratch
---

> [!NOTE]
> 若您选择通过示例项目快速启动或克隆了现有项目，可跳过本指南直接进入[基础知识学习](../learn-the-basics)指南。

_您想从零开始构建TanStack Start项目？_

本指南将帮助您构建一个**极其**基础的TanStack Start网页应用。我们将共同使用TanStack Start实现：

- 提供首页服务...
- 展示计数器...
- 包含持久化递增计数器的按钮。

[效果预览](https://stackblitz.com/github/tanstack/router/tree/main/examples/solid/start-bare)

首先创建并初始化项目目录：

```shell
mkdir myApp
cd myApp
npm init -y
```

> [!NOTE] > 示例中均使用`npm`，但您可替换为任意包管理器。

## TypeScript配置

强烈建议搭配TypeScript使用TanStack Start。创建`tsconfig.json`文件并至少包含以下配置：

```jsonc
{
  "compilerOptions": {
    "jsx": "preserve",
    "jsxImportSource": "solid-js",
    "moduleResolution": "Bundler",
    "module": "ESNext",
    "target": "ES2022",
    "skipLibCheck": true,
    "strictNullChecks": true,
  },
}
```

> [!NOTE] > 启用`verbatimModuleSyntax`可能导致服务端代码泄漏至客户端，建议保持禁用。

## 安装依赖

TanStack Start（当前\*）基于[Vinxi](https://vinxi.vercel.app/)和[TanStack Router](https://tanstack.com/router)，需安装相应依赖。

> [!NOTE] > \*在1.0.0版本发布前将移除Vinxi，TanStack将仅依赖Vite和Nitro。涉及Vinxi的命令和API可能被Vite插件或专用CLI取代。

执行安装命令：

```shell
npm i @tanstack/solid-start @tanstack/solid-router vinxi
```

还需安装Solid及其Vite插件：

```shell
npm i solid-js
npm i -D vite-plugin-solid vite-tsconfig-paths
```

以及TypeScript：

```shell
npm i -D typescript
```

## 更新配置文件

修改`package.json`使用Vinxi CLI并设置`"type": "module"`：

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

配置TanStack Start的`app.config.ts`：

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

TanStack Start需要四个核心文件：

1. 路由配置
2. 服务端入口
3. 客户端入口
4. 应用根组件

配置完成后目录结构如下：

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

此文件定义TanStack Router的行为，可配置[预加载功能](/router/latest/docs/framework/solid/guide/preloading)到[缓存过期策略](/router/latest/docs/framework/solid/guide/data-loading)等。

> [!NOTE]
> 初始时无`routeTree.gen.ts`文件，首次运行TanStack Start后自动生成。

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

作为[SSR](https://unicorn-utterances.com/posts/what-is-ssr-and-ssg)框架，需将路由信息传递至服务端入口：

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

将相同路由信息传递至客户端入口以实现水合：

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

在`app/routes`目录下创建路由文件：

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

大功告成！🤯 您已成功搭建TanStack Start项目并创建首个路由。🎉

执行`npm run dev`启动服务，访问`http://localhost:3000`查看效果。

需要部署应用？请查阅[托管指南](./hosting.md)。
