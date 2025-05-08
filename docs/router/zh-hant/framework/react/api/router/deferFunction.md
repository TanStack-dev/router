---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:10.085Z'
id: deferFunction
title: defer function
---

> [!CAUTION]
> 你不再需要手動呼叫 `defer`，現在 Promise 會自動處理。

`defer` 函式會將一個 promise 包裝成具有延遲狀態的物件，該物件可用於檢查 promise 的狀態。這個延遲的 promise 可以傳遞給 [`useAwaited`](./useAwaitedHook.md) 鉤子 (hook) 或 [`<Await>`](./awaitComponent.md) 元件，以便在 promise 被解析 (resolved) 或拒絕 (rejected) 之前暫停渲染。

`defer` 函式接受單一參數，即要包裝成延遲狀態物件的 `promise`。

## defer 選項

- 類型: `Promise<T>`
- 必填
- 要包裝成延遲狀態物件的 promise。

## defer 回傳值

- 一個可傳遞給 [`useAwaited`](./useAwaitedHook.md) 鉤子 (hook) 或 [`<Await>`](./awaitComponent.md) 元件的 promise。

## 範例

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

  // 或

  return (
    <Await promise={deferredPromise}>
      {(data) => <div>{JSON.stringify(data)}</div>}
    </Await>
  )
}
```
