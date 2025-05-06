---
source-updated-at: '2025-04-14T17:55:22.000Z'
translation-updated-at: '2025-05-06T22:06:22.686Z'
id: server-functions
title: 服务端函数
---

## 什么是服务端函数 (Server Functions)？

服务端函数允许您定义可在几乎任何地方（包括客户端）调用，但**仅**在服务器端执行的逻辑。实际上，它们与 API 路由 (API Route) 并无太大差异，但存在几个关键区别：

- 它们没有稳定的公开 URL（但很快将支持此功能！）
- 可从应用程序的任何位置调用，包括加载器 (loader)、钩子 (hook)、组件等，但无法从 API 路由中调用

然而，它们与常规 API 路由的相似之处在于：

- 可访问请求上下文，允许读取请求头、设置 Cookie 等
- 可访问敏感信息（如环境变量），而不会暴露给客户端
- 可用于执行任何类型的服务端逻辑，例如从数据库获取数据、发送邮件或与其他服务交互
- 可返回任何类型的值，包括原始值、JSON 可序列化对象甚至原始的 Response 对象
- 可抛出错误，包括重定向 (redirect) 和未找到 (notFound) 错误，这些错误可由路由自动处理

> 服务端函数与 "React 服务端函数 (React Server Functions)" 有何不同？
>
> - TanStack 服务端函数不依赖特定前端框架，可与任何前端框架或无框架环境配合使用
> - TanStack 服务端函数基于标准 HTTP 请求，可随意调用而不会遭遇串行执行瓶颈

## 工作原理

服务端函数可在应用程序的任何位置定义，但必须在文件的顶层定义。它们可在应用程序各处调用，包括加载器、钩子等。传统上，这种模式被称为远程过程调用 (RPC)，但由于这些函数的同构特性，我们称其为服务端函数。

- 在服务端打包时，服务端函数逻辑保持不变。由于它们已在正确位置，无需额外处理
- 在客户端打包时，服务端函数会被移除；它们仅存在于服务端。客户端对服务端函数的任何调用都将被替换为向服务器发送的 `fetch` 请求，以执行服务端函数并将响应返回给客户端

## 服务端函数中间件

服务端函数可使用中间件来共享逻辑、上下文、常见操作、前置条件等。要了解更多关于服务端函数中间件的信息，请参阅[中间件指南](./middleware.md)。

## 定义服务端函数

> 我们要感谢 [tRPC](https://trpc.io/) 团队为 TanStack Start 的服务端函数设计提供的灵感以及在实现过程中的指导。我们非常喜爱（并推荐）在 API 路由中使用 tRPC，因此我们坚持让服务端函数获得同样的一流待遇和开发者体验。感谢！

服务端函数使用 `@tanstack/react-start` 包中的 `createServerFn` 函数定义。该函数接受一个可选的 `options` 参数用于配置 HTTP 方法和响应类型等，并允许您链式定义服务端函数体、输入验证、中间件等。以下是一个简单示例：

```tsx
// getServerTime.ts
import { createServerFn } from '@tanstack/react-start'

export const getServerTime = createServerFn().handler(async () => {
  // 等待 1 秒
  await new Promise((resolve) => setTimeout(resolve, 1000))
  // 返回当前时间
  return new Date().toISOString()
})
```

### 配置选项

创建服务端函数时，可提供配置选项来自定义其行为：

```tsx
import { createServerFn } from '@tanstack/react-start'

export const getData = createServerFn({
  method: 'GET', // 使用的 HTTP 方法
  response: 'data', // 响应处理模式
}).handler(async () => {
  // 函数实现
})
```

#### 可用选项

**`method`**

指定服务端函数请求的 HTTP 方法：

```tsx
method?: 'GET' | 'POST'
```

默认情况下，如果未指定，服务端函数使用 `GET` 方法。

**`response`**

控制响应如何处理和返回：

```tsx
response?: 'data' | 'full' | 'raw'
```

- `'data'`（默认）：自动解析 JSON 响应并仅返回数据
- `'full'`：返回包含结果数据、错误信息和上下文的响应对象
- `'raw'`：直接返回原始 Response 对象，支持流式响应和自定义头部

## 可在何处调用服务端函数？

- 从服务端代码
- 从客户端代码
- 从其他服务端函数

> [!WARNING]
> 服务端函数无法从 API 路由中调用。如果需要在服务端函数和 API 路由之间共享业务逻辑，请将共享逻辑提取为可被两者导入的实用函数。

## 接受参数

服务端函数接受单个参数，可以是多种类型：

- 标准 JavaScript 类型
  - `string`
  - `number`
  - `boolean`
  - `null`
  - `Array`
  - `Object`
- FormData
- ReadableStream（可包含上述任何类型）
- Promise（可解析为上述任何类型）

以下是一个接受简单字符串参数的服务端函数示例：

```tsx
import { createServerFn } from '@tanstack/react-start'

export const greet = createServerFn({
  method: 'GET',
})
  .validator((data: string) => data)
  .handler(async (ctx) => {
    return `Hello, ${ctx.data}!`
  })

greet({
  data: 'John',
})
```

## 运行时输入验证/类型安全

服务端函数可配置为在运行时验证输入数据，同时增加类型安全性。这有助于在执行服务端函数前确保输入类型正确，并提供更友好的错误消息。

这通过 `validator` 方法实现。它将接受传递给服务端函数的任何输入。您从此函数返回的值（和类型）将成为传递给实际服务端函数处理程序的输入。

验证器还可与外部验证器（如 Zod）无缝集成。

### 基本验证

以下是一个验证输入参数的简单服务端函数示例：

```tsx
import { createServerFn } from '@tanstack/react-start'

type Person = {
  name: string
}

export const greet = createServerFn({ method: 'GET' })
  .validator((person: unknown): Person => {
    if (typeof person !== 'object' || person === null) {
      throw new Error('Person must be an object')
    }

    if ('name' in person && typeof person.name !== 'string') {
      throw new Error('Person.name must be a string')
    }

    return person as Person
  })
  .handler(async ({ data }) => {
    return `Hello, ${data.name}!`
  })
```

### 使用验证库

可像这样使用 Zod 等验证库：

```tsx
import { createServerFn } from '@tanstack/react-start'

import { z } from 'zod'

const Person = z.object({
  name: z.string(),
})

export const greet = createServerFn({ method: 'GET' })
  .validator((person: unknown) => {
    return Person.parse(person)
  })
  .handler(async (ctx) => {
    return `Hello, ${ctx.data.name}!`
  })

greet({
  data: {
    name: 'John',
  },
})
```

## 类型安全

由于服务端函数跨越网络边界，确保传递给它们的数据不仅是正确的类型，而且在运行时经过验证非常重要。这在处理用户输入时尤为重要，因为用户输入可能不可预测。为确保开发者验证其 I/O 数据，类型依赖于验证。`validator` 函数的返回类型将成为服务端函数处理程序的输入类型。

```tsx
import { createServerFn } from '@tanstack/react-start'

type Person = {
  name: string
}

export const greet = createServerFn({ method: 'GET' })
  .validator((person: unknown): Person => {
    if (typeof person !== 'object' || person === null) {
      throw new Error('Person must be an object')
    }

    if ('name' in person && typeof person.name !== 'string') {
      throw new Error('Person.name must be a string')
    }

    return person as Person
  })
  .handler(
    async ({
      data, // Person
    }) => {
      return `Hello, ${data.name}!`
    },
  )

function test() {
  greet({ data: { name: 'John' } }) // 正确
  greet({ data: { name: 123 } }) // 错误：类型 '{ name: number; }' 的参数不能赋给类型 'Person' 的参数
}
```

## 类型推断

服务端函数根据 `validator` 的输入和 `handler` 函数的返回值推断其输入和输出类型。实际上，您定义的 `validator` 甚至可以有自己的独立输入/输出类型，这在验证器对输入数据执行转换时非常有用。

以下是一个使用 `zod` 验证库的示例：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { z } from 'zod'

const transactionSchema = z.object({
  amount: z.string().transform((val) => parseInt(val, 10)),
})

const createTransaction = createServerFn()
  .validator(transactionSchema)
  .handler(({ data }) => {
    return data.amount // 返回数字
  })

createTransaction({
  data: {
    amount: '123', // 接受字符串
  },
})
```

## 非验证推断

虽然我们强烈建议使用验证库来验证网络 I/O 数据，但您可能出于某种原因不希望验证数据，但仍希望保持类型安全。为此，可使用标识函数作为 `validator` 向服务端函数提供类型信息，将输入和/或输出类型化为正确类型：

```tsx
import { createServerFn } from '@tanstack/react-start'

type Person = {
  name: string
}

export const greet = createServerFn({ method: 'GET' })
  .validator((d: Person) => d)
  .handler(async (ctx) => {
    return `Hello, ${ctx.data.name}!`
  })

greet({
  data: {
    name: 'John',
  },
})
```

## JSON 参数

服务端函数可接受 JSON 可序列化对象作为参数。这对于向服务器传递复杂数据结构非常有用：

```tsx
import { createServerFn } from '@tanstack/react-start'

type Person = {
  name: string
  age: number
}

export const greet = createServerFn({ method: 'GET' })
  .validator((data: Person) => data)
  .handler(async ({ data }) => {
    return `Hello, ${data.name}! You are ${data.age} years old.`
  })

greet({
  data: {
    name: 'John',
    age: 34,
  },
})
```

## FormData 参数

服务端函数可接受 `FormData` 对象作为参数：

```tsx
import { createServerFn } from '@tanstack/react-start'

export const greetUser = createServerFn({ method: 'POST' })
  .validator((data) => {
    if (!(data instanceof FormData)) {
      throw new Error('Invalid form data')
    }
    const name = data.get('name')
    const age = data.get('age')

    if (!name || !age) {
      throw new Error('Name and age are required')
    }

    return {
      name: name.toString(),
      age: parseInt(age.toString(), 10),
    }
  })
  .handler(async ({ data: { name, age } }) => {
    return `Hello, ${name}! You are ${age} years old.`
  })

// 用法
function Test() {
  return (
    <form
      onSubmit={async (event) => {
        event.preventDefault()
        const formData = new FormData(event.currentTarget)
        const response = await greetUser({ data: formData })
        console.log(response)
      }}
    >
      <input name="name" />
      <input name="age" />
      <button type="submit">Submit</button>
    </form>
  )
}
```

## 服务端函数上下文

除了服务端函数接受的单个参数外，您还可以使用 `@tanstack/react-start/server` 中的实用程序从任何服务端函数内部访问服务器请求上下文。在底层，我们使用 [Unjs](https://unjs.io/) 的 `h3` 包执行跨平台 HTTP 请求。

有许多上下文函数可供您使用，例如：

- 访问请求上下文
- 访问/设置请求头
- 访问/设置会话/Cookie
- 设置响应状态码和状态消息
- 处理多部分表单数据
- 读取/设置自定义服务器上下文属性

有关可用上下文函数的完整列表，请参阅所有可用的 [h3 方法](https://h3.unjs.io/utils/request) 或检查 [@tanstack/start-server-core 源代码](https://github.com/TanStack/router/tree/main/packages/start-server-core/src)。

以下是几个示例：

## 访问请求上下文

让我们使用 `getWebRequest` 函数从服务端函数内部访问请求本身：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { getWebRequest } from '@tanstack/react-start/server'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    const request = getWebRequest()

    console.log(request.method) // GET

    console.log(request.headers.get('User-Agent')) // Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3
  },
)
```

## 访问请求头

使用 `getHeaders` 函数从服务端函数内部访问所有请求头：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { getHeaders } from '@tanstack/react-start/server'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    console.log(getHeaders())
    // {
    //   "accept": "*/*",
    //   "accept-encoding": "gzip, deflate, br",
    //   "accept-language": "en-US,en;q=0.9",
    //   "connection": "keep-alive",
    //   "host": "localhost:3000",
    //   ...
    // }
  },
)
```

您还可以使用 `getHeader` 函数访问单个请求头：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { getHeader } from '@tanstack/react-start/server'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    console.log(getHeader('User-Agent')) // Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3
  },
)
```

## 返回值

服务端函数可以返回几种不同类型的值：

- 原始值
- JSON 可序列化对象
- `redirect` 错误（也可抛出）
- `notFound` 错误（也可抛出）
- 原始 Response 对象

## 返回原始值和 JSON

要返回任何原始值或 JSON 可序列化对象，只需从服务端函数返回值：

```tsx
import { createServerFn } from '@tanstack/react-start'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    return new Date().toISOString()
  },
)

export const getServerData = createServerFn({ method: 'GET' }).handler(
  async () => {
    return {
      message: 'Hello, World!',
    }
  },
)
```

默认情况下，服务端函数假定任何非 Response 对象的返回值都是原始值或 JSON 可序列化对象。

## 使用自定义头部响应

要使用自定义头部响应，可使用 `setHeader` 函数：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { setHeader } from '@tanstack/react-start/server'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    setHeader('X-Custom-Header', 'value')
    return new Date().toISOString()
  },
)
```

## 使用自定义状态码响应

要使用自定义状态码响应，可使用 `setResponseStatus` 函数：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { setResponseStatus } from '@tanstack/react-start/server'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    setResponseStatus(201)
    return new Date().toISOString()
  },
)
```

## 返回原始 Response 对象

要返回原始 Response 对象，可从服务端函数返回 Response 对象并设置 `response: 'raw'`：

```tsx
import { createServerFn } from '@tanstack/react-start'

export const getServerTime = createServerFn({
  method: 'GET',
  response: 'raw',
}).handler(async () => {
  // 从 s3 读取文件
  return fetch('https://example.com/time.txt')
})
```

`response: 'raw'` 选项还支持流式响应等功能：

```tsx
import { createServerFn } from '@tanstack/react-start'

export const streamEvents = createServerFn({
  method: 'GET',
  response: 'raw',
}).handler(async ({ signal }) => {
  // 创建 ReadableStream 发送数据块
  const stream = new ReadableStream({
    async start(controller) {
      // 立即发送初始响应
      controller.enqueue(new TextEncoder().encode('Connection established\n'))

      let count = 0
      const interval = setInterval(() => {
        // 检查客户端是否断开连接
        if (signal.aborted) {
          clearInterval(interval)
          controller.close()
          return
        }

        // 发送数据块
        controller.enqueue(
          new TextEncoder().encode(
            `Event ${++count}: ${new Date().toISOString()}\n`,
          ),
        )

        // 10 个事件后结束
        if (count >= 10) {
          clearInterval(interval)
          controller.close()
        }
      }, 1000)

      // 确保在请求中止时清理
      signal.addEventListener('abort', () => {
        clearInterval(interval)
        controller.close()
      })
    },
  })

  // 返回流式响应
  return new Response(stream, {
    headers: {
      'Content-Type': 'text/event-stream',
      'Cache-Control': 'no-cache',
      Connection: 'keep-alive',
    },
  })
})
```

`response: 'raw'` 选项特别适用于：

- 增量发送数据的流式 API
- 服务器发送事件 (Server-sent events)
- 长轮询响应
