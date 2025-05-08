---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:19:08.511Z'
title: 預載入
---

在 TanStack Router 中，預載 (Preloading) 是一種在使用者實際導航到路由之前先行載入的方式。這對於使用者接下來可能造訪的路由特別有用。舉例來說，若你有一個文章列表，而使用者很可能會點擊其中一篇文章，你可以預載該文章路由，如此當使用者點擊時就能立即顯示。

## 支援的預載策略

- 意圖 (Intent)
  - **「意圖」**預載是透過在 `<Link>` 元件上使用 hover 和 touch start 事件來預載目標路由的依賴項。
  - 此策略適用於預載使用者接下來可能造訪的路由。
- 視窗可見性 (Viewport Visibility)
  - **「視窗」**預載是透過 Intersection Observer API，當 `<Link>` 元件進入視窗時預載目標路由的依賴項。
  - 此策略適用於預載位於折疊下方或螢幕外的路由。
- 渲染 (Render)
  - **「渲染」**預載是在 `<Link>` 元件渲染至 DOM 時立即預載目標路由的依賴項。
  - 此策略適用於預載總是需要的路由。

## 預載資料會在記憶體中保留多久？

預載的路由匹配會暫時快取在記憶體中，但有幾個重要注意事項：

- **未使用的預載資料預設在 30 秒後移除。** 這可以透過設定路由器的 `defaultPreloadMaxAge` 選項來調整。
- **當然，當路由被載入時，其預載版本會晉升為路由器的正常待處理匹配狀態。**

若你需要更多控制預載、快取或預載資料的垃圾回收，應使用外部快取函式庫如 [TanStack Query](https://tanstack.com/query)。

為你的應用程式預載路由最簡單的方式是將整個路由器的 `defaultPreload` 選項設為 `intent`：

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  defaultPreload: 'intent',
})
```

這會預設開啟應用程式中所有 `<Link>` 元件的 `intent` 預載。你也可以在個別 `<Link>` 元件上設定 `preload` 屬性來覆寫預設行為。

## 預載延遲

預設情況下，預載會在使用者 hover 或觸碰 `<Link>` 元件 **50ms** 後開始。你可以透過設定路由器的 `defaultPreloadDelay` 選項來調整此延遲：

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  defaultPreloadDelay: 100,
})
```

你也可以在個別 `<Link>` 元件上設定 `preloadDelay` 屬性來針對特定連結覆寫預設行為。

## 內建預載與 `preloadStaleTime`

若你使用內建的載入器 (loaders)，可以透過設定 `routerOptions.defaultPreloadStaleTime` 或 `routeOptions.preloadStaleTime` 為毫秒數，來控制預載資料被視為新鮮的時間，直到觸發另一次預載。**預設情況下，預載資料被視為新鮮的時間為 30 秒。**

要變更此設定，你可以在路由器上設定 `defaultPreloadStaleTime` 選項：

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  defaultPreloadStaleTime: 10_000,
})
```

或者，你也可以在個別路由上使用 `routeOptions.preloadStaleTime` 選項：

```tsx
// src/routes/posts.$postId.tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => fetchPost(params.postId),
  // 若預載快取超過 10 秒，則再次預載路由
  preloadStaleTime: 10_000,
})
```

## 使用外部函式庫進行預載

當整合如 React Query 這類具有自有機制來判斷資料是否過時的外部快取函式庫時，你可能會想覆寫 TanStack Router 的預設預載與 stale-while-revalidate 邏輯。這些函式庫通常使用如 staleTime 等選項來控制資料的新鮮度。

要自訂 TanStack Router 的預載行為並充分運用外部函式庫的快取策略，你可以透過將 `routerOptions.defaultPreloadStaleTime` 或 `routeOptions.preloadStaleTime` 設為 0 來繞過內建快取。這確保所有預載在內部被標記為過時，且載入器總是會被呼叫，讓你的外部函式庫（如 React Query）來管理資料載入與快取。

例如：

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  defaultPreloadStaleTime: 0,
})
```

這樣一來，你就能使用如 React Query 的 `staleTime` 選項來控制預載的新鮮度。

## 手動預載

若你需要手動預載路由，可以使用路由器的 `preloadRoute` 方法。它接受一個標準的 TanStack `NavigateOptions` 物件，並回傳一個在路由預載完成時解析的 promise。

```tsx
function Component() {
  const router = useRouter()

  useEffect(() => {
    async function preload() {
      try {
        const matches = await router.preloadRoute({
          to: postRoute,
          params: { id: 1 },
        })
      } catch (err) {
        // 預載路由失敗
      }
    }

    preload()
  }, [router])

  return <div />
}
```

若你只需要預載路由的 JS chunk，可以使用路由器的 `loadRouteChunk` 方法。它接受一個路由物件，並回傳一個在路由 chunk 載入完成時解析的 promise。

```tsx
function Component() {
  const router = useRouter()

  useEffect(() => {
    async function preloadRouteChunks() {
      try {
        const postsRoute = router.routesByPath['/posts']
        await Promise.all([
          router.loadRouteChunk(router.routesByPath['/']),
          router.loadRouteChunk(postsRoute),
          router.loadRouteChunk(postsRoute.parentRoute),
        ])
      } catch (err) {
        // 預載路由 chunk 失敗
      }
    }

    preloadRouteChunks()
  }, [router])

  return <div />
}
```
