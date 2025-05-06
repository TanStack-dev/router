---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:28:56.000Z
title: 预加载
---

# 预加载

TanStack Router 中的预加载是一种在用户实际导航到路由之前就加载该路由的方式。这对于用户接下来可能访问的路由非常有用。例如，如果您有一个帖子列表，而用户很可能会点击其中的某个帖子，您可以预先加载该帖子路由，这样当用户点击时就能立即显示。

## 支持的预加载策略

- 意图预加载
  - **"意图"** 预加载通过使用 `<Link>` 组件上的悬停和触摸开始事件来预加载目标路由的依赖项。
  - 此策略适用于预加载用户接下来可能访问的路由。
- 视口可见性预加载
  - **"视口"** 预加载通过使用 Intersection Observer API，在 `<Link>` 组件进入视口时预加载目标路由的依赖项。
  - 此策略适用于预加载位于首屏下方或屏幕外的路由。
- 渲染预加载
  - **"渲染"** 预加载在 `<Link>` 组件渲染到 DOM 时就立即预加载目标路由的依赖项。
  - 此策略适用于预加载始终需要的路由。

## 预加载数据在内存中保留多久？

预加载的路由匹配会被临时缓存在内存中，但有以下重要注意事项：

- **未使用的预加载数据默认在 30 秒后移除。** 可以通过在路由器上设置 `defaultPreloadMaxAge` 选项来配置此时间。
- **显然，当路由被加载时，其预加载版本会提升为路由器的正常待处理匹配状态。**

如果您需要对预加载数据的预加载、缓存和/或垃圾回收有更多控制，应该使用像 [TanStack Query](https://tanstack.com/query) 这样的外部缓存库。

为应用程序预加载路由的最简单方法是将整个路由器的 `defaultPreload` 选项设置为 `intent`：

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  defaultPreload: 'intent',
})
```

这将默认启用应用程序中所有 `<Link>` 组件的 `intent` 预加载。您还可以在单个 `<Link>` 组件上设置 `preload` 属性来覆盖默认行为。

## 预加载延迟

默认情况下，预加载会在用户悬停或触摸 `<Link>` 组件 **50 毫秒** 后开始。您可以通过在路由器上设置 `defaultPreloadDelay` 选项来更改此延迟：

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  defaultPreloadDelay: 100,
})
```

您还可以在单个 `<Link>` 组件上设置 `preloadDelay` 属性，以基于每个链接覆盖默认行为。

## 内置预加载与 `preloadStaleTime`

如果您使用内置加载器，可以通过设置 `routerOptions.defaultPreloadStaleTime` 或 `routeOptions.preloadStaleTime` 为毫秒数来控制预加载数据被视为新鲜的时间，直到触发另一次预加载。**默认情况下，预加载数据被视为新鲜的时间为 30 秒。**

要更改此设置，您可以在路由器上设置 `defaultPreloadStaleTime` 选项：

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  defaultPreloadStaleTime: 10_000,
})
```

或者，您可以在单个路由上使用 `routeOptions.preloadStaleTime` 选项：

```tsx
// src/routes/posts.$postId.tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => fetchPost(params.postId),
  // 如果预加载缓存超过 10 秒，则重新预加载路由
  preloadStaleTime: 10_000,
})
```

## 使用外部库进行预加载

当集成像 React Query 这样具有自己确定陈旧数据机制的外部缓存库时，您可能希望覆盖 TanStack Router 的默认预加载和重新验证逻辑。这些库通常使用像 staleTime 这样的选项来控制数据的新鲜度。

要在 TanStack Router 中自定义预加载行为并充分利用外部库的缓存策略，您可以通过将 `routerOptions.defaultPreloadStaleTime` 或 `routeOptions.preloadStaleTime` 设置为 0 来绕过内置缓存。这确保所有预加载在内部都被标记为陈旧，并且总是调用加载器，从而允许您的外部库（如 React Query）管理数据加载和缓存。

例如：

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  defaultPreloadStaleTime: 0,
})
```

这样，您就可以使用像 React Query 的 `staleTime` 这样的选项来控制预加载数据的新鲜度。

## 手动预加载

如果需要手动预加载路由，可以使用路由器的 `preloadRoute` 方法。它接受一个标准的 TanStack `NavigateOptions` 对象，并返回一个在路由预加载完成时解析的 Promise。

```tsx
function Component() {
  const router = useRouter()

  useEffect(() => {
    async function preload() {
      try {
        const matches = await router.preloadRoute({
          to: postRoute,
          params: { id: 1 },
        })
      } catch (err) {
        // 预加载路由失败
      }
    }

    preload()
  }, [router])

  return <div />
}
```

如果只需要预加载路由的 JS 块，可以使用路由器的 `loadRouteChunk` 方法。它接受一个路由对象，并返回一个在路由块加载完成时解析的 Promise。

```tsx
function Component() {
  const router = useRouter()

  useEffect(() => {
    async function preloadRouteChunks() {
      try {
        const postsRoute = router.routesByPath['/posts']
        await Promise.all([
          router.loadRouteChunk(router.routesByPath['/']),
          router.loadRouteChunk(postsRoute),
          router.loadRouteChunk(postsRoute.parentRoute),
        ])
      } catch (err) {
        // 预加载路由块失败
      }
    }

    preloadRouteChunks()
  }, [router])

  return <div />
}
```
