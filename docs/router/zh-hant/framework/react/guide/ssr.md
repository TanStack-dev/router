---
source-updated-at: '2025-02-26T10:16:23.000Z'
translation-updated-at: '2025-05-08T20:18:28.635Z'
id: ssr
title: 伺服器渲染 (SSR)
---

伺服器渲染 (Server Side Rendering, SSR) 是將元件在伺服器端渲染後，將 HTML 標記傳送至客戶端的過程。客戶端隨後會將標記水合 (hydrate) 為完全互動的元件。

通常需要考慮兩種不同的 SSR 模式：

- 非串流式 SSR (Non-streaming SSR)
  - 整個頁面在伺服器端渲染後，透過單一 HTML 請求傳送至客戶端，包含應用程式在客戶端水合所需的序列化資料。
- 串流式 SSR (Streaming SSR)
  - 頁面的關鍵首次渲染 (first paint) 會在伺服器端完成，並透過單一 HTML 請求傳送至客戶端，包含應用程式水合所需的序列化資料
  - 頁面其餘部分會隨著伺服器端渲染進度，逐步串流至客戶端。

本指南將說明如何使用 TanStack Router 實作這兩種 SSR 模式！

## 非串流式 SSR (Non-Streaming SSR)

非串流式伺服器渲染是傳統的 SSR 流程，會在伺服器端渲染完整應用程式頁面的標記，並將完整的 HTML 標記（與資料）傳送至客戶端。客戶端隨後會將標記水合為完全互動的應用程式。

要使用 TanStack Router 實作非串流式 SSR，你需要以下工具：

- `StartServer`（來自 `@tanstack/react-start/server`）
  - 例如：`<StartServer router={router} />`
  - 在伺服器入口渲染此元件時，會渲染你的應用程式並自動處理應用程式層級的水合/脫水 (hydration/dehydration)，同時實作 `Router` 上的 `Wrap` 元件選項
- `StartClient`（來自 `@tanstack/react-start`）
  - 例如：`<StartClient router={router} />`
  - 在客戶端入口渲染此元件時，會渲染你的應用程式並自動實作 `Router` 上的 `Wrap` 元件選項

### 路由器建立 (Router Creation)

由於你的路由器會同時存在於伺服器與客戶端，因此必須確保兩邊環境建立路由器的方式一致。最簡單的方法是透過共享檔案公開一個 `createRouter` 函式，讓伺服器與客戶端入口檔案都能匯入並呼叫。

- `src/router.tsx`

```tsx
import * as React from 'react'
import { createRouter as createTanstackRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

export function createRouter() {
  return createTanstackRouter({ routeTree })
}

declare module '@tanstack/react-router' {
  interface Register {
    router: ReturnType<typeof createRouter>
  }
}
```

現在你可以在伺服器與客戶端入口檔案中匯入此函式並建立路由器。

- `src/entry-server.tsx`

```tsx
import { createRouter } from './router'

export async function render(req, res) {
  const router = createRouter()
}
```

- `src/entry-client.tsx`

```tsx
import { createRouter } from './router'

const router = createRouter()
```

### 伺服器端歷史記錄 (Server History)

在客戶端，Router 預設使用 `createBrowserHistory` 的實例，這是客戶端首選的歷史記錄類型。但在伺服器端，你應該改用 `createMemoryHistory` 的實例，因為 `createBrowserHistory` 依賴 `window` 物件，而伺服器端並不存在此物件。

> 🧠 請確保使用伺服器正在渲染的 URL 來初始化你的記憶體歷史記錄。

- `src/entry-server.tsx`

```tsx
const router = createRouter()

const memoryHistory = createMemoryHistory({
  initialEntries: [opts.url],
})
```

建立記憶體歷史記錄實例後，你可以更新路由器以使用它。

- `src/entry-server.tsx`

```tsx
router.update({
  history: memoryHistory,
})
```

### 在伺服器端載入關鍵路由器資料 (Loading Critical Router Data on the Server)

為了在伺服器端渲染你的應用程式，你需要確保路由器已透過路由載入器 (route loaders) 載入所有關鍵資料。為此，你可以在渲染應用程式前使用 `await router.load()`。這會確實等待當前 URL 匹配的所有路由，並平行執行它們的 `loader` 函式。

- `src/entry-server.tsx`

```tsx
await router.load()
```

## 自動載入器脫水/水合 (Automatic Loader Dehydration/Hydration)

路由取得的已解析載入器資料會由 TanStack Router 自動脫水與再水合，只要你完成本指南所述的標準 SSR 步驟即可。

⚠️ 如果你使用延遲資料串流 (deferred data streaming)，還需要確保已實作本指南後半部的 [SSR 串流與串流轉換](#streaming-ssr) 模式。

有關如何使用資料載入與資料串流的更多資訊，請參閱 [資料載入](./data-loading.md) 和 [資料串流](../data-streaming) 指南。

### 在伺服器端渲染應用程式 (Rendering the Application on the Server)

現在你已擁有載入當前 URL 所有關鍵資料的路由器實例，可以在伺服器端渲染你的應用程式：

```tsx
// src/entry-server.tsx

const html = ReactDOMServer.renderToString(<StartServer router={router} />)
```

### 處理未找到錯誤 (Handling Not Found Errors)

路由器提供 `hasNotFoundMatch` 方法，用於檢查渲染過程中是否發生未找到錯誤。使用此方法來檢查是否發生此類錯誤，並相應設定回應狀態碼：

```tsx
// src/entry-server.tsx
if (router.hasNotFoundMatch()) statusCode = 404
```

### 完整範例 (All Together Now!)

以下是整合上述所有概念的完整伺服器入口檔案範例。

```tsx
// src/entry-server.tsx
import * as React from 'react'
import ReactDOMServer from 'react-dom/server'
import { createMemoryHistory } from '@tanstack/react-router'
import { StartServer } from '@tanstack/react-start/server'
import { createRouter } from './router'

export async function render(url, response) {
  const router = createRouter()

  const memoryHistory = createMemoryHistory({
    initialEntries: [url],
  })

  router.update({
    history: memoryHistory,
  })

  await router.load()

  const appHtml = ReactDOMServer.renderToString(<StartServer router={router} />)

  response.statusCode = router.hasNotFoundMatch() ? 404 : 200
  response.setHeader('Content-Type', 'text/html')
  response.end(`<!DOCTYPE html>${appHtml}`)
}
```

## 在客戶端渲染應用程式 (Rendering the Application on the Client)

在客戶端，事情簡單許多。

- 建立你的路由器實例
- 使用 `<StartClient />` 元件渲染你的應用程式

```tsx
// src/entry-client.tsx

import * as React from 'react'
import ReactDOM from 'react-dom/client'

import { StartClient } from '@tanstack/react-start'
import { createRouter } from './router'

const router = createRouter()

ReactDOM.hydrateRoot(document, <StartClient router={router} />)
```

透過此設定，你的應用程式將在伺服器端渲染後，於客戶端進行水合！

## 串流式 SSR (Streaming SSR)

串流式 SSR 是最現代的 SSR 模式，是隨著伺服器端渲染進度，持續且漸進地將 HTML 標記傳送至客戶端的過程。這在概念上與傳統 SSR 略有不同，因為除了能對關鍵首次渲染進行脫水與再水合外，優先級較低或回應時間較慢的標記與資料，可以在初始渲染後透過同一個請求串流至客戶端。

此模式對於具有慢速或高延遲資料獲取需求的頁面非常有用。例如，如果你的頁面需要從第三方 API 獲取資料，可以先將關鍵的初始標記與資料串流至客戶端，再將較不重要的第三方資料在解析後串流至客戶端。

**只要使用 `renderToPipeableStream`，此串流模式會全自動處理**。

## 串流脫水/水合 (Streaming Dehydration/Hydration)

串流脫水/水合是一種進階模式，不僅限於標記，還能讓你從伺服器脫水並串流任何支援資料至客戶端，並在到達時進行再水合。這對於可能需要進一步使用/管理伺服器端渲染初始標記所用底層資料的應用程式非常有用。

## 資料序列化 (Data Serialization)

使用 SSR 時，在伺服器與客戶端之間傳遞的資料必須在跨越網路邊界前進行序列化。TanStack Router 使用一個非常輕量的序列化器來處理此過程，它支援 JSON.stringify/JSON.parse 以外的常見資料類型。

預設支援以下類型：

- `undefined`
- `Date`
- `Error`
- `FormData`

如果你認為有其他類型應預設支援，請在 TanStack Router 儲存庫中開啟議題。

如果你使用更複雜的資料類型如 `Map`、`Set`、`BigInt` 等，可能需要使用自訂序列化器以確保類型定義準確且資料正確序列化與反序列化。我們目前正在開發更強大的序列化器，以及為你的應用程式自訂序列化器的方法。如果你有興趣協助，請開啟議題！

<!-- 這就是 `createRouter` 上的 `serializer` 選項的用途。 -->

資料序列化 API 允許使用自訂序列化器，讓我們在跨網路通訊時能透明地使用這些資料類型。

<!-- 以下範例展示與 [SuperJSON](https://github.com/blitz-js/superjson) 的使用方式，但任何實作 [`Start Serializer`](../api/router/RouterOptionsType.md#serializer-property) 的套件都可以使用。 -->

```tsx
import { SuperJSON } from 'superjson'

const router = createRouter({
  serializer: SuperJSON,
})
```

就這樣，TanStack Router 現在會適當使用 SuperJSON 來跨網路序列化資料。
