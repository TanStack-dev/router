---
source-updated-at: 2025-02-26T10:16:23.000Z
translation-updated-at: 2025-04-05T03:37:25.000Z
title: 服务端渲染 (SSR)
id: ssr
---

以下是翻译后的中文文档，保持所有代码块、Markdown格式、HTML标签和变量不变：

# 服务端渲染（SSR）

服务端渲染（Server Side Rendering，SSR）是指在服务器端渲染组件并将HTML标记发送至客户端的过程。客户端随后将这些标记水合（hydrate）为完全交互式的组件。

通常需要考虑两种不同的SSR模式：

- 非流式SSR
  - 整个页面在服务器端渲染后，通过一次HTML请求发送至客户端，包含应用在客户端水合所需的序列化数据。
- 流式SSR
  - 页面的关键首屏内容在服务器端渲染后，通过一次HTML请求发送至客户端，包含应用在客户端水合所需的序列化数据
  - 页面剩余部分将在服务器端渲染的同时以流式传输至客户端。

本指南将说明如何使用TanStack Router实现这两种SSR模式！

## 非流式SSR

非流式服务端渲染是传统模式，即在服务器端渲染整个应用页面的标记，并将完整的HTML标记（及数据）发送至客户端。客户端随后将这些标记水合为完全交互式的应用。

要实现非流式SSR，你需要以下工具：

- `@tanstack/react-start/server`中的`StartServer`
  - 示例：`<StartServer router={router} />`
  - 在服务器入口渲染此组件会渲染你的应用，并自动处理应用级的水合/脱水，同时实现`Router`上的`Wrap`组件选项
- `@tanstack/react-start`中的`StartClient`
  - 示例：`<StartClient router={router} />`
  - 在客户端入口渲染此组件会渲染你的应用，并自动实现`Router`上的`Wrap`组件选项

### 路由创建

由于路由将在服务器和客户端同时存在，确保两者环境中的路由创建方式一致非常重要。最简单的方法是在共享文件中暴露一个`createRouter`函数，供服务器和客户端入口文件导入调用。

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

现在你可以在服务器和客户端入口文件中导入此函数并创建路由。

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

### 服务器历史记录

在客户端，Router默认使用`createBrowserHistory`实例，这是在客户端首选的history类型。但在服务器端，你需要改用`createMemoryHistory`实例，因为`createBrowserHistory`依赖`window`对象，而服务器端不存在此对象。

> 🧠 确保使用正在渲染的服务器URL初始化内存history。

- `src/entry-server.tsx`

```tsx
const router = createRouter()

const memoryHistory = createMemoryHistory({
  initialEntries: [opts.url],
})
```

创建内存history实例后，可以更新路由以使用它。

- `src/entry-server.tsx`

```tsx
router.update({
  history: memoryHistory,
})
```

### 在服务器端加载关键路由数据

为了在服务器端渲染应用，需要确保路由已通过其路由加载器加载所有关键数据。为此，可以在渲染应用前`await router.load()`。这将等待当前URL匹配的所有路由并行执行其`loader`函数。

- `src/entry-server.tsx`

```tsx
await router.load()
```

## 自动加载器脱水/水合

只要按照本指南所述的SSR标准步骤操作，路由获取的已解析加载器数据会自动由TanStack Router进行脱水和再水合。

⚠️ 如果使用延迟数据流式传输，还需确保实现了本指南末尾的[SSR流式传输与流转换](#streaming-ssr)模式。

更多关于数据加载和数据流式传输的信息，请参阅[数据加载](./data-loading.md)和[数据流式传输](../data-streaming)指南。

### 在服务器端渲染应用

现在你已拥有一个加载了当前URL所有关键数据的路由实例，可以在服务器端渲染应用：

```tsx
// src/entry-server.tsx

const html = ReactDOMServer.renderToString(<StartServer router={router} />)
```

### 处理未找到错误

`router`提供了`hasNotFoundMatch`方法，用于检查渲染过程中是否发生未找到错误。使用此方法检查并相应设置响应状态码：

```tsx
// src/entry-server.tsx
if (router.hasNotFoundMatch()) statusCode = 404
```

### 完整示例

以下是整合上述所有概念的完整服务器入口文件示例。

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

## 在客户端渲染应用

在客户端，操作更为简单。

- 创建路由实例
- 使用`<StartClient />`组件渲染应用

```tsx
// src/entry-client.tsx

import * as React from 'react'
import ReactDOM from 'react-dom/client'

import { StartClient } from '@tanstack/react-start'
import { createRouter } from './router'

const router = createRouter()

ReactDOM.hydrateRoot(document, <StartClient router={router} />)
```

通过此设置，你的应用将在服务器端渲染后在客户端进行水合！

## 流式SSR

流式SSR是最现代的SSR模式，指在服务器端渲染的同时持续增量地将HTML标记发送至客户端。与传统SSR在概念上略有不同，因为除了能够脱水和再水合关键首屏外，优先级较低或响应较慢的标记和数据可以在初始渲染后通过同一请求流式传输至客户端。

此模式适用于具有慢速或高延迟数据获取需求的页面。例如，如果页面需要从第三方API获取数据，可以先流式传输关键的首屏标记和数据至客户端，然后在解决后将非关键的第三方数据流式传输至客户端。

**只要使用`renderToPipeableStream`，此流式传输模式将全自动完成**。

## 流式脱水/水合

流式脱水/水合是一种高级模式，不仅限于标记，还允许你脱水和流式传输服务器至客户端的任何支持数据，并在到达时进行再水合。这对于可能需要进一步使用/管理服务器端初始渲染标记所用底层数据的应用非常有用。

## 数据序列化

使用SSR时，服务器与客户端间传递的数据必须在跨越网络边界前进行序列化。TanStack Router使用一个非常轻量级的序列化器处理此过程，支持JSON.stringify/JSON.parse之外的常见数据类型。

默认支持以下类型：

- `undefined`
- `Date`
- `Error`
- `FormData`

如果你认为还应默认支持其他类型，请在TanStack Router仓库提交issue。

如果使用更复杂的数据类型如`Map`、`Set`、`BigInt`等，可能需要使用自定义序列化器以确保类型定义准确且数据正确序列化和反序列化。我们正在开发更健壮的序列化器以及为应用自定义序列化器的方法。如有兴趣协助，请提交issue！

<!-- 这就是`createRouter`上的`serializer`选项的用途。 -->

数据序列化API允许使用自定义序列化器，使我们能够在跨网络通信时透明地使用这些数据类型。

<!-- 以下示例展示了与[SuperJSON](https://github.com/blitz-js/superjson)的用法，但任何实现[`Start Serializer`](../api/router/RouterOptionsType.md#serializer-property)的工具均可使用。 -->

```tsx
import { SuperJSON } from 'superjson'

const router = createRouter({
  serializer: SuperJSON,
})
```

就这样，TanStack Router现在将使用SuperJSON来序列化跨网络的数据。
