---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:19:09.172Z'
title: 路由器上下文
---

# 路由器上下文 (Router Context)

TanStack Router 的路由器上下文 (router context) 是一個非常強大的工具，可用於依賴注入 (dependency injection) 等許多用途。正如其名，路由器上下文會通過路由器傳遞，並向下傳遞至每個匹配的路由。在層次結構中的每個路由處，都可以修改或添加上下文內容。以下是幾種實際使用路由器上下文的方式：

- 依賴注入 (Dependency Injection)
  - 您可以提供依賴項（例如載入函數 (loader function)、資料獲取客戶端 (data fetching client)、變更服務 (mutation service)），路由及其所有子路由都可以存取和使用這些依賴項，而無需直接導入或創建。
- 麵包屑導航 (Breadcrumbs)
  - 雖然每個路由的主要上下文物件在向下傳遞時會合併，但每個路由的獨特上下文也會被儲存，因此可以將麵包屑或方法附加到每個路由的上下文中。
- 動態元標籤管理 (Dynamic meta tag management)
  - 您可以將元標籤 (meta tags) 附加到每個路由的上下文中，然後使用元標籤管理器在使用者瀏覽網站時動態更新頁面上的元標籤。

這些只是路由器上下文的建議用途。您可以根據需要自由使用它！

## 類型化路由器上下文 (Typed Router Context)

與其他所有內容一樣，根路由器上下文 (root router context) 是嚴格類型化的。此類型可以通過任何路由的 `beforeLoad` 選項在路由匹配樹 (route match tree) 向下合併時進行擴充。若要限制根路由器上下文的類型，您必須使用 `createRootRouteWithContext<YourContextTypeHere>()(routeOptions)` 函數來創建新的路由器上下文，而不是使用 `createRootRoute()` 函數來創建根路由。以下是一個範例：

```tsx
import {
  createRootRouteWithContext,
  createRouter,
} from '@tanstack/react-router'

interface MyRouterContext {
  user: User
}

// 使用 routerContext 創建您的根路由
const rootRoute = createRootRouteWithContext<MyRouterContext>()({
  component: App,
})

const routeTree = rootRoute.addChildren([
  // ...
])

// 使用 routerContext 創建您的路由器
const router = createRouter({
  routeTree,
})
```

## 傳遞初始路由器上下文 (Passing the initial Router Context)

路由器上下文在實例化時傳遞給路由器。您可以通過 `context` 選項將初始路由器上下文傳遞給路由器：

> [!TIP]
> 如果您的上下文有任何必需的屬性，當您未在初始路由器上下文中傳遞它們時，您將看到 TypeScript 錯誤。如果您的所有上下文屬性都是可選的，您將不會看到 TypeScript 錯誤，並且傳遞上下文將是可選的。如果您不傳遞路由器上下文，它將預設為 `{}`。

```tsx
import { createRouter } from '@tanstack/react-router'

// 使用您創建的 routerContext 來創建您的路由器
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

### 使路由器上下文失效 (Invalidating the Router Context)

如果您需要使傳入路由器的上下文狀態失效，可以調用 `invalidate` 方法來告訴路由器重新計算上下文。這在您需要更新上下文狀態並讓路由器為所有路由重新計算上下文時非常有用。

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

## 使用路由器上下文 (Using the Router Context)

一旦您定義了路由器上下文類型，就可以在路由定義中使用它：

```tsx
// src/routes/todos.tsx
export const Route = createFileRoute('/todos')({
  component: Todos,
  loader: ({ context }) => fetchTodosByUserId(context.user.id),
})
```

您甚至可以注入資料獲取和變更實作本身！事實上，這是非常推薦的做法 😜

讓我們嘗試用一個簡單的函數來獲取一些待辦事項：

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

然後，在您的路由中：

```tsx
// src/routes/todos.tsx
export const Route = createFileRoute('/todos')({
  component: Todos,
  loader: ({ context }) => context.fetchTodosByUserId(context.userId),
})
```

### 如何使用外部資料獲取函式庫？

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

然後，在您的路由中：

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

## 如何使用 React 上下文/鉤子？ (How about using React Context/Hooks?)

當嘗試在路由的 `beforeLoad` 或 `loader` 函數中使用 React 上下文或鉤子時，重要的是要記住 React 的 [鉤子規則](https://react.dev/reference/rules/rules-of-hooks)。您不能在非 React 函數中使用鉤子，因此不能在 `beforeLoad` 或 `loader` 函數中使用鉤子。

那麼，我們如何在路由的 `beforeLoad` 或 `loader` 函數中使用 React 上下文或鉤子呢？我們可以使用路由器上下文將 React 上下文或鉤子傳遞給路由的 `beforeLoad` 或 `loader` 函數。

讓我們看一個範例的設置，其中我們將 `useNetworkStrength` 鉤子傳遞給路由的 `loader` 函數：

- `src/routes/__root.tsx`

```tsx
// 首先，確保根路由的上下文已類型化
import { createRootRouteWithContext } from '@tanstack/react-router'
import { useNetworkStrength } from '@/hooks/useNetworkStrength'

interface MyRouterContext {
  networkStrength: ReturnType<typeof useNetworkStrength>
}

export const Route = createRootRouteWithContext<MyRouterContext>()({
  component: App,
})
```

在這個範例中，我們會在渲染使用 `<RouterProvider />` 的路由器之前實例化鉤子。這樣，鉤子將在 React 環境中被調用，從而遵守鉤子規則。

- `src/router.tsx`

```tsx
import { createRouter } from '@tanstack/react-router'

import { routeTree } from './routeTree.gen'

export const router = createRouter({
  routeTree,
  context: {
    networkStrength: undefined!, // 我們會在 React 環境中設置這個
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
  // 將鉤子的返回值注入到路由器上下文中
  return <RouterProvider router={router} context={{ networkStrength }} />
}

// ...
```

現在，在我們的 `loader` 函數中，我們可以從路由器上下文中存取 `networkStrength` 鉤子：

- `src/routes/posts.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  component: Posts,
  loader: ({ context }) => {
    if (context.networkStrength === 'STRONG') {
      // 執行某些操作
    }
  },
})
```

## 修改路由器上下文 (Modifying the Router Context)

路由器上下文會沿著路由樹向下傳遞，並在每個路由處合併。這意味著您可以在每個路由處修改上下文，並且這些修改將對所有子路由可用。以下是一個範例：

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

## 處理累積的路由上下文 (Processing Accumulated Route Context)

上下文，特別是獨立的路由 `context` 物件，使得累積和處理所有匹配路由的上下文物件變得非常簡單。以下是一個範例，我們使用所有匹配路由的上下文來生成麵包屑導航：

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

使用相同的路由上下文，我們還可以為頁面的 `<head>` 生成標題標籤：

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
