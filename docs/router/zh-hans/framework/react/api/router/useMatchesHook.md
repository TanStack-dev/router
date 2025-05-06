---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:32:24.275Z'
id: useMatchesHook
title: useMatches hook
---

`useMatches` 钩子会返回路由中所有的 [`RouteMatch`](./RouteMatchType.md) 对象，**无论调用者在 React 组件树中的位置如何**。

> [!TIP]
> 如果只需要父级或子级匹配项，可以根据需求使用 [`useParentMatches`](./useParentMatchesHook.md) 或 [`useChildMatches`](./useChildMatchesHook.md)。

## useMatches 选项

`useMatches` 钩子接受一个可选的参数，即 `options` 对象。

### `opts.select` 选项

- 可选
- `(matches: RouteMatch[]) => TSelected`
- 如果提供，此函数会接收路由匹配项作为参数，其返回值将作为 `useMatches` 的返回结果。该值还会用于通过浅层比较决定是否触发父组件的重新渲染。

### `opts.structuralSharing` 选项

- 类型: `boolean`
- 可选
- 配置是否为 `select` 返回的值启用结构共享。
- 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。

## useMatches 返回值

- 如果提供了 `select` 函数，则返回该函数的执行结果。
- 如果未提供 `select` 函数，则返回 [`RouteMatch`](./RouteMatchType.md) 对象数组。

## 示例

```tsx
import { useMatches } from '@tanstack/react-router'

function Component() {
  const matches = useMatches()
  //     ^? [RouteMatch, RouteMatch, ...]
  // ...
}
```
