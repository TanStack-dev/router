---
source-updated-at: '2025-04-17T08:13:24.000Z'
translation-updated-at: '2025-05-06T22:05:29.407Z'
id: learn-the-basics
title: 学习基础知识
---

本指南将帮助你了解 TanStack Start 的基础工作原理，无论你如何设置项目。

## 依赖项

TanStack Start（当前\*）由 [Vinxi](https://vinxi.vercel.app/)、[Nitro](https://nitro.unjs.io/) 和 [TanStack Router](https://tanstack.com/router) 提供支持。

- **TanStack Router**: 用于构建 Web 应用的 路由系统 (router)。
- **Nitro**: 用于构建服务端应用的框架。
- **Vinxi**: 用于构建 Web 应用的服务端框架。

> [!注意] Vinxi 将在 1.0.0 版本发布前被移除，TanStack 将仅依赖 Vite 和 Nitro。使用 Vinxi 的命令和 API 可能会被 Vite 插件取代。

## 一切从 路由系统 (Router) 开始

这个文件将决定 TanStack Start 中使用的路由行为。在这里，你可以配置从默认的 [预加载功能](/router/latest/docs/framework/react/guide/preloading) 到 [缓存过期策略](/router/latest/docs/framework/react/guide/data-loading) 的所有内容。

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

- 注意 `scrollRestoration` 属性，它用于在路由间导航时恢复页面的滚动位置。

## 路由生成

`routeTree.gen.ts` 文件在你首次运行 TanStack Start（通过 `npm run dev` 或 `npm run start`）时生成。该文件包含生成的路由树和一些 TS 工具，使 TanStack Start 完全类型安全。

## 服务端入口点

尽管 TanStack Start 设计为客户端优先的 API，但它本质上是一个全栈框架。这意味着所有用例（包括动态和静态）都依赖服务端或构建时入口来渲染应用的初始 HTML 内容。

这是通过 `app/ssr.tsx` 文件完成的：

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

无论是静态生成应用还是动态提供服务，`ssr.tsx` 文件都是执行所有 服务端渲染 (SSR) 相关工作的入口点。

- 重要的是，每个请求都要创建一个新的路由实例，这确保路由处理的任何数据都是该请求独有的。
- `getRouterManifest` 函数用于生成路由清单，用于确定应用的资源管理和预加载的许多方面。
- `defaultStreamHandler` 函数用于将应用渲染为流，从而可以利用流式 HTML 传输到客户端。（这是默认处理程序，但你也可以使用其他处理程序如 `defaultRenderHandler`，甚至自定义处理程序）

## 客户端入口点

将 HTML 发送到客户端只是成功的一半。到达客户端后，我们需要在路由解析到客户端时水合 (hydrate) 客户端 JavaScript。我们通过使用 `StartClient` 组件水合应用的根节点来实现：

```tsx
// app/client.tsx
import { hydrateRoot } from 'react-dom/client'
import { StartClient } from '@tanstack/react-start'
import { createRouter } from './router'

const router = createRouter()

hydrateRoot(document, <StartClient router={router} />)
```

这使我们能够在用户的初始服务端请求完成后启动客户端路由。

## 应用的根节点

除了客户端入口点，应用的 `__root` 路由是应用的入口点。该文件中的代码将包裹应用中的所有其他路由，包括主页。它的行为类似于整个应用的路径无关的布局路由。

因为它 **总是被渲染**，所以它是构建应用外壳和处理任何全局逻辑的理想位置。

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

- 随着我们推出 SPA 模式，此布局可能会发生变化，该模式允许根路由渲染 SPA 外壳而无需任何页面特定内容。
- 注意 `Scripts` 组件，它用于加载应用的所有客户端 JavaScript。

## 路由

路由是 TanStack Router 的广泛功能，[路由指南](/router/latest/docs/framework/react/routing/file-based-routing) 中有详细介绍。简要总结：

- 路由使用 `createFileRoute` 函数定义。
- 路由会自动代码拆分和懒加载。
- 关键数据获取由路由的加载器 (loader) 协调。
- 还有更多！

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
      Add 1 to {state}?
    </button>
  )
}
```

## 导航

TanStack Start 完全构建在 TanStack Router 之上，因此 TanStack Router 的所有导航功能都可供你使用。简要总结：

- 使用 `Link` 组件导航到新路由。
- 使用 `useNavigate` 钩子以命令式方式导航。
- 在应用中的任何位置使用 `useRouter` 钩子访问路由实例并执行失效操作。
- 每个返回状态的路由钩子都是响应式的，意味着它会在适当的状态变化时自动重新运行。

以下是使用 `Link` 组件导航到新路由的简单示例：

```tsx
import { Link } from '@tanstack/react-router'

function Home() {
  return <Link to="/about">About</Link>
}
```

有关导航的更多深入信息，请查看 [导航指南](/router/latest/docs/framework/react/guide/navigation)。

## 服务端函数 (RPCs)

你可能已经注意到我们使用 `createServerFn` 创建的 **服务端函数**。这是 TanStack 最强大的功能之一，允许你创建可以从 服务端渲染 (SSR) 期间的服务端和客户端调用的服务端函数！

以下是服务端函数工作原理的简要概述：

- 服务端函数使用 `createServerFn` 函数创建。
- 它们可以从 服务端渲染 (SSR) 期间的服务端和客户端调用。
- 它们可用于从服务端获取数据或执行其他服务端操作。

以下是使用服务端函数从服务端获取并返回数据的简单示例：

```tsx
import { createServerFn } from '@tanstack/react-start'
import * as fs from 'node:fs'
import { z } from 'zod'

const getUserById = createServerFn({ method: 'GET' })
  // 始终验证发送到函数的数据，这里我们使用 Zod
  .validator(z.string())
  // 处理函数是执行服务端逻辑的地方
  .handler(async ({ data }) => {
    return db.query.users.findFirst({ where: eq(users.id, data) })
  })

// 在应用的其他位置
const user = await getUserById({ data: '1' })
```

要了解更多关于服务端函数的信息，请查看 [服务端函数指南](../server-functions)。

### 变更操作 (Mutations)

服务端函数也可用于在服务端执行变更操作。这也是使用相同的 `createServerFn` 函数完成的，但额外要求是失效客户端上受变更影响的任何数据。

- 如果仅使用 TanStack Router，可以使用 `router.invalidate()` 方法失效所有路由数据并重新获取。
- 如果使用 TanStack Query，可以使用 `queryClient.invalidateQueries()` 方法失效数据，以及其他更具体的方法来定位特定查询。

以下是使用服务端函数在服务端执行变更操作并在客户端失效数据的简单示例：

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

// 在应用的其他位置
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

// 在应用的其他位置
import { useUpdateUser } from '...'

function MyComponent() {
  const updateUser = useUpdateUser()
  const onClick = useCallback(async () => {
    await updateUser({ id: '1', name: 'John' })
    console.log('Updated user')
  }, [updateUser])

  return <button onClick={onClick}>Click Me</button>
}
```

要了解更多关于变更操作的信息，请查看 [变更操作指南](/router/latest/docs/framework/react/guide/data-mutations)。

## 数据加载

TanStack Router 的另一个强大功能是数据加载。这允许你为 服务端渲染 (SSR) 获取数据，并在路由渲染前预加载路由数据。这是通过路由的 `loader` 函数完成的。

以下是数据加载工作原理的简要概述：

- 数据加载通过路由的 `loader` 函数完成。
- 数据加载器是 **同构的**，意味着它们在服务端和客户端都会执行。
- 要执行仅服务端逻辑，可以从加载器内部调用服务端函数。
- 类似于 TanStack Query，数据加载器在客户端缓存，并在数据过时时重新使用甚至在后台重新获取。

要了解更多关于数据加载的信息，请查看 [数据加载指南](/router/latest/docs/framework/react/guide/data-loading)。
