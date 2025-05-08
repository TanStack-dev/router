---
source-updated-at: '2025-03-15T05:01:48.000Z'
translation-updated-at: '2025-05-08T20:20:10.461Z'
id: external-data-loading
title: 外部資料載入
---

> [!IMPORTANT]
> 本指南主要針對外部狀態管理函式庫及其與 TanStack Router 的整合，涵蓋資料獲取、伺服器渲染 (SSR)、水合/脫水 (hydration/dehydration) 和串流等主題。若您尚未閱讀標準的[資料載入指南](./data-loading.md)，請先閱讀該文件。

## 選擇「儲存」還是「協調」？

雖然 Router 本身已具備儲存和管理大多數資料需求的能力，但有時您可能需要更強大的解決方案！

Router 設計為外部資料獲取與快取函式庫的完美協調者 (coordinator)。這意味著您可以使用任何資料獲取/快取函式庫，而 Router 會根據用戶導航和資料新鮮度預期來協調資料載入流程。

## 支援哪些資料獲取函式庫？

任何支援非同步 Promise 的資料獲取函式庫都能與 TanStack Router 搭配使用，包括：

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

實際上，任何能夠返回 Promise 並讀寫資料的函式庫都能整合。

## 使用 Loader 確保資料載入

將外部快取/資料函式庫整合到 Router 最簡單的方式，是使用 `route.loader` 來確保路由所需的資料已載入並可顯示。

> ⚠️ 但為什麼要這樣做？預載關鍵渲染資料非常重要，原因如下：
>
> - 避免「載入狀態閃爍」現象
> - 避免由基於元件的資料獲取引起的瀑布式載入 (waterfall fetching)
> - 更有利於 SEO。若資料在渲染時已就緒，將能被搜尋引擎索引

以下是一個簡單示意（請勿直接使用）展示如何使用路由的 `loader` 選項來預載快取資料：

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

此範例明顯有缺陷，但說明了您可以使用路由的 `loader` 選項來預載快取資料。讓我們看一個使用 TanStack Query 的更實際範例。

- 將 `fetchPosts` 替換為您偏好資料獲取函式庫的預取 API
- 將 `postsCache` 替換為您偏好函式庫的讀取或獲取 API/hook

## 使用 TanStack Query 的實際範例

讓我們看一個使用 TanStack Query 的更實際範例：

```tsx
// src/routes/posts.tsx
const postsQueryOptions = queryOptions({
  queryKey: ['posts'],
  queryFn: () => fetchPosts(),
})

export const Route = createFileRoute('/posts')({
  // 使用 `loader` 選項確保資料已載入
  loader: () => queryClient.ensureQueryData(postsQueryOptions),
  component: () => {
    // 從快取讀取資料並訂閱更新
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

### TanStack Query 的錯誤處理

當使用 `suspense` 與 `Tanstack Query` 發生錯誤時，您需要讓查詢知道在重新渲染時重試。這可以通過 `useQueryErrorResetBoundary` hook 提供的 `reset` 函式實現。我們可以在錯誤元件掛載時透過 effect 調用此函式。這將確保查詢被重置，並在路由元件再次渲染時嘗試重新獲取資料。此方法也涵蓋用戶點擊「重試」按鈕前導航離開路由的情況。

```tsx
export const Route = createFileRoute('/posts')({
  loader: () => queryClient.ensureQueryData(postsQueryOptions),
  errorComponent: ({ error, reset }) => {
    const router = useRouter()
    const queryErrorResetBoundary = useQueryErrorResetBoundary()

    useEffect(() => {
      // 重置查詢錯誤邊界
      queryErrorResetBoundary.reset()
    }, [queryErrorResetBoundary])

    return (
      <div>
        {error.message}
        <button
          onClick={() => {
            // 使路由失效以重新載入 loader，並重置任何路由錯誤邊界
            router.invalidate()
          }}
        >
          retry
        </button>
      </div>
    )
  },
})
```

## SSR 脫水/水合 (Dehydration/Hydration)

支援的工具可以整合 TanStack Router 提供的便捷脫水/水合 API，在伺服器與客戶端之間傳遞脫水資料並在需要時重新水合。我們將分別說明如何處理第三方關鍵資料與延遲資料。

## 關鍵資料的脫水/水合

對於首次渲染/繪製所需的關鍵資料，TanStack Router 在配置 `Router` 時支援 `dehydrate` 和 `hydrate` 選項。這些回調函式會在伺服器和客戶端自動調用，讓您能用自己的資料擴充脫水資料。

`dehydrate` 函式可返回任何可序列化的 JSON 資料，這些資料會合併到脫水負載中傳送至客戶端。此負載透過 `DehydrateRouter` 元件傳遞，當該元件渲染時，會透過客戶端的 `hydrate` 函式將資料提供給您。

例如，讓我們脫水並水合一個 TanStack Query 的 `QueryClient`，使我們在伺服器獲取的資料能在客戶端水合：

```tsx
// src/router.tsx

export function createRouter() {
  // 請確保在 `createRouter` 函式內建立您的 loader client 或類似資料儲存
  // 這能確保每個請求都有獨立的資料儲存，且資料儲存在伺服器和客戶端都存在
  const queryClient = new QueryClient()

  return createRouter({
    routeTree,
    // 可選：將 loaderClient 提供給路由上下文以便使用
    // （您可以將任何內容提供給路由上下文！）
    context: {
      queryClient,
    },
    // 在伺服器端脫水 loader client，讓 Router 能序列化並傳送至客戶端
    dehydrate: () => {
      return {
        queryClientState: dehydrate(queryClient),
      }
    },
    // 在客戶端，用伺服器脫水的資料水合 loader client
    hydrate: (dehydrated) => {
      hydrate(queryClient, dehydrated.queryClientState)
    },
    // 可選：使用 `Wrap` 將路由包裹在 loader client 的 Provider 中
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
