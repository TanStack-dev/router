---
source-updated-at: '2025-02-08T21:36:40.000Z'
translation-updated-at: '2025-05-06T17:31:17.328Z'
id: useSearchHook
title: useSearch hook
---

`useSearch` 方法是一个钩子 (hook)，用于以对象形式返回当前路径的搜索查询参数。该钩子在组件中访问当前搜索字符串和查询参数时非常有用。

## useSearch 选项

`useSearch` 钩子接受一个 `options` 对象作为参数。

### `opts.from` 选项

- 类型: `string`
- 必填
- 用于匹配搜索查询参数的路由 ID (RouteID)。

### `opts.shouldThrow` 选项

- 类型: `boolean`
- 可选
- `默认值: true`
- 如果设为 `false`，当在当前渲染的匹配项中未找到匹配时，`useSearch` 不会抛出异常 (invariant exception)；此时会返回 `undefined`。

### `opts.select` 选项

- 类型: `(search: SelectedSearchSchema) => TSelected`
- 可选
- 如果提供此函数，它会被传入搜索对象并调用，其返回值将作为 `useSearch` 的返回结果。

### `opts.structuralSharing` 选项

- 类型: `boolean`
- 可选
- 配置是否为 `select` 返回的值启用结构共享 (structural sharing)。
- 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。

### `opts.strict` 选项

- 类型: `boolean`
- 可选 - `默认值: true`
- 如果设为 `false`，将忽略 `opts.from` 选项，并将类型放宽为 `Partial<FullSearchSchema>` 以反映所有搜索查询参数的共享类型。

## useSearch 返回值

- 如果提供了 `opts.from`，则返回当前路径的搜索查询参数对象；如果提供了 `select` 函数，则返回 `TSelected`。
- 如果 `opts.strict` 为 `false`，则返回当前路径的搜索查询参数对象；如果提供了 `select` 函数，则返回 `TSelected`。

## 示例

```tsx
import { useSearch } from '@tanstack/react-router'

function Component() {
  const search = useSearch({ from: '/posts/$postId' })
  //    ^ FullSearchSchema

  // 或

  const selected = useSearch({
    from: '/posts/$postId',
    select: (search) => search.postView,
  })
  //    ^ string

  // 或

  const looseSearch = useSearch({ strict: false })
  //    ^ Partial<FullSearchSchema>

  // ...
}
```
