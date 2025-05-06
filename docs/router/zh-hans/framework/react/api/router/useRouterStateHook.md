---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:31:12.749Z'
id: useRouterStateHook
title: useRouterState hook
---

`useRouterState` 方法是一个钩子，用于返回路由器的当前内部状态。该钩子在组件中访问路由器的当前状态时非常有用。

> [!TIP]
> 如果您需要访问当前位置或当前匹配项，建议先尝试使用 [`useLocation`](./useLocationHook.md) 和 [`useMatches`](./useMatchesHook.md) 钩子。这些钩子设计得比直接访问路由器状态更符合人体工程学且更易用。

## useRouterState 选项

`useRouterState` 钩子接受一个可选的 `options` 对象。

### `opts.select` 选项

- 类型: `(state: RouterState) => TSelected`
- 可选
- 如果提供，该函数将接收 [`RouterState`](./RouterStateType.md) 对象作为参数，其返回值将作为 `useRouterState` 的返回结果。

### `opts.structuralSharing` 选项

- 类型: `boolean`
- 可选
- 配置是否为 `select` 返回的值启用结构共享 (structural sharing)。
- 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。

## useRouterState 返回值

- 当前的 [`RouterState`](./RouterStateType.md) 对象，如果提供了 `select` 函数则返回 `TSelected` 类型值。

## 示例

```tsx
import { useRouterState } from '@tanstack/react-router'

function Component() {
  const state = useRouterState()
  //    ^ RouterState

  // 或

  const selected = useRouterState({
    select: (state) => state.location,
  })
  //    ^ ParsedLocation

  // ...
}
```
