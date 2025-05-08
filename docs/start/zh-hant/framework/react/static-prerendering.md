---
source-updated-at: '2025-02-26T20:55:39.000Z'
translation-updated-at: '2025-05-08T20:25:21.080Z'
id: static-prerendering
title: 靜態預渲染 (Static Prerendering)
---

> 靜態預渲染 (Static Prerendering) 是 Nitro 的一項功能，雖然在 TanStack Start 中可以使用，但我們仍在探索最佳實踐。請謹慎使用！

靜態預渲染 (Static Prerendering) 是指為您的應用程式生成靜態 HTML 檔案的過程。這對於提升應用程式效能非常有用，因為它允許您直接向用戶提供預先渲染的 HTML 檔案，而無需即時生成，同時也適用於部署靜態網站到不支援伺服器渲染 (SSR) 的平台。

## 由 Nitro 驅動的預渲染功能

TanStack Start 基於 Nitro 構建，這意味著我們可以利用 Nitro 的預渲染能力。Nitro 可以將您的應用程式預渲染為靜態 HTML 檔案，這些檔案可以直接提供給用戶而無需即時生成。要啟用預渲染功能，您可以在 `app.config.js` 檔案中添加 `server.prerender` 選項：

```js
// app.config.js

import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    prerender: {
      routes: ['/'],
      crawlLinks: true,
    },
  },
})
```

更多關於預渲染的選項可以參考 [Nitro 配置文件中關於預渲染的文檔](https://nitro.unjs.io/config#prerender)。

## 使用 Nitro 預渲染動態路由

Nitro 內置了一些預構建的鉤子 (hooks)，讓您可以自定義預渲染過程等。其中一個鉤子是 `prerender:routes`，它允許您獲取異步數據並將路由添加到一個需要預渲染的 `Set` 集合中。

以下示例假設我們有一個博客，其中包含一系列文章。我們希望預渲染每篇文章的頁面。我們的文章路由格式為 `/posts/$postId`。我們可以使用 `prerender:routes` 鉤子來獲取所有文章，並將每篇文章的路徑添加到路由集合中。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    hooks: {
      'prerender:routes': async (routes) => {
        // 獲取您想要渲染的頁面
        const posts = await fetch('https://api.example.com/posts')
        const postsData = await posts.json()

        // 將每篇文章的路徑添加到路由集合中
        postsData.forEach((post) => {
          routes.add(`/posts/${post.id}`)
        })
      },
    },
    prerender: {
      routes: ['/'],
      crawlLinks: true,
    },
  },
})
```

截至本文撰寫時，[Nitro 鉤子文檔](https://nitro.build/config#hooks) 尚未包含關於這些鉤子的詳細信息。
