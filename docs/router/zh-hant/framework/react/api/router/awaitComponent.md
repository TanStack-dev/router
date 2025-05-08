---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:28.013Z'
id: awaitComponent
title: Await component
---

`Await` 元件是一個會暫停渲染直到提供的 promise 被解析 (resolved) 或拒絕 (rejected) 的元件。  
此元件僅在 React 18 中需要使用。  
若您使用 React 19，可以直接使用 `use()` 鉤子 (hook) 替代。

## Await 屬性

`Await` 元件接受以下屬性 (props)：

### `props.promise` 屬性

- 類型 (Type): `Promise<T>`
- 必填 (Required)
- 需要等待的 promise 物件。

### `props.children` 屬性

- 類型 (Type): `(result: T) => React.ReactNode`
- 必填 (Required)
- 一個會以 promise 解析值作為參數呼叫的渲染函式。

## Await 回傳行為

- 若 promise 被拒絕 (rejected)，會拋出錯誤。
- 若 promise 處於待定狀態 (pending)，會暫停渲染 (拋出 promise)。
- 若 promise 被解析 (resolved)，會透過 `props.children` 渲染函式回傳延遲 promise 的解析值。

## 範例

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
