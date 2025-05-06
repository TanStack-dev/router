---
source-updated-at: 2025-03-27T00:19:57.000Z
translation-updated-at: 2025-04-05T03:43:35.000Z
title: 学习基础知识
id: learn-the-basics
---

本指南将帮助您理解 TanStack Start 的基础工作原理，无论您如何设置项目。

## 依赖项

TanStack Start（当前\*）由 [Vinxi](https://vinxi.vercel.app/)、[Nitro](https://nitro.unjs.io/) 和 [TanStack Router](https://tanstack.com/router) 提供支持。

- **TanStack Router**：用于构建 Web 应用程序的路由器。
- **Nitro**：用于构建服务器应用程序的框架。
- **Vinxi**：用于构建 Web 应用程序的服务器框架。

> [!NOTE] Vinxi 将在 1.0.0 版本发布前被移除，TanStack 将仅依赖 Vite 和 Nitro。使用 Vinxi 的命令和 API 可能会被 Vite 插件取代。

## 一切从路由器“开始”

这个文件将决定 Start 中使用的 TanStack Router 的行为。在这里，您可以配置从默认的[预加载功能](/router/latest/docs/framework/solid/guide/preloading)到[缓存过期](/router/latest/docs/framework/solid/guide/data-loading)的所有内容。

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

- 注意 `scrollRestoration` 属性。它用于在路由之间导航时恢复页面的滚动位置。

## 路由生成

`routeTree.gen.ts` 文件在首次运行 TanStack Start（通过 `npm run dev` 或 `npm run start`）时生成。该文件包含生成的路由树和一些 TS 工具，使 TanStack Start 完全类型安全。

## 服务器入口点

尽管 TanStack Start 设计为客户端优先的 API，但它主要是一个全栈框架。这意味着所有用例，包括动态和静态，都依赖于服务器或构建时入口来渲染应用程序的初始 HTML 负载。

这是通过 `app/ssr.tsx` 文件完成的：

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

无论我们是静态生成应用程序还是动态提供服务，`ssr.tsx` 文件都是执行所有 SSR 相关工作的入口点。

- 重要的是为每个请求创建一个新的路由器。这确保路由器处理的任何数据都是该请求独有的。
- `getRouterManifest` 函数用于生成路由器清单，该清单用于确定应用程序的资产管理预加载的许多方面。
- `defaultStreamHandler` 函数用于将应用程序渲染为流，允许我们利用流式 HTML 传输到客户端。（这是默认处理程序，但您也可以使用其他处理程序，如 `defaultRenderHandler`，甚至构建自己的处理程序）

## 客户端入口点

将 HTML 传输到客户端只是成功的一半。到达客户端后，我们需要在路由解析到客户端后水合客户端 JavaScript。我们通过使用 `StartClient` 组件水合应用程序的根来实现这一点：

```tsx
// app/client.tsx
/// <reference types="vinxi/types/client" />
import { hydrate } from 'solid-js/web'
import { StartClient } from '@tanstack/solid-start'
import { createRouter } from './router'

const router = createRouter()

hydrate(() => <StartClient router={router} />, document)
```

这使我们能够在用户的初始服务器请求完成后启动客户端路由。

## 应用程序的根

除了客户端入口点外，应用程序的 `__root` 路由是应用程序的入口点。该文件中的代码将包装应用程序中的所有其他路由，包括主页。它的行为类似于整个应用程序的无路径布局路由。

因为它**总是被渲染**，所以它是构建应用程序外壳和处理任何全局逻辑的理想位置。

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

- 这种布局在未来可能会随着我们推出 SPA 模式而改变，该模式允许根路由渲染 SPA 外壳而不包含任何页面特定内容。
- 注意 `Scripts` 组件。它用于加载应用程序的所有客户端 JavaScript。

## 路由

路由是 TanStack Router 的一个广泛功能，并在[路由指南](/router/latest/docs/framework/solid/routing/file-based-routing)中有详细介绍。总结如下：

- 路由使用 `createFileRoute` 函数定义。
- 路由会自动代码拆分和懒加载。
- 关键数据获取由路由的加载器协调。
- 还有更多！

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

## 导航

TanStack Start 完全建立在 TanStack Router 之上，因此 TanStack Router 的所有导航功能都可供您使用。总结如下：

- 使用 `Link` 组件导航到新路由。
- 使用 `useNavigate` 钩子以命令式方式导航。
- 在应用程序的任何地方使用 `useRouter` 钩子访问路由器实例并执行失效操作。
- 每个返回状态的路由器钩子都是响应式的，这意味着它会在适当的状态更改时自动重新运行。

以下是一个快速示例，展示如何使用 `Link` 组件导航到新路由：

```tsx
import { Link } from '@tanstack/solid-router'

function Home() {
  return <Link to="/about">About</Link>
}
```

有关导航的更深入信息，请查看[导航指南](/router/latest/docs/framework/solid/guide/navigation)。

## 服务器函数（RPC）

您可能已经注意到我们使用 `createServerFn` 创建的**服务器函数**。这是 TanStack 最强大的功能之一，允许您创建可以从 SSR 期间的服务器和客户端调用的服务器端函数！

以下是服务器函数工作原理的快速概述：

- 服务器函数使用 `createServerFn` 函数创建。
- 它们可以从 SSR 期间的服务器和客户端调用。
- 它们可用于从服务器获取数据或执行其他服务器端操作。

以下是一个快速示例，展示如何使用服务器函数从服务器获取并返回数据：

```tsx
import { createServerFn } from '@tanstack/solid-start'
import * as fs from 'node:fs'
import { z } from 'zod'

const getUserById = createServerFn({ method: 'GET' })
  // 始终验证发送到函数的数据，这里我们使用 Zod
  .validator(z.string())
  // 处理函数是执行服务器端逻辑的地方
  .handler(async ({ data }) => {
    return db.query.users.findFirst({ where: eq(users.id, data) })
  })

// 在应用程序的其他地方
const user = await getUserById({ data: '1' })
```

要了解更多关于服务器函数的信息，请查看[服务器函数指南](../server-functions)。

### 变更

服务器函数也可用于在服务器上执行变更。这也是使用相同的 `createServerFn` 函数完成的，但需要额外注意的是，您需要使客户端上受变更影响的任何数据失效。

- 如果您仅使用 TanStack Router，可以使用 `router.invalidate()` 方法使所有路由器数据失效并重新获取。
- 如果您使用 TanStack Query，可以使用 `queryClient.invalidateQueries()` 方法使数据失效，以及其他更具体的方法来针对特定查询。

以下是一个快速示例，展示如何使用服务器函数在服务器上执行变更并使客户端上的数据失效：

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

// 在应用程序的其他地方
await updateUser({ data: { id: '1', name: 'John' } })
```

要了解更多关于变更的信息，请查看[变更指南](/router/latest/docs/framework/solid/guide/data-mutations)。

## 数据加载

TanStack Router 的另一个强大功能是数据加载。这允许您为 SSR 获取数据并在路由渲染前预加载路由数据。这是通过路由的 `loader` 函数完成的。

以下是数据加载工作原理的快速概述：

- 数据加载使用路由的 `loader` 函数完成。
- 数据加载器是**同构的**，意味着它们在服务器和客户端上执行。
- 要执行仅限服务器的逻辑，请从加载器中调用服务器函数。
- 类似于 TanStack Query，数据加载器在客户端上缓存，并在数据过时时重新使用甚至在后台重新获取。

要了解更多关于数据加载的信息，请查看[数据加载指南](/router/latest/docs/framework/solid/guide/data-loading)。
