---
source-updated-at: 2025-02-25T21:17:33.000Z
translation-updated-at: 2025-04-05T03:41:37.000Z
title: 服务端渲染 (SSR)
id: ssr
---

服务端渲染（SSR）是指在服务器上渲染应用程序，并将渲染后的HTML发送或流式传输到客户端的过程。这既能提升应用性能，又能优化SEO效果，因为它让用户能更快看到应用内容，同时也便于搜索引擎抓取你的应用。

## SSR基础

TanStack Start 开箱即用地支持服务端渲染。要启用服务端渲染功能，请在项目中创建 `app/ssr.tsx` 文件：

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

该文件导出一个用于创建服务端渲染处理器的函数。处理器通过 `@tanstack/react-start/server` 中的 `createStartHandler` 函数创建，该函数接收包含以下属性的对象：

- `createRouter`: 创建应用路由的函数。每次调用时都应返回一个新的路由实例。
- `getRouterManifest`: 返回应用中所有路由清单的函数。

随后使用 `@tanstack/react-start/server` 中的 `defaultStreamHandler` 函数调用该处理器，该函数负责将响应流式传输到客户端。
