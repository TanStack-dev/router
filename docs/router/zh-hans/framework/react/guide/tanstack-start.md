---
source-updated-at: '2025-03-16T10:41:11.000Z'
translation-updated-at: '2025-05-06T18:41:12.610Z'
id: tanstack-start
title: TanStack Start
---

TanStack Start 是一个基于 [TanStack Router](https://tanstack.com/router) 构建的全栈框架，用于开发服务端渲染 (SSR) 的 React 应用。

要搭建 TanStack Start 项目，你需要：

1. 安装依赖项
2. 添加配置文件
3. 创建必要的模板文件

按照本指南构建一个基础的 TanStack Start 网页应用。我们将共同使用 TanStack Start 实现：

- 提供首页...
- 展示计数器...
- 包含持久化增加计数器的按钮。

[这是最终效果示例](https://stackblitz.com/github/tanstack/router/tree/main/examples/react/start-basic-counter)

如果是全新项目，请先创建项目目录：

```shell
mkdir myApp
cd myApp
npm init -y
```

创建 `tsconfig.json` 文件，至少包含以下配置：

```jsonc
{
  "compilerOptions": {
    "jsx": "react-jsx",
    "moduleResolution": "Bundler",
    "module": "Preserve",
    "target": "ES2022",
    "skipLibCheck": true,
  },
}
```

# 安装依赖

TanStack Start 由以下核心包驱动，需安装为依赖项：

- [@tanstack/start](https://github.com/tanstack/start)
- [@tanstack/react-router](https://tanstack.com/router)
- [Vinxi](https://vinxi.vercel.app/)

> [!注意]
> Vinxi 是临时依赖项，后续将被简单的 Vite 插件或专用 Start CLI 替代。

执行以下命令安装：

```shell
npm i @tanstack/react-start @tanstack/react-router vinxi
```

还需安装 React 和 Vite React 插件：

```shell
npm i react react-dom @vitejs/plugin-react
```

为了你、其他开发者和用户的利益，请使用 TypeScript：

```shell
npm i -D typescript @types/react @types/react-dom
```

# 更新配置文件

更新 `package.json` 使用 Vinxi CLI 并设置 `"type": "module"`：

```jsonc
{
  // ...
  "type": "module",
  "scripts": {
    "dev": "vinxi dev",
    "build": "vinxi build",
    "start": "vinxi start",
  },
}
```

然后配置 TanStack Start 的 `app.config.ts` 文件：

```typescript
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({})
```

# 添加基础模板

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

此文件定义 Start 中使用的 TanStack Router 行为，可配置从默认的[预加载功能](./preloading.md)到[缓存过期策略](./data-loading.md)等所有内容。

```tsx
// app/router.tsx
import { createRouter as createTanStackRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

export function createRouter() {
  const router = createTanStackRouter({
    routeTree,
  })

  return router
}

declare module '@tanstack/react-router' {
  interface Register {
    router: ReturnType<typeof createRouter>
  }
}
```

> `routeTree.gen.ts` 此时无需手动创建，
> 首次运行 TanStack Start（通过 `npm run dev` 或 `npm run start`）时会自动生成。

## 服务端入口

由于 TanStack Start 是 [服务端渲染 (SSR)](https://unicorn-utterances.com/posts/what-is-ssr-and-ssg) 框架，需将路由信息传递至服务端入口：

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

这使系统能在用户访问特定路由时执行对应的路由和加载器。

## 客户端入口

需要在路由解析到客户端后激活客户端 JavaScript：

```tsx
// app/client.tsx
import { hydrateRoot } from 'react-dom/client'
import { StartClient } from '@tanstack/react-start'
import { createRouter } from './router'

const router = createRouter({
  scrollRestoration: true,
})

hydrateRoot(document!, <StartClient router={router} />)
```

这实现了在用户初始服务端请求完成后启动客户端路由。

## 应用根组件

最后需要创建应用的根组件，该文件代码将包裹应用中的所有其他路由。

```tsx
// app/routes/__root.tsx
import { createRootRoute, HeadContent, Scripts } from '@tanstack/react-router'
import { Outlet } from '@tanstack/react-router'
import * as React from 'react'

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

function RootDocument({ children }: { children: React.ReactNode }) {
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

# 编写第一个路由

基础模板搭建完成后，可以在 `app/routes` 目录创建新文件来编写第一个路由。

```tsx
// app/routes/index.tsx
import * as fs from 'fs'
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
      onClick={() => {
        updateCount({ data: 1 }).then(() => {
          router.invalidate()
        })
      }}
    >
      当前值 {state}，点击加 1
    </button>
  )
}
```

大功告成！🤯 你已成功搭建 TanStack Start 项目并编写了第一个路由。🎉

现在可以运行 `npm run dev` 启动服务，访问 `http://localhost:3000` 查看运行效果。
