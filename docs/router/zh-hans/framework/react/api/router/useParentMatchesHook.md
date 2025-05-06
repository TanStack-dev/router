---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:31:32.589Z'
id: useParentMatchesHook
title: useParentMatches hook
---

`useParentMatches` 钩子返回从根路由到当前匹配上下文的直接父级之间的所有父级 [`RouteMatch`](./RouteMatchType.md) 对象。**它不包含当前匹配项（可通过 `useMatch` 钩子获取）。**

> [!IMPORTANT]
> 如果路由器存在待处理的匹配项且正在显示其待处理组件回退时，将使用 `router.state.pendingMatches` 替代 `router.state.matches`。

## useParentMatches 选项

`useParentMatches` 钩子接受一个可选的 `options` 对象。

### `opts.select` 选项

- 可选
- `(matches: RouteMatch[]) => TSelected`
- 如果提供，此函数将接收路由匹配数组作为参数，其返回值将作为 `useParentMatches` 的返回结果。该值还会用于通过浅层相等性检查决定是否触发父组件重新渲染。

### `opts.structuralSharing` 选项

- 类型: `boolean`
- 可选
- 配置是否为 `select` 返回的值启用结构共享 (structural sharing)。
- 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。

## useParentMatches 返回值

- 若提供 `select` 函数，则返回该函数的执行结果。
- 若未提供 `select` 函数，则返回 [`RouteMatch`](./RouteMatchType.md) 对象数组。

## 示例

```tsx
import { useParentMatches } from '@tanstack/react-router'

function Component() {
  const parentMatches = useParentMatches()
  //    ^ [RouteMatch, RouteMatch, ...]
}
```
