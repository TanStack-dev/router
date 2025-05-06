---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:27:19.000Z
title: 延迟数据加载
id: deferred-data-loading
---

TanStack Router 的设计理念是并行运行加载器，并等待所有加载器解析完成后再渲染下一个路由。这在大多数情况下非常理想，但偶尔您可能希望先向用户展示部分内容，同时其他数据在后台继续加载。

延迟数据加载是一种模式，允许路由器在渲染下一个位置的关键数据/标记时，让速度较慢的非关键路由数据在后台继续解析。这一过程在客户端和服务器端（通过流式传输）均可实现，是提升应用感知性能的有效方式。

如果您正在使用 [TanStack Query](https://react-query.tanstack.com) 或其他数据获取库，延迟数据加载的工作方式会有所不同。请直接跳转到 [使用外部库的延迟数据加载](#deferred-data-loading-with-external-libraries) 部分了解更多信息。

## 使用 `Await` 实现延迟数据加载

要延迟加载速度较慢或非关键数据，只需在加载器响应中返回一个 **未等待/未解析** 的 Promise：

```tsx
// src/routes/posts.$postId.tsx
import { createFileRoute, defer } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  loader: async () => {
    // 获取较慢的数据但不等待
    const slowDataPromise = fetchSlowData()

    // 获取并等待快速解析的数据
    const fastData = await fetchFastData()

    return {
      fastData,
      deferredSlowData: slowDataPromise,
    }
  },
})
```

一旦所有需要等待的 Promise 解析完成，下一个路由将开始渲染，同时延迟的 Promise 继续解析。

在组件中，可以使用 `Await` 组件来解析和利用延迟的 Promise：

```tsx
// src/routes/posts.$postId.tsx
import { createFileRoute, Await } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  // ...
  component: PostIdComponent,
})

function PostIdComponent() {
  const { deferredSlowData, fastData } = Route.useLoaderData()

  // 处理 fastData

  return (
    <Await promise={deferredSlowData} fallback={<div>Loading...</div>}>
      {(data) => {
        return <div>{data}</div>
      }}
    </Await>
  )
}
```

> [!TIP]
> 如果您的组件是代码分割的，可以使用 [getRouteApi 函数](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper) 来避免导入 `Route` 配置以获取类型化的 `useLoaderData()` 钩子。

`Await` 组件通过触发最近的 Suspense 边界来解析 Promise，解析完成后会将组件 `children` 作为函数渲染，并传入解析后的数据。

如果 Promise 被拒绝，`Await` 组件将抛出序列化的错误，该错误可以被最近的错误边界捕获。

[//]: # 'DeferredWithAwaitFinalTip'

> [!TIP]
> 在 React 19 中，您可以使用 `use()` 钩子替代 `Await`

[//]: # 'DeferredWithAwaitFinalTip'

## 使用外部库实现延迟数据加载

当您通过 [外部数据加载](./external-data-loading.md) 策略（如使用 [TanStack Query](https://tanstack.com/query) 等外部库）来获取路由信息时，延迟数据加载的工作方式有所不同，因为这些库会在 TanStack Router 之外处理数据获取和缓存。

因此，您不需要使用 `defer` 和 `Await`，而是应该通过路由的 `loader` 启动数据获取，然后在组件中使用库的钩子来访问数据。

```tsx
// src/routes/posts.$postId.tsx
import { createFileRoute } from '@tanstack/react-router'
import { slowDataOptions, fastDataOptions } from '~/api/query-options'

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ context: { queryClient } }) => {
    // 启动较慢数据的获取但不等待
    queryClient.prefetchQuery(slowDataOptions())

    // 获取并等待快速解析的数据
    await queryClient.ensureQueryData(fastDataOptions())
  },
})
```

然后在组件中，您可以使用库的钩子来访问数据：

```tsx
// src/routes/posts.$postId.tsx
import { createFileRoute } from '@tanstack/react-router'
import { useSuspenseQuery } from '@tanstack/react-query'
import { slowDataOptions, fastDataOptions } from '~/api/query-options'

export const Route = createFileRoute('/posts/$postId')({
  // ...
  component: PostIdComponent,
})

function PostIdComponent() {
  const fastData = useSuspenseQuery(fastDataOptions())

  // 处理 fastData

  return (
    <Suspense fallback={<div>Loading...</div>}>
      <SlowDataComponent />
    </Suspense>
  )
}

function SlowDataComponent() {
  const data = useSuspenseQuery(slowDataOptions())

  return <div>{data}</div>
}
```

## 缓存与失效

流式传输的 Promise 遵循与其关联的加载器数据相同的生命周期，它们甚至可以被预加载！

[//]: # 'SSRContent'

## SSR 和流式延迟数据

**流式传输需要服务器支持，并且 TanStack Router 需要正确配置才能使用。**

请完整阅读 [流式 SSR 指南](/docs/framework/react/guide/ssr#streaming-ssr) 了解如何为流式传输设置服务器的详细步骤。

## SSR 流式生命周期

以下是 TanStack Router 中延迟数据流式传输的高层次概述：

- 服务器端
  - 从路由加载器返回的 Promise 会被标记和跟踪
  - 所有加载器解析完成，延迟的 Promise 被序列化并嵌入 HTML
  - 路由开始渲染
  - 使用 `<Await>` 组件渲染的延迟 Promise 会触发 Suspense 边界，允许服务器流式传输 HTML 到该点
- 客户端
  - 客户端接收来自服务器的初始 HTML
  - `<Await>` 组件使用占位 Promise 暂停，等待服务器解析数据
- 服务器端
  - 当延迟的 Promise 解析后，其结果（或错误）被序列化并通过内联脚本标签流式传输到客户端
  - 解析后的 `<Await>` 组件及其 Suspense 边界被处理，生成的 HTML 与脱水数据一起流式传输到客户端
- 客户端
  - `<Await>` 中的占位 Promise 被流式传输的数据/错误响应解析，然后渲染结果或将错误抛给最近的错误边界

[//]: # 'SSRContent'
