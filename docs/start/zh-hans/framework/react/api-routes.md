---
source-updated-at: 2025-03-31T08:23:31.000Z
translation-updated-at: 2025-04-05T03:41:37.000Z
title: API 路由
id: api-routes
---

API 路由是 TanStack Start 的一项强大功能，允许你在应用中创建服务端端点，而无需单独搭建服务器。API 路由适用于处理表单提交、用户认证等场景。

默认情况下，API 路由定义在项目的 `./app/routes/api` 目录中，由 TanStack Start 服务器自动处理。

> 🧠 这意味着默认情况下，你的 API 路由会以 `/api` 为前缀，并与应用共用同一个服务器。你可以通过修改 TanStack Start 配置中的 `server.apiBaseURL` 来自定义基础路径。

## 文件路由约定

TanStack Start 的 API 路由遵循与 TanStack Router 相同的基于文件的路由约定。这意味着 `routes` 目录中所有以 `api` 前缀（可配置）开头的文件都会被视作 API 路由。示例如下：

- `routes/api.users.ts` 会创建 `/api/users` 路由
- `routes/api/users.ts` 同样会创建 `/api/users` 路由
- `routes/api/users.index.ts` 同样会创建 `/api/users` 路由
- `routes/api/users/$id.ts` 会创建 `/api/users/$id` 路由
- `routes/api/users/$id/posts.ts` 会创建 `/api/users/$id/posts` 路由
- `routes/api.users.$id.posts.ts` 同样会创建 `/api/users/$id/posts` 路由
- `routes/api/file/$.ts` 会创建 `/api/file/$` 路由

以 `api` 为前缀的路由文件可视为对应 API 路径的处理器。

重要提示：每个路由只能关联一个处理器文件。例如若存在 `routes/api/users.ts` 文件（对应 `/api/users` 路径），则不能存在以下会解析到相同路径的文件：

- `routes/api/users.index.ts`
- `routes/api.users.ts`
- `routes/api.users.index.ts`

❗ 另需注意：API 路由不支持无路径布局路由或并行路由的概念。因此：

- `routes/api/_pathlessLayout/users.ts` 会解析为 `/api/_pathlessLayout/users` 而非 `/api/users`

## 嵌套目录 vs 文件名

从上述示例可见，文件命名约定非常灵活，允许混合使用目录和文件名。这种设计让你能按应用需求组织 API 路由。更多细节可参阅 [TanStack Router 文件路由指南](/router/latest/docs/framework/react/routing/file-based-routing#s-or-s)。

## 设置入口处理器

创建 API 路由前，需先配置 TanStack Start 项目的入口处理器。这个与 `client` 和 `ssr` 类似的处理器会接收 API 请求并将其路由到对应的处理器。API 入口处理器定义在项目的 `app/api.ts` 文件中：

```ts
// app/api.ts
import {
  createStartAPIHandler,
  defaultAPIFileRouteHandler,
} from '@tanstack/react-start/api'

export default createStartAPIHandler(defaultAPIFileRouteHandler)
```

该文件创建的处理器会根据请求自动加载并执行对应的 API 路由处理器。

## 定义 API 路由

API 路由通过调用 `createAPIFileRoute` 函数并导出 APIRoute 实例来定义。与 TanStack Router 的其他文件路由类似，该函数的第一个参数是路由路径，返回的函数会接收定义各 HTTP 方法处理器的对象。

> [!TIP]
> 如果开发服务器已运行，新建 API 路由时会自动生成初始处理器，之后可按需自定义。

> [!NOTE]
> 导出变量必须命名为 `APIRoute`，否则会返回 `404 not found`。

```ts
// routes/api/hello.ts
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/api/hello')({
  GET: async ({ request }) => {
    return new Response('Hello, World! from ' + request.url)
  },
})
```

每个 HTTP 方法处理器接收包含以下属性的对象：

- `request`: 入站请求对象（详见 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/API/Request)）
- `params`: 包含路由动态路径参数的对象。例如路径为 `/users/$id` 且请求发往 `/users/123` 时，`params` 为 `{ id: '123' }`

处理完成后需返回 `Response` 对象或 `Promise<Response>`（详见 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/API/Response)）。

## 动态路径参数

API 路由支持以 `$` 开头的动态路径参数。例如 `routes/api/users/$id.ts` 会创建接受动态 `id` 参数的 `/api/users/$id` 路由。

```ts
// routes/api/users/$id.ts
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/users/$id')({
  GET: async ({ params }) => {
    const { id } = params
    return new Response(`User ID: ${id}`)
  },
})

// 访问 /api/users/123 将返回
// User ID: 123
```

单一路由可包含多个动态参数。例如 `routes/api/users/$id/posts/$postId.ts` 会创建接受两个动态参数的路由。

```ts
// routes/api/users/$id/posts/$postId.ts
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/users/$id/posts/$postId')({
  GET: async ({ params }) => {
    const { id, postId } = params
    return new Response(`User ID: ${id}, Post ID: ${postId}`)
  },
})

// 访问 /api/users/123/posts/456 将返回
// User ID: 123, Post ID: 456
```

## 通配符参数

路径末尾的通配符参数以 `$` 表示。例如 `routes/api/file/$.ts` 会创建接受通配参数的路由。

```ts
// routes/api/file/$.ts
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/file/$')({
  GET: async ({ params }) => {
    const { _splat } = params
    return new Response(`File: ${_splat}`)
  },
})

// 访问 /api/file/hello.txt 将返回
// File: hello.txt
```

## 处理带请求体的请求

处理 POST 请求时，可在路由对象中添加 `POST` 处理器，通过 `request.json()` 访问请求体。

```ts
// routes/api/hello.ts
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/api/hello')({
  POST: async ({ request }) => {
    const body = await request.json()
    return new Response(`Hello, ${body.name}!`)
  },
})

// 向 /api/hello 发送 POST 请求及 { "name": "Tanner" } 正文
// 返回: Hello, Tanner!
```

PUT、PATCH 和 DELETE 方法同理。注意 `request.json()` 返回解析请求体的 Promise，需用 `await` 获取结果。也可使用 `request.text()` 或 `request.formData()` 访问请求体。

## 返回 JSON 响应

返回 JSON 响应的常见模式：

```ts
// routes/api/hello.ts
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/api/hello')({
  GET: async ({ request }) => {
    return new Response(JSON.stringify({ message: 'Hello, World!' }), {
      headers: {
        'Content-Type': 'application/json',
      },
    })
  },
})

// 访问 /api/hello 将返回
// {"message":"Hello, World!"}
```

## 使用 `json` 辅助函数

也可使用 `json` 辅助函数自动设置 `Content-Type` 并序列化 JSON：

```ts
// routes/api/hello.ts
import { json } from '@tanstack/react-start'
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/api/hello')({
  GET: async ({ request }) => {
    return json({ message: 'Hello, World!' })
  },
})

// 访问 /api/hello 将返回
// {"message":"Hello, World!"}
```

## 设置响应状态码

设置状态码有两种方式：

1. 通过 `Response` 构造函数的第二个参数

   ```ts
   return new Response('User not found', {
     status: 404,
   })
   ```

2. 使用 `@tanstack/react-start/server` 的 `setResponseStatus` 辅助函数
   ```ts
   setResponseStatus(404)
   return new Response('User not found')
   ```

## 设置响应头

设置响应头也有两种方式：

1. 通过 `Response` 构造函数的第二个参数

   ```ts
   return new Response('Hello, World!', {
     headers: {
       'Content-Type': 'text/plain',
     },
   })
   ```

2. 使用 `@tanstack/react-start/server` 的 `setHeaders` 辅助函数
   ```ts
   setHeaders({
     'Content-Type': 'text/plain',
   })
   return new Response('Hello, World!')
   ```
