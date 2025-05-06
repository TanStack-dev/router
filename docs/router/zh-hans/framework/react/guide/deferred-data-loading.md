---
source-updated-at: '2025-04-13T21:11:21.000Z'
translation-updated-at: '2025-05-06T22:02:01.683Z'
id: deferred-data-loading
title: 延迟数据加载
---

TanStack Router 的设计理念是并行运行加载器 (loader) 并等待所有加载器解析完成后再渲染下一个路由。大多数情况下这很理想，但有时您可能希望先向用户展示部分内容，同时让其他非关键数据在后台继续加载。

延迟数据加载 (Deferred data loading) 是一种模式，允许路由在渲染新位置的关键数据/标记时，让较慢的非关键路由数据在后台继续解析。该流程在客户端和服务端（通过流式传输）均可工作，是提升应用感知性能 (perceived performance) 的有效方式。

如果您使用 [TanStack Query](https://react-query.tanstack.com) 或其他数据获取库，延迟数据加载的工作方式会有所不同。请直接跳转到 [使用外部库实现延迟数据加载](#deferred-data-loading-with-external-libraries) 章节了解更多信息。

## 使用 `Await` 实现延迟数据加载

要延迟加载较慢或非关键数据，只需在加载器响应中返回一个 **未等待/未解析** 的 Promise：

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

只要任意被等待的 Promise 解析完成，下一个路由就会开始渲染，同时延迟的 Promise 继续在后台解析。

在组件中，可以通过 `Await` 组件来解析和使用延迟的 Promise：

```tsx
// src/routes/posts.$postId.tsx
import { createFileRoute, Await } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  // ...
  component: PostIdComponent,
})

function PostIdComponent() {
  const { deferredSlowData, fastData } = Route.useLoaderData()

  // 使用 fastData 进行某些操作

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
> 如果组件采用代码分割 (code-split)，可以使用 [getRouteApi 函数](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper) 来避免导入 `Route` 配置即可访问类型化的 `useLoaderData()` 钩子。

`Await` 组件通过触发最近的悬念边界 (suspense boundary) 来解析 Promise，解析完成后会将组件 `children` 作为函数渲染，并传入解析后的数据。

如果 Promise 被拒绝，`Await` 组件会抛出序列化的错误，该错误可以被最近的错误边界捕获。

[//]: # 'DeferredWithAwaitFinalTip'

> [!TIP]
> 在 React 19 中，您可以使用 `use()` 钩子替代 `Await`

[//]: # 'DeferredWithAwaitFinalTip'

## 使用外部库实现延迟数据加载

当您通过 [TanStack Query](https://tanstack.com/query) 等外部库使用 [外部数据加载](./external-data-loading.md) 策略时，延迟数据加载的工作方式有所不同，因为这些库会在 TanStack Router 之外处理数据获取和缓存。

此时不需要使用 `defer` 和 `Await`，而应该通过路由的 `loader` 启动数据获取，然后在组件中使用库提供的钩子访问数据：

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

然后在组件中使用库的钩子访问数据：

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

  // 使用 fastData 进行某些操作

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

流式 Promise 的生命周期与其关联的加载器数据相同，它们甚至可以被预加载！

[//]: # 'SSRContent'

## 服务端渲染 (SSR) 与流式延迟数据

**流式传输需要服务端支持，并且 TanStack Router 需正确配置。**

请完整阅读 [流式 SSR 指南](./ssr.md#streaming-ssr) 了解服务端流式配置的逐步指导。

## SSR 流式生命周期

以下是 TanStack Router 延迟数据流式传输的高层工作流程：

- 服务端
  - 从路由加载器返回的 Promise 会被标记和追踪
  - 所有加载器解析完成，延迟的 Promise 会被序列化并嵌入 HTML
  - 路由开始渲染
  - 通过 `<Await>` 组件渲染的延迟 Promise 会触发悬念边界，允许服务端流式传输至此的 HTML
- 客户端
  - 接收服务端发送的初始 HTML
  - `<Await>` 组件使用占位 Promise 暂停渲染，等待服务端数据解析
- 服务端
  - 当延迟 Promise 解析后，其结果（或错误）会被序列化并通过内联脚本标签流式传输至客户端
  - 已解析的 `<Await>` 组件及其悬念边界会被处理，生成的 HTML 与脱水数据 (dehydrated data) 一同流式传输至客户端
- 客户端
  - `<Await>` 中的占位 Promise 会被流式数据/错误响应解析，根据结果渲染内容或将错误抛给最近的错误边界

[//]: # 'SSRContent'
