---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:37:01.000Z
title: 路由器上下文
---

TanStack Router 的路由上下文是一个非常强大的工具，可用于依赖注入等多种场景。顾名思义，路由上下文会通过路由器传递，并向下传递给每个匹配的路由。在层级结构的每个路由中，上下文都可以被修改或添加内容。以下是几种实际使用路由上下文的方式：

- **依赖注入**
  - 你可以提供依赖项（例如加载函数、数据获取客户端、变更服务），路由及其所有子路由无需直接导入或创建即可访问和使用这些依赖。
- **面包屑导航**
  - 虽然每个路由的主上下文对象在向下传递时会合并，但每个路由的独立上下文也会被存储，这使得可以为每个路由的上下文附加面包屑或方法。
- **动态元标签管理**
  - 你可以将元标签附加到每个路由的上下文中，然后使用元标签管理器在用户浏览网站时动态更新页面上的元标签。

这些只是路由上下文的建议用途。你可以按需使用它！

## 类型化路由上下文

与其他功能一样，根路由上下文是严格类型化的。这个类型可以通过任何路由的 `beforeLoad` 选项在路由匹配树向下合并时进行扩展。为了约束根路由上下文的类型，你必须使用 `createRootRouteWithContext<YourContextTypeHere>()(routeOptions)` 函数来创建新的路由上下文，而不是使用 `createRootRoute()` 函数创建根路由。下面是一个示例：

```tsx
import {
  createRootRouteWithContext,
  createRouter,
} from '@tanstack/react-router'

interface MyRouterContext {
  user: User
}

// 使用 routerContext 创建根路由
const rootRoute = createRootRouteWithContext<MyRouterContext>()({
  component: App,
})

const routeTree = rootRoute.addChildren([
  // ...
])

// 使用 routerContext 创建路由器
const router = createRouter({
  routeTree,
})
```

## 传递初始路由上下文

路由上下文在实例化时传递给路由器。你可以通过 `context` 选项将初始路由上下文传递给路由器：

> [!TIP]
> 如果你的上下文包含任何必需属性，如果不传递这些属性，你会看到 TypeScript 错误。如果所有上下文属性都是可选的，则不会看到 TypeScript 错误，传递上下文也是可选的。如果不传递路由上下文，默认为 `{}`。

```tsx
import { createRouter } from '@tanstack/react-router'

// 使用已创建的 routerContext 创建路由器
const router = createRouter({
  routeTree,
  context: {
    user: {
      id: '123',
      name: 'John Doe',
    },
  },
})
```

### 使路由上下文失效

如果需要使传递给路由器的上下文状态失效，可以调用 `invalidate` 方法通知路由器重新计算上下文。这在需要更新上下文状态并让路由器为所有路由重新计算上下文时非常有用。

```tsx
function useAuth() {
  const router = useRouter()
  const [user, setUser] = useState<User | null>(null)

  useEffect(() => {
    const unsubscribe = auth.onAuthStateChanged((user) => {
      setUser(user)
      router.invalidate()
    })

    return unsubscribe
  }, [])

  return user
}
```

## 使用路由上下文

定义路由上下文类型后，可以在路由定义中使用它：

```tsx
// src/routes/todos.tsx
export const Route = createFileRoute('/todos')({
  component: Todos,
  loader: ({ context }) => fetchTodosByUserId(context.user.id),
})
```

你甚至可以注入数据获取和变更实现本身！实际上，这非常推荐 😜

让我们用一个简单的函数来获取一些待办事项：

```tsx
const fetchTodosByUserId = async ({ userId }) => {
  const response = await fetch(`/api/todos?userId=${userId}`)
  const data = await response.json()
  return data
}

const router = createRouter({
  routeTree: rootRoute,
  context: {
    userId: '123',
    fetchTodosByUserId,
  },
})
```

然后，在你的路由中：

```tsx
// src/routes/todos.tsx
export const Route = createFileRoute('/todos')({
  component: Todos,
  loader: ({ context }) => context.fetchTodosByUserId(context.userId),
})
```

### 如何使用外部数据获取库？

```tsx
import {
  createRootRouteWithContext,
  createRouter,
} from '@tanstack/react-router'

interface MyRouterContext {
  queryClient: QueryClient
}

const rootRoute = createRootRouteWithContext<MyRouterContext>()({
  component: App,
})

const queryClient = new QueryClient()

const router = createRouter({
  routeTree: rootRoute,
  context: {
    queryClient,
  },
})
```

然后，在你的路由中：

```tsx
// src/routes/todos.tsx
export const Route = createFileRoute('/todos')({
  component: Todos,
  loader: async ({ context }) => {
    await context.queryClient.ensureQueryData({
      queryKey: ['todos', { userId: user.id }],
      queryFn: fetchTodos,
    })
  },
})
```

## 如何使用 React Context/Hooks？

尝试在路由的 `beforeLoad` 或 `loader` 函数中使用 React Context 或 Hooks 时，重要的是记住 React 的 [Hooks 规则](https://react.dev/reference/rules/rules-of-hooks)。你不能在非 React 函数中使用 hooks，因此不能在 `beforeLoad` 或 `loader` 函数中使用 hooks。

那么，如何在路由的 `beforeLoad` 或 `loader` 函数中使用 React Context 或 Hooks？我们可以使用路由上下文将 React Context 或 Hooks 传递到路由的 `beforeLoad` 或 `loader` 函数中。

让我们看一个示例设置，其中我们将 `useNetworkStrength` hook 传递到路由的 `loader` 函数中：

- `src/routes/__root.tsx`

```tsx
// 首先，确保根路由的上下文是类型化的
import { createRootRouteWithContext } from '@tanstack/react-router'
import { useNetworkStrength } from '@/hooks/useNetworkStrength'

interface MyRouterContext {
  networkStrength: ReturnType<typeof useNetworkStrength>
}

export const Route = createRootRouteWithContext<MyRouterContext>()({
  component: App,
})
```

在这个示例中，我们会在使用 `<RouterProvider />` 渲染路由器之前实例化 hook。这样，hook 会在 React 环境中调用，从而遵守 Hooks 规则。

- `src/router.tsx`

```tsx
import { createRouter } from '@tanstack/react-router'

import { routeTree } from './routeTree.gen'

export const router = createRouter({
  routeTree,
  context: {
    networkStrength: undefined!, // 我们会在 React 环境中设置它
  },
})
```

- `src/main.tsx`

```tsx
import { RouterProvider } from '@tanstack/react-router'
import { router } from './router'

import { useNetworkStrength } from '@/hooks/useNetworkStrength'

function App() {
  const networkStrength = useNetworkStrength()
  // 将 hook 的返回值注入到路由上下文中
  return <RouterProvider router={router} context={{ networkStrength }} />
}

// ...
```

现在，在路由的 `loader` 函数中，我们可以从路由上下文中访问 `networkStrength` hook：

- `src/routes/posts.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  component: Posts,
  loader: ({ context }) => {
    if (context.networkStrength === 'STRONG') {
      // 做一些事情
    }
  },
})
```

## 修改路由上下文

路由上下文会沿着路由树向下传递，并在每个路由处合并。这意味着你可以在每个路由处修改上下文，并且这些修改对所有子路由都可用。下面是一个示例：

- `src/routes/__root.tsx`

```tsx
import { createRootRouteWithContext } from '@tanstack/react-router'

interface MyRouterContext {
  foo: boolean
}

export const Route = createRootRouteWithContext<MyRouterContext>()({
  component: App,
})
```

- `src/router.tsx`

```tsx
import { createRouter } from '@tanstack/react-router'

import { routeTree } from './routeTree.gen'

const router = createRouter({
  routeTree,
  context: {
    foo: true,
  },
})
```

- `src/routes/todos.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/todos')({
  component: Todos,
  beforeLoad: () => {
    return {
      bar: true,
    }
  },
  loader: ({ context }) => {
    context.foo // true
    context.bar // true
  },
})
```

## 处理累积的路由上下文

上下文，尤其是独立的路由 `context` 对象，使得为所有匹配的路由累积和处理路由上下文对象变得非常简单。以下是一个示例，我们使用所有匹配的路由上下文生成面包屑导航：

```tsx
// src/routes/__root.tsx
export const Route = createRootRoute({
  component: () => {
    const matches = useRouterState({ select: (s) => s.matches })

    const breadcrumbs = matches
      .filter((match) => match.context.getTitle)
      .map(({ pathname, context }) => {
        return {
          title: context.getTitle(),
          path: pathname,
        }
      })

    // ...
  },
})
```

使用相同的路由上下文，我们还可以为页面的 `<head>` 生成标题标签：

```tsx
// src/routes/__root.tsx
export const Route = createRootRoute({
  component: () => {
    const matches = useRouterState({ select: (s) => s.matches })

    const matchWithTitle = [...matches]
      .reverse()
      .find((d) => d.context.getTitle)

    const title = matchWithTitle?.context.getTitle() || 'My App'

    return (
      <html>
        <head>
          <title>{title}</title>
        </head>
        <body>{/* ... */}</body>
      </html>
    )
  },
})
```
