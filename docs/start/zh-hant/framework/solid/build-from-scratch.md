---
source-updated-at: '2025-04-23T01:22:58.000Z'
translation-updated-at: '2025-05-08T20:26:09.286Z'
id: build-from-scratch
title: 從零開始建構
---

> [!NOTE]
> 如果您選擇透過範例或複製專案快速開始，可以跳過本指南，直接前往[學習基礎](../learn-the-basics)指南。

_所以您想從頭開始建立一個 TanStack Start 專案？_

本指南將幫助您建立一個**非常**基礎的 TanStack Start 網頁應用程式。我們將一起使用 TanStack Start 來：

- 提供一個首頁...
- 顯示一個計數器...
- 並有一個按鈕可以持續增加計數器。

[這是完成後的效果](https://stackblitz.com/github/tanstack/router/tree/main/examples/solid/start-bare)

讓我們建立一個新的專案目錄並初始化它。

```shell
mkdir myApp
cd myApp
npm init -y
```

> [!NOTE] > 我們在這些範例中使用 `npm`，但您可以改用您喜歡的套件管理工具。

## TypeScript 設定

我們強烈建議在 TanStack Start 中使用 TypeScript。建立一個 `tsconfig.json` 檔案，至少包含以下設定：

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

> [!NOTE] > 啟用 `verbatimModuleSyntax` 可能會導致伺服器套件洩漏到客戶端套件中。建議保持此選項關閉。

## 安裝依賴套件

TanStack Start（目前\*）由 [Vinxi](https://vinxi.vercel.app/) 和 [TanStack Router](https://tanstack.com/router) 提供支援，並需要它們作為依賴套件。

> [!NOTE] > \*Vinxi 將在版本 1.0.0 發布前移除，TanStack 將僅依賴 Vite 和 Nitro。使用 Vinxi 的命令和 API 可能會被 Vite 插件或專用的 TanStack Start CLI 取代。

執行以下命令安裝它們：

```shell
npm i @tanstack/solid-start @tanstack/solid-router vinxi
```

您還需要 Solid 和 Vite Solid 插件，所以也安裝它們：

```shell
npm i solid-js
npm i -D vite-plugin-solid vite-tsconfig-paths
```

以及一些 TypeScript：

```shell
npm i -D typescript
```

## 更新設定檔案

我們將更新 `package.json` 以使用 Vinxi 的 CLI 並設定 `"type": "module"`：

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

然後設定 TanStack Start 的 `app.config.ts` 檔案：

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

## 新增基本模板

TanStack Start 的使用需要四個必要的檔案：

1. 路由設定
2. 伺服器入口點
3. 客戶端入口點
4. 應用程式的根目錄

設定完成後，我們將得到如下的檔案結構：

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

## 路由設定

這個檔案將決定 Start 中使用的 TanStack Router 的行為。在這裡，您可以設定從預設的[預載功能](/router/latest/docs/framework/solid/guide/preloading)到[快取過期](/router/latest/docs/framework/solid/guide/data-loading)的所有內容。

> [!NOTE]
> 您還不會有 `routeTree.gen.ts` 檔案。這個檔案將在您第一次運行 TanStack Start 時生成。

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

## 伺服器入口點

由於 TanStack Start 是一個[伺服器渲染 (SSR)](https://unicorn-utterances.com/posts/what-is-ssr-and-ssg) 框架，我們需要將這些路由資訊傳遞到我們的伺服器入口點：

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

這讓我們能夠知道當用戶訪問特定路由時需要執行哪些路由和載入器。

## 客戶端入口點

現在我們需要一種方法，一旦路由解析到客戶端，就水合我們的客戶端 JavaScript。我們通過將相同的路由資訊傳遞到客戶端入口點來實現這一點：

```tsx
// app/client.tsx
/// <reference types="vinxi/types/client" />
import { hydrate } from 'solid-js/web'
import { StartClient } from '@tanstack/solid-start'
import { createRouter } from './router'

const router = createRouter()

hydrate(() => <StartClient router={router} />, document.body)
```

這使我們能夠在用戶的初始伺服器請求完成後啟動客戶端路由。

## 應用程式的根目錄

最後，我們需要建立應用程式的根目錄。這是所有其他路由的入口點。此檔案中的程式碼將包裹應用程式中的所有其他路由。

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

## 編寫您的第一個路由

現在我們已經完成了基本的模板設定，可以編寫我們的第一個路由了。這需要在 `app/routes` 目錄中建立一個新檔案。

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
      Add 1 to {state()}?
    </button>
  )
}
```

就是這樣！🤯 您現在已經設定了一個 TanStack Start 專案並編寫了您的第一個路由。🎉

您現在可以運行 `npm run dev` 來啟動您的伺服器，並導航到 `http://localhost:3000` 查看您的路由實際運作。

想要部署您的應用程式嗎？查看[託管指南](./hosting.md)。
