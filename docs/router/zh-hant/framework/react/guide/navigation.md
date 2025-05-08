---
source-updated-at: '2025-03-09T10:56:27.000Z'
translation-updated-at: '2025-05-08T23:40:21.176Z'
title: 導航
---

## 一切都是相對的

信不信由你，應用程式中的每一次導航都是**相對的**，即使你沒有使用明確的相對路徑語法（`../../somewhere`）。每當點擊連結或進行命令式導航呼叫時，你總是會有一個**來源**路徑和一個**目的地**路徑，這意味著你正在從一個路由**導航到**另一個路由。

TanStack Router 在每次導航時都考慮了這種相對導航的固定概念，因此你會在 API 中不斷看到兩個屬性：

- `from` - 來源路由路徑
- `to` - 目的地路由路徑

> ⚠️ 如果未提供 `from` 路由路徑，路由器會假設你正在從根 `/` 路由導航，並且只會自動補全絕對路徑。畢竟，你需要知道從哪裡來，才能知道要去哪裡 😉。

## 共享導航 API

TanStack Router 中的每個導航和路由匹配 API 都使用相同的核心介面，根據 API 的不同會有細微差異。這意味著你可以一次性學習導航和路由匹配，並在整個函式庫中使用相同的語法和概念。

### `ToOptions` 介面

這是每個導航和路由匹配 API 使用的核心 `ToOptions` 介面：

```ts
type ToOptions<
  TRouteTree extends AnyRoute = AnyRoute,
  TFrom extends RoutePaths<TRouteTree> | string = string,
  TTo extends string = '',
> = {
  // `from` 是一個可選的路由 ID 或路徑。如果未提供，則只有絕對路徑會自動補全並保持類型安全。通常會提供你正在渲染的來源路由的 route.fullPath 以方便使用。如果你不知道來源路由，可以留空並使用絕對路徑或不安全的相對路徑。
  from: string
  // `to` 可以是一個絕對路由路徑或從 `from` 選項到有效路由路徑的相對路徑。⚠️ 不要將路徑參數、哈希或搜尋參數插值到 `to` 選項中。請使用 `params`、`search` 和 `hash` 選項代替。
  to: string
  // `params` 是一個要插值到 `to` 選項中的路徑參數物件，或是一個提供先前參數並允許你返回新參數的函式。這是將動態參數插值到最終 URL 的唯一方法。根據 `from` 和 `to` 路由，你可能需要提供無、部分或全部路徑參數。TypeScript 會在有需要時通知你所需的參數。
  params:
    | Record<string, unknown>
    | ((prevParams: Record<string, unknown>) => Record<string, unknown>)
  // `search` 是一個查詢參數物件，或是一個提供先前搜尋並允許你返回新搜尋的函式。根據 `from` 和 `to` 路由，你可能需要提供無、部分或全部查詢參數。TypeScript 會在有需要時通知你所需的搜尋參數。
  search:
    | Record<string, unknown>
    | ((prevSearch: Record<string, unknown>) => Record<string, unknown>)
  // `hash` 是一個字串，或是一個提供先前哈希並允許你返回新哈希的函式。
  hash?: string | ((prevHash: string) => string)
  // `state` 是一個狀態物件，或是一個提供先前狀態並允許你返回新狀態的函式。狀態存儲在 history API 中，可用於在路由之間傳遞你不想永久存儲在 URL 搜尋參數中的數據。
  state?:
    | Record<string, any>
    | ((prevState: Record<string, unknown>) => Record<string, unknown>)
}
```

> 🧠 每個路由物件都有一個 `to` 屬性，可用作任何導航或路由匹配 API 的 `to`。在可能的情況下，這將允許你避免使用純字串，而是使用類型安全的路由引用：

```tsx
import { Route as aboutRoute } from './routes/about.tsx'

function Comp() {
  return <Link to={aboutRoute.to}>About</Link>
}
```

### `NavigateOptions` 介面

這是擴展 `ToOptions` 的核心 `NavigateOptions` 介面。任何實際執行導航的 API 都會使用此介面：

```ts
export type NavigateOptions<
  TRouteTree extends AnyRoute = AnyRoute,
  TFrom extends RoutePaths<TRouteTree> | string = string,
  TTo extends string = '',
> = ToOptions<TRouteTree, TFrom, TTo> & {
  // `replace` 是一個布林值，決定導航是否應替換當前的歷史記錄條目或推送一個新條目。
  replace?: boolean
  // `resetScroll` 是一個布林值，決定在位置提交到瀏覽器歷史記錄後，滾動位置是否應重置為 0,0。
  resetScroll?: boolean
  // `hashScrollIntoView` 是一個布林值或物件，決定在位置提交到歷史記錄後，是否將與哈希匹配的 ID 滾動到視圖中。
  hashScrollIntoView?: boolean | ScrollIntoViewOptions
  // `viewTransition` 是一個布林值或函式，決定在導航時瀏覽器是否以及如何調用 document.startViewTransition()。
  viewTransition?: boolean | ViewTransitionOptions
  // `ignoreBlocker` 是一個布林值，決定導航是否應忽略可能阻止它的任何攔截器。
  ignoreBlocker?: boolean
  // `reloadDocument` 是一個布林值，決定導航到路由器內部的路由是否會觸發完整頁面加載，而不是傳統的 SPA 導航。
  reloadDocument?: boolean
  // `href` 是一個字串，可以用來代替 `to` 導航到一個完整構建的 href，例如指向外部目標。
  href?: string
}
```

### `LinkOptions` 介面

任何實際的 `<a>` 標籤都會使用擴展 `NavigateOptions` 的 `LinkOptions` 介面：

```tsx
export type LinkOptions<
  TRouteTree extends AnyRoute = AnyRoute,
  TFrom extends RoutePaths<TRouteTree> | string = string,
  TTo extends string = '',
> = NavigateOptions<TRouteTree, TFrom, TTo> & {
  // 標準的錨點標籤 target 屬性
  target?: HTMLAnchorElement['target']
  // 預設為 `{ exact: false, includeHash: false }`
  activeOptions?: {
    exact?: boolean
    includeHash?: boolean
    includeSearch?: boolean
    explicitUndefined?: boolean
  }
  // 如果設置，將在懸停時預加載連結的路由，並快取指定的毫秒數，希望用戶最終會導航到那裡。
  preload?: false | 'intent'
  // 將意圖預加載延遲指定的毫秒數。如果意圖在此延遲之前退出，預加載將被取消。
  preloadDelay?: number
  // 如果為 true，將渲染不帶 href 屬性的連結
  disabled?: boolean
}
```

## 導航 API

現在了解了相對導航和所有介面，讓我們來談談你可以使用的不同風格的導航 API：

- `<Link>` 元件
  - 生成一個帶有有效 `href` 的實際 `<a>` 標籤，可以點擊甚至 cmd/ctrl + 點擊在新標籤頁中打開
- `useNavigate()` 鉤子
  - 在可能的情況下，應使用 `Link` 元件進行導航，但有時你需要作為副作用的一部分進行命令式導航。`useNavigate` 返回一個可以調用以執行即時客戶端導航的函式。
- `<Navigate>` 元件
  - 不渲染任何內容並執行即時客戶端導航。
- `Router.navigate()` 方法
  - 這是 TanStack Router 中最強大的導航 API。與 `useNavigate` 類似，它以命令方式導航，但在你可以訪問路由器的任何地方都可用。

⚠️ 這些 API 都不是伺服器端重定向的替代品。如果你需要在掛載應用程式之前立即將用戶從一個路由重定向到另一個路由，請使用伺服器端重定向而不是客戶端導航。

## `<Link>` 元件

`Link` 元件是在應用程式內導航的最常見方式。它渲染一個帶有有效 `href` 屬性的實際 `<a>` 標籤，可以點擊甚至 cmd/ctrl + 點擊在新標籤頁中打開。它還支持任何正常的 `<a>` 屬性，包括在新視窗中打開連結的 `target` 等。

除了 [`LinkOptions`](#linkoptions-介面) 介面外，`Link` 元件還支持以下屬性：

```tsx
export type LinkProps<
  TFrom extends RoutePaths<RegisteredRouter['routeTree']> | string = string,
  TTo extends string = '',
> = LinkOptions<RegisteredRouter['routeTree'], TFrom, TTo> & {
  // 一個返回此連結 `active` 狀態的附加屬性的函式。這些屬性會覆蓋傳遞給連結的其他屬性（`style` 會合併，`className` 會串接）
  activeProps?:
    | FrameworkHTMLAnchorTagAttributes
    | (() => FrameworkHTMLAnchorAttributes)
  // 一個返回此連結 `inactive` 狀態的附加屬性的函式。這些屬性會覆蓋傳遞給連結的其他屬性（`style` 會合併，`className` 會串接）
  inactiveProps?:
    | FrameworkHTMLAnchorAttributes
    | (() => FrameworkHTMLAnchorAttributes)
}
```

### 絕對連結

讓我們創建一個簡單的靜態連結！

```tsx
import { Link } from '@tanstack/react-router'

const link = <Link to="/about">About</Link>
```

### 動態連結

動態連結是其中包含動態段落的連結。例如，指向部落格文章的連結可能如下所示：

```tsx
const link = (
  <Link
    to="/blog/post/$postId"
    params={{
      postId: 'my-first-blog-post',
    }}
  >
    Blog Post
  </Link>
)
```

請記住，通常動態段落參數是 `string` 值，但它們也可以是你解析的任何其他類型。無論如何，類型將在編譯時檢查以確保你傳遞了正確的類型。

### 相對連結

預設情況下，除非提供了 `from` 路由路徑，否則所有連結都是絕對的。這意味著上面的連結將始終導航到 `/about` 路由，無論你當前在哪個路由上。

如果你想創建一個相對於當前路由的連結，可以提供一個 `from` 路由路徑：

```tsx
const postIdRoute = createRoute({
  path: '/blog/post/$postId',
})

const link = (
  <Link from={postIdRoute.fullPath} to="../categories">
    Categories
  </Link>
)
```

如上所示，通常會提供 `route.fullPath` 作為 `from` 路由路徑。這是因為 `route.fullPath` 是一個引用，如果你重構應用程式，它會更新。然而，有時無法直接導入路由，在這種情況下，直接提供路由路徑作為字串也是可以的。它仍然會像往常一樣進行類型檢查！

### 搜尋參數連結

搜尋參數是為路由提供額外上下文的好方法。例如，你可能想為搜尋頁面提供搜尋查詢：

```tsx
const link = (
  <Link
    to="/search"
    search={{
      query: 'tanstack',
    }}
  >
    Search
  </Link>
)
```

通常還希望在不提供有關現有路由的任何其他信息的情況下更新單個搜尋參數。例如，你可能想更新搜尋結果的頁碼：

```tsx
const link = (
  <Link
    to="."
    search={(prev) => ({
      ...prev,
      page: prev.page + 1,
    })}
  >
    Next Page
  </Link>
)
```

### 搜尋參數類型安全

搜尋參數是一種高度動態的狀態管理機制，因此確保你傳遞了正確的類型給搜尋參數非常重要。我們將在後面的部分詳細介紹如何驗證和確保搜尋參數的類型安全，以及其他很棒的功能！

### 哈希連結

哈希連結是連結到頁面特定部分的好方法。例如，你可能想連結到部落格文章的特定部分：

```tsx
const link = (
  <Link
    to="/blog/post/$postId"
    params={{
      postId: 'my-first-blog-post',
    }}
    hash="section-1"
  >
    Section 1
  </Link>
)
```

### 活動和非活動屬性

`Link` 元件支持兩個附加屬性：`activeProps` 和 `inactiveProps`。這些屬性是返回連結 `active` 和 `inactive` 狀態的附加屬性的函式。除了樣式和類之外，這裡傳遞的所有屬性都會覆蓋傳遞給 `Link` 的原始屬性。傳遞的任何樣式或類都會合併在一起。

這是一個例子：

```tsx
const link = (
  <Link
    to="/blog/post/$postId"
    params={{
      postId: 'my-first-blog-post',
    }}
    activeProps={{
      style: {
        fontWeight: 'bold',
      },
    }}
  >
    Section 1
  </Link>
)
```

### `data-status` 屬性

除了 `activeProps` 和 `inactiveProps` 屬性外，`Link` 元件還會在渲染的元素處於活動狀態時添加一個 `data-status` 屬性。根據連結的當前狀態，此屬性將為 `active` 或 `undefined`。如果你更喜歡使用數據屬性而不是屬性來設置連結樣式，這會很方便。

### 活動選項

`Link` 元件帶有一個 `activeOptions` 屬性，提供了一些確定連結是否處於活動狀態的選項。以下介面描述了這些選項：

```tsx
export interface ActiveOptions {
  // 如果為 true，則僅當當前路由完全匹配 `to` 路由路徑（無子路由）時，連結才會處於活動狀態
  // 預設為 `false`
  exact?: boolean
  // 如果為 true，則僅當當前 URL 哈希與 `hash` 屬性匹配時，連結才會處於活動狀態
  // 預設為 `false`
  includeHash?: boolean // 預設為 false
  // 如果為 true，則僅當當前 URL 搜尋參數包含 `search` 屬性時，連結才會處於活動狀態
  // 預設為 `true`
  includeSearch?: boolean
  // 這會修改 `includeSearch` 行為。
  // 如果為 true，則 `search` 中明確為 `undefined` 的屬性必須不存在於當前 URL 搜尋參數中，連結才會處於活動狀態。
  // 預設為 `false`
  explicitUndefined?: boolean
}
```

預設情況下，它會檢查結果的 **pathname** 是否是當前路由的前綴。如果提供了任何搜尋參數，它將檢查它們是否與當前位置中的參數*包含性*匹配。預設情況下不檢查哈希。

例如，如果你在 `/blog/post/my-first-blog-post` 路由上，以下連結將處於活動狀態：

```tsx
const link1 = (
  <Link to="/blog/post/$postId" params={{ postId: 'my-first-blog-post' }}>
    Blog Post
  </Link>
)
const link2 = <Link to="/blog/post">Blog Post</Link>
const link3 = <Link to="/blog">Blog Post</Link>
```

然而，以下連結將不會處於活動狀態：

```tsx
const link4 = (
  <Link to="/blog/post/$postId" params={{ postId: 'my-second-blog-post' }}>
    Blog Post
  </Link>
)
```

有些連結僅在完全匹配時才處於活動狀態是很常見的。一個很好的例子是指向首頁的連結。在這種情況下，你可以傳遞 `exact: true` 選項：

```tsx
const link = (
  <Link to="/" activeOptions={{ exact: true }}>
    Home
  </Link>
)
```

這將確保當你在子路由時連結不會處於活動狀態。

還有一些需要注意的選項：

- 如果你想在匹配中包含哈希，可以傳遞 `includeHash: true` 選項
- 如果你**不**想在匹配中包含搜尋參數，可以傳遞 `includeSearch: false` 選項

### 將 `isActive` 傳遞給子元素

`Link` 元件接受一個函式作為其子元素，允許你將其 `isActive` 屬性傳遞給子元素。例如，你可以根據父連結是否處於活動狀態來設置子元件的樣式：

```tsx
const link = (
  <Link to="/blog/post">
    {({ isActive }) => {
      return (
        <>
          <span>My Blog Post</span>
          <icon className={isActive ? 'active' : 'inactive'} />
        </>
      )
    }}
  </Link>
)
```

### 連結預加載

`Link` 元件支持在意圖（目前是懸停或 touchstart）時自動預加載路由。這可以在路由器選項中配置為預設值（我們將很快詳細討論），或者通過將 `preload='intent'` 屬性傳遞給 `Link` 元件。這是一個例子：

```tsx
const link = (
  <Link to="/blog/post/$postId" preload="intent">
    Blog Post
  </Link>
)
```

啟用預加載並具有相對快速的異步路由依賴項（如果有），這個簡單的技巧可以以極少的努力提高應用程式的感知性能。

更好的是，通過使用像 `@tanstack/query` 這樣的快取優先函式庫，預加載的路由將保留並準備好用於過時-同時-重新驗證的體驗，如果用戶決定稍後導航到該路由。

### 連結預加載超時

與預加載一起的是一個可配置的超時時間，它決定了用戶必須懸停在連結上多長時間才能觸發基於意圖的預加載。預設超時時間為 50 毫秒，但你可以通過將 `preloadTimeout` 屬性傳遞給 `Link` 元件來更改
