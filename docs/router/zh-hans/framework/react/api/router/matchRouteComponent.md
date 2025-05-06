---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:38:54.296Z'
id: matchRouteComponent
title: MatchRoute component
---

`useMatchRoute` 钩子的组件版本。它接受与 `useMatchRoute` 相同的选项，并额外提供用于条件渲染的属性。

## MatchRoute 属性

`MatchRoute` 组件接受与 `useMatchRoute` 钩子相同的选项，并额外提供用于条件渲染的属性。

### `...props` 属性

- 类型: [`UseMatchRouteOptions`](./UseMatchRouteOptionsType.md)

### `children` 属性

- 可选
- `React.ReactNode`
  - 当路由匹配时将被渲染的组件。
- `((params: TParams | false) => React.ReactNode)`
  - 一个函数，当路由匹配时将传入匹配路由的参数，若无匹配则传入 `false`。这对于需要始终渲染但根据路由匹配状态渲染不同属性的组件非常有用。

## MatchRoute 返回值

返回 `children` 属性或 `children` 函数的返回值。

## 示例

```tsx
import { MatchRoute } from '@tanstack/react-router'

function Component() {
  return (
    <div>
      <MatchRoute to="/posts/$postId" params={{ postId: '123' }} pending>
        {(match) => <Spinner show={!!match} wait="delay-50" />}
      </MatchRoute>
    </div>
  )
}
```
