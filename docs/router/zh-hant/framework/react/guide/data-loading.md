---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:21:59.420Z'
id: data-loading
title: 資料載入
---

# 資料載入 (Data Loading)

資料載入是網頁應用程式的常見需求，且與路由密切相關。在載入應用程式頁面時，最理想的狀況是所有頁面的非同步需求都能盡早並行地獲取與滿足。路由是協調這些非同步依賴的最佳位置，因為它通常是應用程式中唯一能在內容渲染前就知道用戶將前往何處的地方。

您可能熟悉 Next.js 的 `getServerSideProps` 或 Remix/React-Router 的 `loader`。TanStack Router 具有類似的功能，可以並行地預載/載入每個路由的資源，讓它能在透過 suspense 獲取資料時盡快渲染。

除了這些路由器的基本功能外，TanStack Router 更進一步提供了**內建的 SWR 快取 (SWR Caching)**，這是一個長期記憶體快取層，用於路由載入器。這意味著您可以使用 TanStack Router 預載路由資料使其瞬間載入，或暫時快存取訪過的路由資料以供後續使用。

## 路由載入生命週期 (Route loading lifecycle)

每次檢測到 URL/歷史記錄更新時，路由器會執行以下順序：

- 路由匹配 (由上至下)
  - `route.params.parse`
  - `route.validateSearch`
- 路由預載 (序列化)
  - `route.beforeLoad`
  - `route.onError`
    - `route.errorComponent` / `parentRoute.errorComponent` / `router.defaultErrorComponent`
- 路由載入 (並行)
  - `route.component.preload?`
  - `route.loader`
    - `route.pendingComponent` (選用)
    - `route.component`
  - `route.onError`
    - `route.errorComponent` / `parentRoute.errorComponent` / `router.defaultErrorComponent`

## 使用路由器快取與否？ (To Router Cache or not to Router Cache?)

TanStack 的路由快取很可能適合大多數中小型應用程式，但了解使用它與更強大的快取解決方案（如 TanStack Query）之間的權衡很重要：

TanStack Router 快取的優點：

- 內建、易於使用，無需額外依賴
- 處理去重複、預載、載入、過期重新驗證 (stale-while-revalidate)、基於每個路由的背景重新獲取
- 粗略失效 (一次失效所有路由和快取)
- 自動垃圾回收
- 適用於路由間共享資料較少的應用程式
- 對 SSR「開箱即用」

TanStack Router 快取的缺點：

- 沒有持久化適配器/模型
- 路由間沒有共享快取/去重複
- 沒有內建的變更 API（許多範例中提供了基本的 `useMutation` 鉤子，可能足以應付許多使用情境）
- 沒有內建的快取層級樂觀更新 API（您仍可以使用像 `useMutation` 鉤子的短暫狀態在元件層級實現）

> [!TIP]
> 如果您已確定需要使用更強大的工具如 TanStack Query，請直接跳至[外部資料載入](./external-data-loading.md)指南。

## 使用路由器快取 (Using the Router Cache)

路由器快取是內建的，只需從任何路由的 `loader` 函數返回資料即可使用。讓我們學習如何使用！

## 路由 `loader` (Route `loader`s)

路由 `loader` 函數在路由匹配載入時被呼叫。它們接收一個參數，這是一個包含許多有用屬性的物件。我們稍後會詳細介紹這些屬性，但首先讓我們看一個路由 `loader` 函數的範例：

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
})
```

## `loader` 參數 (`loader` Parameters)

`loader` 函數接收一個包含以下屬性的物件：

- `abortController` - 路由的 abortController。當路由卸載或當前 `loader` 函數調用不再相關時，其 signal 會被取消。
- `cause` - 當前路由匹配的原因，可能是 `enter` 或 `stay`。
- `context` - 路由的上下文物件，是以下內容的合併：
  - 父路由上下文
  - 由 `beforeLoad` 選項提供的此路由上下文
- `deps` - 由 `Route.loaderDeps` 函數返回的物件值。如果未定義 `Route.loaderDeps`，則提供空物件。
- `location` - 當前位置
- `params` - 路由的路徑參數
- `parentMatchPromise` - `Promise<RouteMatch>`（根路由為 `undefined`）
- `preload` - 布林值，當路由被預載而非載入時為 `true`
- `route` - 路由本身

使用這些參數，我們可以做很多酷炫的事情，但首先讓我們看看如何控制它以及何時呼叫 `loader` 函數。

## 從 `loader` 消費資料 (Consuming data from `loader`s)

要從 `loader` 消費資料，請使用 Route 物件上定義的 `useLoaderData` 鉤子。

```tsx
const posts = Route.useLoaderData()
```

如果無法直接存取您的路由物件（例如您位於當前路由的元件樹深處），可以使用 `getRouteApi` 存取相同的鉤子（以及 Route 物件上的其他鉤子）。這應該比直接導入 Route 物件更受青睞，因為後者可能會造成循環依賴。

```tsx
import { getRouteApi } from '@tanstack/react-router'

// 在您的元件中

const routeApi = getRouteApi('/posts')
const data = routeApi.useLoaderData()
```

## 基於依賴的過期重新驗證快取 (Dependency-based Stale-While-Revalidate Caching)

TanStack Router 為路由載入器提供了一個內建的過期重新驗證 (Stale-While-Revalidate) 快取層，其鍵值基於路由的依賴：

- 路由的完整解析路徑名稱
  - 例如 `/posts/1` 與 `/posts/2`
- 由 `loaderDeps` 選項提供的任何額外依賴
  - 例如 `loaderDeps: ({ search: { pageIndex, pageSize } }) => ({ pageIndex, pageSize })`

使用這些依賴作為鍵值，TanStack Router 會快取從路由的 `loader` 函數返回的資料，並用它來滿足對相同路由匹配的後續請求。這意味著如果路由的資料已在快取中，它會立即返回，然後**可能**在背景重新獲取，具體取決於資料的「新鮮度」。

### 鍵值選項 (Key options)

為了控制路由依賴和「新鮮度」，TanStack Router 提供了大量選項來控制路由載入器的鍵值和快取行為。讓我們按您最可能使用的順序來看看它們：

- `routeOptions.loaderDeps`
  - 一個函數，提供路由器的搜尋參數並返回一個依賴物件供 `loader` 函數使用。當這些依賴在導航間變化時，無論 `staleTime` 如何，都會導致路由重新載入。依賴使用深度相等檢查進行比較。
- `routeOptions.staleTime`
- `routerOptions.defaultStaleTime`
  - 嘗試載入時，路由資料被視為新鮮的毫秒數。
- `routeOptions.preloadStaleTime`
- `routerOptions.defaultPreloadStaleTime`
  - 嘗試預載時，路由資料被視為新鮮的毫秒數。
- `routeOptions.gcTime`
- `routerOptions.defaultGcTime`
  - 路由資料在快取中保留的毫秒數，之後會被垃圾回收。
- `routeOptions.shouldReload`
  - 一個函數，接收與 `beforeLoad` 和 `loaderContext` 相同的參數，並返回一個布林值，指示路由是否應重新載入。這提供了對路由何時應重新載入的另一層控制，超越了 `staleTime` 和 `loaderDeps`，可用於實現類似 Remix 的 `shouldLoad` 選項的模式。

### ⚠️ 一些重要的預設值 (Some Important Defaults)

- 預設情況下，`staleTime` 設為 `0`，意味著路由的資料總是會被視為過期，並且在路由重新匹配時總會在背景重新載入。
- 預設情況下，先前預載的路由被視為新鮮**30 秒**。這意味著如果一個路由被預載，然後在 30 秒內再次預載，第二次預載將被忽略。這防止了不必要的預載過於頻繁地發生。**當路由正常載入時，使用標準的 `staleTime`。**
- 預設情況下，`gcTime` 設為**30 分鐘**，意味著任何 30 分鐘內未被存取的路由資料將被垃圾回收並從快取中移除。
- `router.invalidate()` 會強制所有活動路由立即重新載入其載入器，並將每個快取路由的資料標記為過期。

### 使用 `loaderDeps` 存取搜尋參數 (Using `loaderDeps` to access search params)

想像一個 `/posts` 路由透過搜尋參數 `offset` 和 `limit` 支援分頁。為了讓快取能唯一儲存這些資料，我們需要透過 `loaderDeps` 函數存取這些搜尋參數。透過明確標識它們，每個具有不同 `offset` 和 `limit` 的 `/posts` 路由匹配不會混淆！

一旦我們有了這些依賴，路由將在依賴變化時總是重新載入。

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loaderDeps: ({ search: { offset, limit } }) => ({ offset, limit }),
  loader: ({ deps: { offset, limit } }) =>
    fetchPosts({
      offset,
      limit,
    }),
})
```

### 使用 `staleTime` 控制資料被視為新鮮的時間 (Using `staleTime` to control how long data is considered fresh)

預設情況下，導航的 `staleTime` 設為 `0` 毫秒（預載為 30 秒），這意味著路由的資料總是會被視為過期，並且在路由匹配和導航到時總會在背景重新載入。

**這對大多數使用情境來說是個好預設，但您可能會發現某些路由資料更靜態或可能載入成本較高。**在這些情況下，您可以使用 `staleTime` 選項來控制路由資料對導航保持新鮮的時間。讓我們看一個範例：

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  // 將路由資料視為新鮮 10 秒
  staleTime: 10_000,
})
```

透過將 `10_000` 傳遞給 `staleTime` 選項，我們告訴路由器將路由資料視為新鮮 10 秒。這意味著如果使用者在最後一次載入結果的 10 秒內從 `/about` 導航到 `/posts`，路由的資料不會重新載入。如果使用者在 10 秒後從 `/about` 導航到 `/posts`，路由的資料將在**背景**重新載入。

## 關閉過期重新驗證快取 (Turning off stale-while-revalidate caching)

要為路由停用過期重新驗證快取，請將 `staleTime` 選項設為 `Infinity`：

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  staleTime: Infinity,
})
```

您甚至可以透過在路由器上設定 `defaultStaleTime` 選項來為所有路由關閉此功能：

```tsx
const router = createRouter({
  routeTree,
  defaultStaleTime: Infinity,
})
```

## 使用 `shouldReload` 和 `gcTime` 選擇退出快取 (Using `shouldReload` and `gcTime` to opt-out of caching)

類似於 Remix 的預設功能，您可能希望配置路由僅在進入或關鍵載入器依賴變化時載入。您可以透過結合使用 `gcTime` 選項和 `shouldReload` 選項來實現這一點，後者接受一個布林值或一個函數，該函數接收與 `beforeLoad` 和 `loaderContext` 相同的參數，並返回一個布林值，指示路由是否應重新載入。

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loaderDeps: ({ search: { offset, limit } }) => ({ offset, limit }),
  loader: ({ deps }) => fetchPosts(deps),
  // 在卸載後不快取此路由的資料
  gcTime: 0,
  // 僅在使用者導航到或依賴變化時重新載入路由
  shouldReload: false,
})
```

### 選擇退出快取同時仍預載 (Opting out of caching while still preloading)

即使您選擇退出路由資料的短期快取，您仍然可以獲得預載的好處！使用上述配置，預載仍會「開箱即用」並使用預設的 `preloadGcTime`。這意味著如果一個路由被預載，然後導航到，路由的資料將被視為新鮮且不會重新載入。

要選擇退出預載，請不要透過 `routerOptions.defaultPreload` 或 `routeOptions.preload` 選項開啟它。

## 將所有載入器事件傳遞到外部快取 (Passing all loader events to an external cache)

我們在[外部資料載入](./external-data-loading.md)頁面分解了這個使用案例，但如果您想使用像 TanStack Query 這樣的外部快取，可以透過將所有載入器事件傳遞給您的外部快取來實現。只要您使用預設值，唯一需要做的更改是將路由器上的 `defaultPreloadStaleTime` 選項設為 `0`：

```tsx
const router = createRouter({
  routeTree,
  defaultPreloadStaleTime: 0,
})
```

這將確保每個預載、載入和重新載入事件都會觸發您的 `loader` 函數，然後可以由您的外部快取處理和去重複。

## 使用路由器上下文 (Using Router Context)

傳遞給 `loader` 函數的 `context` 參數是一個物件，包含以下內容的合併：

- 父路由上下文
- 由 `beforeLoad` 選項提供的此路由上下文

從路由器的頂部開始，您可以透過 `context` 選項將初始上下文傳遞給路由器。此上下文將對路由器中的所有路由可用，並在每個路由匹配時被複製和擴展。這是透過 `beforeLoad` 選項將上下文傳遞給路由來實現的。此上下文將對該路由的所有子路由可用。最終的上下文將對路由的 `loader` 函數可用。

在這個範例中，我們將在路由上下文中建立一個函數來獲取文章，然後在我們的 `loader` 函數中使用它。

> 🧠 上下文是依賴注入的強大工具。您可以使用它來注入服務、鉤子和其他物件到您的路由器和路由中。您還可以使用路由的 `beforeLoad` 選項在每個路由上遞增地傳遞資料。

- `/utils/fetchPosts.tsx`

```tsx
export const fetchPosts = async () => {
  const res = await fetch(`/api/posts?page=${pageIndex}`)
  if (!res.ok) throw new Error('Failed to fetch posts')
  return res.json()
}
```

- `/routes/__root.tsx`

```tsx
import { createRootRouteWithContext } from '@tanstack/react-router'

// 使用 createRootRouteWithContext<{...}>() 函數建立根路由，並傳遞您希望在路由器上下文中可用的任何類型。
export const Route = createRootRouteWithContext<{
  fetchPosts: typeof fetchPosts
}>()() // 注意：雙重調用是有意的，因為 createRootRouteWithContext 是一個工廠 ;)
```

- `/routes/posts.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

// 注意我們的 postsRoute 如何引用上下文來獲取 fetchPosts 函數
// 這可以是一個強大的工具，用於在您的路由器和路由之間進行依賴注入。
export const Route = createFileRoute('/posts')({
  loader: ({ context: { fetchPosts } }) => fetchPosts(),
})
```

- `/router.tsx`

```tsx
import { routeTree } from './routeTree.gen'

// 使用您的 routerContext 建立一個新的路由器
// 這將要求您滿足 routerContext 的類型要求
const router = createRouter({
  routeTree,
  context: {
    // 將 fetchPosts 函數提供給路由器上下文
    fetchPosts,
  },
})
```

## 使用路徑參數 (Using Path Params)

要在 `loader` 函數中使用路徑參數，請透過函數參數的 `params` 屬性存取它們。以下是一個範例：

```tsx
// routes/posts.$postId.tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: ({ params: { postId } }) => fetchPostById(postId),
})
```

## 使用路由上下文 (Using Route Context)

將全局上下文傳遞給您的路由器很好，但如果您想提供特定於路由的上下文呢？這就是 `beforeLoad` 選項的用武之地。`beforeLoad` 選項是一個函數，在嘗試載入路由之前運行，並接收與 `loader` 相同的參數。除了其重定向潛在匹配、阻止載入器請求等能力外，它還可以返回一個物件，該物件將被合併到路由的上下文中。讓我們看一個範例，我們透過 `beforeLoad` 選項將一些資料注入到路由上下文中：

```tsx
// /routes/posts.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  // 將 fetchPosts 函數傳遞給路由上下文
```
