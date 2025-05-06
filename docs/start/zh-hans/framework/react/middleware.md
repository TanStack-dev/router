---
source-updated-at: 2025-02-25T21:17:33.000Z
translation-updated-at: 2025-04-05T03:41:33.000Z
title: 中间件
id: middleware
---

## 什么是服务端函数中间件？

中间件允许您通过共享验证、上下文等功能，自定义使用 `createServerFn` 创建的服务端函数的行为。中间件甚至可以依赖其他中间件，形成按层级顺序执行的链式操作。

## 在服务端函数中使用中间件可以实现哪些功能？

- **身份验证**：在执行服务端函数前验证用户身份。
- **权限控制**：检查用户是否有执行服务端函数的必要权限。
- **日志记录**：记录请求、响应和错误信息。
- **可观测性**：收集指标、追踪和日志。
- **提供上下文**：将数据附加到请求对象，供其他中间件或服务端函数使用。
- **错误处理**：以一致的方式处理错误。
- 还有更多可能！一切取决于您的需求！

## 为服务端函数定义中间件

中间件通过 `createMiddleware` 函数定义。该函数返回一个 `Middleware` 对象，可通过 `middleware`、`validator`、`server` 和 `client` 等方法进一步定制。

```tsx
import { createMiddleware } from '@tanstack/react-start'

const loggingMiddleware = createMiddleware().server(async ({ next, data }) => {
  console.log('收到请求:', data)
  const result = await next()
  console.log('处理响应:', result)
  return result
})
```

## 在服务端函数中使用中间件

定义中间件后，可结合 `createServerFn` 函数来自定义服务端函数的行为。

```tsx
import { createServerFn } from '@tanstack/react-start'
import { loggingMiddleware } from './middleware'

const fn = createServerFn()
  .middleware([loggingMiddleware])
  .handler(async () => {
    // ...
  })
```

## 中间件方法

有多种方法可用于定制中间件。如果您使用 TypeScript（推荐），类型系统会强制这些方法的顺序，以确保最大的类型推断和安全性。

- `middleware`：向链中添加中间件。
- `validator`：在数据对象传递给当前中间件及嵌套中间件前修改它。
- `server`：定义中间件在嵌套中间件和服务端函数前执行的服务器端逻辑，并将结果传递给下一个中间件。
- `client`：定义中间件在嵌套中间件和客户端 RPC 函数（或服务端函数）前执行的客户端逻辑，并将结果传递给下一个中间件。

## `middleware` 方法

`middleware` 方法用于向链中添加依赖中间件，这些中间件会在当前中间件**之前**执行。只需调用 `middleware` 方法并传入中间件对象数组即可。

```tsx
import { createMiddleware } from '@tanstack/react-start'

const loggingMiddleware = createMiddleware().middleware([
  authMiddleware,
  loggingMiddleware,
])
```

类型安全的上下文和负载验证也会从父中间件继承！

## `validator` 方法

`validator` 方法用于在数据对象传递给当前中间件、嵌套中间件及最终的服务端函数前修改它。此方法接收一个函数，该函数接受数据对象并返回经过验证（可选修改后）的数据对象。通常使用如 `zod` 的验证库实现。示例如下：

```tsx
import { createMiddleware } from '@tanstack/react-start'
import { zodValidator } from '@tanstack/zod-adapter'
import { z } from 'zod'

const mySchema = z.object({
  workspaceId: z.string(),
})

const workspaceMiddleware = createMiddleware()
  .validator(zodValidator(mySchema))
  .server(({ next, data }) => {
    console.log('工作区 ID:', data.workspaceId)
    return next()
  })
```

## `server` 方法

`server` 方法用于定义中间件在嵌套中间件和服务端函数前后执行的**服务器端**逻辑。此方法接收一个包含以下属性的对象：

- `next`：调用时将执行链中下一个中间件的函数。
- `data`：传递给服务端函数的数据对象。
- `context`：存储父中间件数据的对象。可扩展为包含传递给子中间件的额外数据。

## 从 `next` 返回必要结果

`next` 函数用于执行链中的下一个中间件。**必须等待并返回（或直接返回）`next` 函数的结果**，以确保链继续执行。

```tsx
import { createMiddleware } from '@tanstack/react-start'

const loggingMiddleware = createMiddleware().server(async ({ next }) => {
  console.log('收到请求')
  const result = await next()
  console.log('处理响应')
  return result
})
```

## 通过 `next` 为下一个中间件提供上下文

`next` 函数可选择性接收包含 `context` 属性的对象。传递给此 `context` 值的任何属性都会合并到父上下文中，并传递给下一个中间件。

```tsx
import { createMiddleware } from '@tanstack/react-start'

const awesomeMiddleware = createMiddleware().server(({ next }) => {
  return next({
    context: {
      isAwesome: Math.random() > 0.5,
    },
  })
})

const loggingMiddleware = createMiddleware().server(
  async ({ next, context }) => {
    console.log('是否很棒？', context.isAwesome)
    return next()
  },
)
```

## 客户端逻辑

尽管服务端函数主要是服务器端操作，但客户端仍有大量围绕 RPC 请求的逻辑。这意味着我们也可以在中间件中定义客户端逻辑，这些逻辑会在客户端围绕嵌套中间件和最终的 RPC 函数及其响应执行。

## 客户端负载验证

默认情况下，中间件验证仅在服务器端执行以减少客户端包体积。但您也可以通过向 `createMiddleware` 函数传递 `validateClient: true` 选项在客户端验证数据，从而可能节省一次往返请求。

> 为什么不能为客户端传递不同的验证模式？
>
> 客户端验证模式派生自服务器端模式。这是因为客户端验证模式用于在数据发送到服务器前验证其有效性。如果客户端模式与服务器端模式不同，服务器可能接收到意外数据，导致不可预期的行为。

```tsx
import { createMiddleware } from '@tanstack/react-start'
import { zodValidator } from '@tanstack/zod-adapter'
import { z } from 'zod'

const workspaceMiddleware = createMiddleware({ validateClient: true })
  .validator(zodValidator(mySchema))
  .server(({ next, data }) => {
    console.log('工作区 ID:', data.workspaceId)
    return next()
  })
```

## `client` 方法

客户端中间件逻辑通过 `Middleware` 对象的 `client` 方法定义。此方法用于定义中间件在嵌套中间件和客户端 RPC 函数（或服务端函数，如果是 SSR 或从另一个服务端函数调用）前后执行的客户端逻辑。

**客户端中间件逻辑与 `server` 方法创建的逻辑共享大部分相同 API，但它在客户端执行。** 包括：

- 要求调用 `next` 函数以继续链式执行。
- 通过 `next` 函数为下一个客户端中间件提供上下文的能力。
- 在数据传递给下一个客户端中间件前修改它的能力。

与 `server` 函数类似，它也接收包含以下属性的对象：

- `next`：调用时将执行链中下一个客户端中间件的函数。
- `data`：传递给客户端函数的数据对象。
- `context`：存储父中间件数据的对象。可扩展为包含传递给子中间件的额外数据。

```tsx
const loggingMiddleware = createMiddleware().client(async ({ next }) => {
  console.log('请求已发送')
  const result = await next()
  console.log('收到响应')
  return result
})
```

## 向服务器发送客户端上下文

**默认情况下，客户端上下文不会发送到服务器，因为这可能无意中向服务器发送大量数据。** 如果需要向服务器发送客户端上下文，必须在调用 `next` 函数时传递包含 `sendContext` 属性的对象。任何传递给 `sendContext` 的属性都会被合并、序列化，并随数据一起发送到服务器，供嵌套的服务端中间件通过常规上下文对象访问。

```tsx
const requestLogger = createMiddleware()
  .client(async ({ next, context }) => {
    return next({
      sendContext: {
        // 向服务器发送工作区 ID
        workspaceId: context.workspaceId,
      },
    })
  })
  .server(async ({ next, data, context }) => {
    // 哇！我们拥有来自客户端的工作区 ID！
    console.log('工作区 ID:', context.workspaceId)
    return next()
  })
```

## 客户端发送上下文的安全性

您可能已注意到，虽然客户端发送的上下文是类型安全的，但不需要在运行时验证。如果通过上下文传递动态用户生成的数据，可能存在安全隐患。因此，**如果通过上下文从客户端向服务器发送动态数据，应在服务器端中间件中验证后再使用。** 示例如下：

```tsx
import { zodValidator } from '@tanstack/zod-adapter'
import { z } from 'zod'

const requestLogger = createMiddleware()
  .client(async ({ next, context }) => {
    return next({
      sendContext: {
        workspaceId: context.workspaceId,
      },
    })
  })
  .server(async ({ next, data, context }) => {
    // 使用前验证工作区 ID
    const workspaceId = zodValidator(z.number()).parse(context.workspaceId)
    console.log('工作区 ID:', workspaceId)
    return next()
  })
```

## 向客户端发送服务器上下文

类似于向服务器发送客户端上下文，您也可以通过调用 `next` 函数并传递包含 `sendContext` 属性的对象，向客户端发送服务器上下文。任何传递给 `sendContext` 的属性都会被合并、序列化，并随响应一起发送到客户端，供嵌套的客户端中间件通过常规上下文对象访问。在 `client` 中调用 `next` 的返回对象包含从服务器发送到客户端的上下文，并且是类型安全的。中间件能够从 `middleware` 函数链中推断出服务器发送到客户端的上下文。

> [!WARNING] > `client` 中 `next` 的返回类型只能从当前中间件链中已知的中间件推断。因此，`next` 最准确的返回类型位于中间件链末端的中间件中。

```tsx
const serverTimer = createMiddleware().server(async ({ next }) => {
  return next({
    sendContext: {
      // 向客户端发送当前时间
      timeFromServer: new Date(),
    },
  })
})

const requestLogger = createMiddleware()
  .middleware([serverTimer])
  .client(async ({ next }) => {
    const result = await next()
    // 哇！我们拥有来自服务器的时间！
    console.log('来自服务器的时间:', result.context.timeFromServer)

    return result
  })
```

## 读取/修改服务器响应

使用 `server` 方法的中间件与服务端函数在同一上下文中执行，因此您可以完全遵循相同的[服务端函数上下文工具](./server-functions#server-function-context)来读取和修改请求头、状态码等。

## 修改客户端请求

使用 `client` 方法的中间件在**完全不同的客户端上下文**中执行，与服务端函数不同，因此无法使用相同的工具来读取和修改请求。但您仍可以通过在调用 `next` 函数时返回额外属性来修改请求。目前支持的属性包括：

- `headers`：包含要添加到请求中的头的对象。

以下是通过中间件为任何请求添加 `Authorization` 头的示例：

```tsx
import { getToken } from 'my-auth-library'

const authMiddleware = createMiddleware().client(async ({ next }) => {
  return next({
    headers: {
      Authorization: `Bearer ${getToken()}`,
    },
  })
})
```

## 使用中间件

中间件可通过两种方式使用：

- **全局中间件**：应对每个请求执行的中间件。
- **服务端函数中间件**：应对特定服务端函数执行的中间件。

## 全局中间件

全局中间件自动为应用中的每个服务端函数运行。适用于身份验证、日志记录和监控等需应用于所有请求的功能。

要使用全局中间件，请在项目中创建 `global-middleware.ts` 文件（通常位于 `app/global-middleware.ts`）。此文件在客户端和服务器环境中运行，是注册全局中间件的地方。

以下是注册全局中间件的方法：

```tsx
// app/global-middleware.ts
import { registerGlobalMiddleware } from '@tanstack/react-start'
import { authMiddleware } from './middleware'

registerGlobalMiddleware({
  middleware: [authMiddleware],
})
```

### 全局中间件的类型安全性

全局中间件类型本质上与服务端函数**分离**。这意味着如果全局中间件向服务端函数或其他服务端函数特定中间件提供额外上下文，类型不会自动传递给服务端函数或其他服务端函数特定中间件。

```tsx
// app/global-middleware.ts
registerGlobalMiddleware({
  middleware: [authMiddleware],
})
```

```tsx
// authMiddleware.ts
const authMiddleware = createMiddleware().server(({ next, context }) => {
  console.log(context.user) // <-- 此处的类型不会被推断！
  // ...
})
```

为解决此问题，将您尝试引用的全局中间件添加到服务端函数的中间件数组中。**全局中间件将被去重为单一实例（全局实例），您的服务端函数将获得正确的类型。**

以下是此机制的工作示例：```tsx
import { authMiddleware } from './authMiddleware'

const fn = createServerFn()
.middleware([authMiddleware])
.handler(async ({ context }) => {
console.log(context.user)
// ...
})

````

## 中间件执行顺序

中间件按照依赖优先的顺序执行，从全局中间件开始，然后是服务器函数中间件。以下示例将按以下顺序输出日志：

- `globalMiddleware1`
- `globalMiddleware2`
- `a`
- `b`
- `c`
- `d`

```tsx
const globalMiddleware1 = createMiddleware().server(async ({ next }) => {
  console.log('globalMiddleware1')
  return next()
})

const globalMiddleware2 = createMiddleware().server(async ({ next }) => {
  console.log('globalMiddleware2')
  return next()
})

registerGlobalMiddleware({
  middleware: [globalMiddleware1, globalMiddleware2],
})

const a = createMiddleware().server(async ({ next }) => {
  console.log('a')
  return next()
})

const b = createMiddleware()
  .middleware([a])
  .server(async ({ next }) => {
    console.log('b')
    return next()
  })

const c = createMiddleware()
  .middleware()
  .server(async ({ next }) => {
    console.log('c')
    return next()
  })

const d = createMiddleware()
  .middleware([b, c])
  .server(async () => {
    console.log('d')
  })

const fn = createServerFn()
  .middleware([d])
  .server(async () => {
    console.log('fn')
  })
````

## 环境摇树优化

中间件功能会根据每个生成包的环境进行摇树优化。

- 在服务端，不会进行任何摇树优化，因此中间件中使用的所有代码都将包含在服务端包中。
- 在客户端，所有服务端专用代码会从客户端包中移除。这意味着任何在 `server` 方法中使用的代码都会从客户端包中移除。如果 `validateClient` 设置为 `true`，客户端验证代码将包含在客户端包中，否则 `data` 验证代码也会被移除。
