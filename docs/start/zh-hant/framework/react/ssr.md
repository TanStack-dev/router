---
source-updated-at: '2025-02-25T21:17:33.000Z'
translation-updated-at: '2025-05-08T20:25:09.697Z'
id: ssr
title: 伺服器渲染 (SSR)
---

# 伺服器渲染 (SSR)

伺服器渲染 (SSR) 是指在伺服器上渲染應用程式，並將渲染後的 HTML 傳送或串流至客戶端的過程。這對於提升應用程式效能和改善 SEO 都有幫助，因為它能讓使用者更快看到應用程式的內容，同時也讓搜尋引擎更容易爬取你的應用程式。

## SSR 基礎

TanStack Start 原生支援伺服器渲染 (SSR)。要啟用伺服器渲染，請在專案中建立一個 `app/ssr.tsx` 檔案：

```tsx
// app/ssr.tsx

import {
  createStartHandler,
  defaultStreamHandler,
} from '@tanstack/react-start/server'
import { getRouterManifest } from '@tanstack/react-start/router-manifest'

import { createRouter } from './router'

export default createStartHandler({
  createRouter,
  getRouterManifest,
})(defaultStreamHandler)
```

此檔案匯出一個用於建立伺服器渲染處理器 (handler) 的函式。該處理器是使用 `@tanstack/react-start/server` 中的 `createStartHandler` 函式建立的，該函式接收一個包含以下屬性的物件：

- `createRouter`: 一個用於為應用程式建立路由器的函式。每次呼叫時，此函式應回傳一個新的路由器實例。
- `getRouterManifest`: 一個回傳應用程式中所有路由清單 (manifest) 的函式。

接著會使用 `@tanstack/react-start/server` 中的 `defaultStreamHandler` 函式來呼叫該處理器，此函式會將回應串流至客戶端。
