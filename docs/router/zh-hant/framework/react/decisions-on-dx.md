---
source-updated-at: '2025-02-25T23:33:22.000Z'
translation-updated-at: '2025-05-08T20:16:44.637Z'
title: 開發體驗決策
---

當開發者初次使用 TanStack Router 時，通常會圍繞以下主題產生許多疑問：

> 為什麼必須以這種方式處理？

> 為什麼採用這種做法而非其他方式？

> 我習慣於某種做法，為何需要改變？

這些都是合理的問題。大多數情況下，人們習慣使用的路由函式庫都極為相似——它們具有相似的 API、相似的概念以及相似的操作方式。

但 TanStack Router 與眾不同。它既非典型的路由函式庫，也非普通的狀態管理工具，更不是任何常見的解決方案。

## TanStack Router 的起源故事

必須理解的是，TanStack Router 的誕生源自 [Nozzle.io](https://nozzle.io) 的需求——他們需要一個客戶端路由解決方案，既能提供頂級的 _URL 搜尋參數_ 體驗，又能滿足複雜儀表板所需的 **_型別安全_** 要求。

因此，從 TanStack Router 設計之初，每個細節都經過精心考量，以確保其型別安全性和開發者體驗無可匹敵。

## TanStack Router 如何實現這一切？

> TypeScript！TypeScript！TypeScript！

TanStack Router 的每個面向都設計為極致型別安全，這透過充分運用 TypeScript 的型別系統來實現。這涉及使用一些非常進階且複雜的型別、型別推論等功能，以確保開發者體驗盡可能流暢。

但為了達成這個目標，我們必須做出一些偏離路由領域常規的決策。

1. [**路由配置樣板程式碼？**](#1-為什麼路由器的配置採用這種方式)：你必須以允許 TypeScript 最大限度推論路由型別的方式定義路由。
2. [**為路由器宣告 TypeScript 模組？**](#2-宣告路由器實例以進行型別推論)：你必須透過 TypeScript 的模組宣告將 `Router` 實例傳遞給應用程式的其他部分。
3. [**為何提倡基於檔案的路由而非基於程式碼？**](#3-為什麼基於檔案的路由是定義路由的首選方式)：我們提倡將基於檔案的路由作為定義路由的首選方法。

> 簡而言之；TanStack Router 在開發者體驗方面的所有設計決策，都是為了讓你在不犧牲路由配置的控制力、靈活性與可維護性的前提下，獲得頂級的型別安全體驗。

## 1. 為什麼路由器的配置採用這種方式？

當你想充分發揮 TypeScript 的推論功能時，會很快意識到 _泛型_ 是你最好的夥伴。因此，TanStack Router 大量使用泛型來確保路由型別能被最大限度推論。

這意味著你必須以允許 TypeScript 最大限度推論路由型別的方式定義路由。

> 我能使用 JSX 定義路由嗎？

使用 JSX 定義路由是 **不可行的**，因為 TypeScript 將無法推論路由器的路由配置型別。

```tsx
// ⛔️ 這不可行
function App() {
  return (
    <Router>
      <Route path="/posts" component={PostsPage} />
      <Route path="/posts/$postId" component={PostIdPage} />
      {/* ... */}
    </Router>
    // ^? TypeScript 無法推論此配置中的路由
  )
}
```

由於這意味著你必須手動為 `<Link>` 元件的 `to` 屬性添加型別，且無法在執行時捕獲錯誤，因此這不是可行的選擇。

> 或許我可以將路由定義為巢狀物件樹？

```tsx
// ⛔️ 這個檔案只會不斷膨脹...
const router = createRouter({
  routes: {
    posts: {
      component: PostsPage, // /posts
      children: {
        $postId: {
          component: PostIdPage, // /posts/$postId
        },
      },
    },
    // ...
  },
})
```

乍看之下，這似乎是個好主意。它能讓你一目了然地視覺化整個路由層級結構。但這種方法有幾個重大缺點，使其不適合大型應用：

- **擴展性不佳**：隨著應用增長，這棵樹會不斷擴大並變得難以管理。由於所有內容都在單一檔案中定義，維護會變得非常困難。
- **不利於程式碼分割**：你必須手動分割每個元件，然後將其傳遞給路由的 `component` 屬性，這會使路由配置更加複雜，導致路由配置檔案不斷膨脹。

當你開始使用路由器的更多功能時（如巢狀上下文、載入器、搜尋參數驗證等），情況只會變得更糟。

> 那麼，定義路由的最佳方式是什麼？

我們發現的最佳實踐是將路由配置的定義抽象到路由樹之外。然後將你的路由配置組合成一個連貫的路由樹，並傳遞給 `createRouter` 函式。

你可以閱讀 [基於程式碼的路由](./routing/code-based-routing.md) 了解更多關於這種定義路由的方式。

> [!TIP]
> 覺得基於程式碼的路由有些繁瑣？了解為何 [基於檔案的路由](#3-為什麼基於檔案的路由是定義路由的首選方式) 是定義路由的首選方法。

## 2. 宣告路由器實例以進行型別推論

> 為什麼必須宣告 `Router`？

> 這些宣告對我來說太複雜了...

一旦你將路由組合成樹狀結構並正確使用泛型將其傳遞給路由器實例（透過 `createRouter`），接下來你需要將這些資訊傳遞給應用程式的其他部分。

我們考慮了兩種實現方式：

1. **匯入**：你可以從建立路由器實例的檔案中匯入 `Router` 實例，並直接在元件中使用它。

```tsx
import { router } from '@/src/app'
export const PostsIdLink = () => {
  return (
    <Link<typeof router> to="/posts/$postId" params={{ postId: '123' }}>
      前往文章 123
    </Link>
  )
}
```

這種方法的缺點是，你必須在每個需要使用它的檔案中匯入整個 `Router` 實例。這可能導致套件體積增加，管理起來也相當麻煩，且隨著應用增長和使用更多路由器功能，情況只會惡化。

2. **模組宣告**：你可以使用 TypeScript 的模組宣告來宣告 `Router` 實例作為一個模組，這樣可以在應用程式的任何地方進行型別推論，而無需匯入它。

你只需在應用程式中進行一次這樣的宣告。

```tsx
// src/app.tsx
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}
```

然後你可以在應用程式的任何地方享受其自動完成功能，而無需匯入它。

```tsx
export const PostsIdLink = () => {
  return (
    <Link
      to="/posts/$postId"
      // ^? TypeScript 會為你自動完成
      params={{ postId: '123' }} // 這個也是！
    >
      前往文章 123
    </Link>
  )
}
```

我們選擇了 **模組宣告**，因為這是我們發現最具擴展性和可維護性的方法，且具有最少的開銷和樣板程式碼。

## 3. 為什麼基於檔案的路由是定義路由的首選方式？

> 為什麼文件提倡基於檔案的路由？

> 我習慣於在單一檔案中定義路由，為何需要改變？

你會注意到（很快）在 TanStack Router 的文件中，我們提倡 **基於檔案的路由** 作為定義路由的首選方法。這是因為我們發現基於檔案的路由是最具擴展性和可維護性的路由定義方式。

> [!TIP]
> 在繼續之前，請確保你充分理解 [基於程式碼的路由](./routing/code-based-routing.md) 和 [基於檔案的路由](./routing/file-based-routing.md)。

如前所述，TanStack Router 是為需要高度型別安全性和可維護性的複雜應用而設計的。為了實現這一點，路由器的配置採用了精確的方式，以允許 TypeScript 最大限度推論路由型別。

TanStack Router 在設定 _基本_ 應用時的一個關鍵差異是，你的路由配置需要為 `getParentRoute` 提供一個函式，該函式回傳當前路由的父路由。

```tsx
import { createRoute } from '@tanstack/react-router'
import { postsRoute } from './postsRoute'

export const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '/',
})
```

在這個階段，這樣做是為了讓 `postsIndexRoute` 的定義能夠感知其在路由樹中的位置，並正確推論父路由回傳的 `context`、`path params`、`search params` 型別。錯誤定義 `getParentRoute` 函式意味著子路由將無法正確推論父路由的屬性。

因此，這是路由配置的關鍵部分，如果操作不當，將成為失敗點。

但這只是設定基本應用的一部分。TanStack Router 要求所有路由（包括根路由）必須組合成一個 **_路由樹_**，以便在宣告模組上的 `Router` 實例進行型別推論之前，將其傳遞給 `createRouter` 函式。這是路由配置的另一個關鍵部分，如果操作不當，將成為失敗點。

> 🤯 如果這個路由樹位於一個擁有約 40-50 個路由的應用程式的獨立檔案中，它很容易增長到 700 行以上。

```tsx
const routeTree = rootRoute.addChildren([
  postsRoute.addChildren([postsIndexRoute, postsIdRoute]),
])
```

這種複雜性只會隨著你開始使用路由器的更多功能（如巢狀上下文、載入器、搜尋參數驗證等）而增加。因此，在單一檔案中定義路由變得不可行。最終，使用者會建立自己的 _半一致_ 方式在多個檔案中定義路由，這可能導致路由配置的不一致和錯誤。

最後是程式碼分割的問題。隨著應用增長，你會希望分割程式碼以減少應用的初始套件體積。當你在單一檔案甚至多個檔案中定義路由時，管理起來可能會有些頭痛。

```tsx
import { createRoute, lazyRouteComponent } from '@tanstack/react-router'
import { postsRoute } from './postsRoute'

export const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '/',
  component: lazyRouteComponent(() => import('../page-components/posts/index')),
})
```

所有這些樣板程式碼，無論對於提供頂級型別推論體驗多麼重要，都可能讓人感到不知所措，並導致路由配置的不一致和錯誤。

... 而這個範例配置僅用於渲染單一程式碼分割路由。想像一下要為 40-50 個路由進行這樣的配置。現在請記住，你還沒有觸及 `context`、`loaders`、`search param validation` 以及路由器的其他功能 🤕。

> 那麼，為何基於檔案的路由是首選方式？

TanStack Router 的基於檔案的路由旨在解決所有這些問題。它允許你以可預測的方式定義路由，這種方式易於管理和維護，並能隨著應用增長而擴展。

基於檔案的路由方法由 TanStack Router Bundler Plugin 提供支援。它執行了三個基本任務，解決了使用基於程式碼的路由時在路由配置中的痛點：

1. **路由配置樣板程式碼**：它為你的路由配置生成樣板程式碼。
2. **路由樹組合**：它將你的路由配置組合成一個連貫的路由樹。同時在後台，它會正確更新路由配置以定義 `getParentRoute` 函式，將路由與其父路由匹配。
3. **程式碼分割**：它自動分割你的路由內容元件，並使用正確的元件更新路由配置。此外，在執行時，它確保在訪問路由時載入正確的元件。

讓我們看看之前範例的路由配置在使用基於檔案的路由時會是什麼樣子。

```tsx
// src/routes/posts/index.ts
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/')({
  component: () => 'Posts index component goes here!!!',
})
```

就這樣！無需擔心定義 `getParentRoute` 函式、組合路由樹或分割程式碼元件。TanStack Router Bundler Plugin 為你處理這一切。

TanStack Router Bundler Plugin 絕不會剝奪你對路由配置的控制權。它設計得盡可能靈活，允許你以適合應用的方式定義路由，同時減少路由配置的樣板程式碼和複雜性。

查看 [基於檔案的路由](./routing/file-based-routing.md) 和 [程式碼分割](./routing/code-splitting.md) 的指南，以更深入了解它們在 TanStack Router 中的運作方式。
