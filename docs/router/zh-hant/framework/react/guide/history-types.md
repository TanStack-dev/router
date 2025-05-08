---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:19:15.849Z'
title: 歷史記錄類型
---

雖然使用 TanStack Router 並不需要了解 `@tanstack/history` API 本身，但理解其運作原理是個好主意。在底層，TanStack Router 需要並使用一個 `history` 抽象層來管理路由歷史記錄。

如果沒有建立 history 實例，當路由器初始化時，會自動建立一個面向瀏覽器的 API 實例。如果需要特殊類型的 history API，可以使用 `@tanstack/history` 套件來建立自己的實例：

- `createBrowserHistory`：預設的 history 類型。
- `createHashHistory`：使用 hash 來追蹤歷史記錄的 history 類型。
- `createMemoryHistory`：將歷史記錄保存在記憶體中的 history 類型。

建立 history 實例後，可以將其傳遞給 `Router` 建構函式：

```ts
import { createMemoryHistory, createRouter } from '@tanstack/react-router'

const memoryHistory = createMemoryHistory({
  initialEntries: ['/'], // 傳入初始 URL
})

const router = createRouter({ routeTree, history: memoryHistory })
```

## 瀏覽器路由 (Browser Routing)

`createBrowserHistory` 是預設的 history 類型。它使用瀏覽器的 history API 來管理瀏覽器歷史記錄。

## Hash 路由 (Hash Routing)

如果伺服器不支援將 HTTP 請求重寫至 index.html（或其他沒有伺服器的環境），Hash 路由會很有幫助。

```ts
import { createHashHistory, createRouter } from '@tanstack/react-router'

const hashHistory = createHashHistory()

const router = createRouter({ routeTree, history: hashHistory })
```

## 記憶體路由 (Memory Routing)

記憶體路由適用於非瀏覽器環境，或當不希望元件與 URL 互動時。

```ts
import { createMemoryHistory, createRouter } from '@tanstack/react-router'

const memoryHistory = createMemoryHistory({
  initialEntries: ['/'], // 傳入初始 URL
})

const router = createRouter({ routeTree, history: memoryHistory })
```

關於伺服器端渲染 (SSR) 在伺服器上的使用方式，請參考 [SSR 指南](./ssr.md#server-history)。
