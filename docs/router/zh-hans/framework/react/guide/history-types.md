---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:33:56.000Z
title: 历史类型
---

虽然使用 TanStack Router 并不要求掌握 `@tanstack/history` 的 API，但了解其工作原理仍十分有益。实际上，TanStack Router 依赖于并使用了 `history` 这一抽象层来管理路由历史记录。

若未主动创建 history 实例，在路由器初始化时将自动生成一个面向浏览器环境的默认实例。如需特殊的历史记录 API 类型，可通过 `@tanstack/history` 包自行创建：

- `createBrowserHistory`：默认的历史记录类型
- `createHashHistory`：采用哈希值追踪历史记录的变体
- `createMemoryHistory`：将历史记录保存在内存中的类型

创建 history 实例后，可将其传入 `Router` 构造函数：

```ts
import { createMemoryHistory, createRouter } from '@tanstack/react-router'

const memoryHistory = createMemoryHistory({
  initialEntries: ['/'], // 传入初始URL
})

const router = createRouter({ routeTree, history: memoryHistory })
```

## 浏览器路由

`createBrowserHistory` 是默认的历史记录类型，它利用浏览器的 history API 来管理浏览历史。

## 哈希路由

当服务器不支持将 HTTP 请求重写至 index.html（或其他无服务器环境）时，哈希路由会非常有用。

```ts
import { createHashHistory, createRouter } from '@tanstack/react-router'

const hashHistory = createHashHistory()

const router = createRouter({ routeTree, history: hashHistory })
```

## 内存路由

内存路由适用于非浏览器环境，或当您不希望组件与 URL 产生交互时。

```ts
import { createMemoryHistory, createRouter } from '@tanstack/react-router'

const memoryHistory = createMemoryHistory({
  initialEntries: ['/'], // 传入初始URL
})

const router = createRouter({ routeTree, history: memoryHistory })
```

服务器端渲染的使用方法请参阅 [SSR 指南](./ssr.md#server-history)。
