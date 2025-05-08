---
source-updated-at: '2025-04-13T21:11:21.000Z'
translation-updated-at: '2025-05-08T20:20:18.358Z'
id: deferred-data-loading
title: 延遲資料載入
---

TanStack Router 的設計是讓載入器 (loader) 並行執行，並等待所有載入器解析完成後才渲染下一個路由。這在大多數情況下運作良好，但有時你可能希望先向使用者顯示部分內容，同時讓其他資料在背景繼續載入。

延遲資料載入 (Deferred data loading) 是一種模式，允許路由在較慢的非關鍵路由資料於背景解析時，先渲染下個位置 (location) 的關鍵資料/標記 (markup)。此流程在客戶端和伺服器端 (透過串流 streaming) 均可運作，是提升應用程式感知效能 (perceived performance) 的好方法。

如果你使用像 [TanStack Query](https://react-query.tanstack.com) 或其他資料獲取函式庫，延遲資料載入的運作方式會有些不同。請跳至 [使用外部函式庫的延遲資料載入](#deferred-data-loading-with-external-libraries) 章節了解更多資訊。

## 使用 `Await` 實現延遲資料載入

要延遲載入較慢或非關鍵的資料，可在你的載入器 (loader) 回應中回傳一個 **未等待/未解析 (unawaited/unresolved)** 的 promise：

```tsx
// src/routes/posts.$postId.tsx
import { createFileRoute, defer } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  loader: async () => {
    // 獲取較慢的資料，但不等待它
    const slowDataPromise = fetchSlowData()

    // 獲取並等待一些能快速解析的資料
    const fastData = await fetchFastData()

    return {
      fastData,
      deferredSlowData: slowDataPromise,
    }
  },
})
```

一旦任何被等待 (awaited) 的 promise 解析完成，下個路由就會開始渲染，同時延遲的 promise 會繼續解析。

在元件中，可以使用 `Await` 元件來解析並使用延遲的 promise：

```tsx
// src/routes/posts.$postId.tsx
import { createFileRoute, Await } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  // ...
  component: PostIdComponent,
})

function PostIdComponent() {
  const { deferredSlowData, fastData } = Route.useLoaderData()

  // 對 fastData 進行一些操作

  return (
    <Await promise={deferredSlowData} fallback={<div>Loading...</div>}>
      {(data) => {
        return <div>{data}</div>
      }}
    </Await>
  )
}
```

> [!TIP]
> 如果你的元件是程式碼分割 (code-split) 的，可以使用 [getRouteApi 函式](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper) 來避免導入 `Route` 配置，以獲取型別化的 `useLoaderData()` 鉤子 (hook)。

`Await` 元件會透過觸發最近的 suspense 邊界 (boundary) 來解析 promise，直到它被解析後，才會將元件的 `children` 作為函式渲染，並傳入解析後的資料。

如果 promise 被拒絕 (rejected)，`Await` 元件會拋出序列化的錯誤，這可以被最近的錯誤邊界 (error boundary) 捕獲。

[//]: # 'DeferredWithAwaitFinalTip'

> [!TIP]
> 在 React 19 中，你可以使用 `use()` 鉤子 (hook) 來替代 `Await`

[//]: # 'DeferredWithAwaitFinalTip'

## 使用外部函式庫實現延遲資料載入

當你的路由資訊獲取策略依賴於 [外部資料載入 (External Data Loading)](./external-data-loading.md) 並使用像 [TanStack Query](https://tanstack.com/query) 這樣的外部函式庫時，延遲資料載入的運作方式會有些不同，因為函式庫會在 TanStack Router 之外為你處理資料獲取和快取。

因此，與其使用 `defer` 和 `Await`，你應該使用路由的 `loader` 來啟動資料獲取，然後在元件中使用函式庫的鉤子 (hook) 來存取資料。

```tsx
// src/routes/posts.$postId.tsx
import { createFileRoute } from '@tanstack/react-router'
import { slowDataOptions, fastDataOptions } from '~/api/query-options'

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ context: { queryClient } }) => {
    // 啟動較慢資料的獲取，但不等待它
    queryClient.prefetchQuery(slowDataOptions())

    // 獲取並等待一些能快速解析的資料
    await queryClient.ensureQueryData(fastDataOptions())
  },
})
```

然後在你的元件中，可以使用函式庫的鉤子 (hook) 來存取資料：

```tsx
// src/routes/posts.$postId.tsx
import { createFileRoute } from '@tanstack/react-router'
import { useSuspenseQuery } from '@tanstack/react-query'
import { slowDataOptions, fastDataOptions } from '~/api/query-options'

export const Route = createFileRoute('/posts/$postId')({
  // ...
  component: PostIdComponent,
})

function PostIdComponent() {
  const fastData = useSuspenseQuery(fastDataOptions())

  // 對 fastData 進行一些操作

  return (
    <Suspense fallback={<div>Loading...</div>}>
      <SlowDataComponent />
    </Suspense>
  )
}

function SlowDataComponent() {
  const data = useSuspenseQuery(slowDataOptions())

  return <div>{data}</div>
}
```

## 快取與失效 (Caching and Invalidation)

串流的 promise 遵循與其關聯的載入器資料相同的生命週期。它們甚至可以被預載 (preloaded)！

[//]: # 'SSRContent'

## SSR 與串流延遲資料

**串流 (Streaming) 需要支援它的伺服器，並且 TanStack Router 需正確配置才能使用它。**

請閱讀完整的 [串流 SSR 指南](./ssr.md#streaming-ssr) 以獲取逐步說明，了解如何為串流設定你的伺服器。

## SSR 串流生命週期

以下是 TanStack Router 中延遲資料串流運作的高層次概述：

- 伺服器端
  - 從路由載入器回傳的 promise 會被標記並追蹤
  - 所有載入器解析完成，任何延遲的 promise 會被序列化並嵌入 HTML 中
  - 路由開始渲染
  - 使用 `<Await>` 元件渲染的延遲 promise 會觸發 suspense 邊界，允許伺服器串流 HTML 至該點
- 客戶端
  - 客戶端從伺服器接收初始 HTML
  - `<Await>` 元件會使用佔位 (placeholder) promise 暫停 (suspend)，等待其資料在伺服器端解析
- 伺服器端
  - 當延遲的 promise 解析時，其結果 (或錯誤) 會被序列化並透過內聯 script 標籤串流至客戶端
  - 已解析的 `<Await>` 元件及其 suspense 邊界會被解析，其產生的 HTML 會與脫水 (dehydrated) 資料一起串流至客戶端
- 客戶端
  - `<Await>` 中的暫停佔位 promise 會使用串流的資料/錯誤回應來解析，並渲染結果或將錯誤拋至最近的錯誤邊界

[//]: # 'SSRContent'
