---
source-updated-at: '2025-02-27T22:43:21.000Z'
translation-updated-at: '2025-05-08T20:18:53.214Z'
title: 類型安全
---

TanStack Router 的設計目標是在 TypeScript 編譯器和運行時的限制下，盡可能實現型別安全。這意味著它不僅是用 TypeScript 編寫的，還會**完全推斷提供的型別，並將其貫穿於整個路由體驗中**。

最終，這代表開發者需要**編寫的型別更少**，並且隨著程式碼演進，能對其**擁有更高的信心**。

## 路由定義

### 基於檔案的路由 (File-based Routing)

路由是分層的，其定義也是如此。如果你使用基於檔案的路由，大部分型別安全已經為你處理好了。

### 基於程式碼的路由 (Code-based Routing)

如果你直接使用 `Route` 類別，則需要注意如何透過 `Route` 的 `getParentRoute` 選項確保路由被正確賦予型別。這是因為子路由需要知道**所有**父路由的型別。若沒有這樣做，那些從上三層的 _layout_ 和 _pathless layout_ 路由解析出來的寶貴搜尋參數 (search params) 就會消失在 JS 的虛空中。

所以，別忘了將父路由傳遞給子路由！

```tsx
const parentRoute = createRoute({
  getParentRoute: () => parentRoute,
})
```

## 匯出的鉤子 (Hooks)、元件和工具

為了讓路由器的型別能與頂層匯出如 `Link`、`useNavigate`、`useParams` 等一起運作，這些型別必須滲透 TypeScript 模組邊界並直接註冊到函式庫中。為此，我們在匯出的 `Register` 介面上使用宣告合併 (declaration merging)。

```ts
const router = createRouter({
  // ...
})

declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}
```

透過將路由器註冊到模組中，你現在可以使用匯出的鉤子、元件和工具，並享有路由器確切的型別。

## 解決元件上下文問題

元件上下文 (Component context) 是 React 和其他框架中用於向元件提供依賴項的強大工具。然而，如果該上下文在元件層級結構中移動時改變型別，TypeScript 將無法推斷這些變化。為了解決這個問題，基於上下文的鉤子和元件要求你提供提示，說明它們的使用方式和位置。

```tsx
export const Route = createFileRoute('/posts')({
  component: PostsComponent,
})

function PostsComponent() {
  // 每個路由都有 TanStack Router 中大多數內建鉤子的型別安全版本
  const params = Route.useParams()
  const search = Route.useSearch()

  // 有些鉤子需要來自*整個*路由器的上下文，而不僅是當前路由。為了實現型別安全，
  // 我們必須傳遞 `from` 參數來告訴鉤子我們在路由層級結構中的相對位置。
  const navigate = useNavigate({ from: Route.fullPath })
  // ... 等等
}
```

每個需要上下文提示的鉤子和元件都會有一個 `from` 參數，你可以在其中傳遞你正在渲染的路由的 ID 或路徑。

> 🧠 小技巧：如果你的元件是程式碼分割 (code-split) 的，可以使用 [getRouteApi 函式](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper) 來避免傳遞 `Route.fullPath` 以取得型別安全的 `useParams()` 和 `useSearch()` 鉤子。

### 如果我不知道路由怎麼辦？如果是共享元件呢？

`from` 屬性是可選的，這意味著如果你不傳遞它，路由器會根據可用的型別給出最佳猜測。通常，這代表你會得到路由器中所有路由型別的聯集 (union)。

### 如果我傳遞了錯誤的 `from` 路徑怎麼辦？

從技術上講，有可能傳遞一個滿足 TypeScript 的 `from`，但在運行時可能與你實際渲染的路徑不匹配。在這種情況下，每個支援 `from` 的鉤子和元件都會檢測你的預期是否與實際渲染的路由不匹配，並拋出運行時錯誤。

### 如果我不知道路由，或者它是共享元件，無法傳遞 `from` 怎麼辦？

如果你正在渲染一個跨多個路由共享的元件，或者正在渲染一個不在路由內的元件，可以傳遞 `strict: false` 而不是 `from` 選項。這不僅會消除運行時錯誤，還會為你呼叫的鉤子提供寬鬆但準確的型別。一個很好的例子是從共享元件呼叫 `useSearch`：

```tsx
function MyComponent() {
  const search = useSearch({ strict: false })
}
```

在這種情況下，`search` 變數會被賦予路由器中所有路由可能的搜尋參數的聯集型別。

## 路由器上下文 (Router Context)

路由器上下文極其有用，因為它是最終的分層依賴注入 (dependency injection)。你可以向路由器及其渲染的每個路由提供上下文。隨著你建立這個上下文，TanStack Router 會將其與路由的層級結構合併，使每個路由都能存取其所有父路由的上下文。

`createRootRouteWithContext` 工廠函式會建立一個帶有實例化型別的新路由器，這會要求你向路由器履行相同的型別合約，並確保你的上下文在整個路由樹中正確賦予型別。

```tsx
const rootRoute = createRootRouteWithContext<{ whateverYouWant: true }>()({
  component: App,
})

const routeTree = rootRoute.addChildren([
  // ... 所有子路由都能在上下文中存取 `whateverYouWant`
])

const router = createRouter({
  routeTree,
  context: {
    // 現在必須傳遞這個
    whateverYouWant: true,
  },
})
```

## 效能建議

隨著應用程式規模擴大，TypeScript 檢查時間自然會增加。當應用程式規模擴大時，有幾點需要注意以保持 TS 檢查時間在可控範圍內。

### 僅推斷你需要的型別

客戶端資料快取 (client side data caches)（如 TanStack Query 等）的一個好模式是預取資料。例如，在 TanStack Query 中，你可能有一個路由在 `loader` 中呼叫 `queryClient.ensureQueryData`。

```tsx
export const Route = createFileRoute('/posts/$postId/deep')({
  loader: ({ context: { queryClient }, params: { postId } }) =>
    queryClient.ensureQueryData(postQueryOptions(postId)),
  component: PostDeepComponent,
})

function PostDeepComponent() {
  const params = Route.useParams()
  const data = useSuspenseQuery(postQueryOptions(params.postId))

  return <></>
}
```

這看起來可能沒問題，對於小型路由樹，你可能不會注意到任何 TS 效能問題。然而在這種情況下，TS 必須推斷 loader 的返回型別，儘管它在你的路由中從未被使用。如果 loader 資料是一個複雜型別，並且有許多路由以這種方式預取，可能會降低編輯器效能。在這種情況下，改變很簡單，讓 TypeScript 推斷 `Promise<void>`。

```tsx
export const Route = createFileRoute('/posts/$postId/deep')({
  loader: async ({ context: { queryClient }, params: { postId } }) => {
    await queryClient.ensureQueryData(postQueryOptions(postId))
  },
  component: PostDeepComponent,
})

function PostDeepComponent() {
  const params = Route.useParams()
  const data = useSuspenseQuery(postQueryOptions(params.postId))

  return <></>
}
```

這樣 loader 資料永遠不會被推斷，並將推斷移出路由樹，直到你第一次使用 `useSuspenseQuery`。

### 盡可能縮小到相關路由

考慮以下 `Link` 的使用方式：

```tsx
<Link to=".." search={{ page: 0 }} />
<Link to="." search={{ page: 0 }} />
```

**這些例子對 TS 效能不利**。這是因為 `search` 解析為所有路由的所有 `search` 參數的聯集，TS 必須檢查你傳遞給 `search` 屬性的內容是否符合這個可能很大的聯集。隨著應用程式增長，這個檢查時間將隨著路由和搜尋參數的數量線性增加。我們已經盡力優化這種情況（TypeScript 通常會執行此工作一次並快取它），但對這個大型聯集的初始檢查仍然很耗時。這也適用於 `params` 和其他 API，如 `useSearch`、`useParams`、`useNavigate` 等。

相反，你應該嘗試透過 `from` 或 `to` 縮小到相關路由。

```tsx
<Link from={Route.fullPath} to=".." search={{page: 0}} />
<Link from="/posts" to=".." search={{page: 0}} />
```

記住，你總是可以傳遞一個聯集給 `to` 或 `from` 來縮小你感興趣的路由範圍。

```tsx
const from: '/posts/$postId/deep' | '/posts/' = '/posts/'
<Link from={from} to='..' />
```

你也可以傳遞分支給 `from`，僅解析 `search` 或 `params` 來自該分支的任何子路由：

```tsx
const from = '/posts'
<Link from={from} to='..' />
```

`/posts` 可能是一個分支，擁有許多共享相同 `search` 或 `params` 的子路由。

### 考慮使用 `addChildren` 的物件語法

路由通常具有 `params`、`search`、`loaders` 或 `context`，甚至可以引用外部依賴項，這些對 TS 推斷來說也很耗時。對於這類應用程式，使用物件建立路由樹比元組 (tuples) 更高效。

`createChildren` 也可以接受物件。對於具有複雜路由和外部函式庫的大型路由樹，物件在 TS 型別檢查上比大型元組快得多。效能提升取決於你的專案、外部依賴項以及這些函式庫型別的寫法。

```tsx
const routeTree = rootRoute.addChildren({
  postsRoute: postsRoute.addChildren({ postRoute, postsIndexRoute }),
  indexRoute,
})
```

注意這種語法更冗長，但有更好的 TS 效能。在基於檔案的路由中，路由樹是自動生成的，因此冗長的路由樹不是問題。

### 避免未縮小的內部型別

你可能會想重複使用暴露的型別。例如，你可能會想這樣使用 `LinkProps`：

```tsx
const props: LinkProps = {
  to: '/posts/',
}

return (
  <Link {...props}>
)
```

**這對 TS 效能非常不利**。問題在於 `LinkProps` 沒有型別參數，因此是一個非常大的型別。它包含 `search`，這是所有 `search` 參數的聯集；它包含 `params`，這是所有 `params` 的聯集。當將這個物件與 `Link` 合併時，會對這個巨大型別進行結構比較。

相反，你可以使用 `as const satisfies` 來推斷一個精確的型別，而不是直接使用 `LinkProps` 以避免巨大的檢查。

```tsx
const props = {
  to: '/posts/',
} as const satisfies LinkProps

return (
  <Link {...props}>
)
```

由於 `props` 不是 `LinkProps` 型別，因此這個檢查更便宜，因為型別更精確。你也可以透過縮小 `LinkProps` 進一步改善型別檢查。

```tsx
const props = {
  to: '/posts/',
} as const satisfies LinkProps<RegisteredRouter, string '/posts/'>

return (
  <Link {...props}>
)
```

這甚至更快，因為我們檢查的是縮小後的 `LinkProps` 型別。

你也可以使用這個方法將 `LinkProps` 縮小到特定型別，作為函式的屬性或參數使用。

```tsx
export const myLinkProps = [
  {
    to: '/posts',
  },
  {
    to: '/posts/$postId',
    params: { postId: 'postId' },
  },
] as const satisfies ReadonlyArray<LinkProps>

export type MyLinkProps = (typeof myLinkProps)[number]

const MyComponent = (props: { linkProps: MyLinkProps }) => {
  return <Link {...props.linkProps} />
}
```

這比在元件中直接使用 `LinkProps` 更快，因為 `MyLinkProps` 是一個更精確的型別。

另一個解決方案是不使用 `LinkProps`，而是提供反轉控制 (inversion of control) 來渲染一個縮小到特定路由的 `Link` 元件。渲染屬性 (render props) 是反轉控制給元件使用者的好方法。

```tsx
export interface MyComponentProps {
  readonly renderLink: () => React.ReactNode
}

const MyComponent = (props: MyComponentProps) => {
  return <div>{props.renderLink()}</div>
}

const Page = () => {
  return <MyComponent renderLink={() => <Link to="/absolute" />} />
}
```

這個特定例子非常快，因為我們已經將導航的控制反轉給元件的使用者。`Link` 被縮小到我們想要導航的確切路由。
