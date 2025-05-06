---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:33:41.509Z'
id: useMatchRouteHook
title: useMatchRoute hook
---

`useMatchRoute` 钩子 (hook) 返回一个 `matchRoute` 函数，可用于将路由与当前或待定位置 (pending location) 进行匹配。

## useMatchRoute 返回值

- 一个 `matchRoute` 函数，可用于将路由与当前或待定位置进行匹配。

## matchRoute 函数

`matchRoute` 函数可用于将路由与当前或待定位置进行匹配。

### matchRoute 函数选项

`matchRoute` 函数接受一个参数，即 `options` 对象。

- 类型: [`UseMatchRouteOptions`](./UseMatchRouteOptionsType.md)

### matchRoute 函数返回值

- 匹配成功的路由参数 (params)，若未匹配则返回 `false`

## 示例

```tsx
import { useMatchRoute } from '@tanstack/react-router'

// 当前位置: /posts/123
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts/$postId' })
  //    ^ { postId: '123' }
}

// 当前位置: /posts/123
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts' })
  //    ^ false
}

// 当前位置: /posts/123
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts', fuzzy: true })
  //    ^ {}
}

// 当前位置: /posts
// 待定位置: /posts/123
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts/$postId', pending: true })
  //    ^ { postId: '123' }
}

// 当前位置: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts/$postId/foo/$fooId' })
  //    ^ { postId: '123', fooId: '456' }
}

// 当前位置: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { postId: '123' },
  })
  //    ^ { postId: '123', fooId: '456' }
}

// 当前位置: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { postId: '789' },
  })
  //    ^ false
}

// 当前位置: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { fooId: '456' },
  })
  //    ^ { postId: '123', fooId: '456' }
}

// 当前位置: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { postId: '123', fooId: '456' },
  })
  //    ^ { postId: '123', fooId: '456' }
}

// 当前位置: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { postId: '789', fooId: '456' },
  })
  //    ^ false
}
```
