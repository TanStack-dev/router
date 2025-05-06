---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:43:07.442Z'
id: catchBoundaryComponent
title: CatchBoundary component
---

`CatchBoundary` 组件是一个用于捕获子组件抛出错误的组件，它会渲染错误处理组件并可选地调用 `onCatch` 回调函数。该组件还接受 `getResetKey` 函数，用于在键值变化时声明式地重置组件状态。

## CatchBoundary 属性

`CatchBoundary` 组件接受以下属性：

### `props.getResetKey` 属性

- 类型: `() => string`
- 必填
- 该函数返回一个字符串，当键值变化时将用于重置组件状态。

### `props.children` 属性

- 类型: `React.ReactNode`
- 必填
- 无错误时渲染的子组件内容

### `props.errorComponent` 属性

- 类型: `React.ReactNode`
- 可选 - [`默认值: ErrorComponent`](./errorComponentComponent.md)
- 发生错误时渲染的组件

### `props.onCatch` 属性

- 类型: `(error: any) => void`
- 可选
- 当子组件抛出错误时触发的回调函数，参数为捕获的错误对象

## CatchBoundary 返回值

- 无错误时返回子组件内容
- 有错误时返回 `errorComponent` 组件

## 示例代码

```tsx
import { CatchBoundary } from '@tanstack/react-router'

function Component() {
  return (
    <CatchBoundary
      getResetKey={() => 'reset'}
      onCatch={(error) => console.error(error)}
    >
      <div>My Component</div>
    </CatchBoundary>
  )
}
```
