---
source-updated-at: '2025-04-17T08:13:24.000Z'
translation-updated-at: '2025-05-08T20:27:49.854Z'
id: learn-the-basics
title: 學習基礎知識
---

本指南將幫助您學習 TanStack Start 的基本運作原理，無論您如何設定專案。

## 依賴套件

TanStack Start（目前\*）由 [Vinxi](https://vinxi.vercel.app/)、[Nitro](https://nitro.unjs.io/) 和 [TanStack Router](https://tanstack.com/router) 提供技術支援。

- **TanStack Router**：用於構建網頁應用程式的路由系統。
- **Nitro**：用於構建伺服器應用程式的框架。
- **Vinxi**：用於構建網頁應用程式的伺服器框架。

> [!注意] Vinxi 將在 1.0.0 版本發布前移除，TanStack 將僅依賴 Vite 和 Nitro。使用 Vinxi 的指令和 API 可能會被 Vite 插件取代。

## 一切從「路由」開始

這個檔案決定了 TanStack Start 中使用的 TanStack Router 行為。您可以在這裡配置所有內容，從預設的 [預載功能](/router/latest/docs/framework/react/guide/preloading) 到 [快取過期設定](/router/latest/docs/framework/react/guide/data-loading)。

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

- 請注意 `scrollRestoration` 屬性，這用於在路由切換時恢復頁面滾動位置。

## 路由生成

`routeTree.gen.ts` 檔案會在首次執行 TanStack Start（透過 `npm run dev` 或 `npm run start`）時生成。此檔案包含生成的路由樹和一些 TS 工具，使 TanStack Start 完全具備型別安全。

## 伺服器入口點

儘管 TanStack Start 設計為客戶端優先的 API，但它本質上是一個全端框架。這意味著所有使用案例（包括動態和靜態）都依賴伺服器或建置時入口來渲染應用程式的初始 HTML 內容。

這是透過 `app/ssr.tsx` 檔案完成的：

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

無論我們是靜態生成應用程式還是動態提供服務，`ssr.tsx` 檔案都是執行所有 SSR 相關工作的入口點。

- 重要的是，每個請求都需創建一個新的路由實例，這確保路由處理的任何資料對該請求都是唯一的。
- `getRouterManifest` 函數用於生成路由清單，用於決定資產管理和應用程式預載的許多方面。
- `defaultStreamHandler` 函數用於將應用程式渲染為串流，讓我們能夠利用串流 HTML 到客戶端。（這是預設處理程序，但您也可以使用其他處理程序如 `defaultRenderHandler`，甚至自建自己的處理程序）

## 客戶端入口點

將 HTML 傳送到客戶端只是成功的一半。到達後，我們需要在路由解析到客戶端時水合客戶端 JavaScript。我們透過使用 `StartClient` 元件水合應用程式的根來實現這一點：

```tsx
// app/client.tsx
import { hydrateRoot } from 'react-dom/client'
import { StartClient } from '@tanstack/react-start'
import { createRouter } from './router'

const router = createRouter()

hydrateRoot(document, <StartClient router={router} />)
```

這使我們能夠在使用者的初始伺服器請求完成後啟動客戶端路由。

## 應用程式的根目錄

除了客戶端入口點外，應用程式的 `__root` 路由是應用程式的入口點。此檔案中的程式碼將包裹應用程式中的所有其他路由，包括首頁。它的行為類似於整個應用程式的無路徑佈局路由。

由於它 **總是會被渲染**，因此是構建應用程式外殼和處理任何全域邏輯的理想位置。

```tsx
// app/routes/__root.tsx
import {
  Outlet,
  createRootRoute,
  HeadContent,
  Scripts,
} from '@tanstack/react-router'
import type { ReactNode } from 'react'

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

- 此佈局未來可能會變更，因為我們將推出 SPA 模式，允許根路由在不包含任何頁面特定內容的情況下渲染 SPA 外殼。
- 請注意 `Scripts` 元件，這用於載入應用程式的所有客戶端 JavaScript。

## 路由

路由是 TanStack Router 的廣泛功能，並在 [路由指南](/router/latest/docs/framework/react/routing/file-based-routing) 中有詳細說明。簡要概述：

- 路由使用 `createFileRoute` 函數定義。
- 路由會自動進行程式碼分割和懶載入。
- 關鍵資料獲取由路由的 loader 協調。
- 還有更多功能！

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
      將 {state} 加 1？
    </button>
  )
}
```

## 導航

TanStack Start 完全建立在 TanStack Router 之上，因此 TanStack Router 的所有導航功能都可供您使用。簡要概述：

- 使用 `Link` 元件導航到新路由。
- 使用 `useNavigate` 鉤子進行命令式導航。
- 在應用程式的任何地方使用 `useRouter` 鉤子來存取路由實例並執行失效操作。
- 每個返回狀態的路由鉤子都是反應式的，意味著它會在適當的狀態變更時自動重新執行。

以下是使用 `Link` 元件導航到新路由的快速範例：

```tsx
import { Link } from '@tanstack/react-router'

function Home() {
  return <Link to="/about">關於</Link>
}
```

有關導航的更深入資訊，請參閱 [導航指南](/router/latest/docs/framework/react/guide/navigation)。

## 伺服器函數 (RPCs)

您可能已經注意到我們使用 `createServerFn` 創建的 **伺服器函數**。這是 TanStack 最強大的功能之一，允許您創建可以從 SSR 期間的伺服器和客戶端呼叫的伺服器端函數！

以下是伺服器函數運作方式的快速概述：

- 伺服器函數使用 `createServerFn` 函數創建。
- 可以從 SSR 期間的伺服器和客戶端呼叫。
- 可以用於從伺服器獲取資料或執行其他伺服器端操作。

以下是使用伺服器函數從伺服器獲取和返回資料的快速範例：

```tsx
import { createServerFn } from '@tanstack/react-start'
import * as fs from 'node:fs'
import { z } from 'zod'

const getUserById = createServerFn({ method: 'GET' })
  // 始終驗證傳送到函數的資料，這裡我們使用 Zod
  .validator(z.string())
  // 處理函數是執行伺服器端邏輯的地方
  .handler(async ({ data }) => {
    return db.query.users.findFirst({ where: eq(users.id, data) })
  })

// 在應用程式的其他地方
const user = await getUserById({ data: '1' })
```

要了解更多關於伺服器函數的資訊，請參閱 [伺服器函數指南](../server-functions)。

### 變更

伺服器函數也可以用於在伺服器上執行變更。這同樣使用 `createServerFn` 函數完成，但需要額外確保客戶端上受變更影響的任何資料都被失效。

- 如果僅使用 TanStack Router，可以使用 `router.invalidate()` 方法失效所有路由資料並重新獲取。
- 如果使用 TanStack Query，可以使用 `queryClient.invalidateQueries()` 方法失效資料，以及其他更具體的方法來針對特定查詢。

以下是使用伺服器函數在伺服器上執行變更並在客戶端上失效資料的快速範例：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { z } from 'zod'
import { dbUpdateUser } from '...'

const UserSchema = z.object({
  id: z.string(),
  name: z.string(),
})
export type User = z.infer<typeof UserSchema>

export const updateUser = createServerFn({ method: 'POST' })
  .validator(UserSchema)
  .handler(({ data }) => dbUpdateUser(data))

// 在應用程式的其他地方
import { useQueryClient } from '@tanstack/react-query'
import { useRouter } from '@tanstack/react-router'
import { useServerFunction } from '@tanstack/react-start'
import { updateUser, type User } from '...'

export function useUpdateUser() {
  const router = useRouter()
  const queryClient = useQueryClient()
  const _updateUser = useServerFunction(updateUser)

  return useCallback(
    async (user: User) => {
      const result = await _updateUser({ data: user })

      router.invalidate()
      queryClient.invalidateQueries({
        queryKey: ['users', 'updateUser', user.id],
      })

      return result
    },
    [router, queryClient, _updateUser],
  )
}

// 在應用程式的其他地方
import { useUpdateUser } from '...'

function MyComponent() {
  const updateUser = useUpdateUser()
  const onClick = useCallback(async () => {
    await updateUser({ id: '1', name: 'John' })
    console.log('已更新使用者')
  }, [updateUser])

  return <button onClick={onClick}>點擊我</button>
}
```

要了解更多關於變更的資訊，請參閱 [變更指南](/router/latest/docs/framework/react/guide/data-mutations)。

## 資料載入

TanStack Router 的另一個強大功能是資料載入。這允許您為 SSR 獲取資料並在渲染前預載路由資料。這是使用路由的 `loader` 函數完成的。

以下是資料載入運作方式的快速概述：

- 資料載入使用路由的 `loader` 函數完成。
- 資料載入器是 **同構的**，意味著它們在伺服器和客戶端上都會執行。
- 要執行僅限伺服器的邏輯，請從載入器內部呼叫伺服器函數。
- 類似於 TanStack Query，資料載入器會在客戶端上快取，並在資料過期時重新使用甚至在背景中重新獲取。

要了解更多關於資料載入的資訊，請參閱 [資料載入指南](/router/latest/docs/framework/react/guide/data-loading)。
