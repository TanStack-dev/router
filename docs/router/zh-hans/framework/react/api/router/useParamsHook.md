---
source-updated-at: '2025-02-08T21:36:40.000Z'
translation-updated-at: '2025-05-06T17:33:07.820Z'
id: useParamsHook
title: useParams hook
---

`useParams` 方法返回解析出的最接近匹配项及其所有父级匹配项的路径参数。

## useParams 选项

`useParams` 钩子接受一个可选的 `options` 对象。

### `opts.strict` 选项

- 类型: `boolean`
- 可选 - `默认值: true`
- 如果设为 `false`，将忽略 `opts.from` 选项，并将类型放宽为 `Partial<AllParams>` 以反映所有参数的共享类型。

### `opts.shouldThrow` 选项

- 类型: `boolean`
- 可选
- `默认值: true`
- 如果设为 `false`，当在当前渲染的匹配项中未找到匹配时，`useParams` 不会抛出不变式异常；此时会返回 `undefined`。

### `opts.select` 选项

- 可选
- `(params: AllParams) => TSelected`
- 如果提供，此函数将接收参数对象作为输入，其返回值将作为 `useParams` 的返回结果。该值还会用于通过浅层相等性检查决定是否触发父组件的重新渲染。

### `opts.structuralSharing` 选项

- 类型: `boolean`
- 可选
- 配置是否为 `select` 返回的值启用结构共享 (structural sharing)。
- 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。

## useParams 返回值

- 匹配项及其父级匹配项的路径参数对象；如果提供了 `select` 函数，则返回 `TSelected` 类型值。

## 示例

```tsx
import { useParams } from '@tanstack/react-router'

const routeApi = getRouteApi('/posts/$postId')

function Component() {
  const params = useParams({ from: '/posts/$postId' })

  // 或

  const routeParams = routeApi.useParams()

  // 或

  const postId = useParams({
    from: '/posts/$postId',
    select: (params) => params.postId,
  })

  // 或

  const looseParams = useParams({ strict: false })

  // ...
}
```
