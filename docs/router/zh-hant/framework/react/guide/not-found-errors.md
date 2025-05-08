---
source-updated-at: '2025-02-27T22:43:21.000Z'
translation-updated-at: '2025-05-08T20:20:35.735Z'
title: 找不到錯誤
---

> ⚠️ 本頁面涵蓋了用於處理找不到頁面錯誤的新版 `notFound` 函式與 `notFoundComponent` API。`NotFoundRoute` 路由已被棄用，將在未來版本中移除。更多資訊請參閱[從 `NotFoundRoute` 遷移](#migrating-from-notfoundroute)。

## 概覽

在 TanStack Router 中，找不到頁面錯誤有兩種使用情境：

- **未匹配的路由路徑**：當路徑不符合任何已知的路由匹配模式**或**部分匹配某個路由但包含多餘的路徑片段時
  - **路由器**會在路徑不符合任何已知路由匹配模式時自動拋出找不到頁面錯誤
  - 若路由器的 `notFoundMode` 設為 `fuzzy`，則會由最近的上層具有 `notFoundComponent` 的路由處理錯誤。若設為 `root`，則由根路由處理錯誤。
  - 範例：
    - 嘗試存取不存在的 `/users` 路由
    - 嘗試存取 `/posts/1/edit` 但路由樹僅處理 `/posts/$postId`
- **找不到資源**：當資源無法找到時，例如指定 ID 的文章不存在或任何非同步資料不可用/不存在
  - **開發者**需在資源無法找到時手動拋出找不到頁面錯誤。可透過 `beforeLoad` 或 `loader` 函式使用 `notFound` 工具函式實現。
  - 將由最近的上層具有 `notFoundComponent` 的路由或根路由處理
  - 範例：
    - 嘗試存取 `/posts/1` 但 ID 為 1 的文章不存在
    - 嘗試存取 `/docs/path/to/document` 但文件不存在

底層實作上，這兩種情境均使用相同的 `notFound` 函式與 `notFoundComponent` API 處理。

## `notFoundMode` 選項

當 TanStack Router 遇到**無法匹配任何已知路由模式**或**部分匹配但包含多餘路徑片段**的路徑時，會自動拋出找不到頁面錯誤。

根據 `notFoundMode` 選項設定，路由器會以不同方式處理這些自動錯誤：

- ["fuzzy" 模式](#notfoundmode-fuzzy)（預設）：路由器會智能尋找最接近的合適路由並顯示其 `notFoundComponent`
- ["root" 模式](#notfoundmode-root)：所有找不到頁面錯誤均由根路由的 `notFoundComponent` 處理，無視最近匹配的路由

### `notFoundMode: 'fuzzy'`

預設情況下，路由器的 `notFoundMode` 設為 `fuzzy`，表示當路徑不匹配任何已知路由時，路由器會嘗試使用最接近的、具有子路由（即有 outlet）且配置了找不到頁面元件的路由。

> **❓ 為何這是預設值？** 模糊匹配能保留最多上層佈局，提供使用者更多上下文資訊以導航至有用位置。

最接近的合適路由需符合以下條件：

- 該路由必須有子路由，因此會有 `Outlet` 來渲染 `notFoundComponent`
- 該路由必須配置了 `notFoundComponent` 或路由器必須配置了 `defaultNotFoundComponent`

例如以下路由樹：

- `__root__`（配置了 `notFoundComponent`）
  - `posts`（配置了 `notFoundComponent`）
    - `$postId`（配置了 `notFoundComponent`）

若提供路徑 `/posts/1/edit`，將渲染以下元件結構：

- `<Root>`
  - `<Posts>`
    - `<Posts.notFoundComponent>`

`posts` 路由的 `notFoundComponent` 會被渲染，因為它是**最接近的、具有子路由（即有 outlet）且配置了找不到頁面元件的上層路由**。

### `notFoundMode: 'root'`

當 `notFoundMode` 設為 `root` 時，所有找不到頁面錯誤將由根路由的 `notFoundComponent` 處理，而非從最近模糊匹配的路由冒泡上來。

例如以下路由樹：

- `__root__`（配置了 `notFoundComponent`）
  - `posts`（配置了 `notFoundComponent`）
    - `$postId`（配置了 `notFoundComponent`）

若提供路徑 `/posts/1/edit`，將渲染以下元件結構：

- `<Root>`
  - `<Root.notFoundComponent>`

`__root__` 路由的 `notFoundComponent` 會被渲染，因為 `notFoundMode` 設為 `root`。

## 配置路由的 `notFoundComponent`

要處理兩種類型的找不到頁面錯誤，可為路由附加 `notFoundComponent`。當拋出找不到頁面錯誤時，此元件將被渲染。

例如為 `/settings` 路由配置 `notFoundComponent` 以處理不存在的設定頁面：

```tsx
export const Route = createFileRoute('/settings')({
  component: () => {
    return (
      <div>
        <p>Settings page</p>
        <Outlet />
      </div>
    )
  },
  notFoundComponent: () => {
    return <p>This setting page doesn't exist!</p>
  },
})
```

或為 `/posts/$postId` 路由配置 `notFoundComponent` 以處理不存在的文章：

```tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params: { postId } }) => {
    const post = await getPost(postId)
    if (!post) throw notFound()
    return { post }
  },
  component: ({ post }) => {
    return (
      <div>
        <h1>{post.title}</h1>
        <p>{post.body}</p>
      </div>
    )
  },
  notFoundComponent: () => {
    return <p>Post not found!</p>
  },
})
```

## 預設全路由找不到頁面處理

您可能想為應用中所有具有子路由的路由提供預設找不到頁面元件。

> 為何僅限有子路由的路由？**葉節點路由（無子路由）永遠不會渲染 `Outlet`，因此無法處理找不到頁面錯誤。**

為此，可將 `defaultNotFoundComponent` 傳遞給 `createRouter` 函式：

```tsx
const router = createRouter({
  defaultNotFoundComponent: () => {
    return (
      <div>
        <p>Not found!</p>
        <Link to="/">Go home</Link>
      </div>
    )
  },
})
```

## 手動拋出 `notFound` 錯誤

您可在載入器方法與元件中使用 `notFound` 函式手動拋出找不到頁面錯誤。這在需要標示資源找不到時非常有用。

`notFound` 函式運作方式類似 `redirect` 函式。要觸發找不到頁面錯誤，可**拋出 `notFound()`**。

```tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params: { postId } }) => {
    // 若文章不存在則返回 `null`
    const post = await getPost(postId)
    if (!post) {
      throw notFound()
      // 或可讓 notFound 函式直接拋出：
      // notFound({ throw: true })
    }
    // 因已拋出錯誤，此處 post 必定有值
    return { post }
  },
})
```

上述找不到頁面錯誤將由相同路由或最近的上層具有 `notFoundComponent` 路由選項或 `defaultNotFoundComponent` 路由器選項的路由處理。

若找不到合適路由處理錯誤，根路由將使用 TanStack Router **極簡（且刻意不理想）**的預設找不到頁面元件處理，僅渲染 `<div>Not Found</div>`。強烈建議至少為根路由附加一個 `notFoundComponent` 或配置全路由的 `defaultNotFoundComponent` 來處理找不到頁面錯誤。

## 指定處理找不到頁面錯誤的路由

有時您可能想在特定父路由觸發找不到頁面錯誤，並繞過正常的找不到頁面元件傳播機制。為此，可在 `notFound` 函式的 `route` 選項中傳入路由 ID。

```tsx
// _pathlessLayout.tsx
export const Route = createFileRoute('/_pathlessLayout')({
  // 此處將被渲染
  notFoundComponent: () => {
    return <p>Not found (in _pathlessLayout)</p>
  },
  component: () => {
    return (
      <div>
        <p>This is a pathless layout route!</p>
        <Outlet />
      </div>
    )
  },
})

// _pathlessLayout/route-a.tsx
export const Route = createFileRoute('/_pathless/route-a')({
  loader: async () => {
    // 這將讓 LayoutRoute 處理找不到頁面錯誤
    throw notFound({ routeId: '/_pathlessLayout' })
    //                      ^^^^^^^^^ 此處會從註冊的路由器自動完成
  },
  // 此處將不會被渲染
  notFoundComponent: () => {
    return <p>Not found (in _pathlessLayout/route-a)</p>
  },
})
```

### 手動指定根路由

也可透過將匯出的 `rootRouteId` 變數傳遞給 `notFound` 函式的 `route` 屬性來指定根路由：

```tsx
import { rootRouteId } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params: { postId } }) => {
    const post = await getPost(postId)
    if (!post) throw notFound({ routeId: rootRouteId })
    return { post }
  },
})
```

### 在元件中拋出找不到頁面錯誤

也可在元件中拋出找不到頁面錯誤。但**建議在載入器方法而非元件中拋出錯誤，以正確類型化載入器資料並避免閃爍問題**。

TanStack Router 提供類似 `CatchBoundary` 的 `CatchNotFound` 元件，可用於捕捉元件中的找不到頁面錯誤並顯示相應 UI。

### 在 `notFoundComponent` 中載入資料

`notFoundComponent` 在資料載入方面是特例。**`SomeRoute.useLoaderData` 可能未定義，具體取決於您嘗試存取的路由及拋出錯誤的位置**。但 `Route.useParams`、`Route.useSearch`、`Route.useRouteContext` 等將返回定義值。

**若需將不完整的載入器資料傳遞給 `notFoundComponent`，**可透過 `notFound` 函式的 `data` 選項傳遞，並在 `notFoundComponent` 中驗證。

```tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params: { postId } }) => {
    const post = await getPost(postId)
    if (!post)
      throw notFound({
        // 將部分資料傳遞給 notFoundComponent
        // data: someIncompleteLoaderData
      })
    return { post }
  },
  // 呼叫 `notFound` 時透過 `data` 選項將 `data: unknown` 傳遞給元件
  notFoundComponent: ({ data }) => {
    // ❌ 此處不可使用 useLoaderData: const { post } = Route.useLoaderData()

    // ✅:
    const { postId } = Route.useParams()
    const search = Route.useSearch()
    const context = Route.useRouteContext()

    return <p>Post with id {postId} not found!</p>
  },
})
```

## 與 SSR 共同使用

更多資訊請參閱 [SSR 指南](./ssr.md)。

## 從 `NotFoundRoute` 遷移

`NotFoundRoute` API 已被棄用，改用 `notFoundComponent`。`NotFoundRoute` API 將在未來版本中移除。

**使用 `NotFoundRoute` 時，`notFound` 函式與 `notFoundComponent` 將無效。**

主要差異如下：

- `NotFoundRoute` 是需要父路由有 `<Outlet>` 才能渲染的路由。`notFoundComponent` 是可附加至任何路由的元件。
- 使用 `NotFoundRoute` 時無法使用佈局。`notFoundComponent` 可與佈局共用。
- 使用 `notFoundComponent` 時，路徑匹配是嚴格的。這表示若有 `/post/$postId` 路由，嘗試存取 `/post/1/2/3` 將拋出找不到頁面錯誤。使用 `NotFoundRoute` 時，`/post/1/2/3` 會匹配 `NotFoundRoute` 並僅在有 `<Outlet>` 時渲染它。

要從 `NotFoundRoute` 遷移至 `notFoundComponent`，只需進行少量變更：

```tsx
// router.tsx
import { createRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen.'
- import { notFoundRoute } from './notFoundRoute'  // [!code --]

export const router = createRouter({
  routeTree,
- notFoundRoute // [!code --]
})

// routes/__root.tsx
import { createRootRoute } from '@tanstack/react-router'

export const Route = createRootRoute({
  // ...
+ notFoundComponent: () => {  // [!code ++]
+   return <p>Not found!</p>  // [!code ++]
+ } // [!code ++]
})
```

重要變更：

- 為根路由新增 `notFoundComponent` 以全域處理找不到頁面錯誤。
  - 也可為路由樹中其他路由新增 `notFoundComponent` 以處理特定路由的找不到頁面錯誤。
- `notFoundComponent` 不支援渲染 `<Outlet>`。
