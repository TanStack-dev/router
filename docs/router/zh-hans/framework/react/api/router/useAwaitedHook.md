---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:36:59.893Z'
id: useAwaitedHook
title: useAwaited hook
---

`useAwaited` 方法是一个钩子 (hook)，它会暂停执行直到提供的承诺 (promise) 被解决 (resolved) 或拒绝 (rejected)。

## useAwaited 选项

`useAwaited` 钩子 (hook) 接受一个参数，即 `options` 对象。

### `options.promise` 选项

- 类型: `Promise<T>`
- 必需
- 需要等待的延迟承诺 (deferred promise)。

## useAwaited 返回值

- 如果承诺 (promise) 被拒绝 (rejected)，则抛出错误。
- 如果承诺 (promise) 处于待定状态 (pending)，则暂停执行（抛出承诺）。
- 如果承诺 (promise) 被解决 (resolved)，则返回延迟承诺 (deferred promise) 的解决值。

## 示例

```tsx
import { useAwaited } from '@tanstack/react-router'

function Component() {
  const { deferredPromise } = route.useLoaderData()

  const data = useAwaited({ promise: myDeferredPromise })
  // ...
}
```
