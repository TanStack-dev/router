---
source-updated-at: '2025-02-25T23:33:22.000Z'
translation-updated-at: '2025-05-08T20:20:26.173Z'
title: 建立路由器
---

## `Router` 類別

當你準備開始使用路由時，需要建立一個新的 `Router` 實例。這個路由實例是 TanStack Router 的核心大腦，負責管理路由樹、匹配路由，以及協調導航和路由轉換。它同時也是配置全路由設定的地方。

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
})
```

## 路由樹

你可能很快會注意到 `Router` 建構函式需要一個 `routeTree` 選項。這是路由器用來匹配路由和渲染元件的路由樹。

無論你使用[檔案式路由](../routing/file-based-routing.md)還是[程式碼式路由](../routing/code-based-routing.md)，都需要將你的路由樹傳遞給 `createRouter` 函式：

### 檔案系統路由樹

如果你使用我們推薦的檔案式路由，那麼生成的路由樹檔案可能預設位於 `src/routeTree.gen.ts`。如果使用了自訂位置，則需要從該位置導入你的路由樹。

```tsx
import { routeTree } from './routeTree.gen'
```

### 程式碼式路由樹

如果你使用程式碼式路由，那麼可能是透過根路由的 `addChildren` 方法手動建立了路由樹：

```tsx
const routeTree = rootRoute.addChildren([
  // ...
])
```

## 路由型別安全

> [!IMPORTANT]
> 請勿跳過此部分！⚠️

TanStack Router 為 TypeScript 提供了驚人的支援，甚至包括你意想不到的功能，例如直接從函式庫導入的裸模組！為了實現這一點，你必須使用 TypeScript 的[宣告合併 (Declaration Merging)](https://www.typescriptlang.org/docs/handbook/declaration-merging.html) 功能來註冊路由器的型別。具體做法是擴展 `@tanstack/react-router` 上的 `Register` 介面，新增一個 `router` 屬性，其型別為你的 `router` 實例：

```tsx
declare module '@tanstack/react-router' {
  interface Register {
    // 這會推斷我們路由器的型別，並在整個專案中註冊它
    router: typeof router
  }
}
```

註冊路由器後，你將在整個專案中獲得與路由相關的任何操作的型別安全。

## 404 未找到路由

如先前指南所承諾的，我們現在來介紹 `notFoundRoute` 選項。此選項用於配置當找不到其他合適匹配時將渲染的路由。這對於渲染 404 頁面或重定向到預設路由非常有用。

如果你使用檔案式或程式碼式路由，則需要在 `createRootRoute` 中添加 `notFoundComponent` 鍵：

```tsx
export const Route = createRootRoute({
  component: () => (
    // ...
  ),
  notFoundComponent: () => <div>404 未找到</div>,
});
```

## 其他選項

還有許多其他選項可以傳遞給 `Router` 建構函式。你可以在 [API 參考](../api/router/RouterOptionsType.md) 中找到完整的列表。
