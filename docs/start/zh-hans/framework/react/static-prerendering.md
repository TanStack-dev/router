---
source-updated-at: 2025-02-26T20:55:39.000Z
translation-updated-at: 2025-04-05T03:43:03.000Z
title: 静态预渲染
id: static-prerendering
---

> 静态预渲染是 Nitro 的一项功能，虽然 TanStack Start 已支持该特性，但我们仍在探索其最佳实践。使用时请谨慎！

静态预渲染是指为您的应用程序生成静态 HTML 文件的过程。这既能提升应用性能（因为可以直接向用户提供预渲染的 HTML 文件而无需实时生成），也能将静态站点部署到不支持服务端渲染的平台。

## 由 Nitro 驱动的预渲染

TanStack Start 基于 Nitro 构建，这意味着我们可以利用 Nitro 的预渲染能力。Nitro 能将您的应用预渲染为静态 HTML 文件，这些文件可以直接提供给用户而无需实时生成。要启用预渲染，您可以在 `app.config.js` 文件中添加 `server.prerender` 配置项：

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

更多预渲染配置选项详见 [Nitro 预渲染配置文档](https://nitro.unjs.io/config#prerender)。

## 使用 Nitro 预渲染动态路由

Nitro 内置了一些钩子函数，允许您自定义预渲染等流程。其中 `prerender:routes` 钩子能让您获取异步数据，并将需要预渲染的路由添加到 `Set` 集合中。

假设我们有一个博客系统需要预渲染每篇文章页面，文章路由格式为 `/posts/$postId`。我们可以通过 `prerender:routes` 钩子获取所有文章数据，并将每篇文章路径添加到路由集合中：

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    hooks: {
      'prerender:routes': async (routes) => {
        // 获取需要渲染的页面数据
        const posts = await fetch('https://api.example.com/posts')
        const postsData = await posts.json()

        // 将每篇文章路径加入路由集合
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

截至本文撰写时，[Nitro 钩子文档](https://nitro.build/config#hooks)尚未包含相关钩子的具体说明。
