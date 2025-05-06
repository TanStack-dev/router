---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:35:35.996Z'
id: useChildMatchesHook
title: useChildMatches hook
---

`useChildMatches` 钩子返回从最近匹配到最末端叶子匹配的所有子级 [`RouteMatch`](./RouteMatchType.md) 对象。**注意：该结果不包含当前匹配项，当前匹配项可通过 `useMatch` 钩子获取。**

> [!IMPORTANT]
> 如果路由存在待定匹配且正在显示其待定组件回退内容，则会使用 `router.state.pendingMatches` 替代 `router.state.matches`。

## useChildMatches 配置项

`useChildMatches` 钩子接受一个可选的参数，即 `options` 配置对象。

### `opts.select` 配置项

- 可选参数
- 类型：`(matches: RouteMatch[]) => TSelected`
- 若提供此函数，它将以路由匹配数组作为参数被调用，其返回值将作为 `useChildMatches` 的返回结果。该值还会通过浅层相等性检查来决定是否触发父组件的重新渲染。

### `opts.structuralSharing` 配置项

- 类型：`boolean`
- 可选参数
- 用于配置是否为 `select` 返回的值启用结构共享 (structural sharing) 优化。
- 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。

## useChildMatches 返回值

- 若提供了 `select` 函数，则返回该函数的执行结果。
- 若未提供 `select` 函数，则返回 [`RouteMatch`](./RouteMatchType.md) 对象数组。

## 使用示例

```tsx
import { useChildMatches } from '@tanstack/react-router'

function Component() {
  const childMatches = useChildMatches()
  // ...
}
```
