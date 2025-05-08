---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:27.603Z'
id: useMatchRouteHook
title: useMatchRoute hook
---

`useMatchRoute` 鉤子 (hook) 是一個返回 `matchRoute` 函式的鉤子，該函式可用於將路由與當前或待定位置進行匹配。

## useMatchRoute 返回值

- 一個 `matchRoute` 函式，可用於將路由與當前或待定位置進行匹配。

## matchRoute 函式

`matchRoute` 函式是一個可用於將路由與當前或待定位置進行匹配的函式。

### matchRoute 函式選項

`matchRoute` 函式接受一個參數，即 `options` 物件。

- 類型: [`UseMatchRouteOptions`](./UseMatchRouteOptionsType.md)

### matchRoute 函式返回值

- 匹配路由的參數，若無匹配路由則返回 `false`

## 範例

```tsx
import { useMatchRoute } from '@tanstack/react-router'

// 當前位置: /posts/123
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts/$postId' })
  //    ^ { postId: '123' }
}

// 當前位置: /posts/123
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts' })
  //    ^ false
}

// 當前位置: /posts/123
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts', fuzzy: true })
  //    ^ {}
}

// 當前位置: /posts
// 待定位置: /posts/123
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts/$postId', pending: true })
  //    ^ { postId: '123' }
}

// 當前位置: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts/$postId/foo/$fooId' })
  //    ^ { postId: '123', fooId: '456' }
}

// 當前位置: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { postId: '123' },
  })
  //    ^ { postId: '123', fooId: '456' }
}

// 當前位置: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { postId: '789' },
  })
  //    ^ false
}

// 當前位置: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { fooId: '456' },
  })
  //    ^ { postId: '123', fooId: '456' }
}

// 當前位置: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { postId: '123', fooId: '456' },
  })
  //    ^ { postId: '123', fooId: '456' }
}

// 當前位置: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { postId: '789', fooId: '456' },
  })
  //    ^ false
}
```
