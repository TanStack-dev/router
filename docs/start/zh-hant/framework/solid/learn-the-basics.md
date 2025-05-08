---
source-updated-at: '2025-03-27T00:19:57.000Z'
translation-updated-at: '2025-05-08T20:26:34.045Z'
id: learn-the-basics
title: 學習基礎知識
---

本指南將幫助您瞭解 TanStack Start 的基本運作原理，無論您如何設定專案。

## 依賴套件

TanStack Start（目前\*）由 [Vinxi](https://vinxi.vercel.app/)、[Nitro](https://nitro.unjs.io/) 和 [TanStack Router](https://tanstack.com/router) 提供技術支援。

- **TanStack Router**：用於構建網頁應用程式的路由系統。
- **Nitro**：用於構建伺服器應用程式的框架。
- **Vinxi**：用於構建網頁應用程式的伺服器框架。

> [!注意] Vinxi 將在 1.0.0 版本發布前移除，TanStack 將僅依賴 Vite 和 Nitro。使用 Vinxi 的指令和 API 可能會被 Vite 插件取代。

## 一切從「路由」開始 (Router)

這個檔案將決定 TanStack Start 內部使用的 TanStack Router 行為。在這裡，您可以配置所有內容，從預設的[預載功能](/router/latest/docs/framework/solid/guide/preloading)到[快取過期設定](/router/latest/docs/framework/solid/guide/data-loading)。

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

- 請注意 `scrollRestoration` 屬性，這用於在路由導航時恢復頁面滾動位置。

## 路由生成 (Route Generation)

`routeTree.gen.ts` 檔案會在首次執行 TanStack Start（透過 `npm run dev` 或 `npm run start`）時生成。此檔案包含生成的路由樹和一些 TypeScript 工具，使 TanStack Start 完全具備型別安全。

## 伺服器入口點 (Server Entry Point)

雖然 TanStack Start 設計為客戶端優先的 API，但它主要是一個全端框架。這意味著所有使用情境，包括動態和靜態，都依賴伺服器或建置時入口來渲染應用程式的初始 HTML 內容。

這透過 `app/ssr.tsx` 檔案完成：

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

無論我們是靜態生成應用程式還是動態提供服務，`ssr.tsx` 檔案都是執行所有 SSR 相關工作的入口點。

- 重要的是，每個請求都需建立一個新的路由器。這確保路由器處理的任何資料都是該請求獨有的。
- `getRouterManifest` 函式用於生成路由器清單，該清單用於決定應用程式的資源管理和預載等許多方面。
- `defaultStreamHandler` 函式用於將應用程式渲染為串流，讓我們能夠利用串流 HTML 到客戶端。（這是預設處理程式，但您也可以使用其他處理程式如 `defaultRenderHandler`，甚至自行建構）

## 客戶端入口點 (Client Entry Point)

將 HTML 傳送到客戶端只是成功的一半。到達後，我們需要在路由解析到客戶端時水合 (hydrate) 客戶端 JavaScript。我們透過使用 `StartClient` 元件水合應用程式的根來實現這一點：

```tsx
// app/client.tsx
/// <reference types="vinxi/types/client" />
import { hydrate } from 'solid-js/web'
import { StartClient } from '@tanstack/solid-start'
import { createRouter } from './router'

const router = createRouter()

hydrate(() => <StartClient router={router} />, document)
```

這使我們能夠在使用者的初始伺服器請求完成後啟動客戶端路由。

## 應用程式的根 (Root of Your Application)

除了客戶端入口點外，應用程式的 `__root` 路由是應用程式的入口點。此檔案中的程式碼將包裹應用程式中的所有其他路由，包括首頁。它的行為類似於整個應用程式的無路徑佈局路由 (pathless layout route)。

由於它**總是渲染**，因此是構建應用程式外殼 (shell) 和處理任何全域邏輯的理想位置。

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
  return <Outlet />
}
```

- 此佈局未來可能會變更，因為我們將推出 SPA 模式，該模式允許根路由在不包含任何頁面特定內容的情況下渲染 SPA 外殼。
- 請注意 `Scripts` 元件，這用於載入應用程式的所有客戶端 JavaScript。

## 路由 (Routes)

路由是 TanStack Router 的廣泛功能，並在[路由指南](/router/latest/docs/framework/solid/routing/file-based-routing)中有詳細介紹。簡要說明：

- 路由使用 `createFileRoute` 函式定義。
- 路由會自動進行程式碼分割 (code-split) 和延遲載入 (lazy-loaded)。
- 關鍵資料獲取由路由的 loader 協調。
- 還有更多！

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
      Add 1 to {state}?
    </button>
  )
}
```

## 導航 (Navigation)

TanStack Start 100% 建立在 TanStack Router 之上，因此 TanStack Router 的所有導航功能都可供您使用。簡要說明：

- 使用 `Link` 元件導航到新路由。
- 使用 `useNavigate` 鉤子 (hook) 進行命令式導航。
- 在應用程式的任何地方使用 `useRouter` 鉤子來存取路由器實例並執行失效 (invalidation)。
- 每個返回狀態的路由器鉤子都是反應式的 (reactive)，這意味著它會在適當的狀態變更時自動重新執行。

以下是使用 `Link` 元件導航到新路由的快速範例：

```tsx
import { Link } from '@tanstack/solid-router'

function Home() {
  return <Link to="/about">About</Link>
}
```

有關導航的更多深入資訊，請查看[導航指南](/router/latest/docs/framework/solid/guide/navigation)。

## 伺服器函式 (RPCs)

您可能已經注意到我們使用 `createServerFn` 建立的**伺服器函式**。這是 TanStack 最強大的功能之一，允許您建立可以從 SSR 期間的伺服器和客戶端呼叫的伺服器端函式！

以下是伺服器函式運作方式的快速概述：

- 伺服器函式使用 `createServerFn` 函式建立。
- 它們可以從 SSR 期間的伺服器和客戶端呼叫。
- 它們可用於從伺服器獲取資料或執行其他伺服器端操作。

以下是使用伺服器函式從伺服器獲取並返回資料的快速範例：

```tsx
import { createServerFn } from '@tanstack/solid-start'
import * as fs from 'node:fs'
import { z } from 'zod'

const getUserById = createServerFn({ method: 'GET' })
  // 始終驗證傳送到函式的資料，這裡我們使用 Zod
  .validator(z.string())
  // 處理函式是執行伺服器端邏輯的地方
  .handler(async ({ data }) => {
    return db.query.users.findFirst({ where: eq(users.id, data) })
  })

// 在應用程式的其他地方
const user = await getUserById({ data: '1' })
```

要了解更多關於伺服器函式的資訊，請查看[伺服器函式指南](../server-functions)。

### 變更 (Mutations)

伺服器函式也可用於在伺服器上執行變更。這同樣使用 `createServerFn` 函式完成，但額外要求您使客戶端上受變更影響的任何資料失效。

- 如果您僅使用 TanStack Router，可以使用 `router.invalidate()` 方法使所有路由器資料失效並重新獲取。
- 如果您使用 TanStack Query，可以使用 `queryClient.invalidateQueries()` 方法使資料失效，以及其他更具體的方法來針對特定查詢。

以下是使用伺服器函式在伺服器上執行變更並使客戶端資料失效的快速範例：

```tsx
import { createServerFn } from '@tanstack/solid-start'

const UserSchema = z.object({
  id: z.string(),
  name: z.string(),
})

const updateUser = createServerFn({ method: 'POST' })
  .validator(UserSchema)
  .handler(async ({ data }) => {
    return db
      .update(users)
      .set({ name: data.name })
      .where(eq(users.id, data.id))
  })

// 在應用程式的其他地方
await updateUser({ data: { id: '1', name: 'John' } })
```

要了解更多關於變更的資訊，請查看[變更指南](/router/latest/docs/framework/solid/guide/data-mutations)。

## 資料載入 (Data Loading)

TanStack Router 的另一個強大功能是資料載入。這允許您為 SSR 獲取資料並在路由渲染前預載路由資料。這透過路由的 `loader` 函式完成。

以下是資料載入運作方式的快速概述：

- 資料載入透過路由的 `loader` 函式完成。
- 資料載入器是**同構的** (isomorphic)，這意味著它們在伺服器和客戶端上執行。
- 要執行僅限伺服器的邏輯，請從載入器內呼叫伺服器函式。
- 與 TanStack Query 類似，資料載入器在客戶端上快取，並在資料過期時重新使用甚至在背景中重新獲取。

要了解更多關於資料載入的資訊，請查看[資料載入指南](/router/latest/docs/framework/solid/guide/data-loading)。
