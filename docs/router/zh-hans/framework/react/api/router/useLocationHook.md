---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:34:26.489Z'
id: useLocationHook
title: useLocation hook
---

`useLocation` 方法是一个钩子 (hook)，用于返回当前的 [`location`](./ParsedLocationType.md) 对象。当需要在当前路径 (location) 变化时执行某些副作用 (side effect) 时，这个钩子非常有用。

## useLocation 选项

`useLocation` 钩子接受一个可选的 `options` 对象。

### `opts.select` 选项

- 类型: `(state: ParsedLocationType) => TSelected`
- 可选
- 如果提供，该函数会接收 [`location`](./ParsedLocationType.md) 对象作为参数，其返回值将作为 `useLocation` 的返回结果。

## useLocation 返回值

- 当前的 [`location`](./ParsedLocationType.md) 对象，如果提供了 `select` 函数则返回 `TSelected` 类型的结果。

## 示例

```tsx
import { useLocation } from '@tanstack/react-router'

function Component() {
  const location = useLocation()
  //    ^ ParsedLocation

  // 或者

  const pathname = useLocation({
    select: (location) => location.pathname,
  })
  //    ^ string

  // ...
}
```
