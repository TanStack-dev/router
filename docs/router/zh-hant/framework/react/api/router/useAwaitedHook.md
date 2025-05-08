---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:30.426Z'
id: useAwaitedHook
title: useAwaited hook
---

`useAwaited` 方法是一個掛鉤 (hook)，它會暫停執行直到提供的承諾 (promise) 被解析 (resolved) 或拒絕 (rejected)。

## useAwaited 選項

`useAwaited` 掛鉤接受一個參數，即 `options` 物件。

### `options.promise` 選項

- 類型：`Promise<T>`
- 必填
- 需要等待的延遲承諾 (deferred promise)。

## useAwaited 返回值

- 如果承諾被拒絕，則拋出錯誤。
- 如果承諾處於待定狀態 (pending)，則暫停執行（拋出一個承諾）。
- 如果承諾被解析，則返回延遲承諾的解析值。

## 範例

```tsx
import { useAwaited } from '@tanstack/react-router'

function Component() {
  const { deferredPromise } = route.useLoaderData()

  const data = useAwaited({ promise: myDeferredPromise })
  // ...
}
```
