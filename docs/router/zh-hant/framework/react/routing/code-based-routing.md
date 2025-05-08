---
source-updated-at: '2025-02-27T22:43:21.000Z'
translation-updated-at: '2025-05-08T20:19:00.209Z'
title: 基於程式碼的路由
---

> [!TIP]
> 基於程式碼的路由 (code-based routing) 並不推薦用於大多數應用程式。建議改用[基於檔案的路由 (File-Based Routing)](./file-based-routing.md)。

## ⚠️ 開始前的注意事項

- 如果您正在使用[基於檔案的路由 (File-Based Routing)](./file-based-routing.md)，**請跳過本指南**。
- 如果您仍堅持使用基於程式碼的路由，必須先閱讀[路由概念 (Routing Concepts)](./routing-concepts.md)指南，因為該指南也涵蓋了路由器的核心概念。

## 路由樹 (Route Trees)

基於程式碼的路由與基於檔案的路由在概念上並無不同，兩者都使用相同的路由樹 (route tree) 概念來組織、匹配並將匹配的路由組合成元件樹。唯一的區別在於，基於程式碼的路由是使用程式碼而非檔案系統來組織路由。

讓我們參考[路由樹與嵌套 (Route Trees & Nesting)](./route-trees.md#route-trees)指南中的同一個路由樹，並將其轉換為基於程式碼的路由：

以下是基於檔案的版本：

```
routes/
├── __root.tsx
├── index.tsx
├── about.tsx
├── posts/
│   ├── index.tsx
│   ├── $postId.tsx
├── posts.$postId.edit.tsx
├── settings/
│   ├── profile.tsx
│   ├── notifications.tsx
├── _pathlessLayout.tsx
├── _pathlessLayout/
│   ├── route-a.tsx
├── ├── route-b.tsx
├── files/
│   ├── $.tsx
```

以下是簡化的基於程式碼的版本：

```tsx
import { createRootRoute, createRoute } from '@tanstack/react-router'

const rootRoute = createRootRoute()

const indexRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/',
})

const aboutRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'about',
})

const postsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts',
})

const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '/',
})

const postRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '$postId',
})

const postEditorRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts/$postId/edit',
})

const settingsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'settings',
})

const profileRoute = createRoute({
  getParentRoute: () => settingsRoute,
  path: 'profile',
})

const notificationsRoute = createRoute({
  getParentRoute: () => settingsRoute,
  path: 'notifications',
})

const pathlessLayoutRoute = createRoute({
  getParentRoute: () => rootRoute,
  id: 'pathlessLayout',
})

const pathlessLayoutARoute = createRoute({
  getParentRoute: () => pathlessLayoutRoute,
  path: 'route-a',
})

const pathlessLayoutBRoute = createRoute({
  getParentRoute: () => pathlessLayoutRoute,
  path: 'route-b',
})

const filesRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'files/$',
})
```

## 路由結構剖析 (Anatomy of a Route)

除了根路由 (root route) 外，所有其他路由都是使用 `createRoute` 函數來配置：

```tsx
const route = createRoute({
  getParentRoute: () => rootRoute,
  path: '/posts',
  component: PostsComponent,
})
```

`getParentRoute` 選項是一個函數，它會回傳您正在建立的路由的父路由。

**❓❓❓ "等等，你是要我為每個建立的路由都傳入父路由？"**

沒錯！要求傳入父路由的原因**完全與 TanStack Router 神奇的型別安全 (type safety)**有關。沒有父路由，TypeScript 就無法知道該為您的路由提供什麼型別！

> [!IMPORTANT]
> 對於**非**根路由 (Root Route) 或無路徑的布局路由 (Pathless Layout Route) 的每一個路由，都必須提供 `path` 選項。這個路徑將用於匹配 URL 路徑名稱，以確定路由是否匹配。

在配置路由的 `path` 選項時，它會忽略開頭和結尾的斜線（不包括「索引」路由路徑 `/`）。您可以選擇包含它們，但它們會在 TanStack Router 內部被標準化。以下是有效路徑及其標準化後的對照表：

| 路徑     | 標準化後的路徑 |
| -------- | -------------- |
| `/`      | `/`            |
| `/about` | `about`        |
| `about/` | `about`        |
| `about`  | `about`        |
| `$`      | `$`            |
| `/$`     | `$`            |
| `/$/`    | `$`            |

## 手動建立路由樹 (Manually building the route tree)

在使用程式碼建立路由樹時，僅定義每個路由的父路由是不夠的。您還必須透過將每個路由添加到其父路由的 `children` 陣列中來構建最終的路由樹。這是因為路由樹不會像基於檔案的路由那樣自動為您構建。

```tsx
/* prettier-ignore */
const routeTree = rootRoute.addChildren([
  indexRoute,
  aboutRoute,
  postsRoute.addChildren([
    postsIndexRoute,
    postRoute,
  ]),
  postEditorRoute,
  settingsRoute.addChildren([
    profileRoute,
    notificationsRoute,
  ]),
  pathlessLayoutRoute.addChildren([
    pathlessLayoutARoute,
    pathlessLayoutBRoute,
  ]),
  filesRoute.addChildren([
    fileRoute,
  ]),
])
/* prettier-ignore-end */
```

但在繼續構建路由樹之前，您需要了解基於程式碼的路由的路由概念如何運作。

## 基於程式碼的路由概念 (Routing Concepts for Code-Based Routing)

信不信由你，基於檔案的路由實際上是基於程式碼的路由的超集，它使用檔案系統和一些程式碼生成抽象來自動生成您在上面看到的這種結構。

我們假設您已經閱讀了[路由概念 (Routing Concepts)](./routing-concepts.md)指南，並且熟悉以下主要概念：

- 根路由 (Root Route)
- 基本路由 (Basic Routes)
- 索引路由 (Index Routes)
- 動態路由區段 (Dynamic Route Segments)
- 萬用字元 / 全捕捉路由 (Splat / Catch-All Routes)
- 布局路由 (Layout Routes)
- 無路徑路由 (Pathless Routes)
- 非嵌套路由 (Non-Nested Routes)

現在，讓我們來看看如何在程式碼中建立這些路由類型。

## 根路由 (The Root Route)

在基於程式碼的路由中建立根路由的方式與基於檔案的路由相同。只需呼叫 `createRootRoute()` 函數。

不過與基於檔案的路由不同，您不需要匯出根路由（如果您不想的話）。當然，不建議在單一檔案中構建整個路由樹和應用程式（儘管您可以這樣做，我們在範例中這樣做是為了簡潔地演示路由概念）。

```tsx
// 標準根路由
import { createRootRoute } from '@tanstack/react-router'

const rootRoute = createRootRoute()

// 帶有上下文 (Context) 的根路由
import { createRootRouteWithContext } from '@tanstack/react-router'
import type { QueryClient } from '@tanstack/react-query'

export interface MyRouterContext {
  queryClient: QueryClient
}
const rootRoute = createRootRouteWithContext<MyRouterContext>()
```

要了解更多關於 TanStack Router 中的上下文 (Context)，請參閱[路由器上下文 (Router Context)](../guide/router-context.md)指南。

## 基本路由 (Basic Routes)

要建立基本路由，只需向 `createRoute` 函數提供一個普通的 `path` 字串：

```tsx
const aboutRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'about',
})
```

看，就是這麼簡單！`aboutRoute` 將匹配 URL `/about`。

## 索引路由 (Index Routes)

與基於檔案的路由使用 `index` 檔案名稱來表示索引路由不同，基於程式碼的路由使用單一斜線 `/` 來表示索引路由。例如，上面範例路由樹中的 `posts.index.tsx` 檔案在基於程式碼的路由中會這樣表示：

```tsx
const postsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts',
})

const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  // 注意這裡的單一斜線 `/`
  path: '/',
})
```

因此，`postsIndexRoute` 將匹配 URL `/posts/`（或 `/posts`）。

## 動態路由區段 (Dynamic Route Segments)

動態路由區段在基於程式碼的路由中的運作方式與基於檔案的路由完全相同。只需在路徑的區段前加上 `$`，它就會被捕捉到路由的 `loader` 或 `component` 的 `params` 物件中：

```tsx
const postIdRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '$postId',
  // 在 loader 中
  loader: ({ params }) => fetchPost(params.postId),
  // 或在元件中
  component: PostComponent,
})

function PostComponent() {
  const { postId } = postIdRoute.useParams()
  return <div>文章 ID: {postId}</div>
}
```

> [!TIP]
> 如果您的元件是程式碼分割 (code-split) 的，可以使用 [getRouteApi 函數](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper)來避免導入 `postIdRoute` 配置以獲取型別化的 `useParams()` 鉤子 (hook)。

## 萬用字元 / 全捕捉路由 (Splat / Catch-All Routes)

正如預期的那樣，萬用字元/全捕捉路由在基於程式碼的路由中的運作方式也與基於檔案的路由相同。只需在路徑的區段前加上 `$`，它就會被捕捉到 `params` 物件中的 `_splat` 鍵下：

```tsx
const filesRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'files',
})

const fileRoute = createRoute({
  getParentRoute: () => filesRoute,
  path: '$',
})
```

對於 URL `/documents/hello-world`，`params` 物件將如下所示：

```js
{
  '_splat': 'documents/hello-world'
}
```

## 布局路由 (Layout Routes)

布局路由是將其子路由包裹在布局元件中的路由。在基於程式碼的路由中，您可以透過簡單地將一個路由嵌套在另一個路由下來建立布局路由：

```tsx
const postsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts',
  component: PostsLayoutComponent, // 布局元件
})

function PostsLayoutComponent() {
  return (
    <div>
      <h1>文章</h1>
      <Outlet />
    </div>
  )
}

const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '/',
})

const postsCreateRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: 'create',
})

const routeTree = rootRoute.addChildren([
  // postsRoute 是布局路由
  // 它的子路由將嵌套在 PostsLayoutComponent 中
  postsRoute.addChildren([postsIndexRoute, postsCreateRoute]),
])
```

現在，`postsIndexRoute` 和 `postsCreateRoute` 都將在 `PostsLayoutComponent` 內部渲染其內容：

```tsx
// URL: /posts
<PostsLayoutComponent>
  <PostsIndexComponent />
</PostsLayoutComponent>

// URL: /posts/create
<PostsLayoutComponent>
  <PostsCreateComponent />
</PostsLayoutComponent>
```

## 無路徑的布局路由 (Pathless Layout Routes)

在基於檔案的路由中，無路徑的布局路由會以 `_` 為前綴，但在基於程式碼的路由中，這只是一個具有 `id` 而非 `path` 選項的路由。這是因為基於程式碼的路由不使用檔案系統來組織路由，因此不需要用 `_` 前綴來表示它沒有路徑。

```tsx
const pathlessLayoutRoute = createRoute({
  getParentRoute: () => rootRoute,
  id: 'pathlessLayout',
  component: PathlessLayoutComponent,
})

function PathlessLayoutComponent() {
  return (
    <div>
      <h1>無路徑布局</h1>
      <Outlet />
    </div>
  )
}

const pathlessLayoutARoute = createRoute({
  getParentRoute: () => pathlessLayoutRoute,
  path: 'route-a',
})

const pathlessLayoutBRoute = createRoute({
  getParentRoute: () => pathlessLayoutRoute,
  path: 'route-b',
})

const routeTree = rootRoute.addChildren([
  // 無路徑的布局路由沒有路徑，只有 id
  // 因此它的子路由將嵌套在無路徑的布局路由下
  pathlessLayoutRoute.addChildren([pathlessLayoutARoute, pathlessLayoutBRoute]),
])
```

現在，`/route-a` 和 `/route-b` 都將在 `PathlessLayoutComponent` 內部渲染其內容：

```tsx
// URL: /route-a
<PathlessLayoutComponent>
  <RouteAComponent />
</PathlessLayoutComponent>

// URL: /route-b
<PathlessLayoutComponent>
  <RouteBComponent />
</PathlessLayoutComponent>
```

## 非嵌套路由 (Non-Nested Routes)

在基於程式碼的路由中構建非嵌套路由不需要在路徑中使用尾隨 `_`，但需要您使用正確的路徑和嵌套來構建路由和路由樹。讓我們考慮一個路由樹，其中我們希望文章編輯器**不**嵌套在文章路由下：

- `/posts_/$postId/edit`
- `/posts`
  - `$postId`

要做到這一點，我們需要為文章編輯器建立一個單獨的路由，並在 `path` 選項中包含從我們希望路由嵌套的根（在本例中是根路由）開始的完整路徑：

```tsx
// 文章編輯器路由嵌套在根路由下
const postEditorRoute = createRoute({
  getParentRoute: () => rootRoute,
  // 路徑包含我們需要匹配的完整路徑
  path: 'posts/$postId/edit',
})

const postsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts',
})

const postRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '$postId',
})

const routeTree = rootRoute.addChildren([
  // 文章編輯器路由嵌套在根路由下
  postEditorRoute,
  postsRoute.addChildren([postRoute]),
])
```
