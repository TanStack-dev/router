---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:40:35.690Z'
id: deferFunction
title: defer function
---

> [!CAUTION]
> 不再需要手动调用 `defer` 函数，现在 Promise 会被自动处理。

`defer` 函数将一个 Promise 包装为带有延迟状态的对象，该对象可用于检查 Promise 的状态。这个被延迟的 Promise 可以传递给 [`useAwaited`](./useAwaitedHook.md) 钩子或 [`<Await>`](./awaitComponent.md) 组件，以便在 Promise 被解决 (resolved) 或拒绝 (rejected) 之前暂停渲染。

`defer` 函数接收一个参数，即需要包装为延迟状态对象的 `promise`。

## defer 参数选项

- 类型: `Promise<T>`
- 必填
- 需要包装为延迟状态对象的 Promise。

## defer 返回值

- 一个可传递给 [`useAwaited`](./useAwaitedHook.md) 钩子或 [`<Await>`](./awaitComponent.md) 组件的 Promise。

## 示例代码

```tsx
import { defer } from '@tanstack/react-router'

const route = createRoute({
  loader: () => {
    const deferredPromise = defer(fetch('/api/data'))
    return { deferredPromise }
  },
  component: MyComponent,
})

function MyComponent() {
  const { deferredPromise } = Route.useLoaderData()

  const data = useAwaited({ promise: deferredPromise })

  // 或者

  return (
    <Await promise={deferredPromise}>
      {(data) => <div>{JSON.stringify(data)}</div>}
    </Await>
  )
}
```
