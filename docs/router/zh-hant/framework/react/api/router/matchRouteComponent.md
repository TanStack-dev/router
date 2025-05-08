---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:50.922Z'
id: matchRouteComponent
title: MatchRoute component
---

`useMatchRoute` 鉤子 (hook) 的元件版本。它接受與 `useMatchRoute` 相同的選項，並額外提供用於條件式渲染 (conditional rendering) 的屬性。

## MatchRoute 屬性 (props)

`MatchRoute` 元件接受與 `useMatchRoute` 鉤子相同的選項，並額外提供用於條件式渲染的屬性。

### `...props` 屬性

- 類型: [`UseMatchRouteOptions`](./UseMatchRouteOptionsType.md)

### `children` 屬性

- 可選
- `React.ReactNode`
  - 當路由匹配時將渲染的元件。
- `((params: TParams | false) => React.ReactNode)`
  - 一個函式，當路由匹配時會傳入匹配路由的參數 (params)，若未匹配則傳入 `false`。這對於需要始終渲染，但根據路由匹配與否渲染不同屬性的元件非常有用。

## MatchRoute 回傳值

回傳 `children` 屬性或 `children` 函式的回傳值。

## 範例

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
