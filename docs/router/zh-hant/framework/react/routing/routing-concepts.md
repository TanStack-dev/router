---
source-updated-at: '2025-05-01T22:17:05.000Z'
translation-updated-at: '2025-05-08T20:18:00.294Z'
title: 路由概念
---

TanStack Router 支援多種強大的路由概念，讓您能輕鬆建構複雜且動態的路由系統。

這些概念各自實用且強大，我們將在以下章節深入探討每個概念。

## 路由結構剖析

除了[根路由](#the-root-route)之外，所有其他路由都是透過 `createFileRoute` 函式進行配置，該函式在使用檔案式路由時提供型別安全：

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  component: PostsComponent,
})
```

`createFileRoute` 函式接受單一參數，即檔案路由的路徑字串。

**❓❓❓「等等，你要我將路由檔案的路徑傳給 `createFileRoute`？」**

沒錯！但別擔心，這個路徑**會透過 TanStack Router Bundler Plugin 或 Router CLI 自動為您寫入和管理**。因此，當您建立新路由、移動路由或重新命名路由時，路徑會自動為您更新。

這個路徑名稱的存在與 TanStack Router 神奇的型別安全息息相關。若沒有這個路徑名稱，TypeScript 就無法知道我們在哪個檔案中！（我們希望 TypeScript 內建這個功能，但目前還沒有 🤷‍♂️）

## 根路由

根路由是整個路由樹中最頂層的路由，並封裝了所有其他子路由。

- 它沒有路徑
- **永遠**會被匹配
- 它的 `component` **永遠**會被渲染

儘管沒有路徑，根路由仍擁有與其他路由相同的所有功能，包括：

- 元件
- 載入器 (loader)
- 搜尋參數驗證
- 等等

要建立根路由，請呼叫 `createRootRoute()` 函式並將其匯出為路由檔案中的 `Route` 變數：

```tsx
// 標準根路由
import { createRootRoute } from '@tanstack/react-router'

export const Route = createRootRoute()

// 帶有上下文的根路由
import { createRootRouteWithContext } from '@tanstack/react-router'
import type { QueryClient } from '@tanstack/react-query'

export interface MyRouterContext {
  queryClient: QueryClient
}
export const Route = createRootRouteWithContext<MyRouterContext>()
```

若要進一步了解 TanStack Router 中的上下文，請參閱[路由上下文](../guide/router-context.md)指南。

## 基礎路由

基礎路由會匹配特定路徑，例如 `/about`、`/settings`、`/settings/notifications` 都是基礎路由，因為它們會精確匹配路徑。

讓我們來看一個 `/about` 路由：

```tsx
// about.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/about')({
  component: AboutComponent,
})

function AboutComponent() {
  return <div>About</div>
}
```

基礎路由簡單明瞭。它們精確匹配路徑並渲染提供的元件。

## 索引路由

索引路由專門在其父路由**被精確匹配且沒有子路由被匹配**時觸發。

讓我們來看一個 `/posts` URL 的索引路由：

```tsx
// posts.index.tsx
import { createFileRoute } from '@tanstack/react-router'

// 注意結尾的斜線，用於指定索引路由
export const Route = createFileRoute('/posts/')({
  component: PostsIndexComponent,
})

function PostsIndexComponent() {
  return <div>請選擇一篇文章！</div>
}
```

當 URL 精確為 `/posts` 時，此路由會被匹配。

## 動態路由區段

以 `$` 開頭並後接標籤的路由路徑區段是動態的，會將該部分的 URL 擷取到 `params` 物件中供應用程式使用。例如，路徑名為 `/posts/123` 會匹配 `/posts/$postId` 路由，而 `params` 物件會是 `{ postId: '123' }`。

這些參數可在路由配置和元件中使用！讓我們來看一個 `posts.$postId.tsx` 路由：

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  // 在載入器中
  loader: ({ params }) => fetchPost(params.postId),
  // 或在元件中
  component: PostComponent,
})

function PostComponent() {
  // 在元件中！
  const { postId } = Route.useParams()
  return <div>文章 ID：{postId}</div>
}
```

> 🧠 動態區段會作用於路徑的**每個**區段。例如，您可以有一個路徑為 `/posts/$postId/$revisionId` 的路由，每個 `$` 區段都會被擷取到 `params` 物件中。

## 萬用字元 / 全捕捉路由

路徑僅為 `$` 的路由稱為「萬用字元」路由，因為它**總是**會擷取從 `$` 到結尾的**任何**剩餘 URL 路徑名。擷取的路徑名會以特殊的 `_splat` 屬性出現在 `params` 物件中。

例如，目標路徑為 `files/$` 的路由就是一個萬用字元路由。如果 URL 路徑名為 `/files/documents/hello-world`，`params` 物件會包含 `documents/hello-world` 並以特殊的 `_splat` 屬性表示：

```js
{
  '_splat': 'documents/hello-world'
}
```

> ⚠️ 在路由器的 v1 版本中，萬用字元路由也會以 `*` 表示而非 `_splat` 鍵，以保持向後相容性。這將在 v2 中移除。

> 🧠 為什麼使用 `$`？感謝像 Remix 這樣的工具，我們知道儘管 `*` 是最常見的代表萬用字元的字元，但它們與檔案名稱或 CLI 工具的相容性不佳，因此我們決定像他們一樣改用 `$`。

## 佈局路由

佈局路由用於以額外的元件和邏輯包裹子路由。它們適用於：

- 以佈局元件包裹子路由
- 在顯示任何子路由前強制執行 `loader` 需求
- 驗證並提供搜尋參數給子路由
- 為子路由提供錯誤元件或待處理元素的後備方案
- 為所有子路由提供共享上下文
- 以及更多！

讓我們來看一個名為 `app.tsx` 的佈局路由範例：

```
routes/
├── app.tsx
├── app.dashboard.tsx
├── app.settings.tsx
```

在上面的樹狀結構中，`app.tsx` 是一個佈局路由，包裹了兩個子路由 `app.dashboard.tsx` 和 `app.settings.tsx`。

此樹狀結構用於以佈局元件包裹子路由：

```tsx
import { Outlet, createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/app')({
  component: AppLayoutComponent,
})

function AppLayoutComponent() {
  return (
    <div>
      <h1>應用程式佈局</h1>
      <Outlet />
    </div>
  )
}
```

下表顯示基於 URL 會渲染哪些元件：

| URL 路徑         | 元件                     |
| ---------------- | ------------------------ |
| `/`              | `<Index>`                |
| `/app/dashboard` | `<AppLayout><Dashboard>` |
| `/app/settings`  | `<AppLayout><Settings>`  |

由於 TanStack Router 支援混合扁平與目錄式路由，您也可以使用目錄中的佈局路由來表達應用程式的路由：

```
routes/
├── app/
│   ├── route.tsx
│   ├── dashboard.tsx
│   ├── settings.tsx
```

在此巢狀樹狀結構中，`app/route.tsx` 檔案是佈局路由的配置，包裹了兩個子路由 `app/dashboard.tsx` 和 `app/settings.tsx`。

佈局路由還讓您能為動態路由區段強制執行元件和載入器邏輯：

```
routes/
├── app/users/
│   ├── $userId/
|   |   ├── route.tsx
|   |   ├── index.tsx
|   |   ├── edit.tsx
```

## 無路徑佈局路由

與[佈局路由](#layout-routes)類似，無路徑佈局路由用於以額外的元件和邏輯包裹子路由。然而，無路徑佈局路由不需要在 URL 中匹配 `path`，而是用於在不需匹配 URL 中 `path` 的情況下，以額外的元件和邏輯包裹子路由。

無路徑佈局路由會以前綴底線 (`_`) 標示它們是「無路徑」的。

> 🧠 `_` 前綴後的路徑部分會作為路由的 ID，這是必需的，因為每個路由都必須能唯一識別，尤其是在使用 TypeScript 時，以避免型別錯誤並有效實現自動完成。

讓我們來看一個名為 `_pathlessLayout.tsx` 的路由範例：

```
routes/
├── _pathlessLayout.tsx
├── _pathlessLayout.a.tsx
├── _pathlessLayout.b.tsx
```

在上面的樹狀結構中，`_pathlessLayout.tsx` 是一個無路徑佈局路由，包裹了兩個子路由 `_pathlessLayout.a.tsx` 和 `_pathlessLayout.b.tsx`。

`_pathlessLayout.tsx` 路由用於以無路徑佈局元件包裹子路由：

```tsx
import { Outlet, createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/_pathlessLayout')({
  component: PathlessLayoutComponent,
})

function PathlessLayoutComponent() {
  return (
    <div>
      <h1>無路徑佈局</h1>
      <Outlet />
    </div>
  )
}
```

下表顯示基於 URL 會渲染哪些元件：

| URL 路徑 | 元件                  |
| -------- | --------------------- |
| `/`      | `<Index>`             |
| `/a`     | `<PathlessLayout><A>` |
| `/b`     | `<PathlessLayout><B>` |

由於 TanStack Router 支援混合扁平與目錄式路由，您也可以使用目錄中的無路徑佈局路由來表達應用程式的路由：

```
routes/
├── _pathlessLayout/
│   ├── route.tsx
│   ├── a.tsx
│   ├── b.tsx
```

然而，與佈局路由不同，由於無路徑佈局路由不會基於 URL 路徑區段進行匹配，這意味著這些路由不支援在其路徑中使用[動態路由區段](#dynamic-route-segments)，因此無法在 URL 中匹配。

這表示您不能這樣做：

```
routes/
├── _$postId/ ❌
│   ├── ...
```

而是必須這樣做：

```
routes/
├── $postId/
├── _postPathlessLayout/ ✅
│   ├── ...
```

## 非巢狀路由

非巢狀路由可以透過在父檔案路由區段後綴 `_` 來建立，用於**解除**路由與其父路由的巢狀關係，並渲染自己的元件樹。

考慮以下扁平路由樹：

```
routes/
├── posts.tsx
├── posts.$postId.tsx
├── posts_.$postId.edit.tsx
```

下表顯示基於 URL 會渲染哪些元件：

| URL 路徑          | 元件                         |
| ----------------- | ---------------------------- |
| `/posts`          | `<Posts>`                    |
| `/posts/123`      | `<Posts><Post postId="123">` |
| `/posts/123/edit` | `<PostEditor postId="123">`  |

- `posts.$postId.tsx` 路由會正常巢狀在 `posts.tsx` 路由下，並渲染 `<Posts><Post>`。
- `posts_.$postId.edit.tsx` 路由**不共享**與其他路由相同的 `posts` 前綴，因此會被視為頂層路由並渲染 `<PostEditor>`。

## 從路由中排除檔案和資料夾

檔案和資料夾可以透過在檔案名稱前加上 `-` 前綴來排除於路由生成之外。這讓您能將邏輯共置於路由目錄中。

考慮以下路由樹：

```
routes/
├── posts.tsx
├── -posts-table.tsx // 👈🏼 被忽略
├── -components/ // 👈🏼 被忽略
│   ├── header.tsx // 👈🏼 被忽略
│   ├── footer.tsx // 👈🏼 被忽略
│   ├── ...
```

我們可以從被排除的檔案中匯入到 posts 路由

```tsx
import { createFileRoute } from '@tanstack/react-router'
import { PostsTable } from './-posts-table'
import { PostsHeader } from './-components/header'
import { PostsFooter } from './-components/footer'

export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  component: PostComponent,
})

function PostComponent() {
  const posts = Route.useLoaderData()

  return (
    <div>
      <PostsHeader />
      <PostsTable posts={posts} />
      <PostsFooter />
    </div>
  )
}
```

被排除的檔案不會被加入 `routeTree.gen.ts`。

## 無路徑路由群組目錄

無路徑路由群組目錄使用 `()` 作為一種方式來將路由檔案分組，而不論其路徑為何。它們純粹是組織性的，不會以任何方式影響路由樹或元件樹。

```
routes/
├── index.tsx
├── (app)/
│   ├── dashboard.tsx
│   ├── settings.tsx
│   ├── users.tsx
├── (auth)/
│   ├── login.tsx
│   ├── register.tsx
```

在上面的範例中，`app` 和 `auth` 目錄純粹是組織性的，不會以任何方式影響路由樹或元件樹。它們用於將相關路由分組，以便更容易導航和組織。

下表顯示基於 URL 會渲染哪些元件：

| URL 路徑     | 元件          |
| ------------ | ------------- |
| `/`          | `<Index>`     |
| `/dashboard` | `<Dashboard>` |
| `/settings`  | `<Settings>`  |
| `/users`     | `<Users>`     |
| `/login`     | `<Login>`     |
| `/register`  | `<Register>`  |

如您所見，`app` 和 `auth` 目錄純粹是組織性的，不會以任何方式影響路由樹或元件樹。
