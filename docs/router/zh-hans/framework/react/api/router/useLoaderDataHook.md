---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:35:05.399Z'
id: useLoaderDataHook
title: useLoaderData hook
---

`useLoaderData` 钩子返回组件树中最近的 [`RouteMatch`](./RouteMatchType.md) 的加载器数据。

## useLoaderData 配置项

`useLoaderData` 钩子接收一个 `options` 配置对象。

### `opts.from` 配置项

- 类型: `string`
- 指定最近父级路由匹配项的 ID
- 可选，但推荐填写以保证完整的类型安全
- 若 `opts.strict` 为 `true` 且未提供此配置项，TypeScript 将发出警告
- 若 `opts.strict` 为 `false`，TypeScript 会对返回的加载器数据放宽类型限制

### `opts.strict` 配置项

- 类型: `boolean`
- 可选 - `默认值: true`
- 若设为 `false`，将忽略 `opts.from` 配置项，并放宽类型以反映所有可能的加载器数据的共享类型

### `opts.select` 配置项

- 可选
- `(loaderData: TLoaderData) => TSelected`
- 若提供此函数，它将接收加载器数据作为参数，其返回值将作为 `useLoaderData` 的返回结果。该值还会用于通过浅层比较决定是否触发父组件重新渲染

### `opts.structuralSharing` 配置项

- 类型: `boolean`
- 可选
- 配置是否对 `select` 返回的值启用结构共享
- 详见 [渲染优化指南](../../guide/render-optimizations.md)

## useLoaderData 返回值

- 若提供了 `select` 函数，则返回该函数的执行结果
- 若未提供 `select` 函数，则返回加载器数据；当 `opts.strict` 为 `false` 时返回放宽类型限制的版本

## 示例

```tsx
import { useLoaderData } from '@tanstack/react-router'

function Component() {
  const loaderData = useLoaderData({ from: '/posts/$postId' })
  //     ^? { postId: string, body: string, ... }
  // ...
}
```
