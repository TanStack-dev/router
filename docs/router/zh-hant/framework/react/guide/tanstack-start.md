---
source-updated-at: '2025-03-16T10:41:11.000Z'
translation-updated-at: '2025-05-08T20:18:09.167Z'
id: tanstack-start
title: TanStack Start
---

TanStack Start 是一個基於 [TanStack Router](https://tanstack.com/router) 構建的伺服器渲染 (SSR) React 應用程式全端框架 (full-stack framework)。

要設定一個 TanStack Start 專案，你需要：

1. 安裝相依套件
2. 新增設定檔
3. 建立必要的模板文件

請按照本指南來建構一個基礎的 TanStack Start 網頁應用程式。我們將一起使用 TanStack Start 來：

- 提供一個首頁...
- 顯示一個計數器...
- 包含一個可持續增加計數的按鈕

[這是完成後的樣子](https://stackblitz.com/github/tanstack/router/tree/main/examples/react/start-basic-counter)

如果是全新專案，請先建立專案目錄：

```shell
mkdir myApp
cd myApp
npm init -y
```

建立 `tsconfig.json` 檔案並至少包含以下設定：

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

# 安裝相依套件

TanStack Start 由以下套件驅動，需要安裝為相依套件：

- [@tanstack/start](https://github.com/tanstack/start)
- [@tanstack/react-router](https://tanstack.com/router)
- [Vinxi](https://vinxi.vercel.app/)

> [!NOTE]
> Vinxi 是暫時性相依套件，未來將被簡單的 Vite 插件或專用的 Start CLI 取代。

執行以下指令安裝：

```shell
npm i @tanstack/react-start @tanstack/react-router vinxi
```

同時需要安裝 React 和 Vite React 插件的相依套件：

```shell
npm i react react-dom @vitejs/plugin-react
```

為了你自己、其他開發者和使用者的著想，請使用 TypeScript：

```shell
npm i -D typescript @types/react @types/react-dom
```

# 更新設定檔案

接著我們更新 `package.json` 來使用 Vinxi 的 CLI 並設定 `"type": "module"`：

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

然後設定 TanStack Start 的 `app.config.ts` 檔案：

```typescript
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({})
```

# 新增基礎模板

TanStack Start 需要四個必要檔案：

1. 路由設定檔
2. 伺服器進入點
3. 客戶端進入點
4. 應用程式的根元件

完成設定後，檔案結構會如下所示：

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

## 路由設定檔

此檔案決定了 Start 中使用的 TanStack Router 行為。在這裡，你可以設定從預設的 [預載功能](./preloading.md) 到 [快取過期設定](./data-loading.md) 等所有內容。

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

> `routeTree.gen.ts` 目前還不是必須存在的檔案。
> 它會在首次執行 TanStack Start (透過 `npm run dev` 或 `npm run start`) 時自動生成。

## 伺服器進入點

由於 TanStack Start 是一個 [伺服器渲染 (SSR)](https://unicorn-utterances.com/posts/what-is-ssr-and-ssg) 框架，我們需要將路由資訊傳遞給伺服器進入點：

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

這讓我們能在使用者訪問特定路由時知道需要執行哪些路由和載入器 (loader)。

## 客戶端進入點

現在我們需要一種方式在使用者路由解析到客戶端後啟動客戶端 JavaScript。我們通過將相同的路由資訊傳遞給客戶端進入點來實現：

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

這讓我們能在使用者的初始伺服器請求完成後啟動客戶端路由。

## 應用程式的根元件

最後，我們需要建立應用程式的根元件。這是所有其他路由的進入點。此檔案中的程式碼會包裹應用程式中的所有其他路由。

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

# 編寫你的第一個路由

現在基礎模板已設定完成，我們可以編寫第一個路由。這需要在 `app/routes` 目錄中建立新檔案來實現。

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
      Add 1 to {state}?
    </button>
  )
}
```

完成！🤯 你現在已經設定好 TanStack Start 專案並編寫了第一個路由。🎉

現在可以執行 `npm run dev` 來啟動伺服器，並瀏覽 `http://localhost:3000` 查看你的路由運作情況。
