---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:43:24.016Z'
id: awaitComponent
title: Await component
---

`Await` 组件是一个会暂停渲染直到提供的 promise 被解决 (resolved) 或拒绝 (rejected) 的组件。  
该组件仅在 React 18 中需要使用。  
如果使用 React 19，可以直接使用 `use()` 钩子 (hook) 替代。

## Await 属性

`Await` 组件接受以下属性：

### `props.promise` 属性

- 类型：`Promise<T>`
- 必填
- 需要等待的 promise 对象。

### `props.children` 属性

- 类型：`(result: T) => React.ReactNode`
- 必填
- 接收 promise 解决值并返回 React 节点的渲染函数。

## Await 返回值

- 如果 promise 被拒绝，则抛出错误。
- 如果 promise 处于等待状态，则暂停渲染（抛出 promise）。
- 如果 promise 被解决，则通过 `props.children` 渲染函数返回延迟 promise 的解决值。

## 示例

```tsx
import { Await } from '@tanstack/react-router'

function Component() {
  const { deferredPromise } = route.useLoaderData()

  return (
    <Await promise={deferredPromise}>
      {(data) => <div>{JSON.stringify(data)}</div>}
    </Await>
  )
}
```
