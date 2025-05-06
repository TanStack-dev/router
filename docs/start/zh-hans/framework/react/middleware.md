---
source-updated-at: '2025-04-26T19:05:15.000Z'
translation-updated-at: '2025-05-06T22:20:48.112Z'
id: middleware
title: 中间件
---

## 什么是服务端函数中间件？

中间件允许您通过共享验证、上下文等方式，自定义使用 `createServerFn` 创建的服务端函数的行为。中间件甚至可以依赖其他中间件，形成一个按层级顺序执行的链式操作。

## 在服务端函数中，中间件能实现哪些功能？

- **身份验证 (Authentication)**：在执行服务端函数前验证用户身份。
- **授权 (Authorization)**：检查用户是否有执行服务端函数的必要权限。
- **日志记录 (Logging)**：记录请求、响应和错误。
- **可观测性 (Observability)**：收集指标、追踪和日志。
- **提供上下文 (Provide Context)**：将数据附加到请求对象上，供其他中间件或服务端函数使用。
- **错误处理 (Error Handling)**：以一致的方式处理错误。
- 还有更多！可能性由您决定！

## 为服务端函数定义中间件

中间件通过 `createMiddleware` 函数定义。该函数返回一个 `Middleware` 对象，可通过 `middleware`、`validator`、`server` 和 `client` 等方法继续定制中间件。

```tsx
import { createMiddleware } from '@tanstack/react-start'

const loggingMiddleware = createMiddleware().server(async ({ next, data }) => {
  console.log('Request received:', data)
  const result = await next()
  console.log('Response processed:', result)
  return result
})
```

## 在服务端函数中使用中间件

定义中间件后，您可以将其与 `createServerFn` 函数结合使用，以自定义服务端函数的行为。

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

有多种方法可用于定制中间件。如果您（希望）使用 TypeScript，这些方法的顺序将由类型系统强制执行，以确保最大的推断和类型安全。

- `middleware`：向链中添加一个中间件。
- `validator`：在数据对象传递给当前中间件及任何嵌套中间件之前修改它。
- `server`：定义中间件在执行嵌套中间件及最终的服务端函数之前执行的**服务端逻辑**，并将结果提供给下一个中间件。
- `client`：定义中间件在执行嵌套中间件及最终的客户端 RPC 函数（或服务端函数）之前执行的**客户端逻辑**，并将结果提供给下一个中间件。

## `middleware` 方法

`middleware` 方法用于向链中添加依赖的中间件，这些中间件将在**当前中间件之前**执行。只需调用 `middleware` 方法并传入一个中间件对象数组。

```tsx
import { createMiddleware } from '@tanstack/react-start'

const loggingMiddleware = createMiddleware().middleware([
  authMiddleware,
  loggingMiddleware,
])
```

类型安全的上下文和负载验证也会从父中间件继承！

## `validator` 方法

`validator` 方法用于在数据对象传递给当前中间件、嵌套中间件及最终的服务端函数之前修改它。该方法应接收一个函数，该函数接受数据对象并返回一个经过验证（并可选择修改）的数据对象。通常使用像 `zod` 这样的验证库来实现这一点。以下是一个示例：

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
    console.log('Workspace ID:', data.workspaceId)
    return next()
  })
```

## `server` 方法

`server` 方法用于定义中间件在执行嵌套中间件及最终的服务端函数前后执行的**服务端逻辑**。该方法接收一个包含以下属性的对象：

- `next`：一个函数，调用时将执行链中的下一个中间件。
- `data`：传递给服务端函数的数据对象。
- `context`：存储来自父中间件数据的对象。可以扩展为包含将传递给子中间件的额外数据。

## 从 `next` 返回必需的结果

`next` 函数用于执行链中的下一个中间件。**您必须等待并返回（或直接返回）提供给您的 `next` 函数的结果**，以便链继续执行。

```tsx
import { createMiddleware } from '@tanstack/react-start'

const loggingMiddleware = createMiddleware().server(async ({ next }) => {
  console.log('Request received')
  const result = await next()
  console.log('Response processed')
  return result
})
```

## 通过 `next` 向下一中间件提供上下文

`next` 函数可以可选地调用一个包含 `context` 属性的对象，该属性值为一个对象。您传递给此 `context` 值的任何属性都将合并到父 `context` 中，并提供给下一个中间件。

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
    console.log('Is awesome?', context.isAwesome)
    return next()
  },
)
```

## 客户端逻辑

尽管服务端函数主要是服务端绑定的操作，但客户端仍有大量围绕传出 RPC 请求的逻辑。这意味着我们也可以在中间件中定义客户端逻辑，该逻辑将在客户端围绕任何嵌套中间件及最终的 RPC 函数及其对客户端的响应执行。

## 客户端负载验证

默认情况下，中间件验证仅在服务端执行，以保持客户端包体积较小。但您也可以通过向 `createMiddleware` 函数传递 `validateClient: true` 选项选择在客户端验证数据。这将导致数据在发送到服务端之前在客户端验证，可能节省一次往返。

> 为什么不能为客户端传递不同的验证模式？
>
> 客户端验证模式是从服务端模式派生的。这是因为客户端验证模式用于在数据发送到服务端之前验证数据。如果客户端模式与服务端模式不同，服务端将接收未预期的数据，可能导致意外行为。

```tsx
import { createMiddleware } from '@tanstack/react-start'
import { zodValidator } from '@tanstack/zod-adapter'
import { z } from 'zod'

const workspaceMiddleware = createMiddleware({ validateClient: true })
  .validator(zodValidator(mySchema))
  .server(({ next, data }) => {
    console.log('Workspace ID:', data.workspaceId)
    return next()
  })
```

## `client` 方法

客户端中间件逻辑通过 `Middleware` 对象上的 `client` 方法定义。该方法用于定义中间件在执行嵌套中间件及最终的客户端 RPC 函数（或服务端函数，如果您正在进行 SSR 或从另一个服务端函数调用此函数）前后执行的客户端逻辑。

**客户端中间件逻辑与通过 `server` 方法创建的逻辑共享大部分相同的 API，但它是在客户端执行的。** 这包括：

- 要求调用 `next` 函数以继续链。
- 通过 `next` 函数向下一客户端中间件提供上下文的能力。
- 在数据对象传递给下一客户端中间件之前修改它的能力。

与 `server` 函数类似，它也接收一个包含以下属性的对象：

- `next`：一个函数，调用时将执行链中的下一个客户端中间件。
- `data`：传递给客户端函数的数据对象。
- `context`：存储来自父中间件数据的对象。可以扩展为包含将传递给子中间件的额外数据。

```tsx
const loggingMiddleware = createMiddleware().client(async ({ next }) => {
  console.log('Request sent')
  const result = await next()
  console.log('Response received')
  return result
})
```

## 将客户端上下文发送到服务端

**默认情况下，客户端上下文不会发送到服务端，因为这可能无意中将大量负载发送到服务端。** 如果您需要将客户端上下文发送到服务端，必须调用 `next` 函数并传递一个 `sendContext` 属性和对象以传输任何数据到服务端。传递给 `sendContext` 的任何属性将被合并、序列化，并与数据一起发送到服务端，并在任何嵌套服务端中间件的普通上下文对象上可用。

```tsx
const requestLogger = createMiddleware()
  .client(async ({ next, context }) => {
    return next({
      sendContext: {
        // 将工作区 ID 发送到服务端
        workspaceId: context.workspaceId,
      },
    })
  })
  .server(async ({ next, data, context }) => {
    // 哇！我们有了来自客户端的 workspaceId！
    console.log('Workspace ID:', context.workspaceId)
    return next()
  })
```

## 客户端发送上下文的安全性

您可能已经注意到，在上面的示例中，虽然客户端发送的上下文是类型安全的，但并未要求在运行时进行验证。如果您通过上下文传递动态用户生成的数据，可能会带来安全问题，因此**如果您通过上下文从客户端向服务端发送动态数据，应在服务端中间件中使用之前验证它。** 以下是一个示例：

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
    // 在使用前验证 workspaceId
    const workspaceId = zodValidator(z.number()).parse(context.workspaceId)
    console.log('Workspace ID:', workspaceId)
    return next()
  })
```

## 将服务端上下文发送到客户端

类似于将客户端上下文发送到服务端，您也可以通过调用 `next` 函数并传递一个 `sendContext` 属性和对象以传输任何数据到客户端。传递给 `sendContext` 的任何属性将被合并、序列化，并与响应一起发送到客户端，并在任何嵌套客户端中间件的普通上下文对象上可用。在 `client` 中调用 `next` 的返回对象包含从服务端发送到客户端的上下文，并且是类型安全的。中间件能够从通过 `middleware` 函数链式调用的先前中间件推断出从服务端发送到客户端的上下文。

> [!WARNING] > `next` 在 `client` 中的返回类型只能从当前中间件链中已知的中间件推断。因此，`next` 最准确的返回类型是在中间件链末端的中间件中。

```tsx
const serverTimer = createMiddleware().server(async ({ next }) => {
  return next({
    sendContext: {
      // 将当前时间发送到客户端
      timeFromServer: new Date(),
    },
  })
})

const requestLogger = createMiddleware()
  .middleware([serverTimer])
  .client(async ({ next }) => {
    const result = await next()
    // 哇！我们有了来自服务端的时间！
    console.log('Time from the server:', result.context.timeFromServer)

    return result
  })
```

## 读取/修改服务端响应

使用 `server` 方法的中间件在与服务端函数相同的上下文中执行，因此您可以完全按照[服务端函数上下文工具](../server-functions#server-function-context)来读取和修改请求头、状态码等。

## 修改客户端请求

使用 `client` 方法的中间件在与服务端函数**完全不同的客户端上下文**中执行，因此您不能使用相同的工具来读取和修改请求。但您仍然可以通过在调用 `next` 函数时返回额外属性来修改请求。当前支持的属性包括：

- `headers`：包含要添加到请求中的头的对象。

以下是一个使用此中间件为任何请求添加 `Authorization` 头的示例：

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

中间件可以通过两种方式使用：

- **全局中间件 (Global Middleware)**：应对每个请求执行的中间件。
- **服务端函数中间件 (Server Function Middleware)**：应对特定服务端函数执行的中间件。

## 全局中间件

全局中间件自动为应用程序中的每个服务端函数运行。这对于像身份验证、日志记录和监控这样的功能非常有用，这些功能应适用于所有请求。

要使用全局中间件，在项目中创建一个 `global-middleware.ts` 文件（通常位于 `app/global-middleware.ts`）。此文件在客户端和服务端环境中运行，是您注册全局中间件的地方。

以下是注册全局中间件的方法：

```tsx
// app/global-middleware.ts
import { registerGlobalMiddleware } from '@tanstack/react-start'
import { authMiddleware } from './middleware'

registerGlobalMiddleware({
  middleware: [authMiddleware],
})
```

### 全局中间件的类型安全

全局中间件类型本质上与服务端函数本身**分离**。这意味着如果全局中间件向服务端函数或其他特定于服务端函数的中间件提供额外的上下文，类型不会自动传递给服务端函数或其他特定于服务端函数的中间件。

```tsx
// app/global-middleware.ts
registerGlobalMiddleware({
  middleware: [authMiddleware],
})
```

```tsx
// authMiddleware.ts
const authMiddleware = createMiddleware().server(({ next, context }) => {
  console.log(context.user) // <-- 这不会被类型化！
  // ...
})
```

要解决此问题，将您尝试引用的全局中间件添加到服务端函数的中间件数组中。**全局中间件将被去重为单个条目（全局实例），您的服务端函数将接收正确的类型。**

以下是一个示例：

```tsx
import { authMiddleware } from './authMiddleware'

const fn = createServerFn()
  .middleware([authMiddleware])
  .handler(async ({ context }) => {
    console.log(context.user)
    // ...
  })
```

## 中间件执行顺序

中间件按依赖优先的顺序执行，从全局中间件开始，然后是服务端函数中间件。以下示例将按以下顺序记录：

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
```

## 环境 Tree Shaking

中间件功能根据每个生成包的环境进行 tree-shaking。

- 在服务端，不会进行 tree-shaking，因此中间件中使用的所有代码都将包含在服务端包中。
- 在客户端，所有服务端特定的代码将从客户端包中移除。这意味着在 `server` 方法中使用的任何代码始终会从客户端包中移除。如果 `validateClient` 设置为 `true`，客户端验证代码将包含在客户端包中，否则 `data` 验证代码也将被移除。
