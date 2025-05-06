---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:34:44.691Z'
id: useLoaderDepsHook
title: useLoaderDeps hook
---

`useLoaderDeps` 钩子 (hook) 用于返回一个包含依赖项的对象，这些依赖项用于触发指定路由的 `loader`。

## useLoaderDepsHook 选项

`useLoaderDepsHook` 钩子 (hook) 接收一个 `options` 配置对象。

### `opts.from` 选项

- 类型: `string`
- 必填
- 用于获取加载器 (loader) 依赖项的 RouteID 或路径。

### `opts.select` 选项

- 类型: `(deps: TLoaderDeps) => TSelected`
- 可选
- 如果提供，该函数会接收加载器 (loader) 依赖项对象作为参数，其返回值将作为 `useLoaderDeps` 的返回结果。

### `opts.structuralSharing` 选项

- 类型: `boolean`
- 可选
- 配置是否为 `select` 返回的值启用结构共享 (structural sharing)。
- 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。

## useLoaderDeps 返回值

- 加载器 (loader) 依赖项对象；如果提供了 `select` 函数，则返回 `TSelected` 类型的结果。

## 示例

```tsx
import { useLoaderDeps } from '@tanstack/react-router'

const routeApi = getRouteApi('/posts/$postId')

function Component() {
  const deps = useLoaderDeps({ from: '/posts/$postId' })

  // 或

  const routeDeps = routeApi.useLoaderDeps()

  // 或

  const postId = useLoaderDeps({
    from: '/posts',
    select: (deps) => deps.view,
  })

  // ...
}
```
