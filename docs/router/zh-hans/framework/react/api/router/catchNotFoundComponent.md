---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:42:50.384Z'
id: catchNotFoundComponent
title: CatchNotFound Component
---

`CatchNotFound` 组件是一个用于捕获其子组件抛出的未找到错误的组件，它会渲染一个备用组件并可选地调用 `onCatch` 回调函数。当路径变化时，该组件会重置。

## CatchNotFound 属性

`CatchNotFound` 组件接收以下属性：

### `props.children` 属性

- 类型: `React.ReactNode`
- 必填
- 无错误时需要渲染的子组件

### `props.fallback` 属性

- 类型: `(error: NotFoundError) => React.ReactElement`
- 可选
- 发生错误时需要渲染的组件

### `props.onCatch` 属性

- 类型: `(error: any) => void`
- 可选
- 当子组件抛出错误时会调用的回调函数，参数为抛出的错误对象

## CatchNotFound 返回值

- 无错误时返回子组件。
- 有错误时返回 `fallback` 组件。

## 示例

```tsx
import { CatchNotFound } from '@tanstack/react-router'

function Component() {
  return (
    <CatchNotFound
      fallback={(error) => <p>Not found error! {JSON.stringify(error)}</p>}
    >
      <ComponentThatMightThrowANotFoundError />
    </CatchNotFound>
  )
}
```
