---
source-updated-at: 2025-03-15T05:01:48.000Z
translation-updated-at: 2025-04-05T03:27:27.000Z
title: 外部数据加载
id: external-data-loading
---

> [!IMPORTANT]
> 本指南主要面向外部状态管理库及其与TanStack Router的集成，涵盖数据获取、SSR、水合/脱水及流式传输。若您尚未阅读标准的[数据加载指南](./data-loading.md)，请先阅读该文档。

## **存储**还是**协调**？

虽然Router开箱即用就能满足大多数数据存储和管理需求，但有时您可能需要更强大的解决方案！

Router被设计为外部数据获取与缓存库的完美**协调者**。这意味着您可以使用任何数据获取/缓存库，而Router将以符合用户导航路径和新鲜度预期的方式协调数据加载。

## 支持哪些数据获取库？

任何支持异步Promise的数据获取库都能与TanStack Router配合使用，包括：

- [TanStack Query](https://tanstack.com/query/latest/docs/react/overview)
- [SWR](https://swr.vercel.app/)
- [RTK Query](https://redux-toolkit.js.org/rtk-query/overview)
- [urql](https://formidable.com/open-source/urql/)
- [Relay](https://relay.dev/)
- [Apollo](https://www.apollographql.com/docs/react/)

甚至包括：

- [Zustand](https://zustand-demo.pmnd.rs/)
- [Jotai](https://jotai.org/)
- [Recoil](https://recoiljs.org/)
- [Redux](https://redux.js.org/)

实际上，任何**能返回Promise并读写数据**的库都可集成。

## 使用Loader确保数据加载

将外部缓存/数据库集成到Router中最简单的方式是通过`route.loader`确保路由所需数据已加载并准备就绪。

> ⚠️ 但为什么？在loader中预加载关键渲染数据非常重要，原因如下：
>
> - 避免"加载状态闪烁"
> - 消除由基于组件的获取引起的数据瀑布流
> - 更利于SEO。若数据在渲染时可用，搜索引擎将能索引内容

以下是一个基础示例（请勿直接使用），展示如何通过路由的`loader`选项为数据预填充缓存：

```tsx
// src/routes/posts.tsx
let postsCache = []

export const Route = createFileRoute('/posts')({
  loader: async () => {
    postsCache = await fetchPosts()
  },
  component: () => {
    return (
      <div>
        {postsCache.map((post) => (
          <Post key={post.id} post={post} />
        ))}
      </div>
    )
  },
})
```

此示例**显然存在缺陷**，但说明了可通过路由的`loader`选项为缓存预加载数据。下面我们看一个使用TanStack Query的更实际案例。

- 将`fetchPosts`替换为您首选数据获取库的预取API
- 将`postsCache`替换为您首选数据获取库的读取或获取API/hook

## 使用TanStack Query的实际案例

```tsx
// src/routes/posts.tsx
const postsQueryOptions = queryOptions({
  queryKey: ['posts'],
  queryFn: () => fetchPosts(),
})

export const Route = createFileRoute('/posts')({
  // 使用`loader`选项确保数据加载
  loader: () => queryClient.ensureQueryData(postsQueryOptions),
  component: () => {
    // 从缓存读取数据并订阅更新
    const {
      data: { posts },
    } = useSuspenseQuery(postsQueryOptions)

    return (
      <div>
        {posts.map((post) => (
          <Post key={post.id} post={post} />
        ))}
      </div>
    )
  },
})
```

### TanStack Query的错误处理

当使用`suspense`与`Tanstack Query`时发生错误，需要通过`useQueryErrorResetBoundary`钩子提供的`reset`函数让查询在重新渲染时重试。我们可在错误组件挂载时通过effect调用此函数。这将确保查询被重置，并在路由组件再次渲染时重新获取数据。此方案也适用于用户通过导航离开路由而非点击`retry`按钮的情况。

```tsx
export const Route = createFileRoute('/posts')({
  loader: () => queryClient.ensureQueryData(postsQueryOptions),
  errorComponent: ({ error, reset }) => {
    const router = useRouter()
    const queryErrorResetBoundary = useQueryErrorResetBoundary()

    useEffect(() => {
      // 重置查询错误边界
      queryErrorResetBoundary.reset()
    }, [queryErrorResetBoundary])

    return (
      <div>
        {error.message}
        <button
          onClick={() => {
            // 使路由失效以重新加载loader，并重置路由错误边界
            router.invalidate()
          }}
        >
          重试
        </button>
      </div>
    )
  },
})
```

## SSR脱水/水合

支持的工具可通过TanStack Router提供的脱水/水合API，在服务端与客户端之间传输脱水数据并在需要时重新水合。下面我们将介绍如何处理第三方关键数据和第三方延迟数据。

## 关键数据脱水/水合

**对于首次渲染所需的关键数据**，TanStack Router在配置`Router`时支持**`dehydrate`和`hydrate`**选项。这些回调函数会在路由器正常脱水/水合时自动在服务端和客户端调用，允许您用自定义数据增强脱水数据。

`dehydrate`函数可返回任何可序列化的JSON数据，这些数据会被合并到发送至客户端的脱水载荷中。该载荷通过`DehydrateRouter`组件传递，当该组件渲染时，会通过客户端的`hydrate`函数将数据回传。

例如，让我们对TanStack Query的`QueryClient`进行脱水/水合操作，使得服务端获取的数据能在客户端水合：

```tsx
// src/router.tsx

export function createRouter() {
  // 确保在`createRouter`函数内创建loader client或类似数据存储
  // 这能保证每个请求的数据存储是独立的
  // 且始终存在于服务端和客户端
  const queryClient = new QueryClient()

  return createRouter({
    routeTree,
    // 可选：将loaderClient提供给路由上下文以便使用
    // （您可以在路由上下文中提供任何内容！）
    context: {
      queryClient,
    },
    // 在服务端脱水loader client，以便路由器能序列化
    // 并发送至客户端
    dehydrate: () => {
      return {
        queryClientState: dehydrate(queryClient),
      }
    },
    // 在客户端，用服务端脱水数据水合loader client
    hydrate: (dehydrated) => {
      hydrate(queryClient, dehydrated.queryClientState)
    },
    // 可选：使用`Wrap`将路由器包裹在loader client的Provider中
    Wrap: ({ children }) => {
      return (
        <QueryClientProvider client={queryClient}>
          {children}
        </QueryClientProvider>
      )
    },
  })
}
```
