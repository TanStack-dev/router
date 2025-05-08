---
source-updated-at: '2025-04-01T08:21:46.000Z'
translation-updated-at: '2025-05-08T20:19:54.780Z'
title: 文件標頭管理
---

文件標頭 (Document Head) 管理是指管理文件的 head、title、meta、link 和 script 標籤的過程，而 TanStack Router 為使用 Start 的全端應用程式和使用 `@tanstack/react-router` 的單頁應用程式提供了一個強大的方式來管理文件標頭。它提供以下功能：

- 自動去除重複的 `title` 和 `meta` 標籤
- 根據路由可見性自動載入/卸載標籤
- 一種可組合的方式來合併巢狀路由中的 `title` 和 `meta` 標籤

對於使用 Start 的全端應用程式，甚至是使用 `@tanstack/react-router` 的單頁應用程式來說，管理文件標頭是任何應用程式的關鍵部分，原因如下：

- 搜尋引擎優化 (SEO)
- 社群媒體分享
- 分析 (Analytics)
- CSS 和 JS 的載入/卸載

要管理文件標頭，您需要渲染 `<HeadContent />` 和 `<Scripts />` 元件，並使用 `routeOptions.head` 屬性來管理路由的標頭，該屬性返回一個包含 `title`、`meta`、`links` 和 `scripts` 屬性的物件。

## 管理文件標頭

```tsx
export const Route = createRootRoute({
  head: () => ({
    meta: [
      {
        name: 'description',
        content: 'My App is a web application',
      },
      {
        title: 'My App',
      },
    ],
    links: [
      {
        rel: 'icon',
        href: '/favicon.ico',
      },
    ],
    scripts: [
      {
        src: 'https://www.google-analytics.com/analytics.js',
      },
    ],
  }),
})
```

### 去除重複

TanStack Router 預設會去除重複的 `title` 和 `meta` 標籤，優先使用巢狀路由中找到的每個標籤的**最後一個**出現。

- 巢狀路由中定義的 `title` 標籤會覆蓋父路由中定義的 `title` 標籤（但您可以將它們組合在一起，這將在本指南的後續部分中介紹）
- 具有相同 `name` 或 `property` 的 `meta` 標籤會被巢狀路由中找到的該標籤的最後一個出現所覆蓋

### `<HeadContent />`

`<HeadContent />` 元件是**必需**的，用於渲染文件的 head、title、meta、link 和與 head 相關的 script 標籤。

它應該**在根佈局的 `<head>` 標籤中渲染，或者在元件樹中盡可能高的位置渲染**，如果您的應用程式無法或不管理 `<head>` 標籤。

### Start/全端應用程式

```tsx
import { HeadContent } from '@tanstack/react-router'

export const Route = createRootRoute({
  component: () => (
    <html>
      <head>
        <HeadContent />
      </head>
      <body>
        <Outlet />
      </body>
    </html>
  ),
})
```

### 單頁應用程式

```tsx
import { HeadContent } from '@tanstack/react-router'

const rootRoute = createRootRoute({
  component: () => (
    <>
      <HeadContent />
      <Outlet />
    </>
  ),
})
```

## 管理 Body Scripts

除了可以在 `<head>` 標籤中渲染的 scripts 外，您還可以使用 `routeOptions.scripts` 屬性在 `<body>` 標籤中渲染 scripts。這對於需要 DOM 載入後才能執行的 scripts（甚至是內聯 scripts）非常有用，但這些 scripts 需要在應用程式的主要入口點之前執行（如果您使用 Start 或 TanStack Router 的全端實現，則包括 hydration）。

要做到這一點，您必須：

- 使用 `routeOptions` 物件的 `scripts` 屬性
- [渲染 `<Scripts />` 元件](#scripts)

```tsx
export const Route = createRootRoute({
  scripts: [
    {
      children: 'console.log("Hello, world!")',
    },
  ],
})
```

### `<Scripts />`

`<Scripts />` 元件是**必需**的，用於渲染文件的 body scripts。它應該在根佈局的 `<body>` 標籤中渲染，或者在元件樹中盡可能高的位置渲染，如果您的應用程式無法或不管理 `<body>` 標籤。

### 範例

```tsx
import { createFileRoute, Scripts } from '@tanstack/react-router'
export const Router = createFileRoute('/')({
  component: () => (
    <html>
      <head />
      <body>
        <Outlet />
        <Scripts />
      </body>
    </html>
  ),
})
```

```tsx
import { Scripts, createRootRoute } from '@tanstack/react-router'

export const Route = createRootRoute({
  component: () => (
    <>
      <Outlet />
      <Scripts />
    </>
  ),
})
```
